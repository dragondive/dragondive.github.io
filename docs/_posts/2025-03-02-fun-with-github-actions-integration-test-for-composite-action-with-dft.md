---
layout: post
title: Fun with Github Actions—integration test for composite action with DFT!
comments: true
share: true
tags:
  - algorithm
  - ci cd
  - code
  - continuous improvement
  - design for test
  - expert opinion
  - fun project
  - github actions
  - hack
  - how to
  - humour
  - meme
  - open source
  - plantuml
  - power user
  - programming
  - self learning
  - setup
  - software development
  - testing
---

> "ನೀವು ಟೆಸ್ಟ್ ಮಾಡಲಿಲ್ಲ ಅಂದ್ರೆ ನೀವು ಏನು ಮಾಡಲಿಲ್ಲ."
> "अगर आपने टेस्ट नहीं किया तो आपने कुछ नहीं किया."
> "If you didn't test, you didn't do anything."
>
> \- Aravind Pai

## Back in action

After a few months of inactivity due to personal situation and other reasons, last weekend, I got back to one of my favourite side projects: [Composite action `run-plantuml-local`](https://github.com/marketplace/actions/local-plantuml-run)

<figure style="width: 80%; display:block; margin-left: auto; margin-right: auto;">
  <img
    src="/assets/images/it-aint-much-but-its-honest-work.jpg"
    alt="Contributing to your own side project on Github" />
  <figcaption style="text-align: center;">
    Contributing to your side fun project on Github<br>
    It ain't much, but it's honest work.</figcaption>
</figure>

At first, [I thought I'd only](https://tvtropes.org/pmwiki/pmwiki.php/Main/ASimplePlan) update the PlantUML versions used in the tests to match the newest releases made while I was out of action. Naturally, things never go as planned and I ended up dealing with [not one](https://github.com/dragondive/run-plantuml-local/issues/3), but [two](https://github.com/dragondive/run-plantuml-local/issues/3) *deprecation* related updates. Being back in the groove and in a good mood due to getting back to GitHub after spending several months with mediocre alternatives, I took on my previously unsolved problem: [Enable caching the plantuml.jar even for `version` = `latest`](https://github.com/dragondive/run-plantuml-local/issues/2).

But first, it is time for a [flashback](https://tvtropes.org/pmwiki/pmwiki.php/Main/HowWeGotHere).

## Flashback

I learned PlantUML at a previous workplace. Over time, I became its [power user](https://github.com/dragondive/plantuml_demo/blob/main/README.md#plantuml-demo--and-other-useful-stuff), a frequent [community contributor](https://forum.plantuml.net/user/dragondive), a [source code contributor](https://github.com/plantuml/plantuml/commits/master/?author=dragondive), and a [sponsor](https://github.com/dragondive?tab=sponsoring). I hit upon a limitation while using PlantUML in my other side project.

## The problem to solve

The [available PlantUML Github Actions](https://github.com/marketplace?query=plantuml) relied upon either the online server or the docker container. Neither of these work properly when the diagram descriptions include local files. Hence, I created my own Composite Action that would download the `plantuml.jar` and then use that to generate the diagrams.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">There are 2 hard problems in computer science: cache invalidation, naming things, and off-by-1 errors.</p>&mdash; Leon Bambrick (@secretGeek) <a href="https://twitter.com/secretGeek/status/7269997868?ref_src=twsrc%5Etfw">January 1, 2010</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

The next logical feature is caching the downloaded file. Naturally, the version number of the `plantuml.jar` file would be used in the cache key. However, a complication occurs when the version is specified as `latest`. Users generally prefer using a software's latest version with version pinning being used only in exceptional cases. When PlantUML releases a new version, any cached `latest` versions would have to be invalidated.

While I had used many composite actions and examined their source codes, I had never created one myself. Hence, to get my first composite action working, I decided to let my future self deal with it. That future self was me last weekend.

## Solution

With a fresh view of the problem after many months, I found a promising solution:

> When the version is specified as `latest`, use GitHub API to determine the actual version number, and use that in the cache key.

Implementing this solution was easy enough, but the real challenge lies in the testing.

## Some gyan on Testing

I have been fortunate to start my career as a Hardware Design Verification Engineer, and received my early lessons from some of the *world*'s best engineers in this field. While I am still nowhere close to their level, I have gained considerable mastery over it. Testing and Documentation are two technical topics closest to my engineering heart. ❤️ The opening quote of this blog post is inspired by my professional work experience.

> As an engineer, if you cannot reliably test your product, you might as well throw it away.

This applies not only to software, but any non-trivial engineering product. I would even say people who do not realize this within at most 5 years of professional engineering experience should find something else to do and spare the rest of us.

## The testing challenge specific to this change

Now coming to this specific feature of caching the latest version, there are a few challenges:

* GitHub Actions is not a *testing framework* to test other GitHub Actions. Hence, testing would require inventing several hacks.
* Calling the composite action multiple times with or without cache enabled hit a dead end. The [`actions/cache`](https://github.com/actions/cache) updates the cache key in a post-step after the job completes. By then, my "testcase" has already executed.
* My composite action downloads the JAR file using `curl`, which has its own caching mechanism. Hence, any timestamp based testing to check if a file was re-downloaded or taken from the cache would be too unreliable.

## The solution: DFT!

One of the great benefits I enjoy from being open to work across multiple domains is the natural ability to apply learning from one domain to another. People who consciously choose to work in only one domain their whole career fascinate me to no end. 

Anyway, back on topic. I realized that since GitHub Actions is not designed to do this stuff and I am already deep into hack territory, it would be too complex to use a proper solution such as mocking! I am sure my future self with more mastery over GitHub Actions would figure that out too ... and what an amazing day that would be!

Sorry, back on topic again. I recognized that Design for Test (DFT) that is routinely used in Hardware Design Verification would elegantly solve this problem too. Then it was only a matter of putting it into practice.

I modified my composite action to provide some outputs that would provide information about some of its internal computations, specifically the actual version number and whether a cache hit occured. Then I modified my test to split the workflow into multiple jobs. The first job would call the composite action with caching, and subsequent jobs would test if the cached JAR file was used or not as per the specified inputs.

> A test that can never fail is no test at all.

The final piece of the puzzle was to verify that the test fails when the composite action skips the cache when version is specified as `latest`. I achieved this easily by running the test action but without my implementation change, and the test failed as expected.

My solution along with its test is committed here: [feat: enable caching when version is latest](https://github.com/dragondive/run-plantuml-local/commit/c30bdf69ec1fb56da691bf31aafe1188d5b91af3)

## Miscellaneous closing thoughts

* While GitHub Actions is not universally liked, it does bring me a lot of joy working with it. It also enables me to sharpen my technical knife from time to time.
* [GitHub Actions documentation](https://docs.github.com/en/actions) is highly impressive. That usually happens when a community of highly skilled technical people use the right tools to write technical documentation.
* The most satisfying phase of solving a software problem is when it finally works properly and you clean out all the "commented out" codes and squash all intermediate test commits.
* I ran the test on the latest version of the three supported Operating Systems (Linux, Mac and <s>Losedoze</s> Windows), and the result was as shown below. Can you guess which is which? :smirk:

  <figure style="width: 100%; display:block; margin-left: auto; margin-right: auto;">
    <img
      src="/assets/images/test-caching-on-multiple-os.png"
      alt="Test caching on multiple OS" />
    <figcaption style="text-align: center;">
      Results of the caching test on multiple OS
    </figcaption>
  </figure>
