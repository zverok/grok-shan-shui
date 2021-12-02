# Day 01

While others solve their beloved puzzles from AoC, I wanted to have my own advent.

During December, every day, I'll be working towards understanding how the [shan-shui-inf](https://github.com/LingDong-/shan-shui-inf) project works.

## What

`{Shan, Shui}*` (that's the proper name of the project) is a generative art work by an awesome developer/artist [Lingdong Huang](https://github.com/LingDong-) (check also his other projects, they are absolutely stunning!).

It is described as _"Procedurally-generated vector-format infinitely-scrolling Chinese landscape for the browser."_ and fully lives to the description:

![](image00.png)

(Screenshot from the project's README).

And it is _procedural_ (e.g. all the code that generates random pictures is written by hands, not some statistical model ML-trained on existing data).

I want to _grok_ it, to understand how it works, and explain it to others in some form. That' what I'll be doing since December 1, 2021 (which is today when I am writing this line).

## Why

The answer to _why_ would I want to spend my time on this (obviously impractical) project of mine is related to the concept of _engineer intuition_ in two different ways:

**First** I don't know a thing about procedural generation of humanely-looking art; can't even imagine how it might work. While I have no illusions about becoming proficient (I believe one should be a visual artist themself to work with generative art, and it is definitely not my media, I am very textual person), I'd like to _develop some intuitions_ on how those things work.

**Second**, I'd like to _battle-test my intuitions_ about ways of expressing things in programming languages, what should look declarative, how to make the intention obvios, this kind of stuff. Textual person, as I've said! And programming language is first and foremost a language for me. I _hope_ that my _intuition_ (here again!) about the algorithms for this art object being expressable in functional/declarative manner would be proven close to reality. If they would be not, well... at least I will have an incentive to review them.

## How

For me, the best way to understand something complex is to _translate_ it. This work both for natural language (at various points in time, I did a fair share of amateur translations of programming books, philosophical articles, body-mind essays and poetry between English, Ukrainian, Belarussian and Russian), and for software. I did translated some graphic algos from Wolfram Language into Ruby ([1](https://github.com/zverok/xkcdize), [2](https://github.com/zverok/drosterize)); APLs famous Game of Life [into Ruby, again](https://zverok.github.io/blog/2020-05-16-ruby-as-apl.html). And recently, I spent almost a year porting popular spellchecker into Python (and another half-a-year for a [writeup](https://zverok.github.io/spellchecker.html)).

So, I thought that I'll just look into the [code](https://github.com/LingDong-/shan-shui-inf/blob/master/index.html) and... port it to some other language I am more comfortable with, maybe? Yet, from the first half-assed attempt to do it in a language I am most comfortable with (Ruby), I understood I'll probably spend more time trying to fight fighting with image libraries, converting coordinates, waiting in "change code-render-look at image" loop, and still wouldn't be sure "why it looks wrong".

So, I thought: why not try to translate from JS... to JS? I mean, the most modern JS/ES have most of the features I'll need for representing algorithms the way I am used to: classes, `map`/`filter`/`reduce`, short notation for small functions, named arguments. And this way, I can make gradual changes to the original code, refresh the browser page, and immediately have a feedback of "whether I broke something". Nice.

With that, let's start. I plan to spend 30-60 minutes every day of Advent for experiments and notes; then (**if** something interesting will come up, and if I wouldn't give up much earlier), I'll tidy and publish it all.

- Dec 01, 2021, 22:42
