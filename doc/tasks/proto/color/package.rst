.. _package:

package: Guide to color image display
=====================================

**Package: color**

.. raw:: html

  <p>
  INTRODUCTION
  </p>
  <p>
  This guide describes techniques for taking three monochrome IRAF images, a
  <span style="font-family: monospace;">"red"</span> image, a <span style="font-family: monospace;">"green"</span> image, and a <span style="font-family: monospace;">"blue"</span> image and making color
  composites.  There are many techniques which depend on different hardware
  and software.  This guide currently discusses three methods for display on
  an 8-bit color workstation, using Sun 24-bit RGB rasterfiles, creating a
  special color map and image which samples the RGB color space, and pixel
  dithering.  The rasterfiles may be displayed or printed using a variety or
  non-IRAF tools which are readily available and which can be used with 8-bit
  workstations.  The special color map and pixel dithering methods use only
  IRAF images and the standard SAOimage/IMTOOL display servers to display on
  8-bit color workstation.  These techniques are intended to provide a
  rudimentary color composite capability in absence of better hardware such
  as IIS/IVAS devices or 24-bit workstations.
  </p>
  <p>
  For further information on the tasks described here see the approriate
  help pages.
  </p>
  <p>
  SUN 24-BIT RGB RASTERFILES
  </p>
  <p>
  The task <b>rgbsun</b> takes three input IRAF images and produces a 24-bit
  Sun rasterfile.  Though this file type was developed by Sun Microsystems
  it is a relatively simple format which may useful on other machines having
  software designed to use it.  The color image may be display with a variety
  of tools such as <b>xv</b> (a very powerful and generic viewer for X-window
  systems), <b>xloadimage</b> (another X-window display tool),
  <b>screenload</b> (a simple displayer on Sun computers), and <b>snapshot</b>
  (an Open-Look tool).  Also some color printers can be used with this format
  such as a Shinko color printer.
  </p>
  <p>
  The recommended display tool is <b>xv</b> which provides a great deal of
  capability in adjusting the color map and size.  This program compresses the
  24-bit colors to 8-bits on an 8-bit workstation using color dithering
  techniques (there is a choice of a slow and fast method).  This program
  also provides the capability to write the picture out to other formats and
  one may also use screen capture tools such as <b>xwd</b> or <b>snapshot</b>
  to extract and possibly print the picture.
  </p>
  <p>
  For hardcopy there is always the option of photographing the workstation
  screen.  Different sites may also have color printers which accept
  the rasterfile directly or some other form of capture from the screen.
  At NOAO there is a Shinko color printer which may be used directly with
  the rasterfile to make moderate quality color prints and slides.
  </p>
  <p>
  24-BIT to 8-BIT COLOR MAP COMPRESSION
  </p>
  <p>
  The task <b>rgbto8</b> produces an 8-bit color map which samples the full
  range of RGB color values and an associated image with values indexing the
  color map.  The compression algorithm is called the Median Cut Algorithm
  and the image is dithered with this color map using the Floyd-Steinberg
  algorithm.  The resulting image is a short image with 199 values.  The
  color map is output in either a format suitable for use with SAOimage or
  with IMTOOL.  This method is recommended over the pixel dithering method.
  </p>
  <p>
  The RGB values are input as three IRAF images.  The images must each be
  scaled to an 8 bit range.  This is done by specifying a range of input
  values to be mapped to the 8 bit range.  In addition the range can be
  mapped logarithmically to allow a greater dynamic range.
  </p>
  <p>
  The output image is displayed with <b>rgbdisplay</b> and SAOimage, IMTOOL,
  or XIMTOOL.  Note that this requires V1.07 of SAOimage.  The color map
  produced by the <b>rgbto8</b> for a particular image must also be loaded
  into the display server manually.  With IMTOOL use the setup panel and set
  the file name in the user1 or user2 field and then select the appropriate
  map.  With SAOimage you select the <span style="font-family: monospace;">"color"</span> main menu function, and then the
  <span style="font-family: monospace;">"cmap"</span> submenu function, and then the <span style="font-family: monospace;">"read"</span> button.  Note that usually a
  full pathname is required since the server is usually started from the
  login directory.  For XIMTOOL the <span style="font-family: monospace;">"XImtool*cmapDir1"</span> resource must be
  set to the directory containing the color map and XIMTOOL must be
  restarted to cause the directory to be searched for color map files.
  </p>
  <p>
  The display server must be setup in it's default contrast mapping (with
  IMTOOL you can use the RESET option, with XIMTOOL the <span style="font-family: monospace;">"normalize"</span> option is
  used, and with SAOimage you must restart) and the contrast mapping must not
  be changed.  There are no adjustments that can be made in IMTOOL or XIMTOOL
  but with SAOimage you can adjust the colors using the <span style="font-family: monospace;">"gamma"</span> selections
  and the mouse.
  </p>
  <p>
  8-BIT PIXEL DITHERING
  </p>
  <p>
  1. Theory
  </p>
  <p>
  The pixel dithering technique takes the three input IRAF images and makes a
  special output IRAF image in which each pixel in the input images is expanded
  into nine pixels in the output image with a specified pattern such as
  the default of
  </p>
  <div class="highlight-default-notranslate"><pre>
                  brg
  r + g + b =     gbr
                  rgb
  </pre></div>
  <p>
  where r is the red image pixel, g is the green image pixel, and b is the
  blue image pixel.
  </p>
  <p>
  The pixel intensities are linearly mapped from a specified input range to
  one of three sets of 85 levels.  The red pixels map to the values 0 to 84,
  the green pixels to the range 85 to 169, and the blue pixels to the range
  170 to 254.  The display server then uses a special 8-bit look up table
  that maps each set of 85 levels in each pure color from off to the maximum
  intensity.  The displayed image counts on the nearby grouping of pure
  colors to blend in the detector, such as the eye, to give a color composite
  effect.
  </p>
  <p>
  This is essentially the same technique used in some kinds of color printing
  and CRT monitors where each resolution element has three color phosphors
  and three guns to excite them.  The pixel dithering is also related to
  black and white half-toning.  As with any of these, if the image is
  magnified or viewed with enough resolution (by looking very closely at the
  display) the individual color elements can be distinguished.  However, when
  viewed normally without magnification the effect is reasonably good.
  </p>
  <p>
  8-BIT PIXEL DITHERING: Usage
  </p>
  <p>
  The composite image is created by the task <b>rgbdither</b> and displayed
  with the task <b>rgbdisplay</b>.  Unlike the <b>display</b> task there is no
  automated way to define the display ranges for the three images.  These
  must be specified explicitly with the image is created.  The ranges may be
  determined in a variety of ways such as by looking at the histograms,
  <b>imhist</b>, the statistics of the image, <b>imstat</b>, or possibly the
  display range produced by <b>display</b>.  Note, however, that often the
  ranges used to stretch an individual image are not appropriate for color
  balancing between the three images.
  </p>
  <p>
  Because each input pixel is expanded into nine pixels in the composite
  image the composite image will have dimensions three times larger than
  the input image.  The <i>blkavg</i> parameter allows block averaging
  the input images at the same time that the composite image is created.
  If a value of 3, the default, is used then the final displayed image
  will have dimensions nearly the same as the input images.  This is often
  satisfactory and one should try this first.
  </p>
  <p>
  If one wants to display images which have a large dyanmic range it
  may be desirable to first take the logarithm of each image.  This may
  be done with the <i>logmap</i> parameter.  Other types of stretching may
  be accomplished by modifying the individual images first, say with
  imfunction.
  </p>
  <p>
  In addition to creating and loading the composite image within IRAF
  it is also necessary to adjust the image display server.  Either
  SAOimage or IMTOOL may be used.  SAOimage is prefered because
  it is possible to make some adjustments in the color mapping while with
  IMTOOL one must modify the composite image by varying the z1 and z2
  values for the three images.
  </p>
  <p>
  The display servers must be set so that there is no contrast stretching.
  This is how the programs start initially but it may be difficult to return
  to this state if you adjust the contrast with the right mouse button in
  IMTOOL or the contrast adjustments in the (COLOR) menu of SAOimage.
  </p>
  <p>
  You must first determine where the special color maps are located.
  Since the display servers are host programs they require host pathnames.
  You can determine the host pathname from within IRAF using the command
  </p>
  <div class="highlight-default-notranslate"><pre>
  cl&gt; path colorlib$saorgb.lut
  puppis!/ursa/iraf/extern/color/lib/saorgb.lut
  
                  or
  
  cl&gt; path colorlib$imtoolrgb.lut
  puppis!/ursa/iraf/extern/color/lib/imtoolrgb.lut
  </pre></div>
  <p>
  You can either remember these names (without the node prefix) or
  more simply copy the one you need to your IRAF home directory
  (or any place else you like) with the command
  </p>
  <div class="highlight-default-notranslate"><pre>
  cl&gt; copy colorlib$saorgb.lut home$
  
                  or
  
  cl&gt; copy colorlib$imtoolrgb.lut home$
  </pre></div>
  <p>
  With SAOimage load the special look up table by entering the (COLOR) menu,
  then the (CMAP) menu, and then pushing the (READ) button.  When you are
  prompted for the map enter the pathname for the file saorgb.lut.  For
  IMTOOL you need to call up the setup menu and set the pathname for the file
  imtoolrgb.lut in either of the user look up tables and then select the
  appropriate map.
  </p>
  <p>
  For IMTOOL that is all you can do.  Beware, don't adjust the contrast (the
  right mouse button) since this destroys the mapping between the composite
  image values and the look up table.
  </p>
  <p>
  In SAOimage there are a couple of things you can do to make adjustments to
  the display.  Bring up the color editor by clicking on the color bar.  Even
  if you don't adjust the look up table this can be instructive.  If you
  select (GAMMA) in the (COLOR) menu you can then move the mouse with a
  button down and vary the linearity of the color maps.  This can be seen in
  the color editor.  You can also adjust the individual colors by clicking
  the left (red), middle (green), or right (blue) buttons to either move the
  shown points or add and move points in the middle.  Note that the abrupt
  discontinuity between the colors can cause sudden jumps in the color map if
  one point is moved past the other but you can recover by bring the point
  slowly back.  If the map gets too messed up you can always reload the color
  map.
  </p>
  <p>
  One might expect that making a hardcopy of the display would produce a
  comparable quality image.  This may be the case by photographing the CRT
  screen.  However, experiments with capturing the displayed image to a
  rasterfile and printing it on a SHINKO color printer does not produce
  useful hardcopy.
  </p>
  <!-- Contents:  -->
  
