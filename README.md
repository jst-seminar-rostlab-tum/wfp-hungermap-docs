# WFP HungerMap Documentation

This software is dual-licensed:
1. **Open-Source License**: Distributed under the terms of the Server Side Public License (SSPL). See the `LICENSE` file for details.
2. **Commercial License**: Available for businesses requiring proprietary use. Contact the Center for Software Engineering Excellence at partners@csee.tech for more information.
Failure to comply with either license will constitute a violation of intellectual property rights.

This website is built using [Docusaurus](https://docusaurus.io/), a modern open-source static website generator.

## Guidelines

- Add each page as a markdown file under the `/docs/docs/{category}` directory, where category is the topic this document belongs to (Frontend, Chatbot, How to, etc.).
- Add the title of the page to the start of the file as a Heading 1 (with `# `)
- Use the same title as the file name, but in snake_case (How to add new Event -> `how_to_add_new_event`)
- Add your name to the top of the file (under the title) like this: `Author: {full name}`

### Installation

```
$ yarn
```

### Local Development

```
$ yarn start
```

This command starts a local development server and opens up a browser window. Most changes are reflected live without having to restart the server.

### Build

```
$ yarn build
```

This command generates static content into the `build` directory and can be served using any static contents hosting service.

