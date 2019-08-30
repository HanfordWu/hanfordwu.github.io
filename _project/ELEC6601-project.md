---
layout: project_single
title: "ELEC6601: Digital Signal Processing"
use_math: true
date: 2019-08-15
slug: "ELEC6601"
---

We don't have a whole project for this session. Professor hope we having time to enjoy this summer, it's so kind of professor. Instead, we have five Matlab assignments on digital signal processing. 

#### Lab1:

###### <img src="{{site.baseurl}}/assets/img/6601-lab1.png">

##### Q1:

```matlab
clear all; 
w0 = 0.02*pi; a = 0.5; n = 0:150; 
y1 = (exp(1i*w0.*n) -a.^n )/ (2*(1-a*exp(-1i*w0))); y2 = (exp(-1i*w0.*n) -a.^n )/ (2*(1-a*exp(1i*w0))); 
y = y1+y2; stem(n,y); 
title('Sequence of y[n]'); 
xlabel('n'); ylabel('y[n]');
```

*Result:*

<img src="{{site.baseurl}}/assets/img/lab1_question3rd.jpg">

##### Q2:

```matlab
clear all; 
w0 = 0.02*pi; a = 0.5; n = 0:150; 
y(n+3)=0.5*y(n)+cos(w0*n)
stem(n,y);
title('Sequence of y[n]'); 
xlabel('n'); ylabel('y[n]');
```

*Result:*

<img src="{{site.baseurl}}/assets/img/lab1_question3rd.jpg">

*Comment:*

- The result is the same as Q1, they are different way to get output.
-  The transient response goes to zero with the N increasing. Also, this means the system differential equation expressed is stable.

##### Q3:

```matlab
clear all; 
w0 = 0.5*pi; a = 0.5; n = 0:150; 
y1 = (exp(1i*w0.*n) -a.^n )/ (2*(1-a*exp(-1i*w0))); 
y2 = (exp(-1i*w0.*n) -a.^n )/ (2*(1-a*exp(1i*w0))); 
y = y1+y2; stem(n,y); 
title('Sequence of y[n](a=0.5, W0=0.5*pi)'); xlabel('n'); ylabel('y[n]');
```

*Result:*

<img src="{{site.baseurl}}/assets/img/closed-form expression.jpg">

#### Lab2:

<img src="{{site.baseurl}}/assets/img/6601-lab2.png">

**Q1**:  

```matlab
clear all;
A=[0.15 0 -0.15]; % from the transfer function we get vector 
B=[1 0.5 0.7];	 % A and B, they are from numerator and dominator
[H,w]=freqz(A,B,'whole'); % use freqz function get the freqency response H, and angular frequecies from 0 to 2pi
subplot(2,1,1);
plot(w/pi,abs(H));
title('Amplitude');
xlabel('\omega/\pi');
ylabel('|H(e^j^\omega)|');
grid on;
subplot(2,1,2);
plot(w/pi,angle(H));
title('Phase');
xlabel('\omega/\pi');
ylabel('pha(rad)');
grid on;
```

The result is following:

<img src="{{site.baseurl}}/assets/img/lab2_question1.png">

From its Amplitude response, it's an approximate band pass filter.

**Q2**: 

##### System 1:

Method 1:  

```matlab
clear all;
%%First, we use function 'freqz' to get the frequency %%response from the differential equation:
n=0:300;
A=[0.5 0.27 0.77];
B=1;
[H,w]=freqz(A,B,'whole');
```

We treat input x[n] as two parts: 

1. $\cos(20\pi n/256)$  
2. $\cos(200\pi n256)$

```matlab
% H has 512 elements from 0 to 2pi,20pi/256 to the 20th 
% element, 200pi/256 to the 200th element.
h1=H(20); 
h2=H(200);
% Corresponding outputs are amplitude change and phase shift
y1=abs(h1)*cos(20*pi*n/256+angle(h1));
y2=abs(h2)*cos(200*pi*n/256+angle(h2));
% Add two parts' output together, getting the overall output
y=y1+y2;
stem(n,y);
```

Result:

<img src="{{site.baseurl}}/assets/img/lab2_question2_sys1_method1.png">

Method 2: Iterating to generate output sequence

```matlab
clear all;
x(1)=0;x(2)=0;

for n=0:300
x(n+3)=cos(20*pi*n/256)+cos(200*pi*n/256);
y(n+3)=0.5*x(n+3)+0.27*x(n+2)+0.77*x(n+1);
end
m=-2:300;
stem(m,y);
xlim([0 300]);
```

Result:

<img src="{{site.baseurl}}/assets/img/lab2_question2_sys1_method2.png">

**Difference of two method:**

From results of two method, it's not so clear what's the difference, but from the data we generate as following:

|         | n=-2 | n=-1 | n=0      | n=1     | n=2      | n=3      |
| ------- | ---- | ---- | -------- | ------- | -------- | -------- |
| Method1 | -    | -    | 1.875522 | 0.80671 | 2.128327 | 1.033714 |
| Method2 | 0    | 0    | 1        | 0.63851 | 2.131701 | 1.048673 |

At the beginning of the output sequence, there is a greater difference between two method. With N increasing, two results become to the same.

This is because the first result we get is the steady-state response of the system. The second result we are using 'suddenly input', there will be a transient response  at the beginning, which is added on steady-state response. With N increasing, transient response disappears for stable system, remaining only steady-state response, then output should be the same as Method 1.

##### system 2:

Method 1:

```matlab
clear all;
n=0:300;
A=[0.45 0.5 0.45];
B=[1 -0.53 0.46];
[H,w]=freqz(A,B,'whole');
h1=H(20);
h2=H(200);
y1=abs(h1)*cos(20*pi*n/256+angle(h1));
y2=abs(h2)*cos(200*pi*n/256+angle(h2));
y=y1+y2;
stem(n,y);
```

Result:

<img src="{{site.baseurl}}/assets/img/lab2_question2_sys2_method1.png">

Method 2:

```matlab
clear all;
x(1)=0;x(2)=0;
y(1)=0;y(2)=0;
for n=0:300
x(n+3)=cos(20*pi*n/256)+cos(200*pi*n/256);
y(n+3)=0.45*x(n+3)+0.5*x(n+2)+0.45*x(n+1)+0.53*y(n+2)-0.46*y(n+1);
end
m=-2:300;
stem(m,y);
xlim([-2 300]);
```

The result:

<img src="{{site.baseurl}}/assets/img/lab2_question2_sys2_method2.png">

**Difference of two system:**

<img src="{{site.baseurl}}/assets/img/difference of two system1.png">

From their frequency response. System 1 is approximate to a band-stop filter. System 2 is approximate to a low-pass filter, it has less high frequency components.

**Q3**:

Part a: 

From the impulse response we have, we can get the impulse response sequence. After we get the impulse response from 0 to 19, we get the coefficient of the filter. Using these coefficients to be parameters of 'freqz' function, we can get the frequency response of filter.

```matlab
clear all;
wc = 0.3*pi;
L=19;
nd = (L-1)/2;
n = 0:1:(L-1);
m = n - nd + eps; % add smallest number to avoid divide by 					  % zero
h = sin(wc*m) ./ (pi*m);
[H,w]=freqz(h,1,'whole');
subplot(2,1,1);
plot(w/pi,abs(H));
title('Amplitude');
xlabel('\omega/\pi');
ylabel('|H(e^j^\omega)|');
grid on;
subplot(2,1,2);
plot(w/pi,angle(H));
title('Phase');
xlabel('\omega/\pi');
ylabel('pha(rad)');
grid on;
```

Result of L=19:

<img src="{{site.baseurl}}/assets/img/lab2_question3_L=19.png">

And L=31:

<img src="{{site.baseurl}}/assets/img/lab2_question3_L=31.png">

Part b: When system become $(-1)^nh_{FIR}(n)$ :

```matlab
wc = 0.3*pi;
L=19;
nd = (L-1)/2;
n = 0:1:(L-1);
m = n - nd + eps; % add smallest number to avoid divide by 					  % zero
h = (-1).^n.*sin(wc*m) ./ (pi*m);
[H,w]=freqz(h,1,'whole');
subplot(2,1,1);
plot(w/pi,abs(H));
title('Amplitude');
xlabel('\omega/\pi');
ylabel('|H(e^j^\omega)|');
grid on;
subplot(2,1,2);
plot(w/pi,angle(H));
title('Phase');
xlabel('\omega/\pi');
ylabel('pha(rad)');
grid on;
```

The result of L=19:

<img src="{{site.baseurl}}/assets/img/lab2_question3_2_L=19.png">

And L=31:

<img src="{{site.baseurl}}/assets/img/lab2_question3_2_L=31.png">

Therefore, the system $h(n)=(-1)^nh_{FIR}(n)$ basically is a high-pass filter. From theories, $(-1)^n$ means shifting frequency domain to two side of y axis by pi, then low-pass filter become to high-pass filter.

#### Lab3:

<img src="{{site.baseurl}}/assets/img/6601-lab3.png">

**Q1** :

Create a continue-time signal, do Fourier transform, we get the continue signal curve and its frequency spectrum. After that, we create discrete-time signal and its frequency spectrum based on continue-signal with sample rate T=0.0005sec (Fs=2000 Hz) and T= 0.0002 (Fs=5000 Hz):

Fs=2000 Hz:

```matlab
clear all;
				% Continue-time signal
Dt = 0.00005; 
t = 0:Dt:0.005; 
xa = exp(-1000*t);
Omegamax = 2*pi*2000; 
K = 100; 
k = 0:1:K; 
W = k*Omegamax/K;
			% continue-time fourier transform
Xa = xa * exp(-1i*t'*W) * Dt; 
W = [-fliplr(W), W(2:101)];% fliplr is used to copy and 								% then make spectrum symmetry
Xa = [fliplr(Xa), Xa(2:101)];
Xa = abs(Xa);
		% plot continue-time signal and its spectrum
subplot(2,2,1);plot(t*1000,xa); 
xlabel('t in msec.'); ylabel('xa(t)') ;
title('Analog Signal') ;
subplot(2,2,2);
plot(W/(2*pi*1000),Xa);
xlabel('Frequency in KHz'); 
ylabel('Xa(j\Omega)');
title('Continuous-time Fourier Transform');
					% Discrete signal
T=0.0005; fs=1/T; n=0:10;
xn=exp(-1000*n*T);
			% compute dicrete singal spectrum
[H,a]=freqz(xn,1,'whole');
subplot(2,2,3);stem(0:0.5:5,xn); 
xlabel('t in msec'); ylabel('x(n)') ;
title('Discrete Signal (Fs= 2kHz)') ;
subplot(2,2,4);
plot(a*fs/1000/2/pi,abs(H));
xlabel('Frequency in KHz'); 
ylabel('X(e^j^\omega)');
title('Amplitude (Fs= 2kHz)');
ylim([0 3]);
grid on;
```

Result:

<img src="{{site.baseurl}}/assets/img/lab3_Q1_spectrum_fs=2000.png">

Fs=5000 Hz:

```matlab
clear all;
%%%%%%%%% Continue-time signal %%%%%%%%%%%
Dt = 0.00005; 
t = 0:Dt:0.005; 
xa = exp(-1000*t);
Omegamax = 2*pi*2000; 
K = 100; 
k = 0:1:K; 
W = k*Omegamax/K;
%%%%%%%%% continue-time fourier transform %%%%%%%%
Xa = xa * exp(-1i*t'*W) * Dt; 
W = [-fliplr(W), W(2:101)]; % fliplr is used to copy and 								% then make spectrum symmetry
Xa = [fliplr(Xa), Xa(2:101)];
Xa = abs(Xa);
%%%%%%% plot continue-time signal and its spectrum %%%%%%%
subplot(2,2,1);plot(t*1000,xa); 
xlabel('t in msec.'); ylabel('xa(t)') ;
title('Analog Signal') ;
subplot(2,2,2);
plot(W/(2*pi*1000),Xa);
xlabel('Frequency in KHz'); 
ylabel('Xa(j\Omega)');
title('Continuous-time Fourier Transform');
	%%%%%%%%%%% Discrete signal %%%%%%%%%%
T=0.0002; fs=1/T; n=0:25;
xn=exp(-1000*n*T);
	%%%%%%% compute discrete singal spectrum %%%%%%%
[H,a]=freqz(xn,1,'whole');
 %%%%%%%% plot discrete signal sequence and its spectrum %%%
subplot(2,2,3);stem(0:0.2:5,xn); 
xlabel('t in msec'); ylabel('x(n)') ;
title('Discrete Signal (Fs= 2kHz)') ;
subplot(2,2,4);
plot(a*fs/1000/2/pi,abs(H));
xlabel('Frequency in KHz'); 
ylabel('|X(e^j^\omega)|');
title('Amplitude (Fs= 2kHz)');
ylim([0 6]);
xlim([0 5]);
grid on;
```

<img src="{{site.baseurl}}/assets/img/lab3_Q1_spectrum_fs=5000Hz.png">

***Comments***: 

- We see that when Fs=2000 Hz, neighbor spectrum is near to each other, so that they overlap together a lot. 
- With the Fs increasing, when Fs=5000 Hz, neighbor spectrum separate farther away. The overlapping ratio of the whole spectrum decrease, so we can say aliasing decrease. 

But, in this question, $x(t)=e^{-\alpha t}u(t)$ is not an band-limited signal, so, we are not able to eliminate aliasing totally. 

*PS*: I tried using Fs=2k, 5k, 10k and 20k, and plot their spectrum in one plot, but because with the Fs increasing, the whole amplitude of spectrum also increasing (1/T), in this case, it is not clear to show that the aliasing is decreasing. I don't know how to make them at the same scale.

**Q2**

We assume $\omega_1=1000\pi,\omega_2=2000\pi$, $x(t)=cos(\omega_1t)+cos(\omega_2t)$ has the highest frequency $f_h=1000Hz$. We select  $f_s=5000Hz$, so that there is no aliasing.

The filter I am using is function 'fir2' function, to generate the a 10-order FIR digital causal filter, cutting frequency = w2*T/number of interpolation=0.1pi. The frequency response of this FIR filter is following:

<img src="{{site.baseurl}}/assets/img/lab3_Q2_fir.png">

We will use this filter to be an interpolating filter. In order to reconstruct the original signal, FIR filter should have a gain of L (I'm using L=4), however, we see the gain of this FIR filter is 0.5. So, we multiply another 2 at last output.

```matlab
clear all;
%%% Continue-time signal %%%
w1=1000*pi; w2=2000*pi;
t=0:0.00005:0.01;
xt=cos(w1*t)+cos(w2*t);
figure();
subplot(211);
plot(t*1000,xt); 
title('Continue-time Signal');
xlabel('time in ms'); grid on;
%%% Discrete-time signal %%%
T=0.0002; fs=1/T;
n=0:0.01/T;
xn=cos(w1*n*T)+cos(w2*n*T);
subplot(212);
stem(n,xn);
title('Discrete-time Signal(Fs=5kHZ)');
grid on;
%%% Upsampling by itp_n %%%%
itp_n=4;
m=0:itp_n*length(n)-1;
upsam_xn=zeros(1,itp_n*length(xn));
upsam_xn(1:itp_n:length(upsam_xn))=xn;
figure();
subplot(211);
stem(m,upsam_xn);grid on;
title('Upsampled Sequence by 4');
xlim([0,210]);
%%% Create a fir with 10-order and wc=fc %%%%
wc=w2*T/itp_n; L=10;
f=[0 wc/pi wc/pi 1];
mhi=[1 1 0 0];
fir=fir2(L,f,mhi);
%% Interplolation using FIR filter %%
gain=itp_n*2;
itp_xn=conv(upsam_xn,fir);
output=gain*itp_xn(1:length(upsam_xn));
r=0:(length(m)+length(fir))-2;
subplot(212);
stem(m,output);
title('Interpolated Signal After Filter');
xlim([0,210]);grid on;
```

Results:

<img src="{{site.baseurl}}/assets/img/lab3_Q2_beforeinter.png">

***Comments***: 

- The first plot above is the analog signal with 5 period. 
- The second plot above is the sampled discrete signal by using Fs=5k Hz, we can get 50 samples. $x[n]=x_c(n*T)$.

<img src="{{site.baseurl}}/assets/img/lab3_Q2_afterinter.png">

***Comments***: 

- The first plot above is up-sampled sequence, every two samples insert three 0 samples. That means $x_i[n]=x[4*n]=x_c(n*T/4)=x_c(n*T_1)$. 
- The second plot above is the output of the filter, which is interpolated sequence. 
- Because I am using the convolution of filter and signal, we see that the interpolated discrete signal has a distortion at the beginning. After input signal filling into the filter, the output become stable.

***Analog signal reconstruction***:

From the output of the filter, we transform it to analog signal by using 'plot' (further linear interpolation), and change index of n to real time line: $t=n*T_1 (here, T_1=T/itp_-n)$.

```matlab
%% Continue from above code %%
T1=T/itp_n*1000;% time in msec
plot(m*T1,output); grid on;
title('Reconstructed Analog signal');
xlabel('t in msec');
```

Analog signal:

<img src="{{site.baseurl}}/assets/img/lab3_Q2_reconstructed.png">

***Comments***: 

- The reconstructed analog signal has a constant delay because of the filter linear phase response. 
- fir2 filter is not a ideal low-pass filter, so that there are some 

I tried another method: In order to get a better FIR filter, I use truncated (rectangular window) ideal low-pass filter as the interpolating FIR filter: 

$$h_{FIR}(n)=\frac{sin[\omega_c(n-\frac{L-1}{2})]}{\pi(n-\frac{L-1}{2})}$$.    L=19.

 $\omega_c$= w2 * T / number of interpolating = 2000pi * 0.0002s /4 =0.1*pi.

```matlab
clear all;
wc = 0.1*pi; 
L=19;
nd = (L-1)/2
n = 0:1:(L-1);
m = n - nd + eps; % add smallest number to avoid divide by 					  % zero
h = sin(wc*m) ./ (pi*m);
[H,w]=freqz(h,1,'whole');
plot(w/pi,abs(H));
```

Look at the frequency response of this filter:

<img src="{{site.baseurl}}/assets/img/lab3_question3_filter.png">

We use this filter to do convolution with up-sampled signal:

```matlab
clear all;
%%% Continue-time signal %%%
w1=1000*pi; w2=2000*pi;
t=0:0.00005:0.01;
xt=cos(w1*t)+cos(w2*t);
figure();
subplot(211);
plot(t*1000,xt); 
title('Continue-time Signal');
xlabel('time in ms'); grid on;
%%% Discrete-time signal %%%
T=0.0002; fs=1/T;
n=0:0.01/T;
xn=cos(w1*n*T)+cos(w2*n*T);
subplot(212);
stem(n,xn);
title('Discrete-time Signal(Fs=5kHZ)');
grid on;
%%% Upsampling by itp_n %%%%
itp_n=4;
m=0:itp_n*length(n)-1;
upsam_xn=zeros(1,itp_n*length(xn));
upsam_xn(1:itp_n:length(upsam_xn))=xn;
figure();
subplot(211);
stem(m,upsam_xn);grid on;
title('Upsampled Sequence by 4');
xlim([0,210]);
%%% Create a fir with ideal function %%%%
wc=w2*T/itp_n; L=19;
nd = (L-1)/2;
l = 0:1:(L-1);
m1 = l - nd + eps;
fir = sin(wc*m1) ./ (pi*m1);
%% Interplolation using FIR filter %%
gain=itp_n*2;
itp_xn=conv(upsam_xn,fir);
output=gain*itp_xn(1:length(upsam_xn));
r=0:(length(m)+length(fir))-2;
subplot(212);
stem(m,output);
title('Interpolated Signal After Filter');
xlim([0,210]);grid on;
```

Result:

<img src="{{site.baseurl}}/assets/img/lab3_Q2_2.png">

***Comments***:  The output of this FIR filter looks similar to previous one. At some zero points of original discrete signal (n=30, 70, 110, 150, 190), the output  is not accurate. The reason of this can be:

1. I am using rectangular window to truncate ideal low-pass filter. There is a obvious Gibbs effect in the interpolating filter, resulting in distortion around cutting frequency. Therefore, it's not accurate for reconstructing high component of input signal.
2. FIR low-pass filter is not good to use when cutting frequency is very low. Because here we are going to interpolate 3 samples between original samples. In order to avoid aliasing after interpolation, we must have a low cutting frequency filter to kill neighbor frequency (in my case, $w_c=0.1\pi$). The result shows that it's not a good interpolating filter.

**Q3:**

**Part a**

Now we already have the impulse response of the system h[n], we can easily get H(z) by using freqz function.

```matlab
clear all;
n=0:47;
hn1=[-0.025288325 -0.034167931 -0.035752323 -0.016733702...
      0.021602514  0.064938487  0.091002137  0.081894974...
      0.037071157 -0.021998074 -0.060716277 -0.051178658...
      0.007874526  0.084368728  0.126869306  0.094528345...
     -0.012839661 -0.143477028 -0.211829088 -0.140513128...
      0.094601918  0.44138714   0.78587564   1];
%% Using symmetry to get the whole h[n] %%%
hn=[hn1,fliplr(hn1)];
[H,w]=freqz(hn,1,'whole');
figure();
subplot(211);
plot(w/pi,abs(H));
title('Amplitude response');
subplot(212);
plot(w/pi,angle(H));
title('Phase response');
```

Result:

<img src="{{site.baseurl}}/assets/img/lab3_Q3_parta.png">

From its frequency response, it's a low-pass filter.

**Part b**

Multi-phase filter:
$$
h_k[n]=\begin{cases}
h[n+k],\quad n=integer\ multiple\ of\ M \\\\
0,\quad otherwise
\end{cases}
$$
$e_k[n]=h[nM+k]$

$h_k[n]\rarr H_k(z)$, we can get $H_k(z)$ from $h_k[n]$ by using 'freqz' .

```matlab
clear all;
n=0:47;
hn1=[-0.025288325 -0.034167931 -0.035752323 -0.016733702...
      0.021602514  0.064938487  0.091002137  0.081894974...
      0.037071157 -0.021998074 -0.060716277 -0.051178658...
      0.007874526  0.084368728  0.126869306  0.094528345...
     -0.012839661 -0.143477028 -0.211829088 -0.140513128...
      0.094601918  0.44138714   0.78587564   1];
hn=[hn1,fliplr(hn1)];
[H,w]=freqz(hn,1,'whole');
figure();
subplot(211);
plot(w/pi,abs(H));
title('Amplitude response');
subplot(212);
plot(w/pi,angle(H));
title('Phase response');

figure(); subplot(511);
stem(n,hn);
h1n=zeros(1,length(hn));
h2n=zeros(1,length(hn));
h3n=zeros(1,length(hn));
h4n=zeros(1,length(hn));

h1n(1:4:length(hn))=hn(1:4:length(hn));
h2n(2:4:length(hn))=hn(2:4:length(hn));
h3n(3:4:length(hn))=hn(3:4:length(hn));
h4n(4:4:length(hn))=hn(4:4:length(hn));

subplot(512);stem(n,h1n);ylabel('h0[n]');
subplot(513);stem(n,h2n);ylabel('h1[n]');
subplot(514);stem(n,h3n);ylabel('h2[n]');
subplot(515);stem(n,h4n);ylabel('h3[n]');

figure()
[H1,w1]=freqz(h1n,1,'whole');
[H2,w2]=freqz(h2n,1,'whole');
[H3,w3]=freqz(h3n,1,'whole');
[H4,w4]=freqz(h4n,1,'whole');

subplot(221);
plot(w1/pi,abs(H1));
subplot(222);
plot(w2/pi,abs(H2))
subplot(223);
plot(w3/pi,abs(H3));
subplot(224);
plot(w4/pi,abs(H4));

figure()
subplot(221);
plot(w1/pi,angle(H1));
subplot(222);
plot(w2/pi,angle(H2))
subplot(223);
plot(w3/pi,angle(H3));
subplot(224);
plot(w4/pi,angle(H4));

figure();
Hz=abs(H1+H2+H3+H4);
ang=angle(H1+H2+H3+H4);
plot(w/pi,Hz);title('Add all H_k(z) together');
plot(w/pi,ang);title('Add all H_k(z) phase together');
```



***Four sequence:***

<img src="{{site.baseurl}}/assets/img/lab3_Q3_partb1.png">

***Four amplitude-frequency response:***

<img src="{{site.baseurl}}/assets/img/lab3_Q3_partb2.png">

Four $e_k[n]$ frequency response:

<img src="{{site.baseurl}}/assets/img/lab3_Q3_p.png">

Add all $H_k(z)$ together:

<img src="{{site.baseurl}}/assets/img/lab3_Q3_partb_1.png">

It is the same as H(z).

***Four phase-frequency response:***

<img src="{{site.baseurl}}/assets/img/lab3_Q3_partb3.png">

Add all $H_k(z)$ 's phase together:

<img src="{{site.baseurl}}/assets/img/lab3_Q3_partb_2.png">

It's the same as H(z)'s phase.

#### Lab4:

<img src="{{site.baseurl}}/assets/img/6601-lab4.png">

**Q1:**

Now we know two poles at $z=0.9e^{\pm j\pi/4}$, we can get two zeros at $z=\frac{10}{9}e^{\pm j\pi/4}$ because it's all-pass system. From four zeros and poles, we can]); write down the transfer function (Gain=1): 
$$
\frac{Y(z)}{X(z)}=\frac{0.9^2-0.9\sqrt{2}z^{-1}+z^{-2}}{1-0.9\sqrt{2}z^{-1}+(0.9)^2z^{-2}}
$$
From the transfer function, we have a look at the frequency response of this system:

```
clear all;
numerator=[0.9^2 -0.9*sqrt(2) 1];
denomerator=[1 -0.9*sqrt(2) 0.9^2];
[h,w]=freqz(numerator,denomerator,'whole');
subplot(211);
plot(w/pi,abs(h)); xlabel('\omega/\pi');ylim([0 1.1]);
title('Amplitude response');
subplot(212);
plot(w/pi,angle(h));xlabel('\omega/\pi');
title('Phase response');
```

<img src="{{site.baseurl}}/assets/img/lab4_Q1_system.png">

We see that the system has constant gain=1 and non-linear phase.

Next, we get the differential equation: 
$$
y[n]=0.9\sqrt{2}y[n-1]-(0.9)^2y[n-2]+0.9^2x[n]-0.9\sqrt{2}x[n-1]+x[n-2]
$$
I am using above differential equation to generate the output of the system.

**Input $x_1[n]$ and $x_2[n]$ without separating frequency components: **

```
![lab4_Q1_x2_overallinput](D:\Course\ELEC6601\matlab\lab4_Q1_x2_overallinput.png)clear;
x1(1)=0;x1(2)=0;
y1(1)=0;y1(2)=0;
x2(1)=0;x2(2)=0;
y2(1)=0;y2(2)=0;

for n=3:200
x1(n)=cos(6*pi*(n-3)/25)+cos(14*pi*(n-3)/25);
x2(n)=cos(13*pi*(n-3)/50)+cos(33*pi*(n-3)/50);
y1(n)=0.9*sqrt(2)*y1(n-1)-0.9^2*y1(n-2)+0.9^2*x1(n)-0.9*sqrt(2)*x1(n-1)+x1(n-2);
y2(n)=0.9*sqrt(2)*y2(n-1)-0.9^2*y2(n-2)+0.9^2*x2(n)-0.9*sqrt(2)*x2(n-1)+x2(n-2);
end
%% cut the first initial condition %%
y1=y1(3:200);x1=x1(3:200);
y2=y2(3:200);x2=x2(3:200);

%% plot the input and output to compare %%
figure();
subplot(211);plot(x1);title('x1[n]');
subplot(212);plot(y1);title('y1[n]');
figure();
subplot(211);plot(x2);title('x2[n]');
subplot(212);plot(y2);title('y2[n]');
```

In order to compare input and output, I use 'plot' instead of 'stem', because continue curve is easy to compare the distortion.

*Result:*

<img src="{{site.baseurl}}/assets/img/lab4_Q1_x1_overallinputx1.png">

<img src="{{site.baseurl}}/assets/img/lab4_Q1_x2_overallinput.png">

*Comments:*

- At the beginning, there is distortion because of transient response.
- After beginning, outputs still have distortion. Input has two frequency components: $ w_1=\frac{6}{25}\pi $ and $w_2=\frac{14}{25}\pi$.  For all-pass system, the amplitude don't change for both frequency. However, from the phase response of the system, we see that two frequency have different phase response, as a result, they are mixed up at the output end. That's the reason of distortion.
- The result and reason of $x_2[n]$ are the same as $x_1[n]$.

If  two sinusoidal sequences in each case are inputted to the system separately:

**For $x_{11}[n]=cos(\frac{4}{25}\pi n)$ and $x_{12}[n]=cos(\frac{16}{25}\pi n)$ :**

```
clear;
x11(1)=0;x11(2)=0;
y11(1)=0;y11(2)=0;
x12(1)=0;x12(2)=0;
y12(1)=0;y12(2)=0;
Nmax=100;
for n=3:Nmax
x11(n)=cos(6*pi*(n-3)/25);
x12(n)=cos(14*pi*(n-3)/25);
y11(n)=0.9*sqrt(2)*y11(n-1)-0.9^2*y11(n-2)+0.9^2*x11(n)-0.9*sqrt(2)*x11(n-1)+x11(n-2);
y12(n)=0.9*sqrt(2)*y12(n-1)-0.9^2*y12(n-2)+0.9^2*x12(n)-0.9*sqrt(2)*x12(n-1)+x12(n-2);
end
%% cut the first initial condition %%
y11=y11(3:Nmax);x11=x11(3:Nmax);
y12=y12(3:Nmax);x12=x12(3:Nmax);
%% plot the input and output to compare %%
figure();
subplot(221);plot(x11);title('x11(n)=cos(6*pi*(n-3)/25)');
subplot(222);plot(x12);title('x12(n)=cos(14*pi*(n-3)/25)');
subplot(223);plot(y11);title('y11(n)');
subplot(224);plot(y12);title('y12(n)');
```

*Result:*

<img src="{{site.baseurl}}/assets/img/lab4_Q1_x1_separateput.png">

*Comments:*

Except distortion at the beginning because of transient response, there is no distortion. Because the input has only one frequency, the phase delay doesn't result in distortion.

**For $x_{21}[n]=cos(\frac{13}{50}\pi n)$ and $x_{22}[n]=cos(\frac{33}{50}\pi n)$ :**

```
clear;
x21(1)=0;x21(2)=0;
y21(1)=0;y21(2)=0;
x22(1)=0;x22(2)=0;
y22(1)=0;y22(2)=0;
Nmax=100;
for n=3:Nmax
x21(n)=cos(13*pi*(n-3)/50);
x22(n)=cos(33*pi*(n-3)/50);
y21(n)=0.9*sqrt(2)*y21(n-1)-0.9^2*y21(n-2)+0.9^2*x21(n)-0.9*sqrt(2)*x21(n-1)+x21(n-2);
y22(n)=0.9*sqrt(2)*y22(n-1)-0.9^2*y22(n-2)+0.9^2*x22(n)-0.9*sqrt(2)*x22(n-1)+x22(n-2);
end
%% cut the first initial condition %%
y21=y21(3:Nmax);x21=x21(3:Nmax);
y22=y22(3:Nmax);x22=x22(3:Nmax);
%% plot the input and output to compare %%
figure();
subplot(221);plot(x21);title('x21(n)=cos(13*pi*(n-3)/50)');
subplot(222);stem(x22);title('x22(n)=cos(33*pi*(n-3)/50)');
subplot(223);plot(y21);title('y21(n)');
subplot(224);stem(y22);title('y22(n)');ylim([-1 1]);
```

*Result:*

<img src="{{site.baseurl}}/assets/img/lab4_Q1_x2seperate.png">

*Comments:*

Except distortion at the beginning because of transient response, there is no distortion. Because the input has only one frequency, the phase delay doesn't result in distortion.

**Q2:**

From description, we assume:
$$
H_A(z^2)=H_{a1}(z)\times H_{a2}(z)\\H_{a1}(z)=\frac{0.1899+z^{-2}}{1+0.1899z^{-2}}\\H_{a2}(z)=\frac{0.86+z^{-2}}{1+0.86z^{-2}}
$$
and: 
$$
H_b(z)=z^{-1}H_B(z^2)=\frac{0.5517z^{-1}+z^{-3}}{1+0.5517z^{-2}}
$$
we use $H_{a1}(z)$, $H_{a2}(z)$, $H_{b}(z)$ three transfer function and 'freqz' to generate all the frequency response.

```
clear;
%%% Vector of H_a1 %%%
numa1=[0.1899 0 1];
denma1=[1 0 0.1899];
%%% Vector of H_a2 %%%
numa2=[0.86 0 1];
denma2=[1 0 0.86];
%%% Vector of H_b %%%
numb=[0 0.5517 0 1];
denmb=[1 0 0.5517];
%%% frequency response of three transfer function %%%
[Ha1,wa1]=freqz(numa1,denma1,'whole');
[Ha2,wa2]=freqz(numa2,denma2,'whole');
[Hb,wb]=freqz(numb,denmb,'whole');
%using convolution property, do frequecy response operation%
H1=Ha1.* Ha2 + Hb;
H2=Ha1.* Ha2 - Hb;
%% plotting H1 and H2 frequency response %%
subplot(211);
plot(wa1/pi,abs(H1));
ylabel('|H_1(e^j^\omega)|');xlabel('\omega/\pi');
title('Amplitude Response of Filter\_1');
subplot(212);
plot(wa1/pi,abs(H2));
ylabel('|H_2(e^j^\omega)|');xlabel('\omega/\pi');
title('Amplitude Response of Filter\_2');
```

*Result:* 

<img src="{{site.baseurl}}/assets/img/lab4_Q2.png">

**Comments**:

- Exactly as their name, they are complementary in terms of passband. $H_1(z)$ is a low-pass filter, whereas $H_2(z)$ is a high-pass filter. They have the same cutting frequency.
- As the description says, these two filters are consist of all-pass system, so that they share the same parameters, except changing the addition to subtraction, we can reduce cost to implement each other.
- Explanation about gain of 2: Because the all pass filter we are using is gain of 1. In the implementation, we add two all pass system output together, so we must have a gain of 2 output.

**Q3:**

First, we design a digital filter from prototype analog Butterworth filter. In Matlab, 'buttap()' function creates an analog Butterworth filter with 3 dB gain error at Ωc=1 radian. This is what we exactly want. Then use bilinear transformation to get the transfer function of corresponding digital low-pass filter:

```
clear;
T=2;fs=1/T;Wc=1;N=5;
%%% create prototype Butterworth filter %%%
[z_lowpro,p_lowpro,k] = buttap(N);

%%% transfer zero-pole to transfer fucntion %%%
[num_lowpro,den_lowpro] = zp2tf(z_lowpro,p_lowpro,k);

%%% use bilinear transfermation to get digital low-pass %%
%%% filter's transfer function %%%
[numdigital_low,dendigital_low] = bilinear(num_lowpro,den_lowpro,fs);

%%% get the frequecy response and group delay of filter %%%
[H_lowpro,w_lowpro]=freqz(numdigital_low,dendigital_low,'whole');
[gd,w] = grpdelay(numdigital_low,dendigital_low);

subplot(211);
plot(w_lowpro/pi,abs(H_lowpro));
title('Amplitude Response of Low-pass Filter');
subplot(212);
plot(w/pi,gd);
title('Group delay of Low-pass Filter');
```

***Result:*** 

<img src="{{site.baseurl}}/assets/img/lab4_Q3part1.png">

The transfer function of this 5-order low-pass filter's transfer function is:

<img src="{{site.baseurl}}/assets/img/lab4_Q3_1.png">

Next, we try to find two all-pass system to implement low-pass filter above:

<img src="{{site.baseurl}}/assets/img/lab4_Q3_2.png">

**Now we've got the required all-pass systems' transfer function: $H_A(z^2)$ and $H_B(z^2)$! **



***The following is that I'm trying to make use professor's hint.***

***First***: we find the 'complementary' high-pass filter of previous low-pass filter.

If $H_H(s)=H_L(1/s)$, the poles of $H_H(s)$ will be reciprocal of $H_L(s)$'s poles. Also, $H_H(s)$ has extra N zeros at the origin. From prototype low-pass filter, we get its zeros and poles, then make its poles reciprocal, and add N=5 zeros to its zero vector. After doing bi-linear transformation, we can get the transfer function of the 'complementary' digital high-pass filter:

```
clear;

T=2;fs=1/T;Wc=1;N=5;
%% get the zero and poles of low-pass analog filter %%%
[z_lowpro,p_lowpro,k] = buttap(N);

%% get the zero and poles of low-pass analog filter %%%
z_highpro=zeros(5,1);
p_highpro=1./p_lowpro;

%% transform zero-poles to transfer function %%
[num_lowpro,den_lowpro] = zp2tf(z_lowpro,p_lowpro,k);
[num_highpro,den_highpro] = zp2tf(z_highpro,p_highpro,k);
 %% plot low and high pass filter's amplitude response %%
[h1,w1]=freqs(num_lowpro,den_lowpro);
[h2,w2]=freqs(num_highpro,den_highpro);
subplot(211);
plot(w1,abs(h1));hold on;
plot(w2,abs(h2)); 
title('Complementary Analog filter');xlabel('radian');
legend({'low-pass','high-pass'},'Location','northeast');

%% using bilinear transformation to get digital filter %%
[numdigital_low,dendigital_low] = bilinear(num_lowpro,den_lowpro,fs);
[numdigital_high,dendigital_high] = bilinear(num_highpro,den_highpro,fs);

%% plot digital filter amplitude response %%
[H_l,w_l]=freqz(numdigital_low,dendigital_low,'whole');
[H_h,w_h]=freqz(numdigital_high,dendigital_low,'whole');
subplot(212);
plot(w_l/pi,abs(H_l));hold on;
plot(w_h/pi,abs(H_h));
title('Complementary Digital filter');xlabel('\omega/\pi');
legend({'low-pass','high-pass'},'Location','best');
```

<img src="{{site.baseurl}}/assets/img/lab4_Q3part2.png">

***Second:*** Based on Question2, we find two all-pass systems: *sysA* and *sysB*.

$H_L(z)=H_A(z^2)+z^{-1}H_B(z^2)$ and $H_H(z)=H_A(z^2)-z^{-1}H_B(z^2)$ 

So, $H_L(z)+H_H(z)=2\times H_A(z^2)$ and $H_L(z)-H_H(z)=2\times z^{-1}H_B(z^2)$

In order to find $H_B(z^2)$ from $z^{-1}H_B(z^2)$, we need add a coefficient 0, to denominator vector of  $z^{-1}H_B(z^2)$.

```
clear;

T=2;fs=1/T;Wc=1;N=5;
[z_lowpro,p_lowpro,k] = buttap(N);

%% from low-pass zeros and poles to get high-pass zeros and %% poles %%
z_highpro=zeros(5,1);
p_highpro=1./p_lowpro;

[num_lowpro,den_lowpro] = zp2tf(z_lowpro,p_lowpro,k);
[num_highpro,den_highpro] = zp2tf(z_highpro,p_highpro,k);

%% bilinear transformation %%
[numdigital_low,dendigital_low] = bilinear(num_lowpro,den_lowpro,fs);
[numdigital_high,dendigital_high] = bilinear(num_highpro,den_highpro,fs); 

sys1=tf(numdigital_low,dendigital_low,0.1);
sys2=tf(numdigital_high,dendigital_high,0.1);
sysA=sys1+sys2;
sysB=sys1-sys2;

%% to cancel overlapped zeros&poles %%%
sysA=minreal(sysA);
sysB=minreal(sysB);
%%% to extract polynomial vector from system %%
numA=sysA.num{1,1}; denA=sysA.den{1,1};
numB=sysB.num{1,1}; denB=sysB.den{1,1};
denB=[0,denB];%% add one zero to get HB(z)'s denominator
 
[HA,wA]=freqz(numA,denA,'whole');
[HB,wB]=freqz(numB,denB,'whole');

subplot(221);
plot(wA/pi,abs(HA));ylim([0 1.1]);
title('All-pass sysA Amplitude');xlabel('\omega/\pi');
subplot(222);
plot(wB/pi,angle(HA));
title('All-pass sysA Phase');xlabel('\omega/\pi');
subplot(223);
plot(wA/pi,abs(HB));ylim([0 1.1]);
title('All-pass sysB Amplitude');xlabel('\omega/\pi');
subplot(224);
plot(wB/pi,angle(HB));
title('All-pass sysB Phase');xlabel('\omega/\pi');
%% verification of implementation %%
[Hz,wz]=freqz([0 1],1,'whole'); %% generate z^-1 response %%
H1=HA+HB.*Hz;
H2=HA-HB.*Hz;
figure();
plot(wB/pi,abs(H1));hold on;
plot(wB/pi,abs(H2));xlabel('\omega/\pi');
title('Implemented Two Fitlers');
legend({'low-pass','high-pass'},'Location','best');
```

***Result:***

<img src="{{site.baseurl}}/assets/img/lab4_Q3part21.png">

All-pass sysA transfer function is:

<img src="{{site.baseurl}}/assets/img/lab4_Q3_3.png">

All-pass sysB transfer function is: *(please zoom-in to check)*

<img src="{{site.baseurl}}/assets/img/lab4_Q3_4.png">

To verify the implemented system response (do the same thing in Q2):

<img src="{{site.baseurl}}/assets/img/lab4_Q3part22.png">

***Comments:***

- The implemented low-pass and high-pass filters, by using two all-pass filter: *sysA* and *sysB*, are exactly what we want.
- There is a gain=2 for the implemented filter, just like the Q2 showed, if we use gain=1 all pass filter to implement low or high-pass filter by this way, there will be a gain=2 at last.
- *sysB* is a 10 order all-pass filter, which is not good. $H_L(z)-H_H(z)$ has 10 terms that cannot be cancelled.

#### Lab5

**Q1:**

We need to decide the length of each window. 

We need to determine M for Hamming. From table 7.2, Hamming and Blackman: $M\approx 8*\pi/\Delta \omega$. 

We need to determine M and β for Kaiser. From our textbook Equation 7.73, 74, 75, 76, we can get M and β. For δ, we select $min(0.02, 0.025)=0.02$, transition band $\Delta\omega$ we select $min(0.15\pi, 0.15\pi) = 0.15\pi$.  The following is the code:

```
clear;
wp1=0.35*pi;wp2=0.65*pi;
ws1=0.2*pi;ws2=0.8*pi;
wc1=(wp1+ws1)/2;wc2=(wp2+ws2)/2;
%%%%hamming%%%%
M1=ceil(8*pi/(0.15*pi));
w_ham=hamming(M1+1);
n=0:1:M1;
h_ideal_ham=sin(wc2*(n-M1/2+eps))./(pi*(n-M1/2+eps))...
           -sin(wc1*(n-M1/2+eps))./(pi*(n-M1/2+eps));
h_ham=h_ideal_ham'.*w_ham;
[H1,w1]=freqz(h_ham,1,'whole'); 
plot(w1/pi,20*log10(abs(H1)));hold on;
%%%%%Blackman%%%%
M2=ceil(12*pi/(0.15*pi));%%determine M
w_black=blackman(M2+1);
n=0:1:M2;
h_ideal_black=sin(wc2*(n-M2/2+eps))./(pi*(n-M2/2+eps))...
           -sin(wc1*(n-M2/2+eps))./(pi*(n-M2/2+eps));
h_black=h_ideal_black'.*w_black;
[H2,w2]=freqz(h_black,1,'whole'); 
plot(w2/pi,20*log10(abs(H2)));
hold on;
%%%%Kaiser%%%%
delta_w=min(wp1-ws1,ws2-wp2);
delta_p=1-0.98; delta_s=0.025;
delta=min(delta_p,delta_s);
A=-20*log(delta);
M3=ceil((A-8)/2.285/delta_w);%%determine M
%%determin beta%%%%
if A>50
    beta=0.1102*(A-8.7);
else
    if A>=21&& A<=50
        beta=0.5842*(A-21)^0.4+0.07886*(A-21);
    else
        beta=0;
    end
end
w_kaiser=kaiser(M3+1,beta);
n=0:1:M3;
h_ideal_kaiser=sin(wc2*(n-M3/2+eps))./(pi*(n-M3/2+eps))...
           -sin(wc1*(n-M3/2+eps))./(pi*(n-M3/2+eps));
h_kaiser=h_ideal_kaiser'.*w_kaiser;
[H3,w3]=freqz(h_kaiser,1,'whole'); 
plot(w3/pi,20*log10(abs(H3)));
hold on;
title('BPF Amplitude Response of Three Design');
legend('Hamming','Blackman','Kaiser');
xlabel('\omega/\pi'); ylabel('Magnitude(dB)');
```

*Result:*

<img src="{{site.baseurl}}/assets/img/lab5_Q1.png">

##### Q2:

From Hilbert transformer's frequency response, we can take inverse Fourier transform to get impulse response:
$$
h_d(n)=\begin{cases}\frac{2sin^2[\pi(n-\alpha)/2]}{\pi(n-\alpha)},\quad n\ne\alpha\\\qquad0\qquad\qquad n=\alpha  \end{cases}
$$
We use Hanning and Kaiser window on it.

When M=even, it is Type III FIR, so we need to set h(α)=0. When M=Odd, it's Type IV FIR, then we don't need to do this.

M=24:

```
clear;

%%%M=24%%%%
M=24;alpha=M/2; n=0:M;
hd=2/pi*((sin(pi*(n-alpha+eps)./2)).^2)./(n-alpha+eps);
hd(alpha+1)=0;
%%%%Hanning%%%
w_han=(hann(M+1))';
h1=hd.*w_han;
[H1,w1]=freqz(h1,1,'whole');
subplot(221);
stem(n,h1);grid on;xlim([0 24]);
title('Hanning Filter Impulse Response');
xlabel('n');ylabel('Magnitude');
subplot(222);
plot(w1/pi,abs(H1));grid on;
title('Hanning Filter Amplitude Response(TypeIII)');
xlabel('\omega/\pi');ylabel('Magnitude');
%%%%kaiser%%%%%
w_kaiser=(kaiser(M+1,2.5))';
h2=hd.*w_kaiser;
[H2,w2]=freqz(h2,1,'whole');
subplot(223);
stem(n,h2);grid on;xlim([0 24]);
title('Kaiser Filter Impulse Response');
xlabel('n');ylabel('Magnitude');
subplot(224);
plot(w2/pi,abs(H2));grid on;
title('Kaiser Filter Amplitude Response(TypeIII)');
xlabel('\omega/\pi');ylabel('Magnitude');
```

*Result:*

<img src="{{site.baseurl}}/assets/img/lab5_Q2_M24.png">

<img src="{{site.baseurl}}/assets/img/lab5_Q2_M24_phase.png">

**Comments:**

- It is a Type III FIR filter. It's anti-symmetry and it has zeros at $\omega=0$ and $\omega=\pi$.
- I am using β=2.5 of Kaiser window, it has larger sidelobe than Hanning but with smaller transition band.
- From the amplitude response, we can see Type III Hilbert transformer is an all-pass system except near $\omega=0$ and $\omega=\pi$.
- From the phase response, we can see Hilbert transformer has linear phase and constant phase advance of $\pi/2$ when $0<\omega<\pi$, phase delay of $\pi/2$ when $-\pi<\omega<0$. Just as its function.

M=25:

```
clear;

%%%M=25%%%%
M=25;alpha=M/2; n=0:M;
hd=2/pi*((sin(pi*(n-alpha+eps)./2)).^2)./(n-alpha+eps);

%%%%Hanning%%%
w_han=(hann(M+1))';
h1=hd.*w_han;
[H1,w1]=freqz(h1,1,'whole');
subplot(221);
stem(n,h1);grid on;xlim([0 25]);
title('Hanning Filter Impulse Response(M=25)');
xlabel('n');ylabel('Magnitude');
subplot(222);
plot(w1/pi,abs(H1));grid on;
title('Hanning Filter Amplitude Response');
xlabel('\omega/\pi');ylabel('Magnitude');
%%%%kaiser%%%%%
w_kaiser=(kaiser(M+1,2.5))';
h2=hd.*w_kaiser;
[H2,w2]=freqz(h2,1,'whole');
subplot(223);
stem(n,h2);grid on;xlim([0 25]);
title('Kaiser Filter Impulse Response(M=25)');
xlabel('n');ylabel('Magnitude');
subplot(224);
plot(w2/pi,abs(H2));grid on;
title('Kaiser Filter Amplitude Response');
xlabel('\omega/\pi');ylabel('Magnitude');

figure();
subplot(211); plot(w1/pi,angle(H1)/pi);grid on;
xlabel('\omega/\pi');ylabel('ARG/\pi');
title('Hanning Filter Phase Response');
subplot(212);plot(w2/pi,angle(H2)/pi);grid on;
title('Kaiser Filter Phase Response');
xlabel('\omega/\pi');ylabel('ARG/\pi');
```

*Result:*

<img src="{{site.baseurl}}/assets/img/lab5_Q2_M25.png">

<img src="{{site.baseurl}}/assets/img/lab5_Q2_M25_phase.png">

**Comments:**

- It is a Type IV FIR filter. It's anti-symmetry and it has zeros at $\omega=\pi$.
- I am using β=2.5 of Kaiser window, it has larger sidelobe than Hanning but with smaller transition band.
- From the amplitude response, we can see Type III Hilbert transformer is an all-pass system except near $\omega=\pi$.
- From the phase response, we can see Hilbert transformer has linear phase and constant phase delay of $\pi/2$ when $0<\omega<\pi$, phase advance of $\pi/2$ when $-\pi<\omega<0$. Just as its function.

##### Q3:

**a)**   

```
clear;
N=10;n=0:N-1;
k=0:N-1;
xn=cos(0.48*pi*n)+cos(0.52*pi*n);
WN=exp(-1i*2*pi/N); 			%define WN
nk=n' * k; 						%create a matrix of nk value
WNnk=WN.^nk;					%DFT matrix
Xk=xn*WNnk;						%calculate Xk by matrix
stem(k,abs(Xk));
```

*Result:* 

<img src="{{site.baseurl}}/assets/img/lab5_Q3a.png">

I got the same result using 'fft()' function.

**b)** 

```
clear;
N=100;n=0:9;
k=0:N-1;
xn=cos(0.48*pi*n)+cos(0.52*pi*n);

WN=exp(-1i*2*pi/N);
nk=n' * k;
WNnk=WN.^nk;
Xk=xn*WNnk;
stem(k,abs(Xk));
```

*Result:* 

<img src="{{site.baseurl}}/assets/img/lab5_Q3b.png">

**c)** 

```
clear;
N=100;n=0:N-1;
k=0:N-1;
xn=cos(0.48*pi*n)+cos(0.52*pi*n);

WN=exp(-1i*2*pi/N);
nk=n' * k;
WNnk=WN.^nk;
Xk=xn*WNnk;
stem(k,abs(Xk));grid on;
```

*Result:*

<img src="{{site.baseurl}}/assets/img/lab5_Q3c.png">

**d)** 

```
clear;
N=200;n=0:199;
k=0:N-1;
xn=cos(0.48*pi*n)+cos(0.52*pi*n);

WN=exp(-1i*2*pi/N);
nk=n' * k;
WNnk=WN.^nk;
Xk=xn*WNnk;
stem(k,abs(Xk));grid on;
```

*Result:*

<img src="{{site.baseurl}}/assets/img/lab5_Q3d.png">

**e)  Comments:**

- If compare a) with b), because xn is the same in a) and b), but b) is using more DFT points, that means more samples in one period of X(e^jw). DFT of b) can be seen as interpolation of a).  We can see they have the similar envelope, the more DFT points, the more frequency samples: Xk.
- If compare b) with c), DFT points are the same, but c) has more xn values. If we calculate IDFT of c), we can still get xn. But if we use DFT points less than 100, we are not able to get back to xn.
- If compare c) with d), xn length increase with DFT points increasing. From DFT result, it looks like up-sampling. For up-sampling, we don't get more information in frequency domain, because the frequency information  is also increasing.