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

# A new dependency manager

![Me]()
![@robrix]()
![@mdiep]()
![@keithduncan]()
![@alanjrogers]()

^ So we wanted a tool that could solve this problem for us. Our team often
disagrees with CocoaPods’ philosophy, so that wasn’t an option.

^ During one of GitHub’s “Open Source Fridays,” these folks and I started discussing what a new
dependency manager for Cocoa might look like.

---

# What is it?

---

# How do you use it?

---

# Why did we build it?

---

# Why not just use CocoaPods?

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
