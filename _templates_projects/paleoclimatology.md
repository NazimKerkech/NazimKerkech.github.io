---
layout: page
title: Searching for periodicities in irregularly sampled signals in paleoclimatology
description: 
img: assets/img/signal_paleoclimatologie.jpg
importance: 1
category: work
related_publications: true

bibliography: 2020-06-30-SAT.bib
---
Paleoclimatology is a field of climatology that studies the Earth's climate through geological times. To do this, it exploits - among other things - data collected from sedimentary soil cores. In particular, it is possible to calculate the concentration of carbon dioxide present in the atmosphere at at the formation of the sedimentary layer from air bubbles trapped in theses sediments.

One of the challenges in paleoclimatology is to try to find periodicities in climate variations from data collected in core samples.

However, sedimentary layers do not form regularly over time. The data thus collected are therefore irregularly sampled. Nevertheless, it is still possible to date the geological layers from which the samples are taken.

In the article, the authors attempted to address this problem. They had real and dated samples (i.e., they knew the geological time of the formation of the layer of each sample) collected from cores on which they applied spectral analysis methods to search for periodicities. In Figure 1, we can see this data. The x-axis represents time in ky (thousands of years) and the y-axis represents the concentration of carbon dioxide (CCnb).

Échantillons réels recueillis sur des carottes
Figure 1: Real samples collected from cores

To verify the results of this article, we will synthesize a periodic signal with the frequencies obtained in the article, which we will window at several locations and to which we will add noise. We will then irregularly sample it according to the real signal's time vector. Then, we will apply a set of methods to try to recover the original frequencies.

For this, we will try two approaches:

    Without resampling:
    where we will apply methods for detecting periodicity in irregularly sampled signals: irregular periodogram, Lomb-Scargle periodogram, and Sparspec.
    With resampling:
    where we will apply methods for detecting periodicity in regularly sampled signals: periodogram, Multi Taper Method (MTM), spectrogram.

To verify the results obtained with real data published in an article, we synthesized a signal consisting of a sum of sinusoids (with the same frequencies as those found in the article (1/12, 1/23, and 1/54)) windowed by Gaussians positioned at different locations and corrupted with Gaussian white noise of variance 1 (high) and 0.2 (low).

x(t)=cos⁡(2πf1t)⋅w1(t)+cos⁡(2πf2t)⋅w2(t)+cos⁡(2πf3t)⋅w3(t)+b(t)x(t)=cos(2πf1​t)⋅w1​(t)+cos(2πf2​t)⋅w2​(t)+cos(2πf3​t)⋅w3​(t)+b(t)

By calculating its theoretical Power Spectral Density (PSD), we find:

$$S_x(f)=14[∣W_1(f−f1)∣2+∣W_2(f−f2)∣2+∣W_3(f−f3)∣2]S_x​(f)=41​[∣W_1​(f−f1​)∣2+∣W_2​(f−f2​)∣2+∣W_3​(f−f3​)∣2]$$

Where WiWi​ are the Fourier Transforms of the windows wiwi​. We sampled the signal following the time vector of the real data. We used the correlation coefficient (denoted as rr) between this theoretical PSD and the PSD estimated by the different methods as a criterion for the quality of the estimates.

Two approaches:

    Without resampling: Lomb-Scargle Periodogram (r=0.65 for low noise and 0.44 for high noise)
    With resampling (linear interpolation or spline):
        Periodogram: (r=0.85 for low noise and 0.70 for high noise with linear interpolation, and r=0.86 for low noise and 0.65 for high noise with spline interpolation)
        Burg's method (estimation of parameters of an AR model of order 40): (r=0.70 for low noise and 0.68 for high noise with linear interpolation, and r=0.69 for low noise and 0.66 for high noise with spline interpolation)

In conclusion, the method that yielded the best results was computing the periodogram of the resampled signal with linear interpolation (r=0.85) (see figure above).

Technologies used: Matlab.