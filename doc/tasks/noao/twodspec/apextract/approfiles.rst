.. _approfiles:

approfiles: Profile determination algorithms
============================================

**Package: apextract**

.. raw:: html

  <p>
  The foundation of variance weighted or optimal extraction, cosmic ray
  detection and removal, two dimensional flat field normalization, and
  spectrum fitting and modeling is the accurate determination of the
  spectrum profile across the dispersion as a function of wavelength.
  The previous version of the APEXTRACT package accomplished this by
  averaging a specified number of profiles in the vicinity of each
  wavelength after correcting for shifts in the center of the profile.
  This technique was sensitive to perturbations from cosmic rays
  and the exact choice of averaging parameters.  The current version of
  the package uses two different algorithm which are much more stable.
  </p>
  <p>
  The basic idea is to normalize each profile along the dispersion to
  unit flux and then fit a low order function to sets of unsaturated
  points at nearly the same point in the profile parallel to the
  dispersion.  The important point here is that points at the same
  distance from the profile center should have the nearly the same values
  once the continuum shape and spectral features have been divided out.
  Any variations are due to slow changes in the shape of the profile with
  wavelength, differences in the exact point on the profile, pixel
  binning or sampling, and noise.  Except for the noise, the variations
  should be slow and a low order function smoothing over many points will
  minimize the noise and be relatively insensitive to bad pixels such as
  cosmic rays.  Effects from bad pixels may be further eliminated by
  chi-squared iteration and clipping.  Since there will be many points
  per degree of freedom in the fitting function the clipping may even be
  quite aggressive without significantly affecting the profile
  estimates.  Effects from saturated pixels are minimized by excluding
  from the profile determination any profiles containing one or more
  saturated pixels as defined by the <i>saturation</i> parameter.
  </p>
  <p>
  The normalization is, in fact, the one dimensional spectrum.  Initially
  this is the simple sum across the aperture which is then updated by the
  variance weighted sum with deviant pixels possibly removed.  This updated
  one dimensional spectrum is what is meant by the profile normalization
  factor in the discussion below.  The two dimensional spectrum model or
  estimate is the product of the normalization factor and the profile.  This
  model is used for estimating the pixel intensities and, thence, the
  variances.
  </p>
  <p>
  There are two important requirements that must be met by the profile fitting
  algorithm.  First it is essential that the image data not be
  interpolated.  Any interpolation introduces correlated errors and
  broadens cosmic rays to an extent that they may be confused with the
  spectrum profile, particularly when the profile is narrow.  This was
  one of the problems limiting the shift and average method used
  previously.  The second requirement is that data fit by the smoothing
  function vary slowly with wavelength.  This is what precludes, for
  instance, fitting profile functions across the dispersion since narrow,
  marginally sampled profiles require a high order function using only a
  very few points.  One exception to this, which is sometimes useful but
  of less generality, is methods which assume a model for the profile
  shape such as a gaussian.  In the methods used here there is no
  assumption made about the underlying profile other than it vary
  smoothly with wavelength.
  </p>
  <p>
  These requirements lead to two fitting algorithms which the user
  selects with the <i>pfit</i> parameter.  The primary method, <span style="font-family: monospace;">"fit1d"</span>,
  fits low order, one dimensional functions to the lines or columns
  most nearly parallel to the dispersion.  While this is intended for
  spectra which are well aligned with the image axes, even fairly large
  excursions or tilts can be adequately fit in this
  way.  When the spectra become strongly tilted then single lines or
  columns may cross the actual profile relatively quickly causing the
  requirement of a slow variation to be violated.  One thought is to use
  interpolation to fit points always at the same distance from the
  profile.  This is ruled out by the problems introduced by
  image interpolation.  However, there is a clever method which, in
  effect, fits low order polynomials parallel to the direction defined by
  tracing the spectrum but which does not interpolate the image data.
  Instead it weights and couples polynomial coefficients.  This
  method was developed by Tom Marsh and is described in detail in the
  paper, <span style="font-family: monospace;">"The Extraction of Highly Distorted Spectra"</span>, PASP 101, 1032,
  Nov. 1989.  Here we refer to this method as the Marsh or <span style="font-family: monospace;">"fit2d"</span>
  algorithm and do not attempt to explain it further.
  </p>
  <p>
  The choice of when to use the one dimensional or the two dimensional
  fitting is left to the user.  The <span style="font-family: monospace;">"fit1d"</span> algorithm is preferable since it
  is faster, easier to understand, and has proved to be very robust.  The
  <span style="font-family: monospace;">"fit2d"</span> algorithm usually works just as well but is slower and has been
  seen to fail on some data.  The user may simply try both to achieve the
  best results.
  </p>
  <p>
  What follows are some implementation details of the preceding ideas in the
  APEXTRACT package.  For column/line fitting, the fitting function is a
  cubic spline.  A base number of spline pieces is set by rounding up the
  maximum trace excursion; an excursion of 1.2 pixels would use a spline of 2
  pieces.  To this base number is added the number of coefficients in the
  trace function in excess of two; i.e. the number of terms in excess of a
  linear function.  This is done because if the trace wiggles a large amount
  then a higher order function will be needed to fit a line or column as the
  profile shifts under it.  Finally the number of pieces is doubled
  because experience shows that for low tilts it doesn't matter but for
  large tilts this improves the results dramatically.
  </p>
  <p>
  For the Marsh algorithm there are two parameters to be set, the
  polynomial order parallel to the dispersion and the spacing between
  parallel, coupled polynomials.  The algorithm requires that the spacing
  be less than a pixel to provide sufficient sampling.  The spacing is
  arbitrarily set at 0.95 pixels.  Because the method always fits
  polynomials to points at the same position of the profile the order
  should be 1 except for variations in the profile shape with
  wavelength.  To allow for this the profile order is set at 10; i.e. a
  9th order function.  A final parameter in the algorithm is the number
  of polynomials across the profile but this is obviously  determined
  from the polynomial spacing and the width of the aperture including an
  extra pixel on either side.
  </p>
  <p>
  Both fitting algorithms weight the pixels by their variance as computed
  from the background and background variance if background subtraction
  is specified, the spectrum estimate from the profile and the spectrum
  normalization, and the detector noise parameters.  A poisson
  plus constant gaussian readout noise model is used.  The noise model is
  described further in <b>apvariance</b>.
  </p>
  <p>
  As mentioned earlier, the profile fitting can be iterated to remove
  deviant pixels.  This is done by rejecting pixels greater than a
  specified number of sigmas above or below the expected value based
  on the profile, the normalization factor, the background, the
  detector noise parameters, and the overall chi square of the residuals.
  Rejected points are removed from the profile normalization and
  from the fits.
  </p>
  <section id="s_see_also">
  <h3>See also</h3>
  <p>
  apbackground apvariance apall apsum apfit apflatten
  </p>
  
  </section>
  
  <!-- Contents: 'SEE ALSO'  -->
  
