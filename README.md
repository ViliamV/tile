# i3bindings
[![forthebadge](https://forthebadge.com/images/badges/made-with-python.svg)](https://forthebadge.com)
[![forthebadge](https://forthebadge.com/images/badges/no-ragrets.svg)](https://forthebadge.com)

Succinct syntax for i3 config

## Installation
`pip install i3bindings`

## Usage
1. Create *input_file* using [Concepts](#Concepts) bellow.
2. Try it with `i3bindings input_file`
3. Write it to i3 config using `i3bindings --write input_file`

Check out `i3bindings --help` for more options.

## Concepts
### Mapping types
*i3bindings* supports two types of mappings:
1. Key binding, where `$mod+f -> fullscreen toggle` becomes `bindsym $mod+f fullscreen toggle`
1. Exec binding, where `$mod+Return #> xterm` becomes `bindsym $mod+Return exec --no-startup-id xterm`

### Alternatives
*Alternatives* express the idea that the same action should be bound to multiple key-bindings.

For example:

`$mod+h/Left -> focus left` will create two lines of config:

`bindsym $mod+h focus left` and `bindsym $mod+Left focues left`

### Variables
*Variables* are shorter way to write bindings that have a similar structure.

E.g.:
`$mod+{f,s} -> {fullscreen,split} toggle` will create:

`bindsym $mod+f fullscreen toggle`
`bindsym $mod+s split toggle`

#### Range expansion
Inside variables you can write `1-10` and it will be expanded to `1,2,3...`.

#### Variable reference
You can reference current value of the variable using `@n` syntax, where `n` is the variable index.
Every variable is numbered (starting from 0) from left to right. E.g.:
```
foo {bar,{foo,bar}} {0-1} ...
    ^    ^          ^
    0    1          2
```

Use it like this:
`$mod+Control+Shift {1-9,0} -> move container to workspace {1-10}; workspace @1`

Or to avoid repeating long sequences:

`$mod+x -> {[instance=\"calculator\"]} scratchpad show; @0 move position center`

#### Empty value
You can use empty value inside *Variable*, denoted by `_`. E.g. `$mod+{_,Shift+}{h/Left,j/Down,k/Up,l/Right} -> {focus,move} {left,down,up,right}`.
This will expand to both `bindsym $mod+h focus left` and `bindsym $mod+Shift+h move left`

### Nesting
You can use *Alternatives* and *Variables* inside *Variable* to create many bindings at once.


## Additional syntax
### Parenthesis
By default, special characters like space, plus sign, etc. are *token* separators.
`$mod+p/Print` is therefore expanded to `$mod+p` and `$mod+Print`.
You can use parenthesis to modify the behavior:
`($mod+p/Print) #> scrot` will create two lines of config:
`bindsym $mod+p exec --no-startup-id scrot` and `bindsym Print exec --no-startup-id scrot`

### Comments
Empty lines or lines starting with `#` will be ignored.

## Example
To demonstrate the syntax have a look at this input file:
```config
    $mod+{_,Shift+}{h/Left,j/Down,k/Up,l/Right}         -> {focus,move} {left,down,up,right}
    $mod+Control+Shift+{{h/Left,k/Up},{l/Right,j/Down}} -> resize {shrink,grow} {width,height} 5px or 5ppt
    $mod+{_,Shift+}{1-9,0}                              -> {_,move container to }workspace {1-10}
    $mod+Control+Shift {1-9,0}                          -> move container to workspace {1-10}; workspace @1
    # vim:filetype=config
```
The expanded version creates this i3 config:
```i3
    bindsym $mod+h focus left
    bindsym $mod+Left focus left
    bindsym $mod+j focus down
    bindsym $mod+Down focus down
    bindsym $mod+k focus up
    bindsym $mod+Up focus up
    bindsym $mod+l focus right
    bindsym $mod+Right focus right
    bindsym $mod+Shift+h move left
    bindsym $mod+Shift+Left move left
    bindsym $mod+Shift+j move down
    bindsym $mod+Shift+Down move down
    bindsym $mod+Shift+k move up
    bindsym $mod+Shift+Up move up
    bindsym $mod+Shift+l move right
    bindsym $mod+Shift+Right move right
    bindsym $mod+Control+Shift+h resize shrink width 5px or 5ppt
    bindsym $mod+Control+Shift+Left resize shrink width 5px or 5ppt
    bindsym $mod+Control+Shift+k resize shrink height 5px or 5ppt
    bindsym $mod+Control+Shift+Up resize shrink height 5px or 5ppt
    bindsym $mod+Control+Shift+l resize grow width 5px or 5ppt
    bindsym $mod+Control+Shift+Right resize grow width 5px or 5ppt
    bindsym $mod+Control+Shift+j resize grow height 5px or 5ppt
    bindsym $mod+Control+Shift+Down resize grow height 5px or 5ppt
    bindsym $mod+1 workspace 1
    bindsym $mod+2 workspace 2
    bindsym $mod+3 workspace 3
    bindsym $mod+4 workspace 4
    bindsym $mod+5 workspace 5
    bindsym $mod+6 workspace 6
    bindsym $mod+7 workspace 7
    bindsym $mod+8 workspace 8
    bindsym $mod+9 workspace 9
    bindsym $mod+0 workspace 10
    bindsym $mod+Shift+1 move container to workspace 1
    bindsym $mod+Shift+2 move container to workspace 2
    bindsym $mod+Shift+3 move container to workspace 3
    bindsym $mod+Shift+4 move container to workspace 4
    bindsym $mod+Shift+5 move container to workspace 5
    bindsym $mod+Shift+6 move container to workspace 6
    bindsym $mod+Shift+7 move container to workspace 7
    bindsym $mod+Shift+8 move container to workspace 8
    bindsym $mod+Shift+9 move container to workspace 9
    bindsym $mod+Shift+0 move container to workspace 10
    bindsym $mod+Control+Shift 1 move container to workspace 1; workspace 1
    bindsym $mod+Control+Shift 2 move container to workspace 2; workspace 2
    bindsym $mod+Control+Shift 3 move container to workspace 3; workspace 3
    bindsym $mod+Control+Shift 4 move container to workspace 4; workspace 4
    bindsym $mod+Control+Shift 5 move container to workspace 5; workspace 5
    bindsym $mod+Control+Shift 6 move container to workspace 6; workspace 6
    bindsym $mod+Control+Shift 7 move container to workspace 7; workspace 7
    bindsym $mod+Control+Shift 8 move container to workspace 8; workspace 8
    bindsym $mod+Control+Shift 9 move container to workspace 9; workspace 9
    bindsym $mod+Control+Shift 0 move container to workspace 10; workspace 10
```

## Background
[i3](https://i3wm.org/) is a great windows manager but its config is pretty verbose.
I tried [bspwm](https://github.com/baskerville/bspwm) with its hotkey daemon [sxhkd](https://github.com/baskerville/sxhkd) and I much prefer that syntax.
That's why I wrote *i3bindings*.
It uses syntax similar to `sxhkd` for expansion of binding parameters, e.g. `$mod+{1-9,0} -> workspace {1-10}` will become `bindsym $mod+1 workspace 1`, `bindsym $mod+2 workspace 2`, and so on.
In addition, it has few more features.

