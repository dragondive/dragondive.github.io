---
layout: post
title:  "Merge git repositories—without forgetting the history"
comments: true
share: true
tags:
  - git
  - reference
  - self learning
  - setup
  - software development
  - refactoring
  - power user
---

## Motivation 

I recently activated my [heavens-arena](https://github.com/dragondive/heavens-arena) repository to be my public notebook of source codes.

I had previously created two separate repositories for my self learning of [Conan package manager](https://github.com/dragondive/conan_learning) and [PlantUML](https://github.com/dragondive/plantuml_demo). *[By the way, I have given tech talks on these two topics internally in my corporate world at the Smart Coffee Break—of which, I'm also an organizer.]* I decided to consolidate those repositories into this new single notebook.

## Merging git repositories

Simply copy-pasting the files from one repository to another is no fun. If I did that, I would have no reason to write this blog. I wanted to properly *merge* those two repositories into the new one, including their respective commit histories. I'm not sure if this is more amusing or amazing: I could do this seemingly complex task easily without referring any documentation or StackExchange/reddit forums, or any technical blogs. 

One of the most useful things for a git user to know is what a git commit actually is, which I most certainly do. *[I will separately write a post later that will explain a git commit in a simple manner, that would make it very easy to follow various git commands.]* For now, here are the steps to merge a git repository into another, using my above-mentioned conan repository as example.

* **Checkout destination branch in destination repository**: Checkout the branch in the destination repository where the commits from the other (source) repository should be merged.

  In my current case, the destination repository is `heavens-arena`, where I created a new branch `conan`.

  ```bash
  C:\WORK\dragondive\heavens-arena (zero -> origin)
  λ git checkout -b conan
  ```

* **Add the other repository as a remote**: git generally has pointer to a single remote repository (usually named `origin`), but one can configure additional remote repositories.

  In my case, the other repository is [conan_learning](https://github.com/dragondive/conan_learning.git), which I added as a second remote:

  ```bash
  C:\WORK\dragondive\heavens-arena (conan)
  λ git remote add conan https://github.com/dragondive/conan_learning.git
  ```

  - *(Optional) Verify remotes information*:

    ```bash
    C:\WORK\dragondive\heavens-arena (conan)
    λ git remote --verbose
    conan   https://github.com/dragondive/conan_learning.git (fetch)
    conan   https://github.com/dragondive/conan_learning.git (push)
    origin  https://github.com/dragondive/heavens-arena.git (fetch)
    origin  https://github.com/dragondive/heavens-arena.git (push)
    ```

* **Fetch from other remote**: This will bring the commits from the other remote into the "local scope".

  ```bash
  C:\WORK\dragondive\heavens-arena (conan)
  λ git fetch -p conan
  remote: Enumerating objects: 227, done.
  remote: Counting objects: 100% (227/227), done.
  remote: Compressing objects: 100% (154/154), done.
  remote: Total 227 (delta 68), reused 223 (delta 64), pack-reused 0
  Receiving objects: 100% (227/227), 899.79 KiB | 1.60 MiB/s, done.
  Resolving deltas: 100% (68/68), done.
  From https://github.com/dragondive/conan_learning
   * [new branch]      master     -> conan/master
  ```

* **List the commits from the other remote**: This enables `cherry-pick`ing the commits to the current branch in the next step.

  ```bash
  C:\WORK\dragondive\heavens-arena (conan)                                               
  λ git log --oneline conan/master                                                       
  e152634 (conan/master) added doxyfile for plantuml demo                                
  82ccd56 minor fix to CMakeLists.txt                                                    
  514fb75 enhanced conan_gtest example to include boost library                          
  83f84f6 Added README.md with links to blog entries                                     
  b343514 Added blog "Profiles, build_requires and cross-building"                       
  01eb4cb Moved example source codes into sources directory                              
  92b7c4d Added blog "Working with non-Conan packages"                                   
  05d9170 Added example for cross-building from Linux to Windows                         
  ae233a3 Added Conan profile files                                                      
  d99d61b Added example for creating Conan package from non-Conan packages               
  6e2d432 Made a few small improvements                                                  
  869f34a Added blog "Publishing conan package to artifactory"                           
  4659c3f Added blog "Creating and testing a conan package"                              
  9b5a364 Added conanfile.py to create the package                                       
  5e57c52 Added blog "Setting up Google Test with Conan package manager"                 
  ea0aa94 Added blog "Getting Started with Conan"                                        
  38f5b3c Added conan_gtest simple example                                               
  ```

* **`cherry-pick` the required commits**: The required commits can now be `cherry-pick`ed to the current branch.

  There are several ways to do this:

  - *Specify a range of commits*: In my case, this is the most suitable approach as I want to cherry-pick all the commits. From the above `git log` output, I specified the first and last commit as below:

    ```bash
    C:\WORK\dragondive\heavens-arena (conan)
    λ git cherry-pick 38f5b3c..e152634
    ```

  - *Interactive rebase "hack"*: For more complex choice of commits, one could "hack" an interactive rebase and then in the text file that opens up, specify the list and order of commits to be `cherry-pick`ed. *[I may write about this approach separately in another post.]*

  Any merge conflicts that occur during the `cherry-pick`ing should be resolved, in the usual manner as with any rebase operations.

* **Push the new branch to the destination remote**: After the successful `cherry-pick` operation, the commits from the other remote are now available on the new branch in the destination repository, and can be pushed to remote as usual.

  ```bash
  C:\WORK\dragondive\heavens-arena (conan)
  λ git push origin conan
  ```

Similarly, I merged the PlantUML repository onto another branch in heavens-arena. The two branches are now available as below:

* **Conan**: [https://github.com/dragondive/heavens-arena/tree/conan](https://github.com/dragondive/heavens-arena/tree/conan)
* **PlantUML**: [https://github.com/dragondive/heavens-arena/tree/plantuml](https://github.com/dragondive/heavens-arena/tree/plantuml)

Note that the history is not lost on the destination branch as the commit includes information about the original author and time:

```bash
C:\WORK\dragondive\heavens-arena (plantuml -> origin)
λ git log -1 --pretty="fuller"
commit 8ebaeaaac6ceb9fcaacd0eb3a70a3d6cb80284d6 (HEAD -> plantuml, origin/plantuml)
Author:     Aravind Pai <dragondive@outlook.in>
AuthorDate: Thu Oct 21 15:06:57 2021 +0530
Commit:     Aravind Pai <dragondive@outlook.in>
CommitDate: Sat Mar 18 19:16:50 2023 +0530

  bugfix: added video for integration markdown
```