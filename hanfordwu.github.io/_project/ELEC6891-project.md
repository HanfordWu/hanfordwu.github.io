---
layout: project_single
title:  "ELEC6891: Broadcast System Design"
date: 2019-06-20
slug: "ELEC6891"
use_math: true
---



**Abstract**—This project has designed a terrestrial broadcasting system for a medium company. The system has two broadcasting stations, providing one 4K or at least HD program to the users between Ottawa and Montreal. This project adopts ATSC 3.0 digital television standard. Based on ATSC 3.0, we are using LDPC code as the FEC channel coding scheme. By Layer Division Multiplexing ( LDM ), we provide HD and 4K service to the users. On the receiver end, we find the required Signal Noise Ratio, receiving components’ gains and temperature. Then we do link budget to find the transmitting power. Finally, we implement this design in MATLAB by using a picture, the results of simulation are showed in this report.

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

<p style="text-align: center;"> <img src="{{site.baseurl}}/assets/img/Untitled Diagram.jpg"> 
</p> <p style="text-align: center;"> Fig. 1. Layer Division Multiplexing Application </p>



​	D. Forward Error Correction (FEC)

In this project, we only use Low Density Parity Check (LDPC) codes as inner channel coding scheme. The structure of LDPC scheme is showing following:

<img src="{{site.baseurl}}/assets/img/LDPC.jpg">

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

<img src="{{site.baseurl}}/assets/img/1567030492608.png">

*Table 2. Required SNR (dB) for ModCod Combination, long LDPC codes (64800 bits) and AQGN Channel [5]*

For core layer, LDPC code rate is 5/15, modulation scheme is QPSK, we are using long coding (64800 bits). So, the required C/N $(SNR_{CL\_BC})$ is -1.70 dB. For enhance layer, LDPC code rate is 10/15, modulation scheme is 64QAM, we are using long coding. So, the required C/N ($SNR_{EL\_BC}$) is 12.88 dB.

​	G. Injection Level

Injection level is an important system parameter. A higher injection level means less transmission power allocated to the Enhanced layer and more robustness to the Core layer [5]. The figure shows the change of injection level:

<img src="{{site.baseurl}}/assets/img/1567030749132.png">

When the injection level of Enhanced layer is chosen, it is important to consider the required SNR of Core layer before LDM combining. In order to provide enough headroom between the threshold of required SNR of Core PLP(s) and the injection level, it is recommended that the injection level (IL) of Enhanced PLP(s) be chosen as the following condition [5]:

<img src="{{site.baseurl}}/assets/img/1567030782699.png">

We select 2 dB as the Injection Level.

​	H. SNR After LDM Combination

We use the following formula to calculate the SNR of two layers’ after LDM combination. These two formulas come from the ‘Guidelines for the Physical Layer Protocol (A/327)’ [5]. They are base on the power division.

<img src="{{site.baseurl}}/assets/img/1567030856107.png">

From formula, we can calculate the required SNR after combination:

<img src="{{site.baseurl}}/assets/img/1567030883178.png">

From the above discussion, we can determine the overall system parameters as following table:

<img src="{{site.baseurl}}/assets/img/1567030911539.png">

<p style="text-align: center;"> III. Link Budget </p>
This project is using the following simple mode to do link budget calculation:

<img src="{{site.baseurl}}/assets/img/linkbudget.png">

On the receiver side, we have an antenna, LNB, Cable, amplifier, decoder, the parameters are listed in the following table:

<img src="{{site.baseurl}}/assets/img/lingktable.png">

<img src="{{site.baseurl}}/assets/img/lingkcalculation.png">

<img src="{{site.baseurl}}/assets/img/sim.png">

<img src="{{site.baseurl}}/assets/img/sim1.png">

<img src="{{site.baseurl}}/assets/img/sim2.png">

***Appendix: Matlab code***

```matlab
clc; clear all; % clear cache
pic_sender=imread('img-sending.jpg'); % read the sending image to RGB matrix

%%%%% split picture half by half, left half is Core layer, right half is 
%%%%% Enhance layer:
pic_left= pic_sender(:, 1:end/2, :);
pic_right=pic_sender(:, end/2+1:end,:);
pic_size=size(pic_sender);

%change 3D matrix to bit stream
bitstr_CL=reshape(de2bi(pic_left),[],1);
bitstr_EL=reshape(de2bi(pic_right),[],1);
pic_stream=[bitstr_CL;bitstr_EL];%used to calculate BER

% s1,s2 are used to calculate loops and remove padding
s1=length(bitstr_CL); s2=length(bitstr_EL);

i=1;
%%%%%%%%%%%%%%LDPC Encoding%%%%%%%%%%%%%%%

Corelayer_coderate = 1/3;%Corelayer ldpc coderate
Enhlayer_coderate  = 2/3;%Enhancelayer ldpc coderate

h_encoder_CL = dvbs2ldpc(Corelayer_coderate);%generate h check matrix of CL
h_encoder_EL = dvbs2ldpc(Enhlayer_coderate);%generate h check matrix of EL

ldpcencoder_CL = comm.LDPCEncoder(h_encoder_CL);%generate ldpc encoder of CL
ldpcencoder_EL = comm.LDPCEncoder(h_encoder_EL);%generate ldpc encoder of EL

ldpcdecoder_CL = comm.LDPCDecoder(h_encoder_CL);%generate ldpc decoder of CL
ldpcdecoder_EL = comm.LDPCDecoder(h_encoder_EL);%generate ldpc decoder of EL

blocksize_CL = 64800*Corelayer_coderate;%calculate block size of CL
blocksize_EL = 64800*Enhlayer_coderate;%calculate block size of EL
loop_CL =ceil(s1/blocksize_CL);%calculate # of loops of CL when doing encoding
loop_EL =ceil(s2/blocksize_EL);%calculate # of loops of EL when doing encoding

%padding zeros to form the integer times of blocksize:
bitstr_padding_CL=[bitstr_CL;zeros((blocksize_CL*loop_CL- size(bitstr_CL,1)),1)];
bitstr_padding_EL=[bitstr_EL;zeros((blocksize_EL*loop_EL- size(bitstr_EL,1)),1)];


%%create two zero matrix to store encoded blocks, if don't do this, the encoded
%%matrix will change size for every loop, slow down the speed. 
encoded_bitstr_CL=zeros(64800,loop_CL);
encoded_bitstr_EL=zeros(64800,loop_EL);

%ldpc encode core layer block by block:
    for counter = 0:loop_CL-1
        data= bitstr_padding_CL(counter*blocksize_CL+1:counter*blocksize_CL+blocksize_CL);
        ldpc_encoded_CL = ldpcencoder_CL(data);
        encoded_bitstr_CL(:,counter+1)=ldpc_encoded_CL;
    end
%ldpc encode enhance layer block by block:
    for counter = 0:loop_EL-1
        data= bitstr_padding_EL(counter*blocksize_EL+1:counter*blocksize_EL+blocksize_EL);
        ldpc_encoded_EL = ldpcencoder_EL(data);
        encoded_bitstr_EL(:,counter+1)=ldpc_encoded_EL;
    end
        
    encoded_bitstr_CL=reshape(encoded_bitstr_CL,[],1);%reshape matrix to
    encoded_bitstr_EL=reshape(encoded_bitstr_EL,[],1);%encoded bit stream
      
    
%%%%%%%%%%%%%%%%%%%%Modulation%%%%%%%%%%%%%%%%%%%

%modulation use 4qam and 64qam
    mod_signal_CL=qammod(encoded_bitstr_CL,4,'InputType','bit');
    mod_signal_EL=qammod(encoded_bitstr_EL,64,'InputType','bit');

%after modulation,we combine two modulated symblo streams
    mod_signal=[mod_signal_CL;mod_signal_EL]; 
    
    
 %%%%%%%%%%%%Add Noise%%%%%%%%%%%%%%
 for SNR=-2:1:2
    co=(length(encoded_bitstr_CL)+length(encoded_bitstr_EL))/(s1+s2);
    N0=co*10^(-SNR/10);
 %create a gauss noise based on noise power: N0/2
    noise_gauss = sqrt(N0/2)*(randn(size(mod_signal))+1i*randn(size(mod_signal)));
    noised_signal= mod_signal + noise_gauss;%add noise on signal(transmission)

%%%%%%%%%%%%%%%%%%%Demodulation%%%%%%%%%%%%%%%%%%%%%
 
 %split noised signal, then demodulate them using different demodulation
    noised_CL=noised_signal(1:length(mod_signal_CL),1);
    noised_EL=noised_signal(length(mod_signal_CL)+1:end,1);
  
    demod_signal_CL = qamdemod(noised_CL,4,'OutputType','llr');
    demod_signal_EL = qamdemod(noised_EL,64,'OutputType','llr');
    
    decoded_bitstr_CL=zeros(blocksize_CL,loop_CL);% create two matrix to store
    decoded_bitstr_EL=zeros(blocksize_EL,loop_EL);% ldpc decoded blocks
    
 %%%%%%%%%%%%%ldpc decoding%%%%%%%%%%%%%
 for counter=0:loop_CL-1
     data = demod_signal_CL(counter*64800+1:counter*64800+64800);
     ldpc_decoded_CL=ldpcdecoder_CL(data);
     decoded_bitstr_CL(:,counter+1)=ldpc_decoded_CL;
 end
    
 for counter=0:loop_EL-1
     data = demod_signal_EL(counter*64800+1:counter*64800+64800);
     ldpc_decoded_EL=ldpcdecoder_EL(data);
     decoded_bitstr_EL(:,counter+1)=ldpc_decoded_EL;
 end
 %reshape decoded matrix back to bitstream
    decoded_bitstr_CL=reshape(decoded_bitstr_CL,[],1);
    decoded_bitstr_EL=reshape(decoded_bitstr_EL,[],1);
    
 %remove padded zeros according to length of two layer's length
    decoded_bitstr_CL=decoded_bitstr_CL(1:s1,:);
    decoded_bitstr_EL=decoded_bitstr_EL(1:s2,:);
        
 %combine two bitstream back to one, to calculate the total BER
 pic_received_bits = [decoded_bitstr_CL;decoded_bitstr_EL];
 
 BER(1,i) = sum(mod(uint8(pic_received_bits) + pic_stream,2))/(s1+s2);
 i=i+1;
 %%%reshap two bitstream back to halves image
 pic_received_left=reshape(bi2de(reshape(uint8(decoded_bitstr_CL),[],8)),pic_size(1),pic_size(2)/2,3);
 pic_received_right=reshape(bi2de(reshape(uint8(decoded_bitstr_EL),[],8)),pic_size(1),pic_size(2)/2,3);
 %combine two halves image
 pic_received=[pic_received_left,pic_received_right];
    figure;
    imshow(pic_received);
    title(['Left is CL, Right is EL, SNR=',num2str(SNR)]);
end
 figure;
 plot(-2:1:2,BER,'-*');
 title('SNR VS BER');
 xlabel('SNR');
 ylabel('BER');
```

