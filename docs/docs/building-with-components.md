---
title: Building with Components
---

## Requirements

To use Gatsby, you will need a basic understanding of React components.

The [official tutorial](https://facebook.github.io/react/tutorial/tutorial.html) is a good place to start.

## Why React components?

React's component architecture simplifies building large websites by encouraging modularity, reusabilty, and clear abstraction. React has a large ecosystem of open source components, tutorials, and tooling that can be used seamlessly for building sites with Gatsby. Gatsby is built to behave almost exactly like a normal React application.

[Thinking in React](https://facebook.github.io/react/docs/thinking-in-react.html) is a good resource for learning how to structure applications with React.

## How does Gatsby use React Components?

Everything in Gatsby is built using components.

A basic directory structure of a project might look like this:

```sh
.
├── gatsby-config.js
├── package.json
└── src
    ├── html.jsx
    ├── pages
    │   ├── index.jsx
    │   └── posts
    │       ├── 01-01-2017
    │       │   └── index.md
    │       ├── 01-02-2017
    │       │   └── index.md
    │       └── 01-03-2017
    │           └── index.md
    ├── templates
    │   └── post.jsx
    │
    └── layouts
        └── index.jsx
```

### HTML component

`src/html.jsx` is responsible for everything other than where Gatsby lives in the `<body />`.

In this file you can modify the `<head>` metadata, general structure of the document and add external links.

Example:

```jsx
import React from 'react';
import favicon from './favicon.png';

let inlinedStyles = '';
if (process.env.NODE_ENV === 'production') {
  try {
    inlinedStyles = require('!raw-loader!../public/styles.css');
  } catch (e) {
    console.log(e);
  }
}

export default class HTML extends React.Component {
  render() {
    let css;
    if (process.env.NODE_ENV === 'production') {
      css = (
        <style
          id="gatsby-inlined-css"
          dangerouslySetInnerHTML={{ __html: inlinedStyles }}
        />
      );
    }
    return (

      <html lang="en">
        <head>
          <meta charSet="utf-8" />
          <meta
            name="viewport"
            content="width=device-width, initial-scale=1.0"
          />
          {this.props.headComponents}
          <link rel="shortcut icon" href={favicon} />
          {css}
        </head>
        <body>
          <div
            id="___gatsby"
            dangerouslySetInnerHTML={{ __html: this.props.body }}
          />
          {this.props.postBodyComponents}
        </body>
      </html>
    );
  }
}
```

### Layout components

`src/layouts/index.jsx` (optional) wraps page components. You can use it for portions of pages that are shared across pages like headers and footers.

Example:

```jsx
import React from 'react';
import Navigation from '../components/Navigation/Navigation.jsx';

export default class Template extends React.Component {

  render() {
    return (
      <Navigation>
        {this.props.children()}
      </Navigation>
    );
  }
}
```

### Page components

Components under `src/pages` become pages automatically with paths based on their file name. For example `src/pages/index.jsx` is mapped to `yoursite.com` and `src/pages/about.jsx` becomes `yoursite.com/about/`.

Example:

`src/pages/about.jsx`

```jsx
import React, { Component } from 'react';

class AboutPage extends Component {
  render() {
    const config = this.props.data.site.siteMetadata;
    return (
        <div className="about-container">
          <p>About me.</p>
        </div>
    );
  }
}

export default AboutPage;
```

### Page template components

You can programatically create pages using "page template components". All pages are React components but very often these components are fairly simple wrappers around data from files or other sources.

`src/templates/post.jsx` is an example of a page component. It queries GraphQL for markdown data and then renders the page using this data.

Example:

```jsx
import React from "react"

class BlogPostTemplate extends React.Component {
  render() {
    const post = this.props.data.markdownRemark

    return (
      <div>
        <h1>{post.frontmatter.title}</h1>
        <div dangerouslySetInnerHTML={{ __html: post.html }} />
      </div>
    )
  }
}

export default BlogPostTemplate

export const pageQuery = graphql`
query BlogPostBySlug($slug: String!) {
  markdownRemark(fields: { slug: { eq: $slug }}) {
    html
    frontmatter {
      title
    }
  }
}
`
```

These are examples of the different ways React components are used in Gatsby sites. To see full working examples, check out the [examples directory](https://github.com/gatsbyjs/gatsby/tree/master/examples) in the Gatsby repo.
