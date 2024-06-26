.. _overview:

overview: Overview of the package
=================================

**Package: crutil**

.. raw:: html

  <p>
  The cosmic ray package provides tools for identifying and removing cosmic
  rays in images.  The tasks are:
  </p>
  <div class="highlight-default-notranslate"><pre>
  cosmicrays - Remove cosmic rays using flux ratio algorithm
   craverage - Detect CRs against average and avoid objects
   crcombine - Combine multiple exposures to eliminate cosmic rays
      credit - Interactively edit cosmic rays using an image display
       crfix - Fix cosmic rays in images using cosmic ray masks
      crgrow - Grow cosmic rays in cosmic ray masks
    crmedian - Detect and replace cosmic rays with median filter
    crnebula - Detect and replace cosmic rays in nebular data
  </pre></div>
  <p>
  The best way to remove cosmic rays is using multiple exposures of the same
  field.  When this is done the task <b>crcombine</b> is used to combine the
  exposures into a final single image with cosmic rays removed.  The images
  are scaled (if necessary) to a common data level either by multiplicative
  scaling, an additive background offset, or some combination of both.
  Cosmic rays are then found as pixels which differ by some statistical
  amount away for the average or median of the data.
  </p>
  <p>
  A median is the simplest way to remove cosmic rays.  This is an option
  with <b>crcombine</b>.  But this does not make optimal use of the data.
  An average of the pixels remaining after some rejection operation is better.
  If the noise characteristics of the data can be described by a gain and
  read noise then cosmic rays can be optimally rejected using the
  <span style="font-family: monospace;">"crreject"</span> algorithm.  This works on two or more images.  There are
  a number of other rejection algorithms which can be used as described in
  the task help.
  </p>
  <p>
  The rest of the tasks in the package are used when only a single exposure
  is available.  These include interactive editing with <b>credit</b>.  The
  replacement algorithms in this task may also be used non-interactively if
  you have a list of pixel coordinates as input.  Other tasks automatically
  identifying pixels which are significantly higher than surrounding pixels.
  </p>
  <p>
  The simplest of these tasks is <b>crmedian</b>.  This replaces
  cosmic rays with a median value and produces a cosmic ray
  mask which is a simple type of integer image where good pixels have a value
  of zero and bad pixels have a non-zero value.  The tasks <b>crgrow</b> and
  <b>crfix</b> are provided to use this type of cosmic ray mask.  The former
  will flag additional pixels within some radius of the flagged pixels in the
  mask.  The latter is the basic tool for replacing the identified pixels in
  the data by neighboring data.  It uses linear interpolation along lines or
  columns.  The median task is simple but it often will flag the cores of
  stars or other small but real features.
  </p>
  <p>
  The task <b>craverage</b> is similar to <b>crmedian</b> in that it compares
  the pixel values against a smoothed version.  Instead of a median it uses
  an average with the central pixel excluded.  It is more sophisticated
  in that it also compares the average against a larger median to see if
  the region corresponds to an object.  Thus it can detect objects and
  the task could be used as a simple object detection task in its own right.
  Because the hardest part of cosmic ray detection from a single image is
  avoiding truncation of the cores of stars this task does not allow cosmic
  rays to be detected where it thinks there is an object.  This task is
  also more versatile in allow separate mask values and works on a list
  of images.
  </p>
  <p>
  Somewhat more sophisticated algorithms are available in the tasks
  <b>cosmicrays</b> and <b>crnebula</b>.  These attempt to determine if a
  deviant pixel is the core of a star or part of a linear nebular feature
  respectively.
  </p>
  <p>
  The best use of these tasks is to experiment and iterate.  In particular,
  one may want to iterate a task several times and use both <b>cosmicrays</b>
  and <b>craverage</b>.
  </p>
  <p>
  Good hunting!
  </p>
  <!-- Contents:  -->
  
