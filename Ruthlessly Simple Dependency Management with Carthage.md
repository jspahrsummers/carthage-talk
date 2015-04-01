# [fit] Ruthlessly Simple
# [fit] Dependency Management
## with
# [fit] Carthage

![fit](Resources/carthage-logo-gray.png)

^ Carthage is a dependency manager, intended to be simplest way to add frameworks to your Cocoa project. Carthage is also the first dependency manager to officially support Swift, and is written completely in Swift itself.

^ (logo by Reda Lemeden)

---

# What
# Why
# How to use it
# How it works

^ In this talk, I’ll explain exactly what Carthage is, _why_ it exists, how to use it, and then go into some of the inner workings as well.

---

# [fit] Justin Spahr-Summers
# @jspahrsummers

![inline fit](Resources/me.jpg) ![inline fit](Resources/octocat.png) ![inline fit](Resources/mantle-logo.png) ![inline fit](Resources/reactivecocoa-logo.png)

^ I’m a desktop developer at GitHub, primarily working on GitHub for Mac. Besides Carthage, I’ve also played a major role in Mantle and ReactiveCocoa, and several other open source libraries.

---

# [fit] The Problem

^ One of the first things we learn as programmers is that we should reuse code. Why waste time and energy writing the same thing over and over?

^ Reusable code is put into _libraries_ or _frameworks_, which encapsulate components that may be useful in multiple other projects.

---

# Dependencies in Cocoa **(historically)**

1. Copied source files
1. Zipped binaries
1. SVN externals, Git submodules
1. Git subtrees

^ Before CocoaPods and Carthage, there were these ways to distribute Cocoa libraries, all of them terrible.

^ Source files and binaries aren’t versioned, making it hard to avoid conflicts and stay up-to-date. On the other hand, the Subversion, Git, Mercurial, etc. options are often a pain to use in their own right.

---

# Dependencies on other platforms

1. Dependency managers

^ Most modern platforms have dependency managers, usually created by the same team that designs the language.

^ A dependency manager helps you pick the right version for each library you want, and then sets up each library so you can use it.

---

# [fit] _Our_ Problem

^ That’s why dependency management is important in general. But why Carthage? What specific problem were we trying to solve?

---

> GitHub for Mac has what could be called “excessively nested submodules.”
--Me, late 2014

![](Resources/nested-submodules.png)

^ For the longest time, GitHub for Mac imported dependencies exclusively through Git submodules and Xcode subprojects. This works well until two or more of your dependencies have a shared dependency.

---

![original fit](Resources/dependency-graph.pdf)

^ We used RAC and Mantle within several of our other libraries. Because the app must pick _one_ version of RAC to link, problems can arise if projects expect different versions of RAC.

^ Most dangerously, there’s often no indication of conflicts like these, unless you’re lucky and get a build failure.

---

# [fit] Why not use
# [fit] CocoaPods?

^ Although submodules are problematic in some ways, we also weren’t interested in using CocoaPods.

---

# Podspecs

^ As library authors, we were frustrated with CocoaPods’ requirement that we add podspecs for all of our projects. This information already exists in Xcode and Git, so why should we duplicate it?

---

# Less control

^ As users, we were frustrated with CocoaPods’ control of the project setup process. We know how to set up Xcode projects, and the automated setup takes away our flexibility.

^ CocoaPods does have a `--no-integrate` option, which stops it from manipulating your Xcode project, but you still have to use its generated Pods project. You don’t get the choice to integrate dependencies via their own Xcode projects, for example.

---

# Centralized

^ We think CocoaPods’ centralized package list makes library authors’ jobs harder, because they’re now responsible for deploying to yet another place.

^ It also makes it harder to use library versions that haven’t been submitted, and to use libraries without explicit CocoaPods support.

---

# :cry:

^ Of course, whether these are positives or negatives depend on what you want. If you’re happy with CocoaPods, continue to use it!

^ For us, it became clear that the existing solution wouldn’t work, and that we weren’t in a position to change it. We wanted a tool with a completely different philosophy.

---

## @robrix
## @mdiep
## @keithduncan
## @alanjrogers

^ During one of GitHub’s “Open Source Fridays,” these folks and I started discussing what a new dependency manager for Cocoa might look like.

---

# Our goals
# [fit] ![inline fit](Resources/git-logo.png) ⇄ ![inline fit](Resources/carthage-logo-colored.png) ⇆ ![inline fit](Resources/xcode.png)

1. Pick compatible versions for all dependencies
1. Check out dependencies with Git
1. Build frameworks with Xcode

^ We wanted a simple coordinator between Xcode and Git, plus a constraint solver for resolving dependency versions.

^ All the tool should do on its own is find the right version, and the rest should just be communicating with Xcode and Git.

---

# [fit] Using Carthage

^ How do you actually set up an application project with Carthage?

---

# Step 1: Create a Cartfile

```
github "Mantle/Mantle" ~> 1.5
github "ReactiveCocoa/ReactiveCocoa" >= 2.4.7
github "ReactiveCocoa/ReactiveCocoaLayout" == 0.5.2
```

^ Let’s set up a project with Mantle, ReactiveCocoa, and the less well-known ReactiveCocoaLayout. (Explain each line of the Cartfile.)

---

# Step 2: Run Carthage

```
$ carthage update
*** Fetching Mantle
*** Fetching ReactiveCocoa
*** Fetching ReactiveCocoaLayout
*** Fetching Archimedes
*** Downloading Archimedes at "1.1.4"
*** Downloading Mantle at "1.5.4"
*** Downloading ReactiveCocoa at "v2.4.7"
*** Downloading ReactiveCocoaLayout at "0.5.2"
```

^ We’ll run `carthage update` to download and install all the dependencies.

^ Notice that it pulled in Archimedes, which is a dependency of ReactiveCocoaLayout, and that it picked the newest compatible version of Mantle available.

---

# Step 3: Link Frameworks

![inline](Resources/built-frameworks.png) ![inline](Resources/link-frameworks.png)

^ Now that we have built frameworks on disk, we simply need to drag them into the “Linked Frameworks and Libraries” section of the app target.

^ On OS X, these can go directly in the “Embedded Binaries” section.

---

# Step 4: Strip architectures (iOS only)

![inline](Resources/run-script-phase.png)

^ Unfortunately, there’s an App Store submission bug that disallows universal framework binaries, but Carthage offers a command to automatically remove the simulator bits. We can run that command from a Run Script build phase.

^ I’m hoping that Apple eventually fixes my radar and makes this step unnecessary.

---

# That’s it!

^ We now have an app using all of those frameworks. Others using that project can get the same frameworks by running `carthage bootstrap`, and it’s easy to update to newer versions in the future by running `carthage update` again.

---

# [fit] “Ruthlessly Simple”

^ These steps hint at the philosophy behind Carthage.

^ The prevailing design goal, overriding almost all others, is “ruthless simplicity.” We want a tool that is as simple as possible, and will try very hard to avoid features that add significant complexity.

---

## _Easy:_ familiar or approachable
## _Simple:_ fewer concepts and concerns[^1]

[^1]: See Rich Hickey’s talk, [Simple Made Easy](http://www.infoq.com/presentations/Simple-Made-Easy)

^ Simple and easy are not the same thing.

---

# CocoaPods is _easy_
# Carthage is _simple_

^ CocoaPods is all about making it as easy as possible to find and use libraries, but it achieves those goals at the cost of complexity. It becomes _easier_ but less _simple_.

---

# [fit] Simpler tools are…

^ With Carthage, we really wanted to focus more on simplicity, because we believe that the benefits are enormous. For example, simpler tools are…

---

# Easier to _understand_

^ Simplicity makes it easier to form a mental model of what’s going on. If something goes wrong, understanding what the tool does may help you resolve the issue.

---

# Easier to _maintain_
# Easier to _contribute_ to

^ By keeping things simple, we don’t need to handle as many edge cases. And by integrating with other tools, like Xcode and Git, we delegate responsibility to them, resulting in less code (and less complexity) within Carthage.

^ It’s a lot easier to implement fixes or new features within a simple codebase than a complex one.

---

# More _flexible_
# More _composable_

^ It’s impossible for us to predict all the possible ways that someone might want to use our software.

^ If there’s a use case out there that we’re not anticipating, it’s a lot easier to bend Carthage to your will than to convince a complex tool to do exactly what you want.

---

# [fit] _Enhanced_ when other tools improve

^ Wherever we hand off responsibility to Xcode and Git, we automatically benefit from improvements made to those tools, with little or no effort on our part.

^ On the other hand, the more we try to duplicate their functionality, the more fragile our system becomes, and the more we lose when those tools change.

---

# [fit] Behind the Scenes

^ Now, onto the mechanics of how it works!

---

## _Parse_ the Cartfile
## _Resolve_ the dependency graph
## _Download_ all dependencies
## _Build_ each framework

^ These are the high-level steps that Carthage performs when you run `carthage update`.

^ Let’s dive into each step.

---

# :mag: Parsing

**Parse [OGDL](http://www.ogdl.org) into a list of dependencies**

```
github "ReactiveCocoa/ReactiveCocoa" >= 2.4.7
```

^ Carthage files are written in a subset of the Ordered Graph Data Language, a dead simple language that’s useful for configuration files like this.

---

# :mag: Parsing

**Parse [OGDL](http://www.ogdl.org) into a list of dependencies**

```
github "ReactiveCocoa/ReactiveCocoa" >= 2.4.7
```

**Determine the type of each dependency**

```
github "ReactiveCocoa/ReactiveCocoa"
```

^ Currently, dependencies can be GitHub repositories or arbitrary Git repositories. We may add other types in the future, but this covers most of the use cases already.

---

# :mag: Parsing

**Parse [OGDL](http://www.ogdl.org) into a list of dependencies**

```
github "ReactiveCocoa/ReactiveCocoa" >= 2.4.7
```

**Determine the type of each dependency**

```
github "ReactiveCocoa/ReactiveCocoa"
```

**Parse any [Semantic Version](http://semver.org)**

```
>= 2.4.7
```

^ Determine what the version number is, if any, and what it means: “equal to”, “compatible with”, or “at least.” If there’s no version number, it means “pick latest.” You can also specify a branch, tag, or commit by name.

---

# :recycle: Resolving

_CarthageDemo/Cartfile:_

```
github "Mantle/Mantle" ~> 1.5
github "ReactiveCocoa/ReactiveCocoa" >= 2.4.7
github "ReactiveCocoa/ReactiveCocoaLayout" == 0.5.2
```

^ Now we know the dependencies we want, so it’s time to create a directed acyclic graph representing the versions of and relationships between the dependencies.

---

# :recycle: Resolving

**Create a graph** of the latest dependency versions

![inline](Resources/resolving-1.pdf)

^ We start by trying the latest allowed version for every dependency. 99% of the time, these are the versions will end up in the final graph.

---

# :recycle: Resolving

**Insert dependency Cartfiles** into the graph

![inline](Resources/resolving-2.pdf)

^ Now we need to go to each dependency’s repository _at the version we picked_, and look for a Cartfile there.

^ If we find one, we need to include those dependencies in the graph, and pick some candidate versions for those nested dependencies too.

---

# :recycle: Resolving

**If requirements conflict**, throw out the graph

![inline](Resources/resolving-3.pdf)

^ Okay, now we have a graph with some possible versions locked in.

^ Unfortunately, we picked ReactiveCocoa 3.0, while ReactiveCocoaLayout only allows 2.4.7! When this happens, the graph is _inconsistent_ and must be thrown out, because we can’t satisfy both at the same time.

---

# :recycle: Resolving

**Try a new graph** with the next possible version

![inline](Resources/resolving-4.pdf)

^ If this happens, and the graph gets thrown out, we decrement one of the version numbers and start over.

---

# :recycle: Resolving

**Repeat** until a valid graph is found

![inline](Resources/resolving-5.pdf)

^ We keep doing that until we find a set of versions that are all compatible with each other.

^ This is inefficient in the worst case, but performs surprisingly well in practice. Part of it is because we throw out graphs the moment they become invalid, and part of it is because we automatically terminate when we find the first valid solution.

---

# :floppy_disk: Downloading

1. **Fetch the repository** into Carthage’s cache

^ Carthage maintains a global cache of dependency repositories, so you don’t have to download the same thing ten times across all of your projects.

^ So, the first step of downloading a dependency is making sure that cache is up-to-date.

---

# :floppy_disk: Downloading

1. **Fetch the repository** into Carthage’s cache
1. **Copy the right version** into Carthage/Checkouts

^ Then, we use `git checkout` to copy the repository files (without the Git metadata) into the project’s Carthage/Checkouts folder.

---

# :hammer: Building

1. **Symlink Carthage/Build** into the dependency folder

^ Once every dependency has been downloaded, we link all the Carthage/Build folders together. This ensures that any frameworks we build can be used by the _later_ projects we build.

---

# :hammer: Building

1. **Symlink Carthage/Build** into the dependency folder
1. **List framework schemes** from the `.xcodeproj`

^ In the root-most Xcode project, we use `xcodebuild -list` to find all schemes that build a dynamic framework. Static libraries and other products are ignored.

---

# :hammer: Building

1. **Symlink Carthage/Build** into the dependency folder
1. **List framework schemes** from the `.xcodeproj`
1. **Build each scheme** for all supported architectures

^ Now we can use `xcodebuild` to actually build. By looking at the target platform for the scheme, we determine which architectures to build for.

---

# :hammer: Building

1. **Symlink Carthage/Build** into the dependency folder
1. **List framework schemes** from the `.xcodeproj`
1. **Build each scheme** for all supported architectures
1. **Combine multiple architectures** with `lipo`

^ Once all of the architectures have been built, we combine the products into one binary using the `lipo` tool.

---

# :hammer: Building

1. **Symlink Carthage/Build** into the dependency folder
1. **List framework schemes** from the `.xcodeproj`
1. **Build each scheme** for all supported architectures
1. **Combine multiple architectures** with `lipo`
1. **Copy the built frameworks** into Carthage/Build

^ The final universal framework then gets copied into the Carthage/Build folder, for the parent project to find.

---

# :sparkles: BONUS: Prebuilt binaries! :sparkles:

![inline](Resources/release-with-binary.png)

```
*** Downloading ReactiveCocoa at "v2.4.7"
```

^ You may have noticed earlier that Carthage didn’t actually _build_ the dependencies listed in the Cartfile. That’s because each of these projects have GitHub Releases with binaries attached.

^ When possible, Carthage will download binaries instead of building from scratch, saving you time. On GitHub for Mac, this cut build times by almost 70%, from 9.5 minutes to about 3!

---

# [fit] CarthageKit

^ Most of Carthage’s core behavior is actually implemented as a framework of its own, CarthageKit. This increases modularity and makes testing easier, but it’s also intended to make Carthage integration easier.

^ If you want to build a tool on top of Carthage, or compatible with Carthage, CarthageKit is your friend. Using CarthageKit is a great way to add features without increasing the complexity of Carthage itself.

---

# [fit] Technical Choices

^ I’d also like to explain just a few of the technical choices we made while building Carthage.

---

# Dynamic frameworks vs. static libraries

- iOS 8+ only
- Can include resources
- Self-contained and ready-to-use
- Avoid duplicate symbol errors
- **Required for Swift**

^ Carthage uses frameworks and not static libraries, because frameworks come with significant advantages—like being completely self-contained.

^ Built frameworks can be distributed as-is, without any auxiliary files necessary, which is huge for the Carthage use case.

---

# Swift vs. Objective-C

* Type safety
* Value types _(especially enums)_
* Much faster to write
* Better modularization
* The Next Big Thing™

^ Carthage and CarthageKit are written 100% in Swift. Although Swift definitely involves its share of obstacles, we saw huge benefits from using it instead of Objective-C. These are just some of them.

---

# ReactiveCocoa

* Simplifies networking (with the GitHub API)
* Simplifies the dependency resolver
* Simplifies shelling out, via [ReactiveTask](https://github.com/Carthage/ReactiveTask)
* Carthage helps test RAC 3.0 in the real world

^ ReactiveCocoa is a framework for programming with “streams of values over time,” which are known as signals. We use ReactiveCocoa extensively in Carthage, which turns out to be an especially great application for it, because of all the inherently stream-based stuff we need to do (like shell tasks and networking).

^ We also wanted a way to test RAC’s Swift API (part of 3.0) in the real world, to make sure it works well and is pleasant to use. Carthage is probably the biggest user of that API, and, as a result, RAC 3.0 is now getting close to a release.

---

# [fit] 1.0

^ I’d like to finish by talking about why Carthage isn’t at 1.0 yet, and what it will take to get there.

--- 

# Per-project settings

^ Carthage supports flags for changing the default workflow; for example, to only build for one platform, to add dependencies as submodules instead of copying their contents, or to skip the build step entirely.

^ Right now, all of those flags need to be specified on the command line. By 1.0, we want to allow these options to live in Cartfile.private, so all users of the project follow the same configuration automatically.

---

# Review CarthageKit API
# Review command line flags

^ The other major part of 1.0 will be carefully reviewing anything that we can’t really break after release, and making any breaking changes necessary ahead of time.

^ CarthageKit and the CLI are the main public-facing components where we’ll have to maintain backwards compatibility.

---

# Profit!!! 💸

^ 1.0 is really important to me, and I know it will make Carthage more “real” to many folks. We have an 0.7 release to do, and then 1.0 is up right after that, so it shouldn’t be too long now. Keep an eye out!

---

# What
# Why
# How to use it
# How it works

^ So, to recap, we covered what Carthage is and how to use it, why we built it and what problem it’s solving, and how it works behind the scenes.

^ Ultimately, we’ve just scratched the surface, but hopefully this has helped create a clear picture of the project and its goals, as well as the ways in which it’s similar to and different from CocoaPods.

---

# Questions? Comments?

Slides and notes, plus my demo project, are available at:
[https://github.com/jspahrsummers/carthage-talk](https://github.com/jspahrsummers/carthage-talk)

Thanks to everyone who reviewed this presentation, and to Realm for inviting me to speak![^2]

[^2]: :heart: Matt Diephouse, Rob Rix, Alan Rogers, Keith Duncan, Nacho Soto, Tom Brow, James Lawton, Arwa Jumkawala, JP Simard, Tim Anglade, Brendan Forster, Benjamin Encz, Robert Böhnke, and you!
