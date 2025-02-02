![Kaboom Logo](misc/kaboom.png)

Kaboom.js is a JavaScript library that helps you make games fast and fun!

Check out our official [website](https://kaboomjs.com/)!

**NOTE**: Kaboom is still in early active development, expect breaking changes and lots of new features. This README talks about the latest beta version (`kaboom@next`)

## Examples

```html
<script src="https://unpkg.com/kaboom@next/dist/kaboom.js"></script>
<script type="module">

// initialize kaboom context
const k = kaboom();

// add a text of size 32 at position (100, 100)
k.add([
    k.text("oh hi", 32),
    k.pos(100, 100),
]);

</script>
```

You can paste this directly into an `.html` file and start playing around!

Kaboom uses a powerful component system to compose game objects and behaviors.
To make a flappy bird movement you only need a few lines
```js
// init context
const k = kaboom();

// load a default sprite "mark"
k.loadMark();

// compose the player game object from multiple built-in components
const birdy = k.add([
    k.sprite("mark"),
    k.pos(20, 20),
    k.area(),
    k.body(),
]);

// press space to jump (jump behavior is provided by "body" component)
k.keyPress("space", () => birdy.jump());
```

It's easy to make custom components to compose your game object behaviors:
```js
// add an entity to the scene, with a list of component describing its behavior
const player = add([
    // it renders as a sprite
    sprite("mark"),
    // it has a position
    pos(100, 200),
    // it has a collider
    area(),
    // it is a physical body which will respond to physics
    body(),
    // you can easily make custom components to encapsulate reusable logics
    doubleJump(),
    health(8),
    // or give it tags for controlling group behaviors
    "player",
    "friendly",
    // plain objects fields are directly assigned to the game obj
    {
        dir: vec2(-1, 0),
        dead: false,
        speed: 240,
    },
]);

// custom components are plain functions that return an object
function health(hp) {
    return {
        // comp id
        id: "health",
        // comp dependencies
        require: [],
        // custom behaviors
        hurt(n) {
            hp -= n ?? 1;
            this.trigger("hurt");
            if (hp <= 0) {
                this.trigger("death");
            }
        },
        heal(n) {
            hp += n ?? 1;
            this.trigger("heal");
        },
        hp() {
            return hp;
        },
    };
}

// listen to custom events from a custom component
player.on("hurt", () => { ... });

// blocky imperative logic
player.collides("enemy", () => player.hurt(1));
```

Blocky imperative syntax for describing behaviors
```js
// check fall death
player.action(() => {
    if (player.pos.y >= height()) {
        destroy(player);
        gameOver();
    }
});

// if 'player' collides with any object with tag "enemy", run the callback
player.collides("enemy", () => {
    player.hp -= 1;
});

// all objects with tag "enemy" will move towards 'player' every frame
action("enemy", (e) => {
    e.move(player.pos.sub(e.pos).unit().scale(e.speed));
});

keyPress("w", () => {
    player.move(vec2(0, 100)),
});
```

If you don't feel like using kaboom's abstraction systems, can use kaboom like a p5.js or love2d with immediate mode APIs

```js
const k = kaboom();

// runs every frame
k.action(() => {
    // checks if is pressed last frame only
    if (k.keyIsPressed("space")) {
        doSomeThing();
    }
});

// runs every frame after update
k.render(() => {
    // immediate drawing functions
    k.drawSprite("birdy");
    k.drawText("123abc");
    k.drawRect(100, 300);
});
```

## Usage

### Browser CDN

Beta releases (will be published frequently, more prune to break)
```html
<script src="https://unpkg.com/kaboom@next/dist/kaboom.js"></script>
```

Latest release
```html
<script src="https://unpkg.com/kaboom@latest/dist/kaboom.js"></script>
```

Also works with other CDNs like `jsdelivr` etc.

When imported in the browser, the script will expose a global `kaboom` function to initialize a kaboom context, returning an object containing all the functions

```js
const k = kaboom();

k.add();
k.keyPress(...);
k.scene(...);
```

You can also import all functions to the global namespace by giving a `global` flag

```js
kaboom({
    global: true,
});

add();
keyPress(...);
scene(...);
```

Kaboom also provide ES module and commonJS module exports with `.mjs` and `.cjs`, e.g,

```js
import kaboom from "https://unpkg.com/kaboom@next/dist/kaboom.mjs";
```

### NPM Package

```
$ npm install kaboom@next
```

```ts
// main.ts
import kaboom, { Vec2, GameObj, } from "kaboom";

const k = kaboom();

function spawnBullet(p: Vec2): GameObj {
    return k.add([
        k.pos(p),
        k.sprite("bullet"),
    ]);
}
```

also works with cjs

```js
const kaboom = require("kaboom");
```

## Dev

1. `npm run dev` to watch & build lib
1. go to http://localhost:8000/examples
1. edit examples in `examples/` to test
1. make sure not to break any existing examples

## Community

[Github Discussions](https://github.com/replit/kaboom/discussions)

### Misc

- Find bitmap fonts: [Oldschool PC Font](https://int10h.org/oldschool-pc-fonts)
- Featured on [Console 50](https://console.substack.com/p/console-50)
- Shoutout to [Umayr](https://github.com/umayr) for kindly offering the "kaboom" npm package name
