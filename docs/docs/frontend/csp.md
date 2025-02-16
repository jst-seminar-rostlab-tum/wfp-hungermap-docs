# Content Security Policy

**Author:** `Sámuel Fekete`

Content Security Policy (CSP) is a feature that helps to prevent or minimize the risk of certain types of security threats. It consists of a series of instructions from a website to a browser, which instruct the browser to place restrictions on the things that the code comprising the site is allowed to do. [Read more here](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP).

To align with best practices, our application also uses CSP to ensure that our users won't be a victim of an XSS attack. In Next.js, a Content Security Policy can be defined in the root middleware file (`/src/middleware.ts`). We define the CSP header here and attach it to every request and response of our application, as this middleware will be called by Next for all of them.

In the CSP, we can define from which sources do we allow the execution of Javascript (`script-src` line). Since Next.js splits our source code into many chunks and includes some of them inline, we couldn't define a strong policy in this case. `'unsafe-inline'` means that inline scripts can be executed, but since we're using trusted frameworks and don't render user submitted information (especially not without parsing it), XSS attack should still be impossible. We also create a random nonce for every request and allow scripts that have this nonce to run. We then save this nonce as a header, and later the script that injects Google Analytics uses this nonce.

In the `connect-src` line, we can define which websites is our site allowed to download data from. WFP's various backend APIs and Google Analytics is listed here. If you're trying to fetch data from a different source and get a CSP error, you should add the domain here. The `img-src` line is responsible for limiting which sources to show images from. Again, if you want to render images from a different domain, add it here.