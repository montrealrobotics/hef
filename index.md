---
layout: default
title: Harmonic Exponential Filter
permalink: index.html
use_math: True
carousels:
- images:
      - image: "img/moving_banana.gif"
      - image: "img/banana.png"
---
<!--Actual Page-->
<h1 style="text-align: center; font-family: Helvetica, sans-serif">The Harmonic Exponential Filter for Nonparametric Estimation on Motion Groups</h1>

<!--Venue-->
<h4 style="text-align: center; font-family: Helvetica, sans-serif, font-weight: bold">IEEE Robotics and Automation Letters (RA-L 2025)</h4>

<!--Authors-->
{% include_relative _relative_includes/authors.html %}

<!--Badges-->
<br>
{% include_relative _relative_includes/badges.html %}
<br>

<!--Teaser Video-->
<a id="main_video"></a>
{% include_relative _relative_includes/main_video.html %}
<br>

<!--Abstract-->
[//]: # (Abstract)
<div style="text-align: center; width: 85%; margin:auto">
<p style='text-align: justify; font-style: italic'> 
<a style="font-weight: bold"> Abstract. </a>Bayesian estimation is a vital tool in robotics as it allows systems to update the robot state belief using incomplete information from noisy sensors. To render the state estimation problem tractable, many systems assume that the motion and measurement noise, as well as the state distribution, are all unimodal and Gaussian. However, there are numerous scenarios and systems that do not comply with these assumptions. Existing nonparametric filters that are used to model multimodal distributions have drawbacks that limit their ability to represent a diverse set of distributions. This paper introduces a novel approach to nonparametric Bayesian filtering on motion groups, designed to handle multimodal distributions using harmonic exponential distributions. This approach leverages two key insights of harmonic exponential distributions: a) the product of two distributions can be expressed as the element-wise addition of their log-likelihood Fourier coefficients, and b) the convolution of two distributions can be efficiently computed as the tensor product of their Fourier coefficients. These observations enable the development of an efficient and asymptotically exact solution to the Bayes filter up to the band limit of a Fourier transform. We demonstrate our filter's superior performance compared with established nonparametric filtering methods across a range of simulated and real-world localization tasks.</p>
</div>

## About
<p style="text-align: justify;">
    In this page we present an <em>intuitive</em> introduction to our Harmonic Exponential filter, as well as additional videos. For further information and a more detailed explanation, please refer to <a href="https://github.com/montrealrobotics/harmonic-filter">our paper!</a>
</p>

## Task
<p style="text-align: justify;">
    In this work, we address the problem of non-parametric Bayesian filtering for multimodal distributions. The goal is to estimate the posterior belief $bel(x_t) = p(x_t\vert z_{1:t},u_{1:t})$ over the state $x_t$ of an agent at time $t$, given a sequence of noisy measurements $z_{1:t}$ and controls $u_{1:t}$. We consider the case where the state space is defined on a motion group, such as $SE(2)$, with a focus on tasks involving multimodal localization from range-only and bearing-only sensors.
</p>

## Method
<p style="text-align: justify;">
    We propose the Harmonic Exponential Filter (HEF), an asymptotically exact approach to computing the posterior belief $p(x_t\vert z_{1:t},u_{1:t})$ of the Bayes filter on a compact Lie group, for band limited prior $p(x_{t-1}\vert z_{1:t-1},u_{1:t-1})$, motion $p(x_t \vert u_t, x_{t-1})$, and measurement likelihood $p(z_t \vert x_t)$. The key idea of our filter is to leverage the harmonic exponential distribution, a class of probability distributions supported on groups and homogeneous spaces and whose parameters are the <em>Fourier coefficients</em> of the log-density function. To preserve the structure of motion groups, we leverage a generalized Fourier transform that allows the efficient computation of both the convolution and product of two distributions. These two insights allow us to compute the prediction and update step as follows.
</p>
<ul>
  <li><a style="font-weight: bold">Prediction step</a>: Defining the motion model as $x_t = x_{t-1} \circ u_t$, where $u_t$ is a random variable with a known density and $\circ$ a composition operator, the relative motion can be calculated as $x^{-1}_{t-1} \circ x_t = u_t$. Hence, the integral $\overline{bel}(x_t) = \int_{SE(N)} p(x_t \vert u_t, x_{t-1}) bel(x_{t-1}) \, dx_{t-1}$ can be written as a convolution, which we efficiently solve using the Fourier convolution theorem.</li>
  <li><a style="font-weight: bold">Update step</a>: Given a predicted belief and measurement likelihood, both defined with the density of the harmonic exponential distribution, the update step is computed via an element-wise addition of the parameters of the two densities.</li>
  <li><a style="font-weight: bold">Normalization</a>: We can compute the normalization constant using Fourier analysis and retrieve the posterior belief.</li>
</ul>

<p style="text-align: justify;">
    To showcase the effectiveness of our approach capturing real-world distributions, we model the <em>banana-shape distribution</em> with our filter. This distribution appears when the uncertainty in the heading of an agent increases due to the accumulation of error during multiple prediction steps. Starting from a rectangular prior and a Gaussian motion likelihood, we take steps in a horizontal straight path to the right. As expected, the banana-shape distribution emerges after few iterations.
</p>

<br>
{% include_relative _relative_includes/banana_gif.html %}
<br>

<p style="text-align: justify;">
    We compare the ability of our HEF capturing the banana-shape distribution against four other well established filtering approaches in the literature. The results highlight how only the HEF, Invariant EKF and particle filter are capable of modeling it.
</p>

<br>
{% include_relative _relative_includes/banana_comparison.html %}
<br>

<p style="text-align: justify;">
    We demonstrate how the HEF effectively models both continuous and discrete densities, comparing it to a histogram-based approach. While the histogram excels at capturing <em>discontinuous</em> square-like densities, the HEF performs better at modeling continuous densities, as evidenced by a lower Kullback–Leibler divergence. As motion and measurement models are typically smoothly-varying functions, the HEF is a good candidate for many real-world robotics applications. 
</p>

<br>
{% include_relative _relative_includes/row_tables_x2.html title_1="Mixture of Von mises Densities" title_2="Square-like Density" src_1="img/von_mise_reconstruction.png" src_2="img/square_reconstruction.png" %} 
<br>

<p style="text-align: justify;">
    Lastly, we present a runtime comparison between a histogram-based approach and the HEF for computing the convolution of two functions. Hist (PP) is a direct convolution written in pure python. Hist (Scipy) uses the $\texttt{scipy.signal.convolve()}$ function, and the HEF, implemented in python using the Numpy FFT library.
</p>

<br>
{% include_relative _relative_includes/runtime_table.html %}
<br>

<p style="text-align: justify;">
    The results show how the histogram approach quickly becomes intractable while ours scale more gracefully.
</p>

## Experimental setup
<p style="text-align: justify;">
    All our experiments focus on two main metrics. The first metric is the absolute trajectory error (ATE), calculated using two different estimators of the robot position: the mode and the mean of the posterior belief. The ATE, measured in meters, is calculated along the entire trajectory, and we also report its standard deviation. However, as this metric does not entirely encapsulate the non-Gaussian nature of these estimators, we also calculate the negative log-posterior (NLP) of the ground truth position under the current belief. In these assessments, our proposed HEF is compared against the following filtering approaches: an EKF implementation that assumes unimodal Gaussian state and error, a histogram filter (HistF), and a particle filter (PF).
</p>

## Simulation experiments
<p style="text-align: justify;">
    The simulation experiments use range-only measurements. This environment includes a series of landmarks arranged in a line. The robot travels in a counterclockwise circle around the landmarks. The landmarks alternate in providing a range measurement at each timestamp, where we assume known correspondence. All experiments but the EKF start from a bi-modal prior.
</p>

<p style="text-align: justify;">
    We showcase the performance of HEF in this localization task and show the predicted belief, $\overline{bel}(x_t)$, measurement likelihood, $p(z_t \vert x_t)$, and posterior belief, $bel(x_t)$, at each timestamp alongside its posterior mean estimate (left). Similarly, we show the uncertainty of the HEF and the baselines (right).
</p>

<br>
{% include_relative _relative_includes/simulator_video.html %}
<br>

<p style="text-align: justify;">
    The quantitative results of this experiment are presented in the following table, where metrics are computed over 10 different random seeds. Overall, our filter is comparable if not better than the baselines.
</p>

<br>
{% include_relative _relative_includes/simulator_table.html %}
<br>

## UWB experiments
<p style="text-align: justify;">
    For the UWB experiments, we used a Clearpath Husky unmanned ground vehicle (UGV). The UGV is equipped with an ultra-wideband (UWB) tag that communicates with stationary UWB beacons in the surrounding room. Using a ranging protocol between the UGV-mounted UWB tag and the stationary UWB beacons, range measurements between each are computed. The experiment consists of two runs, each with a total of five beacons that were premapped prior to the experiment. For each run, we removed beacons one by one by ignoring their range measurements, producing a total of five trials per run. We assume known correspondence. The motion of the robot is estimated from noisy odometry and IMU data.
</p>

<br>
{% include_relative _relative_includes/landmarks.html %}
<br>

<p style="text-align: justify;">
    We present the performance of the HEF and the baselines during the two runs, with different landmark configurations. The left column are from the first run and the right one from the second run.
</p>

<br>
{% include_relative _relative_includes/row_videos_x2.html title_1="Run 1 - Five landmarks" title_2="Run 2 - Five landmarks" src_1="img/uwb/run1_5landmarks.mp4" src_2="img/uwb/run2_5landmarks.mp4" %}
{% include_relative _relative_includes/row_videos_x2.html title_1="Run 1 - Four landmarks" title_2="Run 2 - Four landmarks" src_1="img/uwb/run1_4landmarks.mp4" src_2="img/uwb/run2_4landmarks.mp4" %}
{% include_relative _relative_includes/row_videos_x2.html title_1="Run 1 - Three landmarks" title_2="Run 2 - Three landmarks" src_1="img/uwb/run1_3landmarks.mp4" src_2="img/uwb/run2_3landmarks.mp4" %}
{% include_relative _relative_includes/row_videos_x2.html title_1="Run 1 - Two landmarks" title_2="Run 2 - Two landmarks" src_1="img/uwb/run1_2landmarks.mp4" src_2="img/uwb/run2_2landmarks.mp4" %}
{% include_relative _relative_includes/row_videos_x2.html title_1="Run 1 - One landmarks" title_2="Run 2 - One landmarks" src_1="img/uwb/run1_1landmarks.mp4" src_2="img/uwb/run2_1landmarks.mp4" %}
<br>

<p style="text-align: justify;">
    The quantitative evaluation in this real-world experiments highlights the performance of our filter.
</p>

<br>
{% include_relative _relative_includes/row_tables_x2.html title_1="Five beacons" title_2="Four beacons" src_1="img/uwb/table_uwb5.png" src_2="img/uwb/table_uwb4.png" %}
{% include_relative _relative_includes/row_tables_x2.html title_1="Three beacons" title_2="Two beacons" src_1="img/uwb/table_uwb3.png" src_2="img/uwb/table_uwb2.png" %}
{% include_relative _relative_includes/uwb_table1.html %}
<br>

## Doors' experiments
<p style="text-align: justify;">
    This experiment consists of localizing an agent navigating in an anticlockwise direction in a corridor using doors detected in RGB images as landmarks. All doors share similar semantic features, such as color or handle. Therefore, we do not assume known correspondence on which of the 21 doors generated a given measurement. We premapped the environment using gmapping and assumed a bearing-only measurement model. Bearing measurements are computed using the centroid of the doors. We integrate the IMU and wheel encoders to estimate the motion of a Jackal platform.
</p>

<br>
{% include_relative _relative_includes/doors_video.html %}
<br>

<p style="text-align: justify;">
    The quantitative results of this experiments are presented below.
</p>

{% include_relative _relative_includes/doors_table.html %}
<br>

## Citation
{% include_relative _relative_includes/citation.html %}

<!-- Carousel Images 
<div style="margin: auto; width: 85%">
    {% include carousel.html height="75" unit="%" duration="4" number="1" %}
</div>
-->
