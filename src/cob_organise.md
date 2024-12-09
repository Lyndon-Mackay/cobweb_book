# Cob file organisation

## defs

Cob files can have variables so you can define common values, that can be used all over your cob files.
This will allow you to make changes in one place to impact multiple nodes.

Lets do an example.

```
//defs is another type of section so far we have only been doing scenes
#defs
$text_colour = Hsla{hue:45 saturation:1.0 lightness:0.5 alpha:1.0}

#scenes
"main_scene"
    MainInterface
    AbsoluteNode{left:40%,flex_direction:Column}
    "cell"
        Animated<BackgroundColor>{
            idle:#FF0000 // You can input colours in other formats
            hover:Hsla{ hue:120 saturation:1.0 lightness:0.50 alpha:1.0 }
            press:Hsla{ hue:240 saturation:1.0 lightness:0.50 alpha:1.0 }
        }
        NodeShadow{color:#FF0000 spread_radius:10px blur_radius:5px }
        "text"
            TextLine{text:"Hello, World!, I am writing using cobweb "  } // change the textline here


"number_text"
    "cell"
        "text"
            TextLine{text:"placeholder"}
            TextLineColor($text_colour) //still can change at runtime
            Interactive //Loads listeners for user interaction


"exit_button"
    TextLine{text:"Exit"}
    TextLineColor($text_colour) //still can change at runtime
    Interactive //Loads listeners for user interaction
"despawn_button"
    TextLine{text:"Despawn"}
    TextLineColor($text_colour) //still can change at runtime
    Interactive //Loads listeners for user interaction
"respawn_button"
    TextLine{text:"Respawn"}
    TextLineColor($text_colour) //still can change at runtime
    Interactive //Loads listeners for user interaction
```

## manifests and imports

You can further extend this over many files using `manifest and imports`
If no scenes are loaded from the imported file then you don't need to load in rust

Make a central manifest file.

`assets/manifest.cob`
```
#manifest
"ui/colour_scheme.cob" as cs
```

In any file where you want to import defs you can do so as below.
```
#import
cs as colours
```

which can be referred using scoping and the `$` character

```
BackgroundColor($colours::background_colour)
```


