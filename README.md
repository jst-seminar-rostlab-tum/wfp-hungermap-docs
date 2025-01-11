# WFP HungerMap Documentation

This software is dual-licensed:
1. **Open-Source License**: Distributed under the terms of the GNU General Public License (GPL). See the `LICENSE` file for details.
2. **Commercial License**: Available for businesses requiring proprietary use. Contact the Center for Software Engineering Excellence at partners@csee.tech for more information.
Failure to comply with either license will constitute a violation of intellectual property rights.

This website is built using [Docusaurus](https://docusaurus.io/), a modern static website generator.

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

### Deployment

Using SSH:

```
$ USE_SSH=true yarn deploy
```

Not using SSH:

```
$ GIT_USER=<Your GitHub username> yarn deploy
```

If you are using GitHub pages for hosting, this command is a convenient way to build the website and push to the `gh-pages` branch.
