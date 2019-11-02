---
title: "TypeScript Exciting New Features"
date: 2019-10-22T22:51:13-03:00
draft: false
---

### At least those that I am waiting for

There are two new features that will come to TypeScript in version 3.7, well there are more features but, in my opinion, these two are the coolest. I am talking about `Optional Chaining` and `Nullish Coalescing`.

#### `Optional Chainig (?.)`

Is one of the most demanded features to be icluded into ECMAScript and is in [stage 3](https://github.com/tc39/proposal-optional-chaining) and for a good reason. Let's see why, suppose we have an object from wich we need to extract just the fullName property, most of the time we usually do something like this:

{{< highlight ts >}}
// ...
if (persons && persons.person && and persons.person.names && persons.person.names.fullName) {
name = persons.person.names.fullName;
}
// ...
{{< / highlight >}}

It would be nice if we just could do this:

{{< highlight ts >}}
// ...
name = persons?.person?.names?.fullName || '';
// ...
{{< / highlight >}}

And that is actually what `optional chaining` does, it allows us to write code where if there is a `null` or `undefined` value in the chain stops execution and return without throw exceptions. Just returning the `undefined` or `null` value. Be aware that `?.` acts differently than those `&&` operations since `&&` will act especially on falsy values like empty `strings`,`0` or even `false`.

#### `Nullish Coalescing (??)`

Is the best companion for `optional chaining`, it is also in [stage 3](https://github.com/tc39/proposal-nullish-coalescing). Let's take the previous persons example again:

{{< highlight ts >}}
// ...
name = persons?.person?.names?.title || 'Mr.';
age = persons?.person?.age || 1;
active = persons?.person?.isActive || true;
// ...
{{< / highlight >}}

What happens if `title` is equal to `''` or if `age` is equal to `0` or if `isActive` is equal to `false`. In all those cases the fallback value will be used instead. And might not be what we want right? It is in this scenario where `nullish coalescing` come to the rescue, performing an equality check against nullary values (`null` or `undefined`).

{{< highlight ts >}}
// ...
name = persons?.person?.names?.title ?? 'Mr.';
age = persons?.person?.age ?? 1;
active = persons?.person?.isActive ?? true;
// ...
{{< / highlight >}}

So, now we can safely be sure that if `title` is equal to `''` won't be replaced with `'Mr.'`. And if `age` is equal to `0` won't be replaced with `1`, and so on. And that is great right?

And this is just the tip of the iceberg, you can read more about this in the TypeScript Beta [announcement](https://devblogs.microsoft.com/typescript/announcing-typescript-3-7-beta/).
