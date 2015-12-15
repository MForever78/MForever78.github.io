---
layout: post
comments: false
title: 「程序员」还是「代码生成器」？
category: essay
tag: ali programmer
---

TL; DR. 工程师在项目中的角色不应只是执行者，而应是整个项目的参与者。

> 按：国外的程序员有一句自嘲的话叫做：*coffee in, code out.* 程序员是最喜欢自嘲的一群人，这不失为一种积极的生活态度。但有些话如果你当真了，结果往往不会很好，比如刚才那句话。

入职以后接到的第一个任务，就是更新和修复现有的部门签名系统。这个系统可以根据当前登入的帐号自动从后台获取相关信息，填入到签名档的对应位置上，因为自动获取到的信息可能有误差，所以还支持用户手动修改。用户修改完成后可以生成一张图片保存下来，加入到邮件的签名档中。因为设计了新一版的邮件签名，所以需要按照新的设计图更新签名样式，同时需要解决当前系统中某一行过长时不能自动换行的 bug，以及后台信息获取不全的问题。

接到任务后，我大致浏览了一遍现有系统的源代码以后就马上着手改版了。在我看来，对于原有的系统来说，这些任务只是一些小修小补，所以我直接选择了在现有的系统上加 patch 的方法。对于新增加的设计图与原来的系统产生的不兼容的部分，也通过一些 patch 来修改掉。

这样做的结果就是，这个任务完成后，系统里充斥着各种各样的 patch ，逻辑异常复杂，如果不仔细想的话，就连自己再读一遍代码也未必能马上明白所有地方。花了一天完成的新系统虽然上线了，布置给我任务的师兄和我自己都不太满意。所幸这只是一个内部的系统，我还有机会补救。我提出重构整个系统。为了不让重构仅仅是重蹈覆辙，我们对于出现的问题进行了分析。

## 问题分析

回顾从接到任务开始到重构前这整个的过程，问题出在了哪里呢？在接到任务以后，我做的是马上着手执行。第一个问题可能就出在「马上」上面。

第一次完成后的系统虽然能正常工作，但面临的最大的问题就是，当需要添加新的设计图或者对现有的设计图做出修改时，非常难以维护。然而在动手之前，我并没有考虑过这些问题。这个系统曾经供五六个不同的部门共同使用，每个部门都有自己的签名档，并且每个的设计也在不断地变化当中。也就是说，这个系统需要高度可维护和可扩展是一个隐含的需求，然而因为我并没有对整个系统的背景进行了解，只是执行眼前的任务，造成了最终难以维护的问题。

**所以，工程师不仅要写代码，还要了解需求的背景，以指导自己的工作。**

在解决后台信息获取不全的这个问题时，我花费了大量的时间在对接接口上，然而最终发现当时系统所用的 PHP 后台接口已经很久没有人维护了，连文档中给出的示例都无法正常运行。这样的经历无疑让人沮丧。究其问题在于，在接到任务的时候，我仅仅了解到后台提供了这样的接口就作罢，没有对接口进行可用性测试，最终浪费了自己的时间。换句话说，需求方提供的资源不一定都是可用的，在正式开始工作之前，需要对资源进行可用性测试。

**所以，工程师不仅要写代码，还要测试资源的可用性，以支持自己的工作。**

我接到的任务不算复杂，但直接就去写代码的执行方式还是白白花费了不少时间和精力。对于文字换行这一个 bug ，其实可考虑的情况有很多：一种是因为部门信息本身就过长，从后端取得的结果直接就超出了一行；另一种是用户在自己修改的时候让结果超过了一行。而第二种情况又可以细分为两种，即用户输入了换行符，或者添加文字后同段的内容超过一行。对于逻辑处理来说，这几种情况每种需要做的处理都不一样。如果没有想清楚就去执行，要么是在过程中发现问题，逻辑反复更改，要么上线后才发现没有考虑周全，这都不是我们想要看到的。

**所以，工程师不仅要写代码，还要对需求进行完整地分析，把需求拆分成具体可执行的动作，以确保自己的工作严谨和周密。**

在学校中，我们常常说 Deadline 是第一生产力。没有时限的工作往往是很难有效推进的。学校里的 Deadline 通常由老师根据经验来确定，而工作中就很难这样了。布置任务的时候，师兄问我说，三个小时能不能做完？我大概只是简单的过了一下脑子，就回答可以。结果最终花了一整天的时间。这里就涉及到一个时间评估的问题。在真实业务场景下，需求方通常要和工程师协商一个时间线，从工程师的角度来讲，这个时间如何评估才比较合理呢？在上一步需求拆分完成后，针对每一个具体可执行的小步骤，都预估一个时间。那些很难直接给出预估时间的地方，就是任务的风险点。比如在这个任务中，我对于 Canvas 的操作不是很熟悉，这里可能占用很多时间，就是一个风险点。对于风险点，要找出解决方案，并且给出足够并且合理的预留时间。把所有时间加到一起，再向上浮动 10% ，才是一个比较准确的预估时间。预估的时间对于整个项目的流程都起着至关重要的作用。

**所以，工程师不仅要写代码，还要对时间进行合理预估，配合需求方制定项目的时间流程。**

在做完以上所有的工作之后，才到了具体的执行阶段。也就是之前我认为的工程师工作的全部——写代码。在完成这个任务的这一过程中，也不是没有可以改进的地方。在写代码时候，遇到的问题我都倾向于独立解决，即使在已经花费了很长时间的情况下，也没有及时与师兄沟通。我们都遇到过这样的情况，自己苦苦想了很久的问题，其实已经进入了死胡同，然而局外人一句话就可能帮我们解决。对于实际项目来讲，沟通不仅仅局限于技术人员之间，发现问题后作为工程师，还应该及时与需求方沟通，保持信息的通畅，来帮助需求方协调整个项目。完成这个系统的时候我遇到的技术上的问题没有及时和别人沟通，在约定的时间快要截止的时候没有通知任何人，这在真实项目中是需要坚决避免的。

**所以，工程师不仅要写代码，还要把问题及时与相关人员沟通，帮助自己也帮助别人高效地工作。**


## 重构

问题分析过后，对于我来说，这一重构的过程也是我对工程师在项目中的定位的重新认识的过程。我记下了这个过程，感兴趣的同学可以移步[这里](/project/2015/08/01/refactoring-the-right-way/)查看。

## 结语

现在有很多工程师抱怨自己在大公司中只是一枚镙丝钉，多了不多，少了不少。他们每天埋头写代码，需求来了，执行任务，再等待下一个需求。至少我认为，「人」的价值不应该仅限于此。如果把人当作黑盒，*coffee in, code out*，那人又和工具有什么差别呢？要做一个「程序员」还是「代码生成器」，值得思考。
