# TeX on a Chromebook
## a brief how-to guide

For the past eight or so years my day-to-day laptop usage has consisted of
reading papers (`.pdf`), browsing the web (`chrome`), and writing LaTeX or the 
occasional bit of code (`vim`). So when someone at a conference remarked to me, 
offhand, that a Chromebook running Linux could do the same, I thought I'd try it 
out. My 2012 Macbook was five years old at the time and a $200 replacement 
seemed a lot more attractive than a new Macbook Pro, especially for a broke 
grad student.  Something like six or seven times more attractive, actually.
So the next trip to Costco I picked up the 15" Acer Chromebook 14 (cb3-431) ---
4GB RAM, 32GB hard drive --- for $220 and change. At the time it was a bit of a 
gamble, but if I couldn't get my workflow set up through crouton or Gallium or 
what have you, there was always Costco's generous return policy.

Fast forward two years to now: if my little Acer mysteriously broke down today 
I'd be perfectly happy with the value I've gotten out of it. In fact, I've barely
touched my Macbook since (it's on my desk in the math department... maybe?).
The purpose of this note is to describe how easy it is to get up and running on 
a Chromebook if your workflow is at all similar to mine, especially with the 
recent release of [Crostini][1]. The features that were important to me are:

+ lightweight, with full-day battery life (3.5lbs, about 8 hours after 2 years 
  of use)
+ TeX editing and compiling (`vim`, `tmux`, `latexmk`, with `zathura` for viewing)
+ a reasonably snappy chrome
+ the ability to print to the department printers (I used [this][2] on 
  `crouton`)
+ access removable USB drives

All in all, not too demanding, but nonetheless impressive in a machine that cost 
less than my desktop's GPU (although in hindsight I maybe didn't need a 15" 
monitor --- 13" would have been just fine).

Okay, let's get to the setup. And at the end, in case you're interested in 
liveTeXing (taking lecture notes in TeX rather than by hand) I've added some 
words about how it's not as difficult as it sounds.


### setting up Crostini

In this note we'll get Linux running on a Chromebook via [Crostini][1]. Crostini 
is Google's catch-all name
> for making Linux application support easy to use and integrating well with 
> Chrome OS. It largely focuses on getting you a Terminal with a container with 
> easy access to install whatever developer-focused tools you might want.
I'm not really a computers person, so I'll just use Crostini to refer to the 
technology that will get a Linux terminal (along with graphical applications 
such as TeX editors or pdf viewers) up and running on a Chromebook.

Right off the bat, the first thing to do is to make sure that Crostini is 
supported on your Chromebook. This depends on the hardware your machine has, so 
if you've got an ancient early-iteration Chromebook, you may be out of luck. 
In that case, you may want to look into [crouton][5] or [Gallium][6] (I don't know 
anything about Gallium, but I would be remiss not to mention that crouton 
bypasses some of ChromeOS' strong security features).
First, find your machine on this [list][3] and check that its board name is in 
the list of [supported boards][4]. My `cb3-431`, for instance, is named `edgar`, 
which has supported Crostini since R70.

[1]: https://chromium.googlesource.com/chromiumos/docs/+/master/containers_and_vms.md
[2]: https://chrome.google.com/webstore/detail/ipp-cups-printing-for-chr/lkhfeoafdgbaecajkdbioenncjopbpmk
[3]: http://dev.chromium.org/chromium-os/developer-information-for-chrome-os-devices
[4]: https://chromium.googlesource.com/chromiumos/docs/+/master/containers_and_vms.md#Supported-Now
[5]: https://github.com/dnschneid/crouton
[6]: https://galliumos.org/

Once you've checked that (and gotten your hands on a machine), just make sure your
ChromeOS is up to date, and then start the `Terminal` application from your 
launcher. This should pop up a dialog box about installing Linux (beta). Click your
way through that, and soon enough you should be at a terminal! That's it! You 
now have a Linux machine running securely in your Chromebook that supports (as of
R75, at least) seamless integration with graphical applications (at least those 
using `Wayland` or `X`). Let's check this out.

First, let ask what distribution of Linux we're running:
```
    cat /etc/os-release
```
At the time of writing, Crostini ships with Debian 9, codename Stretch:
```
    PRETTY_NAME="Debian GNU/Linux 9 (stretch)"
    NAME="Debian GNU/Linux"
    VERSION_ID="9"
    VERSION="9 (stretch)"
    ID=debian
    HOME_URL="https://www.debian.org/"
    SUPPORT_URL="https://www.debian.org/support"
    BUG_REPORT_URL="https://bugs.debian.org/"
```
Next let's get the software that ships with it up to date:
```
    sudo apt update
    sudo apt upgrade
```
After that, let's double check that we can use graphical applications, using the 
classic `xclock`.
```
    sudo apt install x11-apps
    xclock
```
And that's about it. Now let's look at how to get TeX set up.


### setting up TeX

The standard TeX distribution on Debian is `texlive`. There are three different 
packages you might install, depending on how much space you've got on your 
machine. They go as follows:

+ `texlive-base`, 200 MB
+ `texlive`, 400 MB
+ `texlive-full`, 4 GB

You can find out what's included, at least on Debian stretch, [here][7].
These sizes are estimates based on memory, but don't take my word on it.
Running
```
    echo 'n' | sudo apt install texlive-full
```
will tell you (at the bottom) how much space will be used. My Chromebook's hard 
drive can't afford 4 gigs, so I just installed `texlive`. From there you can 
install whatever extra packages you might need via the TeXLive package manager
`tlmgr` (it may take a bit of googling to get `tlmgr` running for the first 
time, due to some strange confusions about relative paths).
If you use `latexmk` to compile your LaTeX, though, you can also install it
via
```
    sudo apt install latexmk
```

Let's check that things are working okay. Open up your favorite text editor (if 
you're not super comfortable with command line editors, you can install 
texmaker, `sudo apt install texmaker`) and create a minimal working example
```TeX
    \documentclass{minimal}
    \begin{document}
        Hello, world!
    \end{document}
```
and compile
```
    pdflatex mwe.tex
```
Assuming you did this in the home directory, you can open up the pdf in Chrome
by opening up the Chrome Files app and opening up the `Linux files` folder under
`My files` in the sidebar. If all went well you should now be able to edit and 
compile TeX!

### odds & ends

You'll probably want to use a pdf viewer that dynamically updates as you 
recompile, especially if you're using something like `latexmk`. I've been using 
`zathura`, which is lightweight, customizable, and uses `vim` keybindings. You 
can google around for other options, but I'll stick to `zathura` for now.
Installation is as usual,
```
    sudo apt install zathura
```
and you should be able to open up the example
```
    zathura mwe.pdf
```
If you change the document and recompile, `zathura` should reload the pdf for 
you automatically; give it a try. One neat thing about `zathura` is that it's 
super customizable.
I'm not much of an expert but I like to run it in a night-mode color scheme,
which makes 
the switch from terminal to pdf viewer much less jarring. You can google around 
for themes and more to put in your `~/.config/zathura/zathurarc`. One thing to 
note is that is that, at the time of writing, two-finger scrolling was broken in 
unconfigured `zathura`. The problem is documented [here][8] and the solution is 
easy: add the line
```
    set smooth-scroll true
```
to your `zathurarc`.

Let me end with some miscellaneous tips.
The first is about removable USB storage devices.  If you
plug in a storage device, in order for Linux to access it you'll have to 
right-click the device in the Chrome Files app and hit `Share with Linux`. Now 
you should be able to access your storage device:
```
    cd /mnt/chromeos/removable/YOUR_USB_DEVICE/
```
The next tip is that, depending on when in the future you hail from, the 
default Terminal app that is set up for Crostini may not be as fully-featured
or responsive as you'd like. Indeed, after installing a few plugins for `vim` 
(I'll talk about some of these below) scrolling slowed down a bit. This might 
not be an issue for you if you're using a Chromebook with a reasonable 
processor, but I ended up installing gnome-terminal and using that instead. I 
find it to be a bit quicker, though for some reason it doesn't automatically 
create an icon in the Chrome launcher. This has to be done [manually][9].
There's a long list of terminals that people like to use and a lot of them work 
well in Crostini, so feel free to go down that rabbit hole.

That's about all I have to say about Crostini, I think.

Oh actually, one warning 
for those who use `node`: the package manager for Debian stretch is, for some 
mysterious reason, on version 4.x, which is *really* old (in fact I think it's 
already been EOL'd). So you'll have to go about installing it another way. If 
you're Linux-savvy you're probably already familiar with the fallibility of 
package managers, but it was a first for me. I'll be a bit more careful with 
checking version numbers from now on...


[7]: https://packages.debian.org/source/stretch/texlive-base
[8]: https://git.pwmt.org/pwmt/zathura/issues/48
[9]: https://old.reddit.com/r/Crostini/wiki/add-icon-for-gnome-terminal


## my liveTeX setup










