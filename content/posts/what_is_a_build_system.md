---
title: "What is a build system"
date: 2022-07-29T16:03:38-05:00
draft: true
---

Related: dependency management
 
Input:
* Code, configuration, documentation, binaries, ... (literal any file)
* File specifying 3rd party dependencies
* Environment variables
Side-effect:
* Run unit test, lint
Output:
* Binary files and anything that are needed to run the software
* Documentation
 
Goal:
* Avoid one-off way to build different projects
 
* Define a set of rule to run to produce artifacts
* Features
 * How is the rule defined?
 * How to detect change?
 * Generate different artifacts: production, debug, local, (architecture, etc.) symbols, configuration, etc.?
 * How to handle external dependencies: different packages, different package versions, conflict?
 * Idempotent?
 * Speed to build?
 * Quality of the artifacts like size, speed
* Tenets
 * Repeatable, reproducible, standard
 
* Common:
 * Bazel
 * C, C++: make
 * Java: Apache maven
