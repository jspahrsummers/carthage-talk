# Ruthlessly Simple Dependency Management with Carthage

![Carthage logo]()

(logo by Reda Lemeden)

^ Carthage is a decentralized dependency manager, intended to be simplest way to
add frameworks to your Cocoa project.

^ In this talk, I’ll explain exactly what Carthage is, how to use it, _why_ it exists,
and then go into some of the inner workings as well.

---

# About me

![Me]()
![GitHub]()

![Projects I’ve worked on]()

^ I’m a desktop developer at GitHub, primarily working on GitHub for Mac. You
may have used one of these open source projects that I’ve worked on.

---

# The problem

> GitHub for Mac has what could be called “excessively nested submodules.”
--Me, late 2014

![submodule list https://gist.github.com/jspahrsummers/e3caf1150db086ecdce4]()

^ For the longest time, GitHub for Mac imported dependencies exclusively through
Git submodules and Xcode subprojects. This works pretty well until your
dependencies have a shared dependency on something else.

---

# The problem

![dependency graph for RAC and Mantle]()

^ In our case, we used ReactiveCocoa and Mantle within several of our other
libraries. Because the app must ultimately pick one version of RAC to link,
problems can arise if libraries expect different versions of RAC.

^ Most dangerously, there’s often no indication that this is the case, unless
you’re (comparatively) lucky and get a build failure.

---

# Why not use CocoaPods?

^ Although submodules are problematic in some ways, we also weren’t interested
in using CocoaPods.

---

# Why not use CocoaPods?

Podspecs

^ As library authors, we were frustrated with CocoaPods’ requirement that we add
podspecs for all of our projects. After all, this information already exists in
Xcode and Git, so why should we duplicate it to another place?

---

# Why not use CocoaPods?

Less control over integration

^ As users, we were frustrated with CocoaPods’ control of the project setup
process. We know how to set up Xcode projects, and the automated nature of it
often took away our flexibility.

^ Yet, if we were to disable that feature, we would be stuck manually adding and
removing files as our dependencies get updated.

---

# Why not use CocoaPods?

Requires a central authority

^ CocoaPods Trunk is a central package management service, backed by a GitHub
repository, that is responsible for serving up podspecs.

^ We think a centralized list makes library authors’ jobs harder, because
they’re now responsible for deploying releases to yet another place. It also
makes it harder to use library versions that haven’t been recorded there, or to
use libraries that don’t have any CocoaPods support at all!

---

# Why not use CocoaPods?

Written in Ruby

^ Even if we could’ve fixed the aforementioned issues, CocoaPods is written in
Ruby. And while we could certainly learn enough Ruby to do something useful,
it’s definitely an obstacle to contributing. We aren’t Ruby developers by trade,
and our time is limited, so we wanted something easier to work on.

---

# A new dependency manager

![Me]()
![@robrix]()
![@mdiep]()
![@keithduncan]()
![@alanjrogers]()

^ So, during one of GitHub’s “Open Source Fridays,” these folks and I started discussing what a new
dependency manager for Cocoa might look like.

---

# What is it?

---

# How do you use it?

---

# “Ruthlessly simple”

---

# Prebuilt binaries

---

# How does it work behind the scenes?

---

# CarthageKit

---

# Why is it written in Swift?

---

# Why does it use ReactiveCocoa?
