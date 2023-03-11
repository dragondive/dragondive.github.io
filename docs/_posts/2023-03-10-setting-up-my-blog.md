---
layout: post
title:  "Setting up my new blog"
comments: true
share: true
tags:
  - software development
  - github
  - jekyll
  - how to
  - setup
  - git
  - web development
---

My [previous post]({% post_url 2023-03-10-start-of-my-new-blog %}) described my motivation to start the blog and why I chose Github Pages with Jekyll for the blog hosting. In this post, I will describe the technical setup—mostly for my future reference. :wink:

## Preparing the Github repository

_[Github provides one "free" personal site on Github Pages. I already had a Github account [https://github.com/dragondive/](https://github.com/dragondive/).]_ 

The server side setup was just creating a new public repository with the name exactly matching [dragondive.github.io](https://github.com/dragondive/dragondive.github.io/) as described in [their documentation](https://pages.github.com/).

_[The client side setup was also simple. For practical reasons, I use Windows on my personal laptop for development. Using [chocolatey](https://chocolatey.org/), I already have many developer tools installed (and regularly updated) and a readily working developer environment.]_ 

I made a local clone of the above repository and was ready for the next step.

## Installing WSL2, Ruby and Jekyll

### WSL2 installation

_[Linux environment still remains much more practically useful for developers than Windows. The [Windows Subsystem for Linux](https://learn.microsoft.com/en-us/windows/wsl/install) is generally an adequate alternative to a full Linux system.]_

WSL2 can be installed using chocolatey with `choco install wsl2`. The Ruby and Jekyll setups described in further sections require this.

I already had a `Ubuntu` distribution installed previously. Nonetheless, I removed the previous installation and reinstalled it. This is only a matter of running two commands and following the instructions that come up—and, of course, restarting the system a few times.

```bash
wsl --unregister Ubuntu     # remove previous Ubuntu
wsl --install -d Ubuntu     # install Ubuntu
```

### Ruby and Jekyll installation

To install Ruby and Jekyll, I followed the installation instructions [Jekyll on Windows](https://jekyllrb.com/docs/installation/windows/). However, this Stack Overflow answer [You don't have write permissions for the /var/lib/gems/2.3.0 directory](https://stackoverflow.com/a/54440419) is more practically useful for the Ruby installation. I also faced some access issues with the installation instructions, which were resolved by following the Stack Overflow answer.

## Creating a local site

Github's instructions here [Creating your site](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll#creating-your-site) are simple to follow.

## Publish to Github Pages

The posts can be written in the `_posts` folder as markdown files. See [Posts \| Jekyll](https://jekyllrb.com/docs/posts/) to get started. There are various Jekyll attributes that provide more features, but I can learn them over time. :smile:

Publishing the website consists of simply pushing the changes to the `gh-pages` branch (which must be configured in the repository's settings on Github). The Github Actions performs the Jekyll build which converts the markdown files into HTML.

_[I already had written elsewhere the content of my first post.  [How many tampons does a woman astronaut need in space?]({% post_url 2023-03-08-how-many-tampons-does-a-woman-need-in-space %}) I rewrote it markdown and published it right away.]_

I struggled for a while with alignment and caption of images but after trying various suggestions on Stack Overflow and Github forums, I went with raw HTML for now, as suggested in [this answer](https://stackoverflow.com/a/44951884). _[I may come back to a better approach when I have a better understanding of Jekyll.]_

### Adding images to the posts

_[I didn't find the documentation very clear on where the Jekyll engine looks for images and how exactly to specify the relative path in the markdown.]_ After reading some forum posts and some experiments, I figured out that these are placed under the `assets/images` directory in the project's root, and the correct syntax in markdown is:

```markdown
![image_title](/assets/images/image-filename.extension){: jekyll inline attributes }
```

The Jekyll inline attributes can be used, for example, to apply css styles to the image.

## Doing a local build

_[For the first several times, I tested my changes by force pushing to Github. This is a needlessly complex and inefficient approach, besides the build with Github Actions alone took over a minute. I decided to "eventually" figure out how to first build locally after I got my blog in a reasonable working state. 

This turned out to be a great decision because when I did eventually get to it, I had to struggle for several hours with [dependency hell](https://www.techtarget.com/searchitoperations/definition/dependency-hell). Nonetheless, I write about it here since this order is more appropriate for my future reference.]_

The command to build locally itself is quite simple:

```bash
jekyll serve --watch --force-polling --livereload
```

This is supposed to host the website on `localhost:4000`. The additional flags do the following:

    --watch             rebuild the site when the source changes
    --force-polling     for a Windows-specific issue that source changes are not reliably detected
    --livereload        reload the webpage automatically after it is rebuilt

However, practically I remember at least three main dependency issues:

* ``<internal:core> core/kernel.rb:236:in `gem_original_require': cannot load such file -- webrick (LoadError)``

  This was resolved relatively easily by manually installing the `webrick` gem, even though this wasn't very intuitive for a beginner.

* ``/home/dragondive/.rbenv/versions/3.1.3/lib/ruby/gems/3.1.0/gems/bundler-2.4.8/lib/bundler/runtime.rb:304:in `check_for_activated_spec!': You have already activated i18n 1.12.0, but your Gemfile requires i18n 0.9.5. Prepending `bundle exec` to your command may solve this. (Gem::LoadError)``

  _[This violated the [Principle of Least Surprise](http://principles-wiki.net/principles:principle_of_least_surprise) for me. As a user, I did not explicitly ask for any specific version of `i18n` (or any other gem). I went with the default Gemfile that the Jekyll site creation command generated. It was surprising to me that these dependencies were not already addressed.]_ Nonetheless, I followed the stated advice to prepend `bundle exec` and the issue was "resolved".

* ``jekyll 3.9.2 | Error:  undefined method `tainted?' for "":String``

  This one, I struggled with, for the longest time. While I do not fully understand it, I found out that it is due to the underlying Ruby language introducing some breaking changes, as described [here](https://github.com/github/pages-gem/issues/859) and [here](https://talk.jekyllrb.com/t/liquid-4-0-3-tainted/7946).
  
  I eventually "fixed" this by downgrading the Ruby version to `3.1.3`. This did not cleanly work, so I had to get rid of my WSL2 Ubuntu installation entirely, create a new one and do all the steps from scratch. :unamused:

_[My first impression of Ruby has not been positive :angry:. I hope this changes in future as I know it better. I hope even more that I wouldn't need to know it any better. :disappointed:]_

## Customizing the blog theme

### Adventures with the minima theme

The blog uses [jekyll/minima](https://github.com/jekyll/minima) theme by default. It's nice, but I strongly prefer the dark mode. This was [easy to configure](https://github.com/jekyll/minima) in the `_config.yml` file:

```yml
minima:
  skin: dark
```

As I tried further customizations, I learned that the `minima` gem is not regularly updated. However, the `_config.yml` [could be configured](https://github.com/dragondive/dragondive.github.io/commit/a76541a5a5b48b02c7b138002552392391004be2) to use it directly from its github repository.

```yml
remote_theme: jekyll/minima
```

From the documentation [Understanding gem-based themes](https://jekyllrb.com/docs/themes/#understanding-gem-based-themes), I learned that for further customization, I could explicitly create some of the site's directories (which are included in the gem but hidden by default), so next I [locally copied those directories](https://github.com/dragondive/dragondive.github.io/commit/a50f0d95cfa13d70680248430faf13ab6c224012) from the theme's repository.

However, I struggled with several dead ends with this customization. In particular, my efforts to add a commenting system to the posts caused the layout and dark mode customization to mess up completely. After spending several hours trying to understand this, I decided to look for other themes to use instead. I had intended to do this eventually anyway.

### Switch to jekyll-clean-dark theme

I discovered the [jekyll-clean-dark](https://github.com/streetturtle/jekyll-clean-dark) theme by [Pavel Makhov](https://pavelmakhov.com/about/) and instantly loved it. I especially found the tags feature immensely beneficial as it suited my multi-dimensional professional identity. I cleaned up the `minima` artifacts and copied over the `jekyll-dark-theme` as suggested in the readme.

### Tagging the blog posts

Tagging with the `jekyll-dark-theme` consists of [two steps](https://github.com/dragondive/dragondive.github.io/commit/05cce3fc279dd7f3c3ec052bbff8d82d3aed8dc5):

* Add a list of tags in the post's front matter:

  ```yml
  tags:
  - DEI
  - story
  ```

* Generating the md files in `tag` directory, which can be done more conveniently [using the `admin` page](http://pavelmakhov.com/jekyll-clean-dark/2016/08/analytics-tags-comments/#tags)

## Adding a commenting system

_[I was familiar with [Disqus](https://disqus.com/) and [Discourse](https://www.discourse.org/) commenting systems and explored them for a while. Then my train of thoughts wandered to a discussion I had with a colleague in my corporate life in a different context. I was telling him about how Github provides an integrated CI/CD platform, Github Actions; an integrated issue tracker (albeit with limited functionality); and an integrated website hosting, Github Pages (although mostly for static content).]_

I wondered if there's a Github-based solution for commenting system as well. As expected, some genius minds had thought of that already. There is [utterances](https://utteranc.es/) and [giscus](https://giscus.app/). I chose giscus because it felt more logical that comments go to Github discussions than to Github issues. The giscus webpage provides a very easy interface to generate the integration code.

I read through the theme's code to understand better how it worked and to figure out where to enter the code for giscus integration. As a first trial, I included it in `_layouts/post.html` and it worked. Then I [reoganized and cleaned up the setup](https://github.com/dragondive/dragondive.github.io/commit/99829e36b268903055443e0c6045bd551ed2ae73) to [place it directly below the blog post](https://github.com/dragondive/dragondive.github.io/commit/a7d864c2c69e9a4073476c33b9dd23923cf2e888). For now, I enable commenting in the [front matter](https://jekyllrb.com/docs/front-matter/) of [every post](https://github.com/dragondive/dragondive.github.io/commit/e012f5379f85d17b7360b88f558acfffce8eb404), but there's likely an option to configure this globally.

With this, I have a reasonably good blog setup in place, which I will improve over time.