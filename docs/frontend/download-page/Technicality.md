## Technicality

The `Download Portal` page is designed to provide users with various reports and data downloads. It utilizes an accordion interface to organize the content into different sections. Each section is represented by a React component that displays specific types of data. The components used are:

  - `YearInReviewReports`
  - `CountryReports`
  - `DownloadCountryAccordion`

### Components Overview

1. **YearInReviewReports**: Displays reports for a specific year.
2. **CountryReports**: Displays reports for different countries.
3. **DownloadCountryAccordion**: Provides an accordion interface for downloading country-specific data.

### Example Usage

#### YearInReviewReports Component

The `YearInReviewReports` component is used to display reports for a specific year.
**Props**:
  - `yearInReviewReports`: An array of objects containing `label` and `url` for each report.

**Example:**
```tsx
import YearInReviewReports from './components/YearInReviewReports';

const yearInReviews: YearInReview[] = [
  { label: '2022 Report', url: 'https://example.com/2022-report' },
  { label: '2021 Report', url: 'https://example.com/2021-report' },
];

<YearInReviewReports yearInReviewReports={yearInReviews} />
```

#### CountryReports Component

The `CountryReports` component is used to display reports for different countries.

**Props**:
  - `countryCodesData`: An array of objects containing `country` information and `url` for each report.

**Example:**
```tsx
import CountryReports from './components/CountryReports';

// this is only an example data, in real you should according to DDD implement Repository to communicate with backend
const countryCodesData: CountryCodesData[] = [
  {
    country: { id: 1, name: 'United States', iso3: 'USA', iso2: 'US' },
    url: { summary: 'https://example.com/us-report' },
  },
  {
    country: { id: 2, name: 'Canada', iso3: 'CAN', iso2: 'CA' },
    url: { summary: 'https://example.com/ca-report' },
  },
];

<CountryReports countryCodesData={countryCodesData} />
```

#### DownloadCountryAccordion Component

The `DownloadCountryAccordion` component is used to display a list of countries in an accordion format, allowing users to download data for each country.

**Props**:
  - `countries`: An array of objects containing `id`, `name`, `iso3`, and `iso2` for each country.

**Example:**
```tsx
import DownloadCountryAccordion from './components/DownloadCountryAccordion';

// this is only an example data, in real you should according to DDD implement Repository to communicate with backend

const countries: ICountry[] = [
  { id: 1, name: 'United States', iso3: 'USA', iso2: 'US' },
  { id: 2, name: 'Canada', iso3: 'CAN', iso2: 'CA' },
];


<DownloadCountryAccordion countries={countries} />
```

Here is how you can integrate these components into a page using an accordion container:

```tsx
import React from 'react';
import AccordionContainer from './components/AccordionContainer';
import YearInReviewReports from './components/YearInReviewReports';
import CountryReports from './components/CountryReports';
import DownloadCountryAccordion from './components/DownloadCountryAccordion';

const yearInReviews: YearInReview[] = [
  { label: '2022 Report', url: 'https://example.com/2022-report' },
  { label: '2021 Report', url: 'https://example.com/2021-report' },
];

const countryCodesData: CountryCodesData[] = [
  {
    country: { id: 1, name: 'United States', iso3: 'USA', iso2: 'US' },
    url: { summary: 'https://example.com/us-report' },
  },
  {
    country: { id: 2, name: 'Canada', iso3: 'CAN', iso2: 'CA' },
    url: { summary: 'https://example.com/ca-report' },
  },
];

const countries: ICountry[] = [
  { id: 1, name: 'United States', iso3: 'USA', iso2: 'US' },
  { id: 2, name: 'Canada', iso3: 'CAN', iso2: 'CA' },
];

const DownloadPortalPage = () => {
  return (
    <div>
      <h1>Download Portal</h1>
      <div>
        <AccordionContainer
          multipleSelectionMode
          items={[
            {
              title: 'Year In Review Reports',
              content: <YearInReviewReports yearInReviewReports={yearInReviews} />,
            },
            {
              title: 'Country Reports',
              content: <CountryReports countryCodesData={countryCodesData} />,
            },
            {
              title: 'Export Country Data',
              content: <DownloadCountryAccordion countries={countries} />,
            },
          ]}
        />
      </div>
    </div>
  );
};

export default DownloadPortalPage;
```

This example demonstrates how to use the `YearInReviewReports`, `CountryReports`, and `DownloadCountryAccordion` components within an accordion container on a download portal page.