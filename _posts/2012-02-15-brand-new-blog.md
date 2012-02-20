---
layout: post
---

Today I decided to abandon my Posterous blog and move over to GitHub Pages. The reason I chose Posterous in the first place was its [Markdown support](http://posterous.uservoice.com/knowledgebase/articles/36531-getting-started-with-markdown) and built-in syntax highlighting. Unfortunately both features are quite cumbersome. When I wrote my last post, the syntax highlighting just didn't work anymore. While searching for a solution I came across [Paul Kinlan's post](https://plus.google.com/116059998563577101552/posts/ehd9CLM9XKQ) which inspired me to try something completely different instead.

After a brief look at [calepin.co](http://calepin.co/) and [scriptogr.am](http://scriptogr.am/) I decided give [jekyllbootstrap](http://jekyllbootstrap.com/) a try. It was interesting to see how much one could squeeze out of a bunch of [Liquid](http://liquidmarkup.org/) templates and a [Rakefile](https://github.com/plusjade/jekyll-bootstrap/blob/master/Rakefile) but like most bootstrap projects this felt a little bit bloated.

## KISS

I decided to keep it simple instead and started from scratch. Since everything I blog about is usually related to a GitHub project in some way, I wanted a theme that resembled the look and feel of a GitHub project page with the same formatting as a project's README. Especially code snippets should look exactly the same.

I stole some design elements from the [official GitHub blog](https://github.com/blog) and made the layout responsive, so that you get a nice one-column view on mobile devices.

Another great thing about GitHub pages is that you get a decent web-based editor for free as well as a version history of your posts and the possibility to work collaboratively.

![Screenshot](/images/2012-02-15-screenshot.jpg)

