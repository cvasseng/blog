title: Help, I accidentally built a compiler!
categories:
- programming
- gamedev
---

**When I was 13 years old, I took an interest in programming. One of the primary driving forces was that I really, really wanted to build a game. Specifically, I wanted to build a role playing game. 17 years later, my quest is still not completed.**

## The Quest

I’ve lost count on how many times I’ve started building my Super Awesome Role Playing Game. Dozens for sure. Usually I’ve made it to some half-assed state where I have a few really basic things working before real-life’s come calling on me. Sometimes I’ve made it further, to the point where something’s been playable, and other times I’ve strayed so far off the path in my quest that I’ve ended up doing something completely different, yet tangibly related (like the at least two times I ended up building UI libraries instead - the latest which is available [here](http://tinkerer.xyz/ThursUI)). 

My compiler is very much of the “how did I even end up here”-type. 

## It Begins (Again)

My latest quest started sometime around August 2014. I was able to get a few hours a week to do pet projects when my work schedule decreased from 16-ish hour days to a more reasonable 10-ish. And what better way to use those extra hours after coding than for more coding? I resurrected my seemingly unattainable quest from the depths of my journal, and began thinking the same thoughts I had so many times before. 2d? 3d? C++? Node/JS? Some existing engine? After a week or two I settled on good old C++, mostly because at the time I was really, really tired of the web-stack which had been my day job for some time. And off I went; starting to define the architecture for *The Engine*. Again. 

All was well. Until it became time to choose a scripting language. Lua is usually my goto-solution, as it’s lean, fast, and fairly easy to bind to if the game/engine is written with it in mind from the beginning. This time though, things were different. A friend of mine had been talking a lot about programming language theory, and his own quest to build his own programming language. The discussions where quite interesting, and piqued my own interest in the topic. I started thinking that maybe a custom solution would be superior. A language designed to build RPG’s and nothing else. A language where RPG-related concepts were language types and constructs. I caved to my inner cravings of reinventing the wheel, and set out to define a syntax for *The Language*. All bets where off at this point.

I started reading whatever I could on implementing virtual machines. I didn’t really find any good material for non-lisp/scheme languages, so I was mostly fumbling around in the dark. I ended up stealing a few instructions from x86 assembly, and created a weirdly encoded bytestream and a bunch of case statements. After not too long, I had a simple thing running where I could perform basic math on some pre-determined registers. I was completely hooked. The code was so simple, but it had so much potential. That’s when I decided - “people will call me crazy, but I’m gonna do this thing”.

I wrote the lexer and parser by hand, and created a simple abstract syntax tree to do code generation. The code was ugly as hell, but was eventually able to compile my language to my own custom bytecode, running in my own custom vm. I felt pretty bad ass.

*First version of the syntax: defining a Hitpoint character attribute*
        
        attribute Hitpoints
            meta
                title "Hitpoints"
            end
            on change
              if Hitpoints < 0 
                  ; The character is dead!
              end
            end
        end

Before I knew it, I had implemented character attributes, AI, items, skills, buffs/de-buffs, and all sorts of things. Finally it was time to pull it all together and create my game! 

## Help, I need a UI

After I had my client integrated and up and running (the vm/engine was written as a headless server at that point), I realized I had 99 problems, and UI was one of them. I searched far and wide for days, and just couldn’t find anything that looked good enough. No disrespect to those of you out there maintaining things like CEGUI - my requirements were somewhat strict. For one, I really wanted something vector based. I’m not an artist, so creating skins is a pain when having to deal with texture atlases - I wanted CSS-esque styling, and I wanted crisp rendering without artifacts when running at high resolutions. A lot of the ones I tried didn’t compile within a reasonable amount of time either, which excluded them immediately (as in - it took me longer than two hours to gather up all the dependencies and get the library to compile).

I changed my searches to find hardware accelerated vector rasterizers instead. It didn’t take long before I stumbled across [nanovg](https://github.com/memononen/nanovg), which looked absolutely perfect. The demo was exactly what I was looking for. Unfortunately, I couldn’t find a UI library built on top of it, so over the next couple of weeks I created [a crude one myself](http://tinkerer.xyz/ThursUI). 

## Help, everything I did was wrong!

In January 2015, the situation at work changed, and I was unable to continue grooming my pet project. Back into the journal it went. Then three months later, I was once again in a position to devote a couple of hours here and there to it. But of course - in typical fashion - I made the decision to abandon everything and start from scratch - this time building the compiler/vm as a separate library, meant to be used similarly to Lua where it’s integrated into other games/engines.
This might seem a bit drastic, but I had learned quite a lot during those 5 months of being away from the code, and I knew that if I started fresh, I’d end up with something a whole lot better. 

I’ve now been working on and off for 7 months on the new version, and it’s closing in on the point where it’s mature enough to be used. The concept is pretty much the same as my “test run”, but the syntax is now more C-ish than Pascal-ish. It also solves certain domain problems in a better, more general way. As an example - instead of having primitives for buffs and debuffs, now there’s just a generic “timer” primitive that has optional modifiers, and that can be applied to an actor in the game. It’s also much, much faster than the first version, and have several things the first version lacked (like variables and proper functions, localization support, a debugger, and more). 

*The current version of the language:*

        attribute HitPoints : InitialValue(100), Max(MaxHealth) {
          meta {
            localized title "Hit Points";
            localized description "Character Health";
          }
        
          on change {
            print("Health changed, and is now", HitPoints);
            if HitPoints <= 0 {
              actor->kill();
            }
          }
        }


## Conclusion

For some, the destination is the most important part. For others - like me - the journey is where it’s at. Every time I’ve started this project I’ve gotten something out of it. This time, I got a very deep understanding of compilers, and virtual machines - two areas that were pretty much magic to me prior to starting. I also got a decent [prototyping UI library](http://tinkerer.xyz/ThursUI) out of it, and a working domain-specific scripting language 

The first public preview version of the language will be released soon under a BSD-style license.
