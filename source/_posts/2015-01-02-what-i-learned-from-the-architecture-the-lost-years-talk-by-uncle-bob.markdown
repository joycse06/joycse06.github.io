---
layout: post
title: "What I learned from the 'Architecture, the Lost years' Talk by Uncle Bob"
date: 2015-01-02 23:05:35 -0500
comments: true
categories: architecture web
---

I was watching [this talk](https://www.youtube.com/watch?v=WpkDN78P884) from [Uncle Bob](https://sites.google.com/site/unclebobconsultingllc/) a while ago and been thinking a lot about the content. Though he primarily talked about Rails in a Ruby Conf, the content is framework agnostic and apply to all other MVCs/frameworks as well.

<!-- more -->

I will be writing a summary of what I have understood so far of it.

Firstly, **the top level directory structure of an application should not communicate any information about the tools being used to build the app.**

Like in any MVC, when we look at the top level directory of any MVC framework based app we see directories like Controllers, Models, Views etc which gives us idea about the MVC pattern which is not related to the intent of the application being developed.

He told that WEB is a delivery mechanism and tools are just detail of the project. The application itself should be decoupled from the tools so it's framework agnostic.

He was talking about slow tests, and told Cucumber is not slow, it's a nice little library. What is slow is to boot up whole Rails to run the tests? Because the application is so coupled with the tools.

He talked about interactors, kind of the bridge between your domain level entities and business logic with the delivery mechanism.

If an application can be developed that way then the domain entities/business logic could be packaged as a gem or jar and can be completly decoupled from the delivery mechanism.

He also told views should not be aware of business objects/entities, instead one should use some kind of bridge like viewmodel/presenters to communicate those information into views so the views are so stupid that you don't have to test it.

He was talking about a use case architecture of a project he built, where they used TDD(Red-Green-Refactor cycle) to do the whole app. It was a WiKi app. They started building it with TDD without any persistance layer, and they were storing objects in memory to do testing. Then at some point, when they were in need a persistent data store they wrote a new plugin to replace the in memory store with a file based storage. They developed the whole thing and deployed it to clients without any kind of database.

Then one day one client came and they need a real database due to some business policy restrictions. To fullfil that request they wrote a real database storage plugin to replace the filesytem storage with a DBMS in one day.

He told it as an example architecture of how a decoupled component-based can help refactor things so easily and the project was fully unit tested they could refactor and deploy it within days without worrying about breaking something.

Got this elements summary in [this post](http://hugo.ferreira.cc/post/28240089877/keynote-architecture-the-lost-years-by-robert)

>Entities — Generic application agnostic data and business rules (e.g. Order, Customer).
>
>Interactors — Application specific business rules; Manipulates the relevant data from the Request Model and interacts with the Entities.
>
>Boundaries / Gateways — Protocols or Interfaces.
>
>Models — Dumb data structures, without methods.
>
>Request Model
>Response Model
>View Model

>Controllers — Convert the stuff submitted by the delivery mechanism into a delivery agnostic Request Model.
>
>Presenters — Turns the delivery agnostic Response Model into a prepared “viewbified” data structure ready for easy manipulation by the view.
>
>"… so the view is so stupid that you don’t have to test it!"

Here are some good things he told in the talk:

* ###Web is a delivery Mecahnism


* ###Database is a detail and not any integral part of the intent of the applicaiton itself

* ### A good architecture allows major decisions to be deferred, like choosing a persistance storage

* ###A good architecture maximizes the number of decisions not made, so you can decide those later and plug them in to fit business needs or respond to changes in business logic




