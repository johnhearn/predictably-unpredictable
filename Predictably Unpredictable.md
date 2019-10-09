# Predictably Unpredictable

Hello, I'm John. I work at Codurance. I'm going to talk a little bit about uncertainty. What's bad about it and what's good about it.

Before we start I'd like to ask for your help with an experiment. Could you try and start to clap together, in sync? Ok. Now I've never done this before but... I'd like *half* of you to stop clapping. OK, that's interesting. *[Do a quarter depending on response]*

1. [Doesn't work] I didn't predict that! [Maybe try it again]
2. [Works] Very clever... how did you decide so quickly who would clap? I remember doing this back when I was at primary school, it still amazes me. Steven Strogatz, well known for his work in chaos theory, also used this in a talk[ref]. Keep this in the back of your mind, we'll come back to it a little later. BTW, I'll put this and other references at the end of this talk and on my blog for people who are interested in going deeper.

## Introduction

[*Hold up pen*] When I drop this pen I'm going to guess that everyone knows what will happen. It's not a trick question, this is about as close to certainty as we can get. [*Drop pen*]

**[Slide "If only we could predict the future"]** <u>We love certainty</u> and we're always looking for new ways to find it... and with good reason, *we think* it means we can predict the future. **[BTTFII slide]** I don't know if you recognise this picture, it's from BTTFII. If things were predictable then we could be like Biff, who had an almanac from the future. He was able to bet on future events which he already knew the result of and used that knowledge to make himself a lot of money. 

When we can predict the future then we can make plans, it's <u>safe-to-plan</u>. And not only Hannibal from the A-Team likes it when a plan comes together.

But there is an **unfortunate truth**.... it's pretty predictable that <u>in many situations</u> we <u>simply can't predict the future</u>, even in principle, no matter how much knowledge we have about the present or the past. 

In fact, this is the <u>fundamental premise that many agile</u> and craftsmanship practices, like short iterations and TDD, <u>are based on</u>. 

Today I'm going try and demonstrate where this uncertainty comes from... and also some ways that, rather than trying to tame uncertainty, and engineer it away, we can not only <u>live and work with it</u> but even consider it an opportunity to take <u>advantage of it</u>.

## Connections In Code

Connections are everywhere. **[Slide `fun first() { second() }`]** The `first` function is obviously connected with `second`.  It's needs to know its name, how to call it, etc. etc. If the behaviour of `second` changes then it affects the behaviour of `first`. Bugs propagate. Have you considered the connection in the opposite sense? Does the correct functioning of `second` depend on `first`? Well actually it could, there are <u>certain preconditions</u> that must be met, parameter values (I'll assume there's no shared state), performance, timing. This is sometimes codified, as in CDD or formal methods, but usually it's implied. A change to `first` <u>can indeed break</u>  `second`. But looking at `second` you wouldn't know that, it's an invisible connection. 

There are similar connections between classes, libraries, modules, etc. Many types of coupling, *or connascence*[ref], are widely known. These aren't the only connections, though. Some are even more insidious.

**[Slide with tiny GildedRose]** This is the GildedRose, quite a well known programming kata, you might have seen it before. The kata covers two main things, creating a set of tests to cover existing behaviour and then refactoring to *reduce complexity*. I want to draw your attention towards the word "***complexity***". What does it mean in this case? First <u>it's difficult to grok and complicated to understand</u>. Refactoring, we like to break things up into bite-sized blocks to make the cognitive load easier. We can still understand it though, if we take the time, it's a simple kind of complexity. There's another meaning though. 

These two pieces of code represent the *same* condition, they are invisibly connected. If, for example, one of those conditions needs to change then <u>probably *both* need to change</u>, we have a dependency, an invisible connection. These connections could be far apart, they needn't even be in the same file. This kind of connection is a source of an enormous number of bugs[ref]. 

BTW, the best solutions to this kata <u>don't just extract</u> the conditions, they <u>make the conditions a part of the design</u>. Something for another talk.

The same applies to copy/pasted logic. If it needs to change then it needs to change everywhere which is why we try and keep DRY. It's also the main driver behind several other design principles.

So connections, especially hidden ones, <u>increase the chance</u> of a change <u>not having the intended effect</u> or, worse, having an <u>unintended effect</u>. Reducing these kind of connections increases your confidence that any change will have **predictable** consequences. It is the reason refactoring and code quality is so important, it helps us remove potential defects before they even happen, and by extension we need tests to give us confidence in those refactorings.

## Connections in systems

**[Slide 1 service + 1 DB]** This kind of situation is not limited to code. Look at these micro services. This is a simplification of a real system which evolved from a single service. **[Slide 2 services]**The first service was split out into separate domains making them <u>independently deployable</u>. Good right? They shared a common logging format, defined in a shared library, another invisible connection, <u>make a change here and you have to deploy both services</u> to get the benefit, which became difficult to maintain **[Slide 3 services]** so the logging library was refactored into a separate service. Nothing unusual. Just three dependencies. Both services depend on logging and the second service also depends on a DB. So let me ask you: why is the first service dependent on the database of the second service?

What actually happened in this case is that the database started rejecting connections for whatever reason, and what does the second service do in this case? Well it causes an unrecoverable error which is <u>dilligently logged</u>, <u>stack trace and all</u>.... for every request, <u>hundreds every second</u>. The logging service is saturated which means that the first service gets timeouts. It's been broken by the problem with the database.

Of course there are any number of ways of mitigating this particular situation. Circuit-breakers, let's shove a Kafka in there for even more fun. That's not the point. **[Slide Mary Poppendieck quote]** The point is that this system evolved for valid reasons but the *law of unintended consequences* took effect. 

[Optional] There are several ways other ways it could have happened. Maybe the services are running on the same infrastructure and suffer the same outage. Or someone broke the load-balancer. These things have happened and I'm sure you can think of a few more failure modes.

## Interactions in systems

**[Slide Vizceral screenshot]** Bearing in mind the previous simple system, imagine the connections present here! Connections grow combinatorially, sometimes called [Metcalf's Law](https://en.wikipedia.org/wiki/Metcalfe%27s_law). 

*[Talk about resilience, anti-fragile etc.?? Or is it too much??]*

**This is a another level of complexity**, one in which even though the individual components are simple, it's still really really hard to predict what will happen. <u>Experience and good practice</u> is essential.

**[Slide Netflix job "to successfully minimize the consequences of encountering inevitable failure"]** In fact, there might be a point when it's <u>too hard to "fix"</u> all these problems, after the fact, <u>with more engineering</u>.... more engineering could even potentially exacerbate the problem. Netflix's approach to resilience is an example of a novel way to think about how to deal with these kinds of problems.

One other thing to notice, as the Scorpions wisely said **[Slide Scorpions quote]** Behaviours don't happen in static diagrams. Changes happen over time. Changes affect behaviour which cause more changes. We're getting into the realm of *dynamics*. This is why observability and monitoring are so important. If you can't see what is happening over time then you can't see trends and unexpected behaviours until they've happened. And if experience has taught me anything then it is that unexpected behaviours *will* happen.

## More interactions in systems 

**[Slide Game of Life]** Here's another kata that I'm sure many of you know well. **[Slide GoL rules]** A square grid, on each iteration update each cell according to some simple rules. Let it go...  The patterns that are formed can be quite <u>astounding given the simplicity</u> of the rules. How can these simple rules produce such complexity? You guessed it - connections. Every cell is directly connected to every cell around it, each of which in turn is connected to every cell around them. Not only that, and this is important, on the second iteration, the cell is indirectly connected to itself, causing a feedback loop.

This is the area of complexity science and on the edge of chaos theory. **This is another level of complexity**, one involving **feedback**. Save for some trivial cases, while these systems are deterministic, they are also **unpredictable**, even in principle, in fact from those simple rules you can get [pseudo-randomness](http://www.bignell.demon.co.uk/life.htm). The only way to test the output is to run the whole thing.

And the Game of Life is just one of many. **[Slide Complexity explorables]** Similar models have been created for chemical reactions, population fluctuations, fluid flow and many other. 

If you're interested, I recommend looking at the Complexity Explorables website for many examples you can play with. More elaborate models replace grids with networks to model epidemics, neurons and aerodynamics. 

The thing they share is **deterministic unpredictablity**. And it's everywhere we look.

**[Slide neurons]** Consider yourself. Although we are literally conscious of the emergent behaviour of our own neurons, we still have so much to learn about how it works. It's more than possible that this is a consequence of this kind of complexity[ref]. 

**[Slide complexity everywhere]** There is a reason weather predictions are so difficult, even in the short term. Similar patterns are seen everywhere at all scales, from the surface of the sun to petri-dishes in the lab, maybe as far as galactic filaments or at the smallest scales as the root of spacetime itself.

Once again the law of <u>unintended consquences</u> rears it's ugly head. Examples abound. **[Slides moquitos]** Researchers trying to irradicate malaria in Brazil have introduced mosquitos which were genetically engineered to be sterile. It turns our not all were sterile and the modifications have found their way into the natural population, potentially making it <u>more resilient to the very actions they are trying to take</u>. Unintended consequences.

## Connections in CASs

So <u>connections make our world less predictable</u> and they are all around us, hidden ones too. Connected things interact and those interactions can result in unanticipated or unpredictable behaviours and feedback, that is self-interaction, even more so. 

It seems bad but it goes further.

**[Slide human network]** What happens when the things that are interacting are not inanimate but rather intelligent agents or even thinking, breathing people? These agents can adapt their behaviour within the system they are in. These are known as **Complex Adaptive Systems**, that is systems which not only have large numbers of internal connections with their own interactions and feedback loops, but also the components of the system adapt to the changing circumstances of the system itself. **This is yet another definition of complexity** and a <u>order or magnitude more unpredictable</u> than the ones we've seen before.

Does that sound familiar? It's the organisations, communities, social networks, cities, countries we live in. It's politics all over the world. We are usually inside these systems, interacting with them ourselves. <u>Once again</u> the law of unintended consequences, the so-called cobra effect, are even more pervasive. Intelligent reactions to policies shift the ground under those same policies, potentially making them less effective or worse counter-productive. The eponymous example originated from India[ref] but examples abound.

**[Slide [Charles Goodhart](https://en.wikipedia.org/wiki/Charles_Goodhart) quote]** People will adapt to the changing context, in effect gaming the system. This is why performance measurements are a bad idea. 

Netflix resilience engineers have this covered too. **[Slide Netflix as a socio-technical]** 

Let's take another example. **[Slide hajj pilgramige]** Simulations were and are used to model crowd movements. The movement of people seems like fluid flow and we've had great sucess modelling that. There was some success at first, simulations are able to predict problematic places where the flow of people became turbulent. Eliptical columns were added to encourange the flow around those points and that worked. But there's a problem. **[Slide Keith quote]** People are not dots on screens. People do not follow simple rules. **[Slide complexity tweet]**

So how can we make decisions in a situation like this? 

It's traditionally been done by command-and-control. The Saudis tried to <u>control crowd movements with traffic lights and detailed schedules</u>. That may be part of the solution but complex and changing contexts quickly <u>invalidate the assumptions</u>. <this happens in any group dynamic, including organisations.

**[Slide hierarchy vs flat]** <u>Hierarchy</u> is an attempt to <u>limit connections to a tree</u>, concentrating information in the highest layers. It's kind of worked for centuries but that is less and less true in our connected age. **[Slide Henney "System is not a tree" quote]** 

**[Slide "individuals and interactions over processes and tools"]** This might sound pretty depressing but it is not all doom-and-gloom. 

Let's go back to the experiment that we did at the beginning. You, the audience, are a complex adaptive system! You were able to synchronise your clapping not because someone told you when and how frequently to clap, but rather by adjusting to those around you. This is known as self-organisation and it is a feature of adaptive systems. By looking around and adjusting your own behaviour a system dynamic was created with no master plan, a headless machine. 

**[Slide cube "Nobody is in charge."]** The conditions were in place and the behaviour emerged. [Optional] Although I not suggesting creating making a death machine like the film, just to make that clear.

In fact, there are many ways that we can take advantage the dynamism that complex adaptive systems bring.

For example, in agile organisations multi-disciplinary teams <u>purposely increase connectedness within teams</u> to take advantage of their capacity as a team to react to changing circumstances.

<u>But this kind of thinking is still linear</u>. Small iterations drag work in small batches, the idea being that these small batches themselves are more predictable, even if we have to change course more often.

## Systems Thinking

We can do more. We can think about complex systems at a more holistic level. 

A really simple example we've been taking about recently: the fear of deploying to production.

**[Slide indiana jones meme]** The industry <u>trend is to deploy more frequently</u> but there are barriers, especially when there is a <u>history of risky or unreliable deployments</u>. It is only human that fear of introducing bugs and other deployment problems tends to make us want to put off production releases. But the longer the release cycle then the more features are released together. More features together means more risk and more potential bugs. It's a vicious cycle. 

**[Slide release cycle causal diagram]** This is a simple example of a feedback loop. Systems thinkers use causal loop diagrams like this one to show the circularity. The connections between features tend to mean that the number of bugs is not simply proportional to the number of features. This is known as a non-linearity and is a critical part of systems thinking. In this scenario, increasing quality may be a leverage point as much as trying to force unconvinced release managers of the need to faster releases. Everything is connected.

**[Slide work load/pressure/quality]** Another well know simple example is the load/quality relation. Greater work load tends to increase the pressure to deliver. Greater pressure to deliver tends to reduce the quality of the delivery. Lower quality delivery tends to increase the work load from critical incidents. Again we have a vicious circle and I'm sure many of you have experienced it. But think about the reverse, lower demand tends to reduce delivery pressure which tends to increase quality, increased quality tends to reduce production incidents which in turn decreases pressure, which means we can support more load. This is a justification for what we all know: <u>in some circumstances</u>, when the conditions are right, <u>reducing demand</u> in the short term can lead to <u>increased delivery quality and performance</u> in the longer term.

This is just a taster of the systems thinking approach and the <u>causal loop diagram</u> is just one method. <u>Flows, stocks and delays</u> are fundamental to understand the complexity of adaptive systems and I encourage you to look deeper into it. Feeback loops are widespread and interlocking and sometimes the leverage points are not where you might expect them to be.

## Cynefin

Another approach which takes a systemic perspective to complex adaptive systems is the Cynefin framework. **[Slide Cynefin]** It does not even try to model the interactions between elements and people but rather provides an <u>extensive framework of tools and techniques</u> to make <u>decisions dependent on situation</u>, including methods which <u>embrace neuroscience and human behaviour</u>, like a penchant for narratives, to make better decisions. 

It divides the landscape of any situation into <u>five separate domains</u>: Simple or Obvious, Complicated, Complex, Chaotic and Disordered in the middle. 

Considering the human-centred complexity (so-called anthro-complexity) we've been talking about we can see how these domains fit. 

The <u>Complex domain</u> coincides with the highest levels of complexity formed by groups of people, organisations, communities, etc. where outcomes are not predictable at all and only discoverable in hindsight. Predicting things in the complex domain is futile but this can be used to advantage. We can **design for serendipity**, make deliberate connections, coherent probes and experiments which are safe-to-fail.

Compare this to the Complicated domain where . This distinction is very important. Agile methods, especially Scrum, tend to be on the limit between Complicated and Complex. What Dave Snowden (the framework's creator) calls the "liminal" areas between the domains. 

Within the <u>complicated domain</u>, things are predictable given enough analysis or experience to be able to understand the interactions although surprises can and will happen. Good-practices and expertise are possible and necessary. Deeper analysis and more upfront design make sense to avoid rework and wasted time. It's <u>safer</u> to plan.

Withing the <u>simple domain</u> you know what you need to do because you've done it a million times. Best-practices, repeatable procedures and recipies make sense. 

The <u>chaotic domain</u> on the other hand, is one in which there are no constraints on behaviour. The quintessential example being the burning house where decisions are made instantly. This is where totally unpredictable things happen, novel solutions are invented, unintended consequences are inevitable. Cynefin has techniques to use this to advantage. Chaotic situations tend to play out quickly.

They are separated by the <u>catastrophic fold</u>, the short distance between simplicity and chaos. Like piling blocks one on top of another, it seems simple until the lot comes toppling down in an instant.

<u>Disorder</u> is when you don't know which domain your in and decisions are made in an ad-hoc fashion. Where many of us are.

The <u>great power of Cynefin</u> is the <u>situational awareness</u> that the domains and the techniques bring. Also it allows us to make better decisions about the techniques we use. Longer term planning, upfront design or even waterfall type projects might be appropriate for obvious domain. Greater analysis and design would be appropriate for the complicated domain. OODA, agile or other adaptive short iterative techniques are more suited to the complex domain than linear ones are.

I'm just starting out on the road to understand the full power of this framework and I don't claim to have any expertise in this area but I really do think that situational awareness, <u>especially in the socio-tecnological world we live in</u> is of immense interest. I encourage you to look deeper.

## Conclusions

OK, so lets recap. 

Connections are everywhere, some more obvious than others. It is these connections and the interactions they facilitate that make things difficult to predice. The way that these connection interact has a profound effect on how the system behaves. 

This is not always clear in our everyday lives because we have a tendency, to avoid uncertainty by reducing things to smaller, understandable chunks, which gives us a **biased view of reality**.

We have seen different levels of complexity, each one becoming a thicker veil to the future. 

- Complexity as complicated (analysis, refactoring, good practices, experience)
- Complexity as unanticipated (robustness/resilience, inevitable surprise)
- Complexity as unpredictable (safe-to-fail, experimentation)
- Complexity as adaptation (ritual, novelty, anti-fragile)

**[Slide Prigogine quote]** Ilya Prigogine, one of the fathers of modern complexity theory went so far as to write abook called "The End of Certainty".

Working with systems, especially if they involve people, make unpredictability practically certain. Interactions and feedback loops which are everywhere around us can dampen or amplify change but can also send systems off in unknown directions, for good or bad.

Concepts like emergence and the law of unintended consequences go hand in hand with complexity and uncertainty. When the conditions are right, rather than trying to engineer predictability and reduce the uncertainty, **we can consider it as a catalyst** for novelty, innovation and resilience.

## References

[How things in nature tend to sync up - Steven Strogatz](https://www.youtube.com/watch?v=aSNrKS-sCE0)

[Connascence.io](https://connascence.io/)

[If considered harmful: How to eradicate 95% of all your bugs in one simple step - Jules May](https://www.youtube.com/watch?v=z43bmaMwagI)

[Complexity Explorables](https://www.complexity-explorables.org/)

[What Is Spacetime, Really?—Stephen Wolfram](http://blog.stephenwolfram.com/2015/12/what-is-spacetime-really)

[Event-Based Simulation of Spiking Neural Networks - Rainer Engelken](https://www.youtube.com/watch?v=cLLQcshWEbE)

[A system is not a tree - Kevlin Henney](https://www.youtube.com/watch?v=ARkLVvtxUZI)

[The Ultimate Guide to the OODA Loop](https://taylorpearson.me/ooda-loop/)