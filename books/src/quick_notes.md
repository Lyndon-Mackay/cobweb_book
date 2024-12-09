# Quick notes

This section will mention notes that did not come up in the tutorial but may still be helpful for you.

## Loading Cob files

When you have a cob file that defines scenes, you must register with the app by using syntax as below
`.load("main.cob")`

### Waiting for LoadState::Done
If your UI is to appear at the start of the game then you should wait to ensure cobweb has had a chance to read it.
`.add_systems(OnEnter(LoadState::Done), build_ui)`

If you UI comes up in response to player actions then calling it using observers or events should be fine as UI would have been read by cobweb.

## Other features not covered (at least for now!)

### Cobweb has React features.
Some examples can be found [here](https://github.com/UkoeHB/bevy_cobweb_ui/tree/main/examples)

You can use broadcasts to refresh your ui, `update_on(broadcast::<MyArbitaryStruct>(),|/*bevy query*/|{});`
Call it using `commands.react().broadcast(MyArbitaryStruct)`.

There are others as well.

### Other features
- RadioButtons, hope to have an example later can also be useful for tabbing, [A good starting point](https://docs.rs/bevy_cobweb_ui/latest/bevy_cobweb_ui/sickle_ext/index.html) .
- localization there is an example in the link above, documentation [here](https://docs.rs/bevy_cobweb_ui/latest/bevy_cobweb_ui/localization/index.html).
- commands


## Loading documentation.
you can find more details about cob files [here](https://docs.rs/bevy_cobweb_ui/latest/bevy_cobweb_ui/loading/index.html)
