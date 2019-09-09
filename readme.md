# Build a static site with WordPress

In this workshop, you'll learn how to create a static site which pull content from WordPress using GatsbyJS. It's a hand on workshop so this guide covers everything you need to make that happen.

## Preparation

It's very important that each of these are in a working state as it can severly hamper your ability to participate in the workshop. If there is any command you need to run, run it in terminal.

- NPM - [Available with Node](https://nodejs.org/en/). Please run `node --version` to verify that it works.
- Gatsby CLI - Run `npm install -g gatsby-cli` to install. Please run `gatsby-cli info` to verify that it works.
- A WordPress site - Get a free one at [QueryWP](https://querywp.com/wcjhb). Make sure http://yourdomain.com/wp/wp-json/v2/ shows you something.
- IDE - [VS Code](https://code.visualstudio.com/), [Sublime Text 2](https://www.sublimetext.com/) or whatever you prefer.

## Step 1

Setting up a Gatsby project is pretty easy. Open up terminal and run:

`gatsby new wcjhb`.

Then move into the directory by going

`cd wcjhb`.

You'll now have a Gatsby project complete with React installed already. Run `gatsby develop` and visit [http://localhost:8000/](http://localhost:8000/).

We have a working website!

![excited](https://media.giphy.com/media/y8Mz1yj13s3kI/giphy.gif)

## Step 2

Now let's learn a little bit of React. We're not going to do a deep dive so just follow along and feel free to ask any questions.

Open the folder you created earlier in step 1 (`wcjhb`) inside your favourite code editor. You should see a bunch of folders and a few files. Let's open up `src/pages/index.js` and have a look at it. If this this is the first time you're seeing React, don't be alarmed! Here are things to note:

- It's mainly Javascript but with a templating syntax called JSX.
- JSX is very similar to HTML except you can put other React components in it which gets compiled.
- Because `gatsby develop` is running in the background, the development server is listening for changes.

Let's make a chance, change:

`<h1>Hi people</h1>`

with:

`<h1>Hi, my name is YOURNAME</h1>` <-- Your actual name.

Then visit the page you opened earlier ([http://localhost:8000/](http://localhost:8000/)) and you should see that it's already changed. Go ahead and change a few more bits of text and see the changes happening instantly.

![hackerman](https://media.giphy.com/media/26tPnAAJxXTvpLwJy/giphy.gif)

## Step 3

So whilst that is very fun, I don't think it's very scalable if you're wanting to write hundreds of blog posts so let's get everything connected to WordPress. Open up terminal, stop the running process (`ctrl + c`) and run the following command:

`npm install --save gatsby-source-wordpress`

This downloads the WordPress plugin and installs it in your project. There is still a little config we need to do. Replace the contents of `gatsby-config.js` with:

```
module.exports = {
  siteMetadata: {
    title: `Gatsby Default Starter`,
    description: `Kick off your next, great Gatsby project with this default starter. This barebones starter ships with the main Gatsby configuration files you might need.`,
    author: `@gatsbyjs`,
  },
  plugins: [
    `gatsby-plugin-react-helmet`,
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        name: `images`,
        path: `${__dirname}/src/images`,
      },
    },
    `gatsby-transformer-sharp`,
    `gatsby-plugin-sharp`,
    {
      resolve: `gatsby-plugin-manifest`,
      options: {
        name: `gatsby-starter-default`,
        short_name: `starter`,
        start_url: `/`,
        background_color: `#663399`,
        theme_color: `#663399`,
        display: `minimal-ui`,
        icon: `src/images/gatsby-icon.png`, // This path is relative to the root of the site.
      },
    },
    {
      resolve: "gatsby-source-wordpress",
      options: {
        /*
         * The base URL of the Wordpress site without the trailingslash and the protocol. This is required.
         * Example : 'gatsbyjsexamplewordpress.wordpress.com' or 'www.example-site.com'
         */
        baseUrl: "wcjhb19.api.querywp.com",
        // The protocol. This can be http or https.
        protocol: `https`,
        // Indicates whether the site is hosted on wordpress.com.
        // If false, then the assumption is made that the site is self hosted.
        // If true, then the plugin will source its content on wordpress.com using the JSON REST API V2.
        // If your site is hosted on wordpress.org, then set this to false.
        hostingWPCOM: false,
        // If useACF is true, then the source plugin will try to import the WordPress ACF Plugin contents.
        // This feature is untested for sites hosted on WordPress.com
        useACF: false,
        // Set WP REST API routes whitelists
        // and blacklists using glob patterns.
        // Defaults to whitelist the routes shown
        // in the example below.
        // See: https://github.com/isaacs/minimatch
        // Example:  `["/*/*/comments", "/yoast/**"]`
        // ` will either include or exclude routes ending in `comments` and
        // all routes that begin with `yoast` from fetch.
        // Whitelisted routes using glob patterns
        includedRoutes: [
          "**/categories",
          "**/posts",
          "**/pages",
          "**/media",
          "**/tags",
          "**/taxonomies",
          "**/users",
        ],
      },
    },
  ],
}
```

Now run `gatsby develop` again and you will see it starting to fetch content for WordPress.

![download](https://media.giphy.com/media/FgiHOQyKUJmwg/giphy.gif)

## Step 4

Let's show some of the blog posts on our homepage. Take the following and replace everything in `src/pages/index.js`:

```
import React from "react"
import { graphql } from "gatsby"
import Layout from "../components/layout"
import SEO from "../components/seo"
export default ({ data }) => {
  return (
    <Layout>
      <SEO title="home" />
      <h1>My WordPress Blog</h1>
      <h4>Posts</h4>
      {data.allWordpressPost.edges.map(({ node }) => (
        <div>
          <p>{node.title}</p>
          <div dangerouslySetInnerHTML={{ __html: node.excerpt }} />
        </div>
      ))}
    </Layout>
  )
}

export const pageQuery = graphql`
  query {
    allWordpressPost {
      edges {
        node {
          title
          excerpt
          slug
        }
      }
    }
  }
`
```

Visit your dev site again and look at all the content. Refresh it a few times and look how fast it is! That's because it's just basically HTML.

![rocket](https://media.giphy.com/media/SQgbkziuGrNxS/giphy.gif)

(I am not referring to WordPress as a snail!)

## Step 5

Learn about Router and links

## Step 6

Link up the posts

## Step 7

Create a page template

## Step 8

Create a menu and link pages

## Step 9

Create a taxonomy template

## Step 10

Pagination

## Step 11

Deployment with Netlify