---
layout: post
title:  "Testing of Meteor & React app"
date:   2016-11-10 11:00:29 +0100
categories: Meteor React
---

I do find the webdev in general to be quite an overwhelming experience. Next to *html*, *css*, *javascript* you are expected to be familiar with dozens of library necessary to accomplish even the simplest of the *"Hello World"* app. I think this is what frequently being referred to as *Javascript fatigue*.

Now, enter the app testing and things do not get any easier. To test the app we will need a general purpose *test suite*, convenience *assertion library*,  framework specific *test rendering library* and a library for *spies, stubs and mocks*. To keep it less abstract, here is the choice for Meteor+React application.

## Glossary

`Mocha` - Javascript test framework running both on [node.js](https://nodejs.org/en/) and in the browser.

`Sinon` - Test spies, stubs and mocks for Javascript.

Refer to: [Elliot](https://medium.com/javascript-scene/what-every-unit-test-needs-f6cd34d9836d#.une8a0q8t)

## How to run

`> meteor test --driver-package practicalmeteor:mocha --port 3100`

## Thing to test for React component

Each returned element [See Dan Abramov](https://facebook.github.io/react/blog/2015/12/18/react-components-elements-and-instances.html) has a *type* and *props* field. These are the first candidates to be tested.

```
{
  type: Button,
  props: {
    color: 'blue',
    children: 'OK!'
  }
}
```

1. Type of the node that was rendered - it is alway 1 with react, therefore <g> or <div> or whatever custom node...`component.node.type`
2. Number of child within that node ... `component.node.props.children.length`
3. Type of the child components `actual.node.props.children.type`,
4. Props that are being passed to the child components `actual.node.props.children.props`
