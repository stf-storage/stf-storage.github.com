---
title: FAQ
layout: default
---

# FAQ

## Where did the name STF come from?

STF stands for "<a href="http://en.wikipedia.org/wiki/Professional_wrestling_holds#STF">Stepover Toehold Facelock</a>". The original authors liked pro-wrestling, and hence the name. It may also mean "STorage Farm"

## Who runs STF ?

Livedoor heavily uses STF to store image files for its blog, photo storage, and other services. Throughout Livedoor, STF handles about 400 million objects (1.3 billion entities) totaling around 70TB of disk space.

## How is it different from MogileFS?

The basic idea is the same. The main difference is that STF by default speaks HTTP and is a PSGI webapp.

STF is also a lot smaller than MogileFS, so you it's a bit easier to read. As of this writing, MogileFS has about 28000+ LOC and STF has about 6000+ LOC. STF runs on plain boring synchronous codebase, so the code flow is a lot simpler.
