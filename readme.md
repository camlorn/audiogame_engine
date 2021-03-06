#Audiogame_engine#

Audiogame_engine provides input and windowing functionality for Audiogame developers through the Python programming language, Sdl, and Pysdl2.  While graphics is not currently supported, adding such is trivial and I will welcome such patches.  This package is aimed at intermediate programmers.  Report issues through Github, and feel free to fork and submit pull requests.

Note: due to the difficulty of compiling and setting up SDL, a compiled dll is part of this package.  If you are on Linux or Mac, you will need to replace it with a version of SDL compiled for your platform, and possibly update __init__.py to point to it.  The precompiled version which was published as of this release did not include Alt+F4 support; getting Alt+F4 to work requires building SDL yourself so I went ahead and did it for you.

Unlike other approaches, Audiogame_engine functions via telling you about things.  The code is well-documented.  Start with main_loop.py, screen_stack.py, and screen.py.  Almost everything has a python docstring telling you what it's for and how to use it. The code is also simple: someone who understands why this package might be attractive should be able to understand exactly how it works.

Typical usage involves overriding tick in a subclass of Screen, adding it to a ScreenStack, and initializing and starting a MainLoop with said ScreenStack.  Keyboard and mouse (save for mouse movement) is handled through KeyboardHandler and MouseHandler.  Every subclass of Screen gets one of these.  When a key or mouse button is pressed and a callback is registered for it, the callback gets called.

The point of having a ScreenStack is this: input can be shadowed, overridden, etc.  The main attraction of ScreenStack is this: time advances for the top screen only.  To make a pause function, simply make a subclass of Screen, say PauseScreen, which does nothing except unpause when the user presses 'p'.  The screens below it stop ticking.  This means that all game logic appropriately stops.  Similar strategies work for any sort of menu: inventory, saving, what have you.  This even works for minigames: to create a minigame, implement it as a Screen, stop all sounds from the main game, and put the minigame at the top of the stack.  When the player wins or loses, pop it, restart your sounds, and the game continues from where it left off.

For the adventurous, the top screen may opt to allow screens below it to tick and receive input.  This is a bit complex and not commonly needed in most games.  For those working on network-based projects, however, this is important: you can't pause the game forr everyone, so things like inventory need to allow the client to keep going anyway.  You can grab any keys you want, allowing screens below them to process everything else.  For example, you could allow the player to keep moving with the arrow keys while using the 'a' and 's' keys to navigate a menu, implemented in a separate screen.

The above may seem complicated to those who use the approach of putting all game logic in one file, stacking if statements 5 and 6 levels deep.  In the end, it is actually better: you can reason about only one, small part of the game at a time.  This reduces the size and complexity of code, without question.  Furthermore, this package keeps time running att a constant speed as much as it can, so the game will try to "catch up" if it falls behind. By choosing Python, you gain access to such packages as Twisted (talk to just about every network protocol you can think of), pickle (easy serialization to disk), Sqlalchemy (easy database accesss), and a great number of others.

You will need to provide your own audio.  Camlorn_audio is known to work well as long as you don't "throw away" sound objects-there are some known issues in that package which cause odd things to happen with the Python garbage collecter, but no one has managed to give me a reproduceable test case and everything I've written with it (I3d and the beginnings of the MMO) doesn't cause it.
The pygame audio mixer should also work well enough for those who need only pan and pitch modification.
For those who do not mind being completely open-source and GPL, I fully recommend [Pyo](http://ajaxsoundstudio.com/software/pyo/): Pyo is crazy awesome once you learn it, supports every effect under the sun, but you won't be able to go commercial and, as per the GPL, need to publish your sourcecode.  If you couple it with [Headspace](https://github.com/crabl/HeadSpace), you get limited 3D HRTF support.  The downside to Pyo and Headspace is that both are actually quite slow: Pyo does everything needed for a game but is actually aimed at synthesis research, so it sacrifices speed for flexibility, and headspace is performing a very, very expensive algorithm on top of it.
The upshot is that getting 3D audio and high quality reverb for all your sounds is hard, but getting plane old panning for all your sounds isn't a big deal.  I am working on a successor that combines the best of all of these packages, hopefully without the problems, but this is still a ways from completion.

This package has been used in two projects, neither of which is currently released.  The first is i3d, a 3D space invaders using Camlorn_audio.  The second is my as-yet-unnamed attempt to create an online MMO, which you may read about on my [blog.](http://camlorn.net/index.html)

For those looking to do networking, this package's main loop features the required places to hook a Twisted _threadedselect, [here](http://twistedmatrix.com/documents/current/api/twisted.internet._threadedselect.html).  It is also capable of being monkeypatched by Gevent safely.

##Installation##

Steps:

- Clone this repository somewhere.  Anywhere will do.

- Instal Pip if you don't have it.  If you are going to be spending more than 5 minutes with Python, you want Pip: it can download packages and their requirements for you with commands like pip install foo.  A guide is [here](http://pip.readthedocs.org/en/latest/installing.html).

- Switch to the directory of this repository, and execute the following two commands: pip install -r requirements.txt and python setup.py install.  The first grabs all dependencies and instals them; the second installs this package.  If both succeed, you're done.
