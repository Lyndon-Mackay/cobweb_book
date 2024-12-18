# Using rust to update text

We will be using rust to modify the contents of a text node at runtime (no hot reloading).

## Modify the cob file

Let's setup the cob file as below.

```rust
"scene"
    AbsoluteNode{left:40%}
    "cell"
        Animated<BackgroundColor>{
            idle:#FF0000 // You can input colours in other formats
            hover:Hsla{ hue:120 saturation:1.0 lightness:0.50 alpha:1.0 }
            press:Hsla{ hue:240 saturation:1.0 lightness:0.50 alpha:1.0 }
        }
        NodeShadow{color:#FF0000 spread_radius:10px blur_radius:5px}
        "text"
            TextLine{text:"Hello, World!, I am writing using cobweb "} // <-- will be overwritten

```
We split position logic into a child node called `cell` which holds most of the positioning and styling logic.
`cell` has a child called `text`. Text is a minimal node responsible for just text stuff.

### Why we split text from styling
This is more of an html/CSS pattern then anything particular with cobweb but it is worth mentioning here.

It just turns out to be easier to position nodes than it is to position text.


## Rust

### Updating text at runtime

Let's change the rust code to be as below.

```rs
fn build_ui(mut c: Commands, mut s: ResMut<SceneLoader>) {
    c.spawn(Camera2d);
    c.ui_root()
        .load_scene_and_edit(("main.cob", "main_scene"), &mut s, |loaded_scene| {
            loaded_scene
                .get("cell::text")
                .update_text("My runtime text");
        });
}
```

We have changed `load_scene` to be `load_scene_and_edit`.

When we load `"main_scene"` in the cob file, we automatically load all the child nodes recursively. The second argument is a closure where we can use `loaded_scene` similar to commands along with extension methods provided by cobweb.

Inside the closure we call `get(cell::text)` which is basically a path syntax to go straight to the text node. It also possible to call `edit` on `"cell"` then call `update_text` inside the resulting closure.

Recompile and run the program. You will see your text has changed to reflect the rust code.

### Spawning new nodes

Cobweb can also spawn new scenes inside other scenes. Let's start with an example.

Below we have our new scene called `number_text`.

If the concept of scenes was a bit confusing before, this should clarify it a bit more.

```rust
#scenes
"scene"
    AbsoluteNode{left:40% flex_direction:Column}
    "cell"
        Animated<BackgroundColor>{
            idle:#FF0000 // You can input colours in other formats
            hover:Hsla{ hue:120 saturation:1.0 lightness:0.50 alpha:1.0 }
            press:Hsla{ hue:240 saturation:1.0 lightness:0.50 alpha:1.0 }
        }
        NodeShadow{color:#FF0000 spread_radius:10px blur_radius:5px}
        "text"
            TextLine{text:"Hello, World!, I am writing using cobweb "}


"number_text"
    "cell"
        "text"
            TextLine{text:"placeholder"}
```

Now let's change our rust code to spawn some scenes.

```rs
fn build_ui(mut c: Commands, mut s: ResMut<SceneLoader>) {
    c.spawn(Camera2d);
    c.ui_root()
        .load_scene_and_edit(("main.cob", "main_scene"), &mut s, |loaded_scene| {
            loaded_scene
                .get("cell::text")
                .update_text("My runtime text");

            // Spawning new ui nodes inside our main scene
            for i in (0..=10).into_iter() {
                loaded_scene.load_scene_and_edit(("main.cob", "number_text"), |loaded_scene| {
                    loaded_scene.get("cell::text").update_text(i.to_string());
                });
            }
        });
}
```

We now have some numbers that appear based on your code. We can still modify the cob files and change styling:

```rust
"number_text"
    "cell"
        "text"
            TextLine{text:"placeholder"}
            TextLineColor(Hsla{hue:45 saturation:1.0 lightness:0.5 alpha:1.0}) // <-- add this
```

#### Making nodes interactive

Setting our UI to react to the user is essential, and easy. First we need to add the `Interactive` loadable.

```rust
"number_text"
    "cell"
        "text"
            TextLine{text:"placeholder"}
            TextLineColor(Hsla{hue:45 saturation:1.0 lightness:0.5 alpha:1.0})
            Interactive // Sets up the node for user interaction

```

Now we can use `on_pressed`:

```rs
fn build_ui(mut c: Commands, mut s: ResMut<SceneLoader>) {
    c.spawn(Camera2d);
    c.ui_root()
        .load_scene_and_edit(("main.cob", "main_scene"), &mut s, |loaded_scene| {
            loaded_scene
                .get("cell::text")
                .update_text("My runtime text");

            for i in (0..=10).into_iter() {
                loaded_scene.load_scene_and_edit(("main.cob", "number_text"), |loaded_scene| {
                    loaded_scene.edit("cell::text", |loaded_scene| {
                        loaded_scene.update_text(i.to_string());
                        loaded_scene.on_pressed(move|/* We can write arbitrary bevy parameters here*/|{
                            println!("You clicked {}", i);
                        });
                    });
                });
            }
        });
}
 ```
