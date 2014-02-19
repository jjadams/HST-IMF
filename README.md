HST-IMF
=======

Python code to extract IMF constraints from resolved photometry of galaxies or star clusters.

This is code written as part of an HST Cycle 21 Archive Grant. JJA started this code on Feb 12, 2014. On Feb 18, 2014, JJA tested it with photometry from the u2pb01 Ursa Minor field. It runs successfully and delivers plausible results. Co-workers have access to a small write-up describing the method. The current major limitations are 1) a fixed distance is used, 2) no correction for Galactic extinction is applied, 3) only one infinitely thin isochrone is used as the model right now, 4) and all photometry uses the same completeness curve. Co-workers may expand the code for those issues later, but I'm afraid this is the last JJA can work on this. The code can be run from a command line as 'python fit_IMF.py file_p1' where file_p1 is the name of your input parameter file. fit_IMF.param exists as an example for the UMi u2pb01 fieldd. There are five lines in the parameter file covering 1) the input photometry, 2) the completeness curve, 3) a model of the Galactic foreground and background composed of Gaussian mixture terms, 4) an isochrone model for the stellar system under analysis, and 5) a flag for the IMF type (0=Salpeter and 1=Chabrier).

The current photometry is taken directly from the Holtzman archive. It retains a header; look at it. 

The current completeness curve file holds three columns: 1) observed magnitude, 2) completeness in filter 1, 3) completeness in filter 2. I made this file from the included gen_hstcomp.py script and the Holtzman *.comp fake star test file. See the comments of the script for details.

The foreground model file holds 8 columns, one for each multi-d Gaussian which, when summed, represent the CMD density of MW stars. 1) index 2) the amplitude of the Gaussian, 3) the central value for the filter 1 magnitude, 4) the central value for the filter1-filter2 color, 5-8) the covariance matrix of the Gaussian in mag1,mag1-mag2 space. 5) is the variance of mag1, 6 and 7) are the two cross terms and are identical, 8) is the variance in the mag1-mag2 color. Actually, I have fit a model for a 5000x more numerous background to get good statistics, and there's a hardcoded variable in fit_IMF.py named fg_enc that corrects for this. I made this file as direct output from Connolly's FastEM code. Go get it. I ran it as:

FastEM in="fg_fastem.in" do_loadcenter="false" loadcenter="" do_savecenter="true" savecenter="fg_fastem_soln.txt" do_saveps="false" saveps="" seed="0" draw="true" nsecs="500" nsteps="-1" varyclusters="true" nclusters="-1" scoretype="aic" aem="false" epsilon="0.000000" verbosity="-1"

I made the input file, fg_fastem.in, with a download from the Besancon model website and an awk statement. I queried 0.00158148 sq.deg around UMi (factor of 5000x larger area than a WFPC2 pointing) down to M_V=20. With that download, I simply ran:
cat besancon_umi.resu | awk 'BEGIN{print "x0 x1"} NR>82&&NR<109795{print $2+5.0*(log($1*1.E3)/log(10.0)-1.0),$11-1.22}' > fg_fastem.in

Finally, the isochrone is taken directly from the online Dartmouth site. It has it's original header; go look.
