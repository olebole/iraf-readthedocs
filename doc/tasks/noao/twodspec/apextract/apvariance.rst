.. _apvariance:

apvariance: Extractions, variance weighting, cleaning, and noise model
======================================================================

**Package: apextract**

.. raw:: html

  <p>
  There are two types of aperture extraction (estimating the background
  subtracted flux across a fixed width aperture at each image line or
  column) in the APEXTRACT package.  One is a simple sum of pixel values
  across an aperture.  It is selected by specifying <span style="font-family: monospace;">"none"</span> for the
  <i>weights</i> parameter.  The second type weights each pixel in the sum
  by it's estimated variance based on a spectrum model and detector noise
  parameters.  This type of extraction is selected by specifying
  <span style="font-family: monospace;">"variance"</span> for the weighting parameter.  These two extractions are
  defined by the following equations.
  </p>
  <div class="highlight-default-notranslate"><pre>
      none:   S = sum { I - B }
  variance:   S = sum { (P**2 / V) (I - B) / P } / sum { P**2 / V }
  </pre></div>
  <p>
  S is the one dimensional spectrum flux at a particular wavelength (line
  or column along the dispersion axis).  The sum is over all pixels at
  that wavelength within the aperture limits.  If the aperture endpoints
  occupy only a fraction of a pixel then the pixel value above the
  background is multiplied by the fraction.  I is the pixel value and B
  is the estimated background at that pixel (see <b>apbackground</b>), P
  is estimated normalized profile value for that pixel (see
  <b>approfile</b>), and V is the estimated variance of the pixel based on
  the noise model described below.  Note that the quantity (I-B)/P is an
  independent estimate of the total flux from one pixel since the
  integral of P is one and it is these estimates that are variance
  weighted.
  </p>
  <p>
  Variance weighting is often called <span style="font-family: monospace;">"optimal"</span> extraction since it
  produces the best unbiased signal-to-noise estimate of the flux in the
  two dimensional profile.  The theory and application of this type of
  weighting has been described in several papers.  The ones which were
  closely examined and used as a model for the algorithms in this
  software are <span style="font-family: monospace;">"An Optimal Extraction Algorithm for CCD Spectroscopy"</span>,
  PASP 98, 609, 1986, by Keith Horne and <span style="font-family: monospace;">"The Extraction of Highly
  Distorted Spectra"</span>, PASP 100, 1032, 1989, by Tom Marsh.
  </p>
  <p>
  The noise model for the image data used in the variance weighting,
  cleaning, and profile fitting consists of a constant gaussian noise and
  a photon count dependent poisson noise.  The signal is related to the
  number of photons detected in a pixel by a gain parameter given
  as the number of photons per data number.  The gaussian noise is given
  by a <i>readnoise</i> parameter which is a defined as a sigma in
  photons.  The poisson noise is approximated as gaussian with sigma
  given by the number of photons.
  </p>
  <p>
  Some additional effects which should be considered in principle, and
  which are possibly important in practice, are that the variance
  estimate should be based on the actual number of photons detected before
  correction for pixel sensitivity; i.e. before flat field correction.
  Furthermore the uncertainty in the flat field should also be included
  in the weighting.  However, the profile must be determined free of
  sensitivity effects including rapid larger scale variations such as
  fringing.  Thus, ideally one should input the unflat-fielded
  observation and the flat field data and carry out the extractions with
  the above points in mind.  However, due to the complexity often
  involved in basic CCD reductions and special steps required for
  producing spectroscopic flat fields this level of sophistication is not
  provided by the current package.  The package does provide, however,
  for propagation of an approximate uncertainty in the background
  estimate when using background subtraction.
  </p>
  <p>
  The noise model is described by the following equations.
  </p>
  <div class="highlight-default-notranslate"><pre>
  (1) V = max (VMIN, (R**2 + I + VB) / G**2)
          max (VMIN, (R**2 + S * P + B + VB) / G**2)
  
  (2) VB = 0.                 if (B = 0)
         = B / (N - 1)        if (B &gt; 0)
  
  (3) VMIN = 1 / G**2         if (R = 0)
             R**2 / G**2      if (R &gt; 0)
  </pre></div>
  <p>
  V is the desired variance of a pixel to use for variance weighting.  R
  is the photon read out noise specified by the parameter <i>readnoise</i>
  and G is the photon per data value gain specified by the parameter
  <i>gain</i>.  There are two forms to (1).  The first is used in the
  initial pass of estimating the spectrum flux S and the actual pixel
  value I (which includes any background) is used for the poisson term.
  The other form is used in a second pass (and further passes if
  cleaning) using the estimated data value based on the normalized
  profile P scaled to the estimated total flux plus the estimated
  background B; i.e. I estimated = S * P + B.
  </p>
  <p>
  The background variance VB is computed using the poisson noise model
  based on the estimated background counts.  If no background subtraction
  is done then both B and VB are set to zero.  If a background is
  determined the background is either an average or function fit to
  pixels in defined background regions.  If a fit is used B need not be a
  constant.   Because the background estimate is based on a finite number of
  pixels, the poisson variance estimate is divided by the number N (minus
  one) of pixels used in determining the background.  The number of
  pixels used includes any box car smoothing.  Thus, the larger the
  number of background pixels the smaller the background noise
  contribution to the variance weighting.  This method is only
  approximate since no correction is made for the number of degrees of
  freedom and correlations when using the fitting method of background
  estimation.
  </p>
  <p>
  VMIN is a minimum variance need to avoid generating zero or negative
  variances from the data.  The definition of VMIN is such that if a zero
  read out noise is specified (which is certainly possible such as with
  photon counting detectors) then a minimum of 1 photon is imposed.
  Otherwise the minimum is set by the read out noise even if the poisson
  count part is (unphysically) negative.
  </p>
  <p>
  One deviation from the linear photon response mode which is considered
  is saturation.   A data level specified by the parameter
  <i>saturation</i> is used to exclude data from the profile fitting.
  During extraction the saturated pixels are not treated any differently
  than unsaturated pixels except that dispersion points with saturated
  pixels are flagged by reversing the sign of the final estimated sigma;
  the sigma output is enabled with the <i>extras</i> parameter.  Exclusion
  of saturated pixels from the extraction, as is done with deviant
  pixels, was tried but this resulted in higher noise in the spectrum.
  </p>
  <p>
  If removal of cosmic rays and other deviant pixels is desired, called
  cleaning and selected with a <i>clean</i> parameter, they are
  iteratively rejected based on the estimated variance and excluded from
  the weighted sum.  Note that a cleaned extraction is always variance
  weighted regardless of the value of the <i>weights</i> parameter.  This
  makes sense since the detector noise parameters must be specified and
  the spectrum profile computed, so all of the computational effort must
  be done anyway, and the variance weighting is as good or superior to a
  simple unweighted extraction.
  </p>
  <p>
  The detection and removal of deviant pixels is straightforward.  Based
  on the noise model described earlier, pixels deviating by more than a
  specified number of sigma (square root of the variance) above or below
  the model are removed from the weighted sum.  A new spectrum estimate
  is made and the rejection is repeated.  The rejections are made one at
  a time starting with the most deviant and up to half the pixels in the
  aperture may be rejected.  The total number of rejected pixels in the
  spectrum is recorded in the logfile and a profile plot of data and
  model profile is recorded in the plotfile.
  </p>
  <p>
  As a final step when computing a weighted/cleaned spectrum the total
  fluxes from the weighted spectrum and the simple unweighted spectrum
  (excluding any deviant and saturated pixels) are computed and a
  <span style="font-family: monospace;">"bias"</span> factor of the ratio of the two fluxes is multiplied into
  the weighted spectrum and the sigma estimate.  This makes the total
  fluxes the same.  The bias factor is recorded in the logfile
  if one is kept.  Also a check is made for unusual bias factors.
  If the two fluxes disagree by more than a factor of two a warning
  is given on the standard output and the logfile with the individual
  total fluxes as well as the bias factor.  If the bias factor is
  negative a warning is also given and no bias factor is applied.
  </p>
  <section id="s_see_also">
  <h3>See also</h3>
  <p>
  apbackground approfiles apall apsum
  </p>
  
  </section>
  
  <!-- Contents: 'SEE ALSO'  -->
  
