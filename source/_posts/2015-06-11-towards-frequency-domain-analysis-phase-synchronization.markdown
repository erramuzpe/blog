---
layout: post
title: "Towards frequency domain analysis and phase synchronization"
date: 2015-06-11 11:54:16 +0200
comments: true
categories: 
---

This week we have been discussing further about the possibility of developing some frequency analysis tools to integrate in the package. Although CPAC already has some tools to work on the frequency domain, we are thinking in building up some valuable scripts that allow us to capture information using the frequency domain. Our intention is to develop measures such Phase Synchronization and identification of patterns based on frequency domain analysis, to be used as features for Fingerprint analysis. Specifically, as starting point, we are going to take as guide and example the methods described in the paper by Ponce et al. [\[1\]](http://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1004100), with the intention of performing a similar analysis over fMRI. Our goal is to do so and extending them with measures like temporal power spectral density (PSDT).

## **Phase synchronization analysis**

This kind of analysis needs of the extraction ef the phases of the fMRI timeseries. We can obtain the analytical signals using the Hilbert transform. This analytic signal represents a signal in the time domain as a rotating vector with an instantaneous phase, phi(t), and amplitude. 

The global level of phase synchrony is given by the parameter R(t), taking values in the [0,1] range (being 0 completely asynchroneous and 1 completely synchronized, respectively):

{% img center /images/r_param.png 300 300 'image' 'image' %}

For quantifying the pairwise phase relation between two given brain regions (timeseries) _k_ and _l_, Phase-Locking Value (PLV) has to be calculated as:

{% img center /images/plv.png 300 300 'image' 'image' %}

Last week, I already deveveloped a simple filter and this week I have been practicing further with the numpy.signal package. Some example of code, partially changed from [this](http://gribblelab.org/scicomp/09_Signals_sampling_filtering.html) excellent tutorial. Under each block of code, I have plotted an example over fMRI data (it is clear that the used data has been already preprocessed and filtered):



{% codeblock lang:py %}
  from numpy import *
	from scipy import * # not a good practice, but just for the examples

	# Power Spectra calculation
	# construct signal
	t = linspace(0, 1, 1001)
	y = sin(2*pi*t*6) + sin(2*pi*t*10) + sin(2*pi*t*13)

	# compute FFT and the magnitude spectrum
	F = fft.fft(y)
	N = len(t)             # number of samples
	dt = 0.001             # inter-sample time difference
	w = fft.fftfreq(N, dt)     # gives us a list of frequencies for the FFT
	ipos = where(w>0)
	freqs = w[ipos]        # only look at positive frequencies
	mags = abs(F[ipos])    # magnitude spectrum
	phase = imag(F[ipos])  # phase component

{% endcodeblock %}

{% img center /images/plot1.png 300 300 'image' 'image' %}

{% codeblock lang:py %}
  # Inverse Fast Fourier transform (IFFT) (reconstruction from freq domain)
	# construct signal
	t = linspace(0, 1, 1001)
	y = sin(2*pi*t*6) + sin(2*pi*t*10) + sin(2*pi*t*13)
	
	# compute FFT and the magnitude spectrum
	F = fft.fft(y)
	N = len(t)             # number of samples
	dt = 0.001             # inter-sample time difference
	w = fft.fftfreq(N, dt)     # gives us a list of frequencies for the FFT
	ipos = where(w>0)
	freqs = w[ipos]        # only look at positive frequencies
	mags = abs(F[ipos])    # magnitude component
	phase = imag(F[ipos])  # phase component
	# inverse
	yr = fft.ifft(F)
	
{% endcodeblock %}

{% img center /images/figure_2.png 300 300 'image' 'image' %}

{% codeblock lang:py %}
  ## Filtering
	# construct signal 
	t = linspace(0, 1, 1001)
	y = sin(2*pi*t*6) + sin(2*pi*t*10) + sin(2*pi*t*13)
	
	# compute FFT and the magnitude spectrum
	F = fft.fft(y)
	N = len(t)             # number of samples
	dt = 0.001             # inter-sample time difference
	w = fft.fftfreq(N, dt)     # gives us a list of frequencies for the FFT
	ipos = where(w>0)
	freqs = w[ipos]        # only look at positive frequencies
	mags = abs(F[ipos])    # magnitude component
	phase = imag(F[ipos])  # phase component
	ip = where(F>5)[0]     # find peaks in FFT
	Fs = copy(F)           # make a copy of the signal FFT
	Fs[ip[[2,3]]] = 0      # set peaks corresponding to 

	yf = fft.ifft(Fs)          # reconstruct
	ip = where(F>5)[0]     # find peaks in FFT
	Ff = copy(F)           # make a copy of the signal FFT
	Ff[ip[[1,2,3,4]]] = 0  # set 10Hz and 13Hz peaks to zero
	magsf = abs(Ff[ipos])  # magnitude component
	phasef = imag(Ff[ipos])# phase component
	yf = fft.ifft(Ff)          # reconstruct
	
	yr = fft.ifft(F)
	
{% endcodeblock %}


{% img center /images/figure_3.png 300 300 'image' 'image' %}

