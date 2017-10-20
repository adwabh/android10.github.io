---
id: 13
title: Smooth your migration to Kotlin
date: 2017-10-20T19:15:58+00:00
author: Fernando Cejas
description: How to smoothly migrate your existing android java codebase to kotlin. These principles can also be applied to any existing technology being introduced.  
layout: post
permalink: /2017/10/20/smooth-your-migration-to-kotlin/
categories:
  - Android
  - Development
  - Kotlin
tags:
  - android
  - androiddev
  - process
  - agile
  - migration
  - transition
  - developer
  - developers
  - development
  - java
  - kotlin
  - mobile
  - mobile dev
  - programmer
  - programming
  - reactive programming
  - rxjava
  - testing
  - continuous learning
  - new technologies
---
<p class="justify">First of all, <span class="boldtext">I would like to give credit to who deserves it,</span> and in this occasion wanna say thanks to <a href="https://twitter.com/Mauin" target="_blank">@Mauin</a> for taking the leadership on the process and let me join the party. <span class="underlinetext">Was a real pleasure.</span> Also, I like to mention, that in this article there is <span class="boldtext">NO</span> source code involved and here is the reason: <span class="boldtext">It is not about technical implementation, it is about the process, philosophy and all the moving parts involved.</span></p>

<p class="justify">Same principles apply not only when introducing a new programming language, but also any new technology.</p>
 
<p class="justify">Actually the motivation behind this writing <a href="https://twitter.com/fernando_cejas/status/920591431730900992" target="_blank">came from a tweet</a> and a couple of discussions and <span class="boldtext">constructive feedback</span> around it:</p>

![fernando-cejas](/assets/images/smooth_kotlin_01.png){:class="img-responsive"}

<p class="justify">So in this article I will bring up <span class="boldtext">insights (and opinions)</span> on how to introduce <a href="https://kotlinlang.org/" target="_blank">Kotlin</a> into <span class="boldtext">your existing Android Java codebase.</span> All this material comes from <span class="boldtext">experiences and real facts,</span> which from my perspective, is the best way to share knowledge and lessons learned. So let's get started.</p>


# The movitation

<p class="justify">Let's say we heard about this new thing called <a href="https://kotlinlang.org/" target="_blank">Kotlin</a> (mainly) being used for <span class="boldtext">Android development</span> nowadays. Many people are talking about it, so we start to dive a little bit deeper and see potential in it, so in the end we decide to bring the topic to the table in our <span class="boldtext">next team meeting.</span></p>

<p class="justify">There should be always a motivation with strong arguments for betting on new technologies, and those reasons do <span class="boldtext">NOT</span> include:</p>

  * <span class="boldtext">Because everyone is using it.</span>
  * <span class="boldtext">Because it is trendy.</span>
  * <span class="boldtext">Because it is cool.</span>

![fernando-cejas](/assets/images/smooth_kotlin_02.jpg){:class="img-responsive"}

<p class="justify"><span class="boldtext">Keep in mind that we are taking risks at a technical and business level.</span> So if we do not do our homework first, we might run into the <span class="boldtext">unknown with disastrous consequences,</span> which are not easy to rollback and consume development time and for instance <span class="underlinetext">money for the organization.</span></p>

<p class="justify">To avoid these issues the first step should be to consider a bunch of basic questions:</p>

  * <span class="boldtext">Is the technology mature enough?</span>
  * <span class="boldtext">Has the technology support from the community?</span>
  * <span class="boldtext">Is out there other companies which have already adopted it?</span>
  * <span class="boldtext">Do we have experts in the team who can lead/guide us in the process?</span>
  * <span class="boldtext">Is the team interested in learning this new programming language?</span>

<p class="justify">These are, in my opinion, the first answers we need, in order to move forward to the next stage.</p>


# Involving the team

<p class="justify"><span class="boldtext">The advice here is to involve our team in the process as much as we can.</span> It is important that everyone feels part of the it by providing feedback, advice and opinions. 
We want to make sure we are in the same boat on our path.</p>

<p class="justify">There are a a couple of things we can do to collect such information and participation:</p>

  * <span class="boldtext">Collective meetings for discussions.</span>
  * <span class="boldtext">Hacker time for spiking.</span>
  * <span class="boldtext">Pair programming on a pet project as example.</span>
  * <span class="boldtext">RFCs through a shared document.</span>
  * <span class="boldtext">Some basic workshop or presentation after investigation.</span>

<p class="justify">We want everyone to commit as much as possible which will help <span class="boldtext">the decision making</span> and the <span class="boldtext">collection of information</span> about potential issues we might run into.</p>

<p class="justify">Something we did was to also talk to the <span class="boldtext">iOS Team,</span> since they had gone through a similar process when migrating from <span class="boldtext">Objective C to Swift:</span> <span class="underlinetext">super valuable feedback and experience.</span></p>

![fernando-cejas](/assets/images/smooth_kotlin_03.png){:class="img-responsive"}


# Resisting the inevitable

<p class="justify">In the past (when Kotlin was not officially supported by Google) we had a few failed attempts which caused <span class="boldtext">uncertainty and frustration</span> in the team due to these doubts:</p>

  * <span class="boldtext">Does it integrate well with our current Continuous Integration Pipeline?</span>
  * <span class="boldtext">What about tools availability? Like static analysis for example.</span>
  * <span class="boldtext">It is still in Beta and APIs are likely to change without backward compatibility in mind.</span>

<p class="justify">But... I think it is time to quote Pablo Neruda here which reflects the <span class="underlinetext">impact Kotlin is provoking<span class="boldtext"> in the development community:</p>

![fernando-cejas](/assets/images/smooth_kotlin_04.png){:class="img-responsive"}

<p class="justify">With that being said, nowadays there are <span class="boldtext">no many reasons to NOT introduce Kotlin,</span> or at least the good things beat the downsides. Anyway, that does not mean we do <span class="boldtext">NOT</span> have to focus on the pros and cons of introducing a new technology based on:</p>

  * <span class="boldtext">Our priorities as a Team/Company: we need time and we might slow down feature development in the beginning, although that is going to pay back in the future.</span>.
  * <span class="boldtext">The state of our current codebase: in our first attempt we were deep in changing our architecture and killing legacy code. Introducing a new programming language would increase our Lava Anti-Pattern. </span>.

<p class="justify">At <a href="https://soundcloud.com/" target="_blank">SoundCloud</a>, after dealing with these 2 aspects we decided <span class="underlinetext">it was worth to move forward,</span> so we came up with a bunch of bullet points (a couple of examples below) we would need to address in the next stage:</p>

  * <span class="boldtext">Make the Java-Kotlin interop as easy as possible.</span>
  * <span class="boldtext">When converting `@AutoValue` classes to `data class`, add static factory methods back in the same way. Ex. `@JvmStatic` functions in `companion objects` of the `data class`: Thus, we use the same methods in Java to create those objects. From Kotlin we can directly call the constructor.</span>
  * <span class="boldtext">If `data class` contains nullable types and those are still used from Java, we should add getters that directly return `Optional<T>` instead of the `Nullable` type, which we might miss in the Java context.</span>
  * <span class="boldtext">Keep in mind: Kotlins types don't always map directly to the Java types. Kotin `Long` is `long` in Java, which doesn't have all the compare methods.</span>

<p class="justify">This way, <span class="underlinetext">we prepare the terrain</span> by detecting potential issues and solutions to the most common problems in our codebase.</p>


# Our First PR

<p class="justify"><span class="boldtext">At this point, we detected possible problems to solve and it was time to officially get our hands dirty.</span> This is an <span class="underlinetext">important stage</span> because we are translating our previous investigation into code.</p>

<p class="justify">In my opinion this should be done with <span class="boldtext">Pair Programming.</span> There should be discussions, suggestions and pair reviews from the rest of the team. <<span class="boldtext">It is a good idea to put a deadline until when the PR will be open,</span> otherwise discussions become endless.</p>

<p class="justify">If you are wondering <span class="boldtext">what to work on for your first Kotlin PR,</span> I would say that just pick a simple functionality (a screen) in your app and refactor it end to end. This way, you start your <span class="boldtext">job vertically,</span> and <span class="boldtext">gradually</span> move to the rest of features. <span class="boldtext">Divide and conquer, remember?</span></p>

<p class="justify">Along the way, we did not only write code, <span class="boldtext">but also performed tests,</span> for example compilation time. The more information we collect sooner, the better.</p>

![fernando-cejas](/assets/images/smooth_kotlin_05.png){:class="img-responsive"}

<p class="justify"><span class="boldtext">Finally it was time to push the button and commit our first Kotlin PR.</span> It was a real pleasure to presence that moment (again thanks <a href="https://twitter.com/Mauin" target="_blank">@Mauin</a>!). Now we are <span class="underlinetext">happy kotlin coders!</span></p>

![fernando-cejas](/assets/images/smooth_kotlin_06.png){:class="img-responsive"}


# More power implies more responsibility

<p class="justify">Everything does not end up once you merge your first piece of Kotlin code. <span class="boldtext">We are embracing a new language (a new technology) so there should be commitment after it.</span> The main idea is to establish <span class="underlinetext">rules</span> we must follow in order to fully transition <span class="underlinetext">from the old stack to the new one.</span></p>

<p class="justify">Here are some a few examples to make this process smooth without impacting productivity too much:</p>

  * <span class="boldtext">Each new piece of code introduced to our codebase should be written in the new language.</span>
  * <span class="boldtext">Each piece of code touched should be migrated to the new language, unless it is a critical bug and needs rapid reaction. </span>
  * <span class="boldtext">When reviewing PRs, we should enforce the team to follow these rules. </span>
  * <span class="boldtext">If newly converted Kotlin code is still called from Java, try to keep the interface changes for the Java callers to a minimum.</span>

<p class="justify"><span class="boldtext">My suggestion is to also allocate time</span> (when possible) every sprint to migrate and refactor features. Maybe it is also a good idea to address technical debt too and write it in Kotlin: kill two birds with only one stone (legacy code and Java).</p>


# Continuous Learning

<p class="justify"><span class="boldtext">This an exciting process and we should keep it up by constantly learning, improving and addressing together problems we find along the way.</span> Sharing and transfer of knowledge is key. As mentioned earlier, what worked for us, were discussions in our collective weekly meeting, presentations, pair programming and little workshops. </p>

![fernando-cejas](/assets/images/smooth_kotlin_07.png){:class="img-responsive"}

<p class="justify">As a colleague of mine say, <span class="boldtext">do not put things in cold water</span> and always encourage continuous improvement.</p>


# Benefits and key points

<p class="justify">Here I wanted to include facts and situations we are getting out of this transition:</p>

  * <span class="boldtext">Boost to developer morale, karma and motivation.</span>
  * <span class="boldtext">Increased null safety in the Android codebase (due to nullable/non-nullable types).</span>
  * <span class="boldtext">Less boilerplate that has to be written, making Pull-Requests smaller and easier to review and the codebase as a whole easier to understand.</span>
  * <span class="boldtext">In the long term, being able to remove some third-party dependencies (e.g. Retrolambda, ButterKnife, AutoValue).</span>
  * <span class="boldtext">In the middle/long term, it also keeps us as a competitive and modern organization betting on the latest/new technologies, in order to attract future talent to the Company/Team.</span>
  * <span class="boldtext">Staying up-to-date with the Android developer community.</span>
  * <span class="boldtext">Testing in Kotlin might help, it is better than nothing but you will not appreciate the real power until you solve real world problems with it.</span>


# Conclusion

<p class="justify"><span class="boldtext">As we have seen, it is never straightforward to introduce such big changes.</span> Also, the bigger the company is, the more difficult, due to its moving parts, team size and amount of code generated every day.</p>

<p class="justify">That is why it is important to come up with a <span class="boldtext">plan to make the process as smooth as possible.</span></p>

<p class="justify">This is all I have to offer for now, <a href="https://twitter.com/fernando_cejas" target="_blank">ping me on Twitter</a> for discussions/feedback. <span class="boldtext">Happy kotlin coding!</span></p>