# Breakout Clone
Bevy comes with it a series of [helpful examples](https://github.com/bevyengine/bevy/blob/latest/examples). One of these is a [breakout clone](https://github.com/bevyengine/bevy/blob/latest/examples/game/breakout.rs), but the problem is - I need to break it down into chunks for me to learn. This is an attempt to do this in a tutorial style.

# Assumptions
This assumes some knowledge with Rust, and won't go into the language itself, but only with Bevy and creating a Breakout-style game

# Start the Project
First, bootstrap a new project (`cargo new breakout`) add to your `Cargo.toml` in the main directory, the following line:

```toml
[dependencies]
bevy = "0.5.0"
```
and run
```bash
cargo run
```
to download and run. See [here](https://bevyengine.org/learn/book/getting-started/setup/) for more information on fast compilation, which makes your life much easier.

# Starting the app
This is the obligatory "hello, world" section of this document. Without the "hello, world". Open `src/main.rs` and add to the top of the file
```rust
use bevy::prelude::*;
```
and replace the main function with 
```rust
fn main() {
    App::build().add_plugins(DefaultPlugins).run();
}
```
and you have a Bevy Application! Of course, it doesn't do anything but registers all the good stuff from Bevy (via `DefaultPlugins` being registered). 

# What is Breakout?
If you don't know what [Breakout](https://en.wikipedia.org/wiki/Breakout_(video_game)) is, it consists of a paddle at the bottom whacking a ball upwards into a series of blocks. When they collide, the block dissapears and points are added. Simple, right?

There are a whole bunch of components for us to model:
- The `Scoreboard` -> keeping track of how many blocks we've pwned
- The `Ball`
- The `Paddle` -> the object you will control
  
Bevy uses `ECS` or `Entity Component Systems`, as well as `Resources` to efficiently managing all the business of running a game. To be honest, these are concepts I'm still grokking, but hopefully through their use we will learn more about them.

# Intial _things_
As mentioned above, we know we're going to need `Scoreboard`, `Ball` and `Paddle` _things_, which are usually plain 'ol Rust structs:

```rust
struct Scoreboard {
    score: usize,
}

struct Ball {
    velocity: Vec3,
}

struct Paddle {
    speed: f32,
}
```

# Setup
Passing off logic and do-ey stuff to `systems` (the `S` in `ECS`) is the name of the game. Let's start with a `setup()` system where we'll create a camera, and initialise the entities above.

```rust
fn setup() {
    mut commands: Commands,
    mut materials: ResMut<Assets<ColorMaterial>>,
    asset_server: Res<AssetServer>,
} {

}
```
To create things in Bevy, we issue commands using the commands object. When we initialise the system (which we will shortly), the arguments we specify in the function will be filled automatically with Bevy. Here, we have `commands`, as well as two resources `Res` for immutable and `ResMut` for mutable.

So this function is saying to Bevy "give me commands, I want the assets (materials) and I'm going to change it, and I want to read the asset server as well".

Now, let's add the paddle, add a (2d) camera, a UI camera:

```rust
fn setup() {
    mut commands: Commands,
    mut materials: ResMut<Assets<ColorMaterial>>,
    asset_server: Res<AssetServer>,
} {
    // Add the game's entities to our world

    // cameras
    commands.spawn_bundle(OrthographicCameraBundle::new_2d());
    commands.spawn_bundle(UiCameraBundle::default());
    // paddle
    commands
        .spawn_bundle(SpriteBundle {
            material: materials.add(Color::rgb(0.5,0.5,1.0).into()),
            transform: Transform::from_xyz(0.0, -215.0, 0.0),
            sprite: Sprite::new(Vec2::new(120.0, 30.0)),
            ..Default::default()
        });
}
```
and replace the contents of `main`:

```rust
    App::build()
        .add_plugins(DefaultPlugins)
        .add_startup_system(setup.system())
        .run();
```
and give it a run. And you get this awesomeness:
![A window with a paddle, oh my](images/screenshot1.png)
Yay, we did that!