# Day 21: Now what?

* **Commit:** [32726a6](https://github.com/zverok/grok-shan-shui/commit/32726a6d3ea0e759e98cd2e9b7734dded10391d1)
* **Functions:**
  * [`chunkrender`](https://github.com/zverok/grok-shan-shui/blob/main/original.html#L3973)
  * [`update`](https://github.com/zverok/grok-shan-shui/blob/main/original.html#L4017)
  * [`chunkloader`](https://github.com/zverok/grok-shan-shui/blob/main/original.html#L3845)
* **Other days about:**
  * `chunkloader`: [12](day12.md), **21**

"OK," I am thinking, looking at the state of this tedious investigation, "If we'll consider it an _advent_, after all, I have just 4 more days to spend somehow." How should I spend them?

I am considering a few options, like, maybe, just do more of the same: rewrite/understand one more tree, or one more architectural element, or that man-on-a-boat, or `flatMount` or just a `roof`? Or maybe go clean up the functions I already handled, make them at least to have similar code style and consistent usage of my "core prototype" extensions? Maybe add docs/comments, or try to adjust to consistency at least _all_ function signatures at once (long parameter names, hash deconstruction, etc.)? All of ideas has its merits, but all mostly feel "more of the same"—all in all, the project was about _trying to understand_ the intuitions behind the beautiful picture generation, and, without much self-consciousness, I might say that by this point I understand most of what happening on all levels from mountain-with-trees-and-towers to single uneven "hand-painted" line. It obviously doesn't bring me closer to having the artistic talent to design something of comparable beauty, but at least I see now how the design might be implemented.

In addition to those doubts, I also have travelling day tomorrow, and not much time on my hands today... So, I eventually set for the rough schedule:
* spend 23rd and 24th for the polishing and wrap-up of the diary that emerged through the previous days (while simultaneously writing a diary about how I do it)—making sure it is convenient to follow with cross-links, cleaner texts and overall structure
* which leaves today and tomorrow for some more code investigation, and today's one would be short and dedicated to the one layer I haven't looked into yet: the highest one which combines all of those chunks together and makes the picture infinite.

So... It seems everything interesting highest-level happens in the function `update()`, which calls `chunkloader()`/`chunkrender()` which we already saw (and somewhat cleaned up). And all of it starts with the `MEM` global state variable and its various properties like `xmax` and `cwid`. So, let's rename those, shouldn't we?

I am starting from renaming `MEM` to `STATE` and then looking at its definition:

```js
STATE = {
  canv: "",
  chunks: [],
  xmin: 0,
  xmax: 0,
  cwid: 512,
  cursx: 0,
  lasttick: 0,
  windx: 3000,
  windy: 800,
  occupied_x: null,
};
```

Now, grepping throught the code for the usages, we can clarify these names a bit:

```js
STATE = {
  // those are actually immutable settings:

  chunkWidth: 512,
  windowWidth: 3000,
  windowHeight: 800,

  // that's really state (of the drawing)

  canvas: "",
  chunks: [],

  xmin: 0, // minimum x current STATE.canvasas is drawn starting from (can be negative)
  xmax: 0, // maximum x current STATE.canvasas is drawn to

  cursorX: 0, // start of currently visible part of the picture

  occupiedXs: null, // global list of x columns already having some mountains, initialized in mountplanner

  lasttick: 0, // debug
};
```

One more cleanup/splitting:
```js
CONFIG = {
  chunkWidth: 512,
  windowWidth: 3000,
  windowHeight: 800,
}

STATE = {
  canvas: "",
  chunks: [],

  xmin: 0, // minimum x current STATE.canvasas is drawn starting from (can be negative)
  xmax: 0, // maximum x current STATE.canvasas is drawn to

  cursorX: 0, // start of currently visible part of the picture

  occupiedXs: null, // global list of x columns already having some mountains, initialized in mountplanner
};
```

Now, looking at how variables are used, I notice that `STATE.canvas` is not actually a global state.
It is reset in each call of the `chunkrender`:

```js
function chunkrender(xmin, xmax) {
  STATE.canvas = "";

  for (var i = 0; i < STATE.chunks.length; i++) {
    if (
      xmin - CONFIG.chunkWidth < STATE.chunks[i].x &&
      STATE.chunks[i].x < xmax + CONFIG.chunkWidth
    ) {
      STATE.canvas += STATE.chunks[i].canv;
    }
  }
}
```
...which is called by the `update()`, and then it uses the canvas. So, we can make it less global (and also simplify the `chunkrender`):

```js
function chunkrender(xmin, xmax) {
  return STATE.chunks
         .filter( chunk => xmin - CONFIG.chunkWidth < chunk.x && chunk.x < xmax + CONFIG.chunkWidth )
         .map( chunk => chunk.canv )
         .join()
}
```

...and now we have a bit cleaned up `update` and one pretend-global-but-not-really variable less:

```js
function update() {
  self.chunkloader(STATE.cursorX, STATE.cursorX + CONFIG.windowWidth);
  canvas = self.chunkrender(STATE.cursorX, STATE.cursorX + CONFIG.windowWidth);

  document.getElementById("BG").innerHTML =
    "<svg id='SVG' xmlns='http://www.w3.org/2000/svg' " +
      `width='${CONFIG.windowWidth}' height='${CONFIG.windowHeight}' style='mix-blend-mode:multiply;'` +
      `viewBox = '${calcViewBox()}'><g id='G' transform='translate(0, 0)'>` +
      canvas +
    "</g></svg>";
}
```

The last function to look into once more, is `chunkloader`. With final understanding of what's what, I did this to it:

```js
function chunkloader(xmin, xmax) {
  while (xmax > STATE.xmax - CONFIG.chunkWidth || xmin < STATE.xmin + CONFIG.chunkWidth) {
    var plan;
    if (xmax > STATE.xmax - CONFIG.chunkWidth) {
      plan = mountplanner(STATE.xmax, STATE.xmax + CONFIG.chunkWidth);
      STATE.xmax += CONFIG.chunkWidth;
    } else {
      plan = mountplanner(STATE.xmin - CONFIG.chunkWidth, STATE.xmin);
      STATE.xmin -= CONFIG.chunkWidth;
    }

    plan.forEach( ({tag, x, y}, i) => {
      switch(tag) {
      case "mount":
        STATE.chunks.put({tag, x, y,            canvas: Mount.mountain(x, y, rand(i * 2), {})});
        STATE.chunks.put({tag, x, y: y - 10000, canvas: water(x, y, i * 2)});
        break
      case "flatmount":
        STATE.chunks.put({tag, x, y,
          canvas: Mount.flatMount(x, y, rand(2 * Math.PI), {wid: rand(600, 1000), hei: 100, cho: rand(0.5, 0.7)}),
        });
        break
      case "distmount":
        STATE.chunks.put({tag, x, y,
           canvas: Mount.distMount(x, y, rand(100), {hei: 150, len: randChoice([500, 1000, 1500])})
         });
        break
      case "boat":
        STATE.chunks.put({tag, x, y,
          canvas: Arch.boat01(x, y, Math.random(), {sca: y / 800, fli: randChoice([true, false])})
        });
        break
      default:
        throw `weird tag: ${tag}`
      }
    })
  }
}
```

With `STATE.chunks.put` defined just under the `STATE`:
```js
STATE.chunks.put = function(newChunk) {
  if (this.length == 0 || newChunk.y >= this.last.y) { this.push(newChunk) }
  else if (newChunk.y <= this.first.y)               { this.unshift(newChunk)}
  else {
    this.eachCons(2).
         findIndex( ([chunk1, chunk2], i) => newChunk.y >= chunk1.y && newChunk.y <= chunk2.y ).
         and_then( idx => this.splice(idx + 1, 0, newChunk) )
  }
};
```

That would do the "overall structure" for me! (Even though though a temptation, say, to turn `while` into some sequential enumerator is high, I will resist it.) This would do the day's work, too. Tomorrow, the new day. One thing to see to on the "global" level is how the smoothness of the transitions is achieved when chunks are put next to each other. Should be something simple yet ingenious.
