---
layout: project_single
title:  "ELEC6891: Broadcast System Design"
slug: "ELEC6891"
---



**Abstract**—This project has designed a terrestrial broadcasting system for a medium company. The system has two broadcasting stations, providing one 4K or at least HD program to the users between Ottawa and Montreal. This project adopts ATSC 3.0 digital television standard. Based on ATSC 3.0, we are using LDPC code as the FEC channel coding scheme. By Layer Division Multiplexing (LDM), we provide HD and 4K service to the users. On the receiver’s end, we find the required Signal Noise Ratio, receiving components’ gains and temperature. Then we do link budget to find the transmitting power. Finally, we implement this design in MATLAB by using a picture, the results of simulation are showed in this report.

<p style="text-align: center;"> I.	Introduction </p>

There are mainly four Digital TV (DTV) standards:

- DVB: Digital Video Broadcasting. 
- ATSC: Advanced Television Systems Committee. 
- DTMB: Digital Terrestrial Multimedia Broadcast.
- ISDB: Integrated Services Digital Broadcasting.

The latest territorial versions of these standards are: DVB-T2, ATSC 3.0, DTMB-T, ISDB-T. This project adopts ATSC 3.0 as digital TV standard.

<p style="text-align: center;"> II.	System Design </p>

​	A. Service Type

First, we select the resolution and frame rate. From the project description, we want to broadcast 4K or at least HD indoor TV streaming. There are few different formats to deliver HD program, in case of 16:9 aspect ratio, we select 1920x1080 progressive scanning (1080p) to be HD type. For the resolution of 4K, we select 3840×2160p. In the North America, according to the electricity frequency, we select 60 frames per second [2]. 

​	B. Data Bitrates 

Requirements The bitrate requirements depend on the video coding standard: HEVC (H.265). In this project, we have two video coding layers for HD and 4K services. At the same time, the quality of picture is related to the perceptual of person. A summary of the required bitrates is presented in the second column of Table I for both HD and 4K-UHD services [3]. The adopted bitrates in this project for each service are listed in third column.

| Format  | Suggested Bitrates with HEVC | Adopted Bitrate in this project |
| :-----: | :--------------------------: | :-----------------------------: |
| Full HD |         2.5-4.5 Mbps         |            3.5 Mbps             |
| 4K-UHD  |          15-32 Mbps          |             20 Mbps             |

​	

​	C. Layer Division Multiplexing

ATSC 3.0 adopts layer division multiplexing. In this project, we use two layers’ division scheme. We treat a HD program as core layer (PLP0). The enhance layer (PLP1) combine core layer together to provide a 4K program.

![Untitled Diagram](/media/hanford/Work/Course/ELEC6891/Untitled Diagram.jpg)

​					*Fig. 1. Layer Division Multiplexing Application*

​	D. Forward Error Correction (FEC)

In this project, we only use Low Density Parity Check (LDPC) codes as inner channel coding scheme. The structure of LDPC scheme is showing following:

![LCPC](/media/hanford/Work/Course/ELEC6891/LCPC.jpg)

The block size of LDPC code is 16200 or 64800 bits, 16200 bits LDPC codes have lower latency but worse performance. In general, 64800 bits codes are the first choice because of superior performance []. In this project, we take 𝑁𝑖𝑛𝑛𝑒𝑟 = 64800 bits for both enhance layer and core layer [4]. Code rate can be 2/15, 3/15, 4/15, 5/15, 6/15, 7/15, 8/15, 9/15, 10/15, 11/15, 12/15, 13/15. The higher code rate, the better protection will be [4]. For Core layer, we can take 5/15, for Enhance layer, we take 10/15.

​	E. Modulation

Modulation scheme should satisfy the bitrate requirement. From part C, we know a HD program bitrate is 3.5 Mbps, a 4K program 20 Mbps. The available bandwidth is 6 MHz. Considering the FEC scheme parity and roll-off factor (Assuming β is 0.1), we get the transmitting capacity:

Core layer:
$$
Capacity=\frac{BW}{1+\beta}\times\log_2M\times LDPC\: Code Rate=1.82\times \log_2M \geq 3.5Mbps
$$
Therefore, M≥4. We select QPSK modulation from the ATSC 3.0 physical layer standard. 

Enhance layer:
$$
Capacity=\frac{BW}{1+\beta}\times\log_2M\times LDPC\: Code Rate=1.82\times \log_2M \geq 20Mbps
$$
Therefore, M≥64. We select 64QAM modulation from the ATSC 3.0 physical layer standard.

​	F. Required SNR

According to ‘Guidelines for the Physical Layer Protocol (A/327)’ [5], we find the required SNR based on ModCod combination.

![1567030492608](/home/hanford/.config/Typora/typora-user-images/1567030492608.png)

*Table 2. Required SNR (dB) for ModCod Combination, long LDPC codes (64800 bits) and AQGN Channel [5]*

For core layer, LDPC code rate is 5/15, modulation scheme is QPSK, we are using long coding (64800 bits). So, the required C/N $(SNR_{CL\_BC})$ is -1.70 dB. For enhance layer, LDPC code rate is 10/15, modulation scheme is 64QAM, we are using long coding. So, the required C/N ($SNR_{EL\_BC}$) is 12.88 dB.

​	G. Injection Level

Injection level is an important system parameter. A higher injection level means less transmission power allocated to the Enhanced layer and more robustness to the Core layer [5]. The figure shows the change of injection level:

![1567030749132](/home/hanford/.config/Typora/typora-user-images/1567030749132.png)

When the injection level of Enhanced layer is chosen, it is important to consider the required SNR of Core layer before LDM combining. In order to provide enough headroom between the threshold of required SNR of Core PLP(s) and the injection level, it is recommended that the injection level (IL) of Enhanced PLP(s) be chosen as the following condition [5]:

![1567030782699](/home/hanford/.config/Typora/typora-user-images/1567030782699.png)

We select 2 dB as the Injection Level.

​	H. SNR After LDM Combination

We use the following formula to calculate the SNR of two layers’ after LDM combination. These two formulas come from the ‘Guidelines for the Physical Layer Protocol (A/327)’ [5]. They are base on the power division.

![1567030856107](/home/hanford/.config/Typora/typora-user-images/1567030856107.png)

From formula, we can calculate the required SNR after combination:

![1567030883178](/home/hanford/.config/Typora/typora-user-images/1567030883178.png)

From the above discussion, we can determine the overall system parameters as following table:

![1567030911539](/home/hanford/.config/Typora/typora-user-images/1567030911539.png)

