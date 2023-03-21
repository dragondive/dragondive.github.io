---
layout: post
title:  "My Hello world story"
comments: true
share: true
tags:
  - software development
  - culture
  - story
  - self learning
  - setup
  - c
  - python
  - ux
  - cognitive load
  - continuous improvement
---

> **hello, world**

The famous _first words_ in most people's computer programming journey!

The tradition started with Professor Brian Kernighan's documentation of the B and C programming languages during his time at Bell Labs. Ozaner Hansha describes it in more detail here: [On the Origin of "Hello, World!"](https://ozanerhansha.medium.com/on-the-origin-of-hello-world-61bfe98196d5)

## My early days of ignorance

When I first read it about 15 years ago in [_**The C Programming Language**_](https://openlibrary.org/works/OL4617640W/The_C_Programming_Language?edition=key%3A/books/OL2030445M) (perhaps more famously known as the _**K&R C book**_), I felt the explanation was either exaggerated or dramatized. As a newborn kid in the IT industry, I had the comfort of working in environments already setup by my predecessors. I couldn't comprehend why it was a big hurdle to execute a simple program.

<figure style="width: 80%; display:block; margin-left: auto; margin-right: auto;">
  <img src="/assets/images/hello-world-page5.jpg" alt="Page 5 from K&R" />
  <img src="/assets/images/hello-world-page6.jpg" alt="Page 6 from K&R" />
  <figcaption  style="text-align: center;">From my owned copy of the K&R C book, complies with <a href="https://copyrightalliance.org/faqs/what-is-fair-use/">Fair Use</a>.</figcaption>
</figure>

## How experience taught me better

I have since grown out of my ignorance and naivete. Over the years, I have setup many working environments from scratch, many of them in my side-projects (of which, this blog itself is [an example]({% post_url 2023-03-10-setting-up-my-blog %})). Those experiences helped me fully appreciate the insights behind that explanation.

In various different scenarios, I have met with each of the mentioned hurdles, and then some more:

* On some systems, the seemingly trivial writing of a text file was a big hurdle. My preferred vi editor wasn't available, I didn't have permissions to install it, and at the time, I didn't know how to properly use the other editors.
* There have been many occasions of going through [dependency hell](https://www.techtarget.com/searchitoperations/definition/dependency-hell) to setup the required build tools.
* On a few occasions, the program executed successfully, but it took me a while to figure out where the program output went.
* In the corporate environment, configuring the corporate proxy and fiddling with the VPN settings is frequently a catastrophe by itself.

## Hello world, bye cognitive load

In my experience, the greatest value that reaching the *Hello world checkpoint* brings is it drastically reduces the [cognitive load](https://bootcamp.uxdesign.cc/what-is-cognitive-load-in-ux-ff068fb8c44d) afterwards. Having seen the first program run successfully, I can fully focus on solving the actual problem. My subconscious mind is not worried about how to prepare the setup for seeing the outcome of the code I'm now writing. It is a reassuring feeling that there is already a working environment on which I can try out my solution and get quick feedback.

## The many flavours of Hello world

The *Hello world checkpoint* is frequently more than just printing hello world. For example, in Python, printing hello world is a simple function call with zero [ceremony](https://www.youtube.com/watch?v=4jCjDEb9KZI). However, that says nothing about whether your setup can use libraries.

* If you intend to write small Python scripts, your Hello world may involve installing one library and calling one of its functions.
* For Python projects with more elaborate package organization, you may need a Hello world with two modules or packages, and invoking some functions in one module from the other.

The Hello world would also depend on the specific purpose of the application:

* For an application that mainly works with a database, the Hello world would include connecting to a database and executing one read and one write command.
* For an application that processes data fetched from API calls, it would include setting up the authentication and making an API call.

## Refining Hello world iteratively

The Hello world checkpoint is not cast in stone. It can—and perhaps should—get refined iteratively as one gains more experience and understanding. My current Hello world for Python projects, for example, includes installing a local package [using a `pyproject.toml` file](https://setuptools.pypa.io/en/latest/userguide/pyproject_config.html), setting up a simple unit test with [pytest](https://docs.pytest.org/), executing the [black formatter](https://pypi.org/project/black/), and running [prospector code analysis](https://prospector.landscape.io/). For my future Python projects, I may add or change some things as my own depth of knowledge increases and based on how the Python world changes.

## Summary

Hello world is not merely a customary ritual, but is immensely helpful for software developers. It is a method to first get the pieces lined up properly before starting to play the game. This makes their subsequent work much easier by closing off one major source of distractions and frustrations.

Hello world is not one-size-fits-all, it varies from case to case. The developer should seek to constantly refine their Hello world in accordance with changing circumstances and as their own understanding increases.