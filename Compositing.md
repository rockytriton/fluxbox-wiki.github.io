[[Category:English howtos]]
[[Category:Misc howtos]]
[[Category:English howtos]]

= xcompmgr / Compton and others =
Alright people. Here's the HOWTO/tutorial/guide in making xcompmgr work. Please note that xcompmgr it has a discontinued development and it is known to have memory leak issues, consider to try something else like its fork [https://github.com/chjj/compton Compton] ([https://wiki.archlinux.org/index.php/Compton Arch wiki]) or [http://cairo-compmgr.tuxfamily.org/ Cairo Composite Manager]. 

'''WARNING: These steps render true transparency with your X engine but can REALLY slow down your computer. Remember that this is nothing but eyecandy.'''


[[Image:Note.png|21px|left|]] [http://forchheimer.se/transset-df/ transset-df] is not longer required since fluxbox has its native trasparency management.


== The steps ==
Install xcompmgr. Should be available on your local port/package distribution system (apt, emerge, ports, whatever), or from: http://cgit.freedesktop.org/xorg/app/xcompmgr/.

Run this in an xterm to see if you need to enable both extensions:
 $ xdpyinfo | grep -i "render\|composite"

Add the following lines to /etc/X11/xorg.conf:
        Section "Extensions"
        Option "Composite" "Enable"
        Option "RENDER" "Enable"
        EndSection

It's worth mentioning, that both RENDER and Composite extentions are still in early testing. However, I have succesfully used them for a longer period of time without them causing any damage, so hopefully yours should be fine as well.

Restart X completely and start it manually again.
It should be working now, however if you want to check you can search your <code>/var/log/Xorg.0.log</code> for an entry like this: 
 (II) Initializing built-in extension COMPOSITE
This means that the composite extention, which is what you need, has been loaded properly. If not, X may not even start, and thus you have to start troubleshooting (check for correct version first, only xorg 6.8RC4 and beyond supports the composite extention).

Now that you have xorg running with your composite extention, you should learn how to use the features. xcompmgr is started by the following command directly from a terminal inside X:
 $ xcompmgr -c &

Please note that other features are available for xcompmgr. Explore them with <code>xcompmgr -h</code> or if you have the manpages, look at them.

Then, you start up an application like you normally would and see the shadows that the composite extention allows. It's neat, no?
<!--Now you want to set your transparency which is done in 10th of percentage; logic should be appearant. Type in the following command and hit enter:
        transset-df 0.6

Now click the application you want to be transparent. Please note, that since this is true transparency, not all text will be readable depending on your choice of background.-->

So, it's working.. But it's running SLOW(!) and it's hogging ressources, right? Yes, sure it is.
The way to fix this, is to enable a few features in your xorg config. I'll guide you through it, step for step though.
For nvidia cards (running the nvidia drivers with hardware accelleration, not the built-in drivers), add:
        Option        "RenderAccel" "true"

''nvidia binary drivers accelerate the RENDER extension in hardware by default so if you use them you don't need this option (appendix-d of the nvidia docs).''

And for ati cards (again running the ati drivers with hardware accelleration, not the built-in drivers) add:
        Option        "backingstore" "true"

Both of these cards also have a second optimizing feature which should apply for both:
        Option        "AllowGLXWithComposite" "true"

Note: In the NVidia 8762 driver README it states that AllowGLXWithComposite shouldn't be set  for X.org servers >= 6.9.0 because NVidia's GLX works correctly with those servers when Composite is on.

All of these (the optimization features) goes into the device section (eg. where you actually define the driver of the video card; use the editors search feature to search for driver, and you should find it)! Please, do not put it anywhere else, or it will fail!
There. The RENDER extention, plus the optimization features should make it run almost flawlessly.

So, there you have it.. A little tutorial on making everything truly transparent in xorg 6.8RC4 and beyond.


== Benchmarking xcompmgr ==
It was suggested to me by a fellow ircer that I'd gather some information about how well xcompmgr works with what drivers/cards people have running. Therefore, I'd like to ask anyone that follows this guide to login and submit their results. I'll have to figure out a way of timing it first, so until then please just leave a note saying how well it went and an approximate benchmark of how xcompmgr runs with the above setting (being 'xcompmgr -c &'). Thanks in advance.


= fbcompose =
In 2001 Fluxbox participated at [http://www.google-melange.com/gsoc/org/google/gsoc2011/fluxbox GSOC] (here the [http://fluxbox.org/gsoc2011/ announce and project ideas]), and Gediminas Liktaras worked on the addition of OpenGL/XRender compositing and desktop effects to fluxbox, similarly to what compiz and xcompmgr provide.

fbcompose is still in development and rather abandoned, volunteers are greatly appreciated. Help us to improve this feature and forget buggy third-party software.

Here the [http://git.fluxbox.org/fluxbox_gediminas.git/ git repository], have fun!

<!--
Debian and Ubuntu users: thanks to Paultag we have [https://launchpad.net/~fluxbox-maintainers/+archive/nightly/+packages nightly builds], the package is fluxbox-fbcompose but keep in mind that "nightly" means potentially unstable; please don't blame anyone if you'll have unexpected behaviours.
Look at [http://fluxbox-wiki.org/index.php?title=Update_fluxbox#Using_Ubuntu_and_Debian the update page] for more informations and how to add the repository to your sources.list.
-->