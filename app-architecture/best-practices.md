---
description: A slightly contrarian view on iOS app architecture.
---

# Best Practices

Over the years I have seen many different architecture patterns in iOS apps. Unfortunately, a lot of the architectures are not the best idea for iOS and have often annoyed me quite a bit.



### Stick to iOS-style patterns

Yes, I know, the software development world is big. You might have learned certain design patterns in school or in Java/XYZ development. But not all of these patterns make sense in the iOS world.

For example, in the Android world MVP (Model View Presenter) is one of the dominant ways to distribute the work in your app. While there is nothing fundamentally wrong with MVP, it's just not how iOS ever worked. The sample code from Apple does not use MVP and generally MVP is a bit of a niche UI architecture pattern anyways.

So it's better to use MVVM, or if you must VIPER. Both are useful evolutions from the traditional MVC that used to be the norm for iOS / macOS and solve the massive view controller issue in a pre-defined way.



### Modularity and abstraction can also be bad

Unfortunately it is still taught in many courses and lectures that everything should be maximum modular. While modularity in general is great, you can overdo it and make your code hard to read.

If your persistence framework already brings a degree of abstraction (e.g. supporting all the storage types you could ever think of), maybe you should not have another layer on top of it separating your models from your persistence framework. Reason: It's unlikely that you will ever replace the persistence framework (unless it it is foreseeable that you will).

Make those things modular where replacement is actually forseeable. You are going to rewrite major parts of your code or replace the whole app in a few years anyways. On the flipside, a codebase can easily take 5x as long to read and understand it for a new dev if practically every part of your app is wrapped a few times.

Another example where abstraction often has to be flagged as YAGNI is concurrency: Some people argue that not every part of the app should depend on Combine / RxSwift / you name it. I think in many cases this is not a good trade-off. Either you abstract away a certain framework because e.g. one team wants to use it and the others do not, then it can make sense. In other cases, it is better to pick one strategy and then stick to that. If the team changes their mind 3 years later, just accept that not everything is consistent anymore and bridge where necessary like you would bridge old iOS APIs with closures to e.g. Swift concurrency. Alternatively embrace the new Swift concurrency and hope it will the standard for the next 5 years. But all this is still better than having not only a mixture of closures, Combine, maybe some RxSwift and Swift Concurrency and then wrapping each of them into your own proprietary concurrency framework which takes a new dev 2-4 weeks to really understand.



### Build with testability in mind

Make everything that needs to be configured and independent from outside dependencies as local and modular as possible. Otherwise you can not isolate parts of your code to test it. Again, pragmatics is important: It's often not really useful to have a big wrapper around persistence but e.g. you must be able to not store things to the SSD but to an in-memory database when running tests. Either your persistence layer already brings this with it, otherwise you need to build it around it.

Avoid references to globals dependencies as much as possible.

It's OK to not always have perfectly testable code from the beginning. You can still refactor a bit later as long as it's not "next year" or after you have left the team.

Focus on making those things modular and configurable that you are going to test first and most. Any v1 / v2 app will rarely have nor need 100% test coverage. You can still improve this later on in v3 or v4 if really needed and it will most of the time be OK.



### Build like a startup if you are one

Maturity has its place. If you are a bank, maybe you need to test every centimeter of your app. Maybe that risk warning needs to be absolutely visible 100% of the time so the bank does not get sued.

But I have seen startups where there apps had less than 100K users and it was not really in a regulated business like medical or finance and every single screen had snapshot tests. On the other hand, the app had some other architectural deficiencies that made it really difficult to extend it. It would have been a better use of time to refactor the app a bit than to have snapshot tests for every possible view in the app.

Also consider that new products usually change their requirements a lot in the first few years because business cases and requirements tend to be "wrong" in the beginning until the right ones are discovered later. So don't try to perfectly engineer things that may be thrown away anyways soon enough unless there is a good reason. E.g. authentication and encryption must always be done properly to keep data safe even if you are a startup, but you still do not need 100% test coverage for the whole app from the first release on.



