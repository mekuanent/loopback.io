---
lang: en
title: 'Mixin'
keywords: LoopBack 4.0, LoopBack 4
tags:
sidebar: lb4_sidebar
permalink: /doc/en/lb4/Mixin.html
summary:
---

It is a commonly used JavaScript/TypeScript strategy to extend a class with new properties and methods.

*question: does our mixinBuilder allow interface extends classes?*

## Mixin Builder

LoopBack provides a mixin builder function `MixinBuilder` to easily extend a class 
with one or multiple mixins,
you can call it to define the new extended class with fluent syntaxed API like:

```ts
import {MixiBuilder} from '@loopback/repository';
import {BaseClass} from '<path_exports_BaseClass>';
import {FooMixin, BarMixin} from '<path_exports_Mixins>';

let newClass = MixinBuilder.mix(baseClass).with(FooMixin, BarMixin);
```

## Define Mixin

By defining a mixin, you create a mixin function that takes in a base class, 
and returns a new class extending the base class with new properties and methods mixed to it.

For example you have a simple controller which only has a greeter function prints out 'hi!':

{% include code-caption.html content="Controllers/myController.ts" %}

```ts
class SimpleController {
    constructor() {

    }
    greet() {
        console.log('hi!');
    }
}
```

Now let's add mixins to it:

- A time stamp mixin that adds a property `createdAt` to record when a controller instance get created.

- A logger mixin that adds a method `logger` to log the `string` you provide.

Define mixin `timeStampMixin`:

{% include code-caption.html content="Mixins/timeStampMixin.ts" %}

```ts
import {Class} from "@loopback/repository";

export function timeStampMixin<T extends Class<any>> (baseClass: T) {
  return class newClass extends baseClass {
      // add a new property `createdAt`
      public createdAt: Date;
      constructor(...args: any[]) {
        super(args);
        this.createTS = new Date();
      }
      printTimeStamp() {
        console.log('Instance created at: ' + this.createdAt);  
      }
  }
}
```

And define mixin `loggerMixin`:

{% include code-caption.html content="Mixins/loggerMixin.ts" %}

```ts
import {Class} from "@loopback/repository";

function loggerMixin<T extends Class<any>> (baseClass: T) {
  return class newClass extends baseClass {
    // add a new method `logger()`
    logger(str: string) {
      console.log('Prints out a string: ' + str);
    };
  }
}
```

Now you can extend `SimpleController` with the two mixins by `MixinBuilder`:

{% include code-caption.html content="Controllers/myController.ts" %}

```ts
import {timeStampMixin} from 'Mixins/timeStampMixin.ts';
import {loggerMixin} from 'Mixins/loggerMixin.ts';

class SimpleController {
    constructor() {

    }
    greet() {
        console.log('hi!');
    }
}

let AdvancedController = MixinBuilder.mix(SimpleController).with(timeStampMixin, loggerMixin);

// verify new method and property are added to `AdvancedController`:
let aControllerInst = new AdvancedController();
aControllerInst.printTimeStamp();
// print out: Instance created at: Tue Oct 17 2017 22:28:49 GMT-0400 (EDT)
aControllerInst.logger('hello world!');
// print out: Prints out a string: hello world!
```