# PDF Preview

The **PDF Preview** feature allows users to view and interact with PDF documents directly within the application. This feature is implemented using the [react-pdf](https://www.npmjs.com/package/react-pdf) library for rendering PDFs, and it integrates seamlessly into the user interface by providing a modal-based preview. 

## Features

The PDF Preview feature provides a modal-based interface where users can:

1. View PDF documents.
2. Scroll through pages.
3. Download the PDF and its related data (e.g., JSON or CSV).

## Design

The modal is implemented using `@nextui-org/modal` and provides a user-friendly interface for previewing PDFs.

### Backdrop and Styling 
The modal includes a gradient backdrop for enhanced visuals.
![PDF Preview Modal](/img/pdf_preview_backdrop.png)
### Error Handling 
If an error occurs (e.g., file not found), a user-friendly error message is displayed.
![PDF Preview Error](/img/pdf_preview_error.png)

### Top Bar
Top bar includes page number, download options, and close button. Download options (PDF, JSON, CSV) are available via a dropdown menu. If an option is not available, it is grayed out.
![PDF Preview Top Bar](/img/pdf_preview_download.png)

## Technical Details

The PDF Preview feature consists of the following components:

### PdfPreview
The main entry point for the feature, responsible for displaying the modal and handling user interactions like opening and closing the preview. In order to display a PDF, the `PdfPreview` component requires the following props:
1. `isModalOpen`: A boolean value indicating whether the modal is open or closed.
2. `toggleModal`: A function to toggle the modal open or closed.
3. `pdfFile`: The PDF file to be displayed. PDF files can be passed as a string representing the name of a file located in public folder or as a [Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob) object representing the PDF file itself. 
4. `error`: An optional error message to display in case the PDF file cannot be loaded.
5. `onDownloadPdf`: A function to handle the download of the PDF file.

### PdfLoader
A lightweight wrapper that dynamically loads the `PdfViewer` component without server-side rendering (SSR). This was implemented to prevent issues `react-pdf` has with `next.js`. 

### PdfViewer 
Handles the actual rendering of the PDF file using `react-pdf`. Includes functionality for text selection, downloads and scrolling through pages. Some technical details include:
- **Responsive Design**: The PDF viewer is responsive and adjusts to the size of the modal by registering an event listener for window resize events and controlling `pageWidth` parameter of the `Page` component depending on the modal width.
```typescript
const onResize = useCallback(() => {
    setPageWidth(topBarRef.current ? topBarRef.current.offsetWidth * 0.8 : window.innerWidth * 0.9);
}, []);
...
useEffect(() => {
    window.addEventListener('resize', onResize);
    ...
    return () => {
        window.removeEventListener('resize', onResize);
    };
}, [onResize]);
```
- **Current Page Number**: The current page number is displayed in the top bar and is updated when the user scrolls through the pages. This is achieved by scheduling a repeated task to track current visible page. A function `trackVisiblePage` is called every 100ms to determine the current page number based on the visible area of the pages.
```typescript
useEffect(() => {
    const trackPageInterval = setInterval(() => {
      PdfViewerOperations.trackVisiblePage(document, setPageNumber);
    }, 100);

    return () => {
      clearInterval(trackPageInterval);
    };
  }, []);
...
static trackVisiblePage(document: Document, updatePageNumber: (pageNumber: number) => void): void {
    const pages = document.querySelectorAll('.react-pdf__Page');
    let largestVisibleArea = 0;
    let currentPage = 1;

    pages.forEach((page, index) => {
      const rect = page.getBoundingClientRect();
      const visibleHeight = Math.min(rect.bottom, window.innerHeight) - Math.max(rect.top, 0);
      if (visibleHeight > largestVisibleArea && visibleHeight > 0) {
        largestVisibleArea = visibleHeight;
        currentPage = index + 1;
      }
    });

    updatePageNumber(currentPage);
}
```

### PDF Worker
For `react-pdf` to work, a PDF.js worker needs to be provided. This worker is a separate file that is loaded in the background and contains the logic for rendering a PDF. The file is provided using a url to the official `pdfjs` worker.
```typescript
pdfjs.GlobalWorkerOptions.workerSrc = `//cdnjs.cloudflare.com/ajax/libs/pdf.js/${pdfjs.version}/pdf.worker.min.mjs`;
```
This approach works in production, it fails in development due to CORS issues. To solve this the `workerSrc` can be set to a local file from the `pdfjs-dist` package.
```typescript
pdfjs.GlobalWorkerOptions.workerSrc = new URL(
  'pdfjs-dist/build/pdf.worker.min.mjs',
  import.meta.url,
).toString();
```