# Build a static site with WordPress

In this workshop, you'll learn how to create a static site which pull content from WordPress using GatsbyJS. It's a hand on workshop so this guide covers everything you need to make that happen.

## Preparation

It's very important that each of these are in a working state as it can severly hamper your ability to participate in the workshop. If there is any command you need to run, run it in terminal.

- NPM - [Available with Node](https://nodejs.org/en/). Please run `node --version` to verify that it works.
- Gatsby CLI - Run `npm install -g gatsby-cli` to install. Please run `gatsby-cli info` to verify that it works.
- A WordPress site - Get a free headless one at QueryWP (use the beta signup and we will add you). For the site to work, make sure http://replacewithyoururl.com/wp-json/wp/v2/ shows you something.
- If you need dummy content, you can get that [here](https://wpcom-themes.svn.automattic.com/demo/theme-unit-test-data.xml).
- IDE - [VS Code](https://code.visualstudio.com/), [Sublime Text 2](https://www.sublimetext.com/) or whatever you prefer.
- A free [Netlify](https://www.netlify.com/) account. 
- A free [Github](https://github.com/) account. 

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
          wordpress_id
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

Let's start looking at actually reading full blog posts. This is where we really dig into the power of Gatsby as a static generator. Open your `gatsby-node.js` and replace with the following:

```
const path = require(`path`)
exports.createPages = ({ graphql, actions }) => {
  const { createPage } = actions
  return graphql(`
    {
      allWordpressPost(sort: { fields: [date] }) {
        edges {
          node {
            wordpress_id
            title
            excerpt
            content
            slug
          }
        }
      }
    }
  `).then(result => {
    result.data.allWordpressPost.edges.forEach(({ node }) => {
      createPage({
        path: node.slug,
        component: path.resolve(`./src/templates/blog-post.js`),
        context: {
          // This is the $slug variable
          // passed to blog-post.js
          slug: node.slug,
        },
      })
    })
  })
}
```

This basically tells Gatsby to go and fetch all the WordPress posts and generate a page for them in your build. Now before we get excited, we need to create a template for the post to use (you can see it referenced in the code). Create a new folder in `src` called `template` and create a file called `blog-post.js`. You can put the following code inside of it:

```
import React from "react"
import Layout from "../components/layout"
import { graphql } from "gatsby"
export default ({ data }) => {
  const post = data.allWordpressPost.edges[0].node
  console.log(post)
  return (
    <Layout>
      <div>
        <h1>{post.title}</h1>
        <div dangerouslySetInnerHTML={{ __html: post.content }} />
      </div>
    </Layout>
  )
}
export const query = graphql`
  query($slug: String!) {
    allWordpressPost(filter: { slug: { eq: $slug } }) {
      edges {
        node {
          title
          content
        }
      }
    }
  }
`
```

Lastly, let's link to these posts from the home page to actually make our blog useful. Open up `src/pages/index.js` again and replace

`import { graphql } from "gatsby"`

with

`import { Link, graphql } from "gatsby"`

and replace

`<p>{node.title}</p>`

with

```
<Link to={`/${node.slug}`}>
  <p>{node.title}</p>
</Link>
```

Go ahead and restart the process again. `ctrl + c` and then `gatsby develop`. Then back in your browser you should now be able to open links to each blog post (#protip: click the text in the header to go back home).

![cooool](https://media.giphy.com/media/nFFguNjdeotwc/giphy.gif)

# Step 6

The next step is to take all the lovely images you have and to actually store those along with your static site. At the same time, we can optimise them and only load the sizes we need (woah!). Back in terminal, stop the running process (`ctrl + c`) and run the following command:

`npm install --save gatsby-transformer-sharp gatsby-plugin-sharp gatsby-image`

Once you've done that, you can type `npm run start` again. You can then open `src/pages/index.js` and replace the following section:

```
export const pageQuery = graphql`
  query {
    allWordpressPost {
      edges {
        node {
          wordpress_id
          title
          excerpt
          slug
        }
      }
    }
  }
`
```

with:

```
export const pageQuery = graphql`
  query {
    allWordpressPost {
      edges {
        node {
          wordpress_id
          title
          excerpt
          slug
          featured_media {
            localFile {
              childImageSharp {
                fluid(maxWidth: 1024) {
                  src
                }
              }
            }
          }
        }
      }
    }
  }
`
```

All we need to do now is add the image to our blog listing page. To this this, add the following code underneat the `<Link>` tag you added earlier:

`{node.featured_media && node.featured_media.localFile.childImageSharp && <img src={node.featured_media.localFile.childImageSharp.fluid.src} alt={node.title} title={node.title} />}`

You should now be able to see images in your blog (if you have featured images that is!).

![looking good](https://media.giphy.com/media/2cdYrnU2YsUIOHOgiu/giphy.gif)

## Step 12

First, log into Github and create a repository (`+` icon on the top right). Call it whatever you like. Once you've created it, it will show a URL for, copy it.

Then open your browser and top the server. You can then run the following commands one by one:

```
git init
git remote add origin https://github.com/username/repo.git <-- replace this with your URL.
git add .
git commit -m "let's get this live!"
git push origin master
```

This should push your code onto github :D 

Now log into Netlify and click "New site from git", connect to Github (it should just ask for permission).

In the "Build command" input, add `npm run build`.

In the "Publish directory" input, add `/public`.

Click save and wait.

![waiting](https://media.giphy.com/media/tXL4FHPSnVJ0A/giphy.gif)

After a little while, you should have a "functioning" blog. It's still missing a bunch of things and could really use a makeover but I truly hope that this sparked a little bit of interest into the world of static sites.