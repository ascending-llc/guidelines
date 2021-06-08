# ASCENDING React/JSX Style Guide

This style guide is mostly based on [Airbnb](https://github.com/airbnb/javascript/blob/master/react/README.md) react coding style. We kept key points as part of our standard.

## Table of Contents

  1. [Basic Rules](#basic-rules)
  1. [Project Scaffold](#project-scaffold)
  1. [Dependency Management](#dependency-management)
  1. [Naming](#naming)
  1. [Variables](#variables)
  1. [Environments](#environments)
  1. [Branching Strategy](#branching-strategy)
  1. [Analytics](#analytics)
  1. [Project ReadMe](#project-readme)

## Basic Rules

- Copy&Paste with your mind, never just copy paste, make sure change the name and content according to each project. Remove unecessary code.
- Always use JSX syntax.
- Fix all the console error at the end of PR

## Project Scaffold
- We made a scaffold project to bootstrap a standard reactjs project according to ASCENDING standard



## Dependency Management
- We should use package.json to manage all runtime/dev dependency and build target
- Run time dependency which is essential to run the front-end application could be defined in `dependencies` node
- Dev dependency which is only required to build the front-end application could be defined in `devDependencies` node

 ```json
    //good
    "dependencies": {
        "react": "YOUR VERSION",
        ...
    },
    "devDependencies": {
        "env-cmd": "YOUR VERSION",
        ...
    }

    //working but bad
    "dependencies": {
        "react": "YOUR VERSION",
        "env-cmd": "YOUR VERSION"
    }
 ```

## Naming