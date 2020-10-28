# xfwm4-themes
Instructions how to make a new xfwm4 theme for xfce

## Glossary

"Windows" includes the operating system, the color scheme, the way windows behave, and more.  In Linux, a lot of those pieces are separated.

First, your **operating system** is Linux.  There are many **distributions** (aka distros) of Linux, like Ubuntu, Debian, etc, and they have different policies oh which apps are available and how to install them, etc.

When you start your computer, you might have a graphical **desktop environment** like xfce or GNOME or KDE.  That's what gives you the shape of your desktop, like having a taskbar or multiple desktops.

Inside that desktop environment, you'll have a **window manager** that takes care of the windows on your screen, which are all the rectangular things that hold xterms and your browser and other apps.  The window manager can change how focus works, like whether it follows a cursor, and can cause windows to snap to the sides, etc.  It also handes how the windows will look.  xfce4 uses xfwm4.

xfwm4 supports **themes**.  A theme describes the shape of the windows, like how thick the border is, if the close button is on the left or right, will it be an X shape or something else, etc.  Most of those themes come with a **style** which is where you can choose the color scheme.  You can use the color scheme that came with the theme by choosing the style with the same name as the theme or you can mix and match.  No problem.

To choose your **theme**, run `xfwm4-settings` and pick your favorite.  To change the **style**, type `xfce4-appearance-settings`.

## Make a new theme

It's easiest to start with an existing theme because there are a ton of files.  Pick a theme that is as close as possible to your goal and then copy it into your personal directory.  For our example, we're going to take the GTK theme and thicken up the borders.

### Make a copy of an existing theme

First, find the GTK theme.  I got it from installing it like this:

```
sudo apt-get install xfwm4-themes
```

Check that you got it: 

```
ls /usr/share/themes/Gtk/xfwm4
```

In that directory you'll see a bunch of xpm files named after different parts of the window, and the names are descriptive.  `close-active.xpm` is what your close button will look like when it's the active window.  `bottom-inactive.xpm` is what the border will look like when the window isn't active, like maybe you clicked away or the cursor isn't there.  `top-right-active.xpm` is the top right corner while active, etc.

We're going to make a copy of all that.

```
mkdir -p ~/.local/share/themes
cp -rP /usr/share/themes/Gtk ~/.local/share/themes/My-New-Theme
```

Now open the window mananger settings and find your new theme:

```
xfwm4-settings
```

It should look the same as the Gtk one, which is also in the list.  Great!  Leave that settings manager open.

### Adjust the theme

We'll start by thickening up the bottom edge of windows.  We're going to do it drastically so that it'll be very noticeable that we did something.

First, open the corresponding file.  I like to use emacs so I type this:

```
emacs ~/.local/share/themes/My-New-Theme/xfwm4/bottom-inactive.xpm
```

By default emacs may want to open it as an image so if you don't see any text, type `C-c C-c`.

You should see a file in the c programming language, that looks like this:

```
/* XPM */
static char * bottom_inactive_xpm[] = {
"5 5 5 1",
" 	c None",
".	c #FFFFFF s inactive_hilight_2",
"+	c #CCCCC7 s inactive_color_2",
"@	c #919189 s inactive_shadow_2",
"#	c #000000 s inactive_border_color",
".....",
"+++++",
"+++++",
"@@@@@",
"#####"};
```

That's an xpm3 file.  It's a string that describes a picture in ASCII art.  It's well [described on wikipedia](https://en.wikipedia.org/wiki/X_PixMap) but I'll go over it here, too.

The first 4 numbers, that's the width, height, number of colors, and characters per pixel.

Then come the colors, one per line.  In the file above, the first line says that a space will have color "None".  Then the next line says that a period (".") will be color "#FFFFFF", which is the hex code for white.  It also says that it can be the symbolic color "inactive_hilight_2".  That color will then come from the **style** that you select.

There are five rows of those, like described on that first line.

Next there are 5 rows with 5 columns each.  Because on the first line, it said 5 by 5.  Even though it's only 5x5, the window manager will stretch it out to cover the entire bottom edge of the window border.  I'm going to add 10 more rows of that inactive_color_2.  I'll also need to adjust the height of the image.  Here's what the file looks like now:

```
/* XPM */
static char * bottom_inactive_xpm[] = {
"5 15 5 1",
" 	c None",
".	c #FFFFFF s inactive_hilight_2",
"+	c #CCCCC7 s inactive_color_2",
"@	c #919189 s inactive_shadow_2",
"#	c #000000 s inactive_border_color",
".....",
"+++++",
"+++++",
"+++++",
"+++++",
"+++++",
"+++++",
"+++++",
"+++++",
"+++++",
"+++++",
"+++++",
"+++++",
"@@@@@",
"#####"};
```

Notice the new rows of plus signs.  Also notice that I adjust the first line to say 15 rows instead of 5.  Now save the file, switch away from the theme and then switch back to it.  Only the inactive windows should be affected because we only changed `bottom-inactive.xpm`.  Also notice that we didn't get 10 new rows of pixels!  That's because the inactive and active have different heights and that will confuse xfwm4.  So go ahead and modify `bottom-active.xpm`, too.  Now the bottom border should be quite thick.  You can go around and do the same for all active and inactive bottom, left, right and corners.

Notice that there is no top.  That's because it's called title and it's more complex because it has a few parts to it, like the part where the buttons are, the part where the title of the window is, etc.  Your best bet is to just adjust the xpm file, see what changes, and then try again.

Buttons are in those files, too, like close, hide, shade, maximize, minimze, etc.  You can adjust them, too.

There's a `themerc` file in there that can override aspects of the defaults.  If you mess with titles and buttons, you might find, for example, that the buttons or the text are no longer vertically centered in the titlebar.  You can fix that by adjusting the offset in `themerc`, along with some of the other settings in there.  Whatever you don't set in there will be taken from the defaults, which are at `/usr/share/xfwm4/defaults`.

## Conclusion

This was a gentle walkthrough but if you want to get all the details and probably answers that are more correct, read https://wiki.xfce.org/howto/xfwm4_theme
