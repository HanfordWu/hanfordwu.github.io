#  Content of this course:

 Digital Transmission Standards (ATSC, DVB-T/T2, DVB-S/S2).
 Video Compression Techniques: JPEG, MPEG-2, H.264, HEVC, J2K.
 Performance measures for Digital TV: Noise, Error.
 Packet Structure: Tables (PAT, PMT).
 Multiplexing and De-multiplexing.
 Channel Coding and Modulation for Digital Television.
 Cyclic codes.
 Digital TV Transmitters: Up/converters, Power Amplifiers, Combiners, Equalizers and pre-correctors.
 Transmission Lines: Cables, Wave Guides, link budget calculation. Transmitting Antennas for Digital Broadcasting.
 COFDM, LDPC Codes.
 Satellite Broadcasting.
 IPTV and Multi-platform formats.

 

- ## Digital Transmission Standards

  We have four digital transmission standards: ATSC(North America), DVB(Europe) , DTMB(China), ISDB(Japan). They adopt different technologies depending on different conditions. As a result, they have different performance. Also, they are evolving. However, the goal of them is the same, transmitting TV signal as high quality as possible. Particularly, they must meet different needs of nowadays.

- ## Component of broadcast system

  1. Content producer, it's what we want to transmit.
  2. Formatter, before transmission, we must process the digital contents to be ready. No one want to transmit raw materials, they are time consuming and bandwidth wasting.
  3. Wired or wireless transmission lines: this part refers to communication technics. Channel coding, cable or wireless, base band to intermediate modulation, radio frequency modulation, ISI, OFDM, multiplexing, antenna. 
  4. On the opposite side, everything should be recovered, till the contents are showed on the screen of users'.

- ## Video signal

   What is video signal? Everyone knows movie is a sequence of still pictures. In our childhood, we watch outdoor movies, we can see many big boxes and film volumes in them. One short section of films looks like all the same picture. This feature is important to do compression. Going through a projector, it starts to move! It just makes use of human's eye's illusion, the same as cartoon. 

- ## Progressive and interlaced picture

  interlaced frame:

  ![1559585564259](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1559585564259.png)

Progressive video refers to scan the whole image line by line, send. Interlaced video refers to  scan image Odd field, send, then Even field, send. If the interlaced scan rate (refresh rate) 2 times progressive video, they have the same bitrate, but interlaced video removes flicking.

In terms of  the monitor, at the same resolution, progressive set is better than interlaced set at the same refresh rate.

- ## Pixels

  Small unit of images and videos. 

- ##  Component and composite video

  Each pixel has a color, which is linear combination of RGB value. This is component video. Human's eye is more sensitive to luminosity. We use Y(luminosity), Cb and Cr to represent RGB by solving a system of three linear equations with three unknowns. Y= 𝑘𝑟𝑅 +𝐾𝑔𝐺 +𝐾𝑏𝐵, 𝐶𝑏 = 𝑌 − 𝐵 and 𝐶𝑟 = 𝑌 −𝑅. The one suggested by  ITU-R called BT.601 is 𝑌 = 𝑘𝑟𝑅 +(1−𝑘𝑟 −𝑘𝑏)𝐺 +𝑘𝑏𝐵, 𝐶𝑏 = 1/2 * 𝐵−𝑌/1−𝑘𝑏 and 𝐶𝑟 = 1/2 * 𝑅−𝑌/1−𝑘𝑟 with 𝑘𝑏 = 0.114 and 𝑘𝑟 = 0.299. Y, Cb, Cr video is called composite video.

  

- ## Sampling

  ![1559590450800](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1559590450800.png)

  Use pulse to represent original signal.

  AD converter

  Nyquist theorem:

  Sampling rate should be more than or equal to twice the highest frequency of analog signal. If not, the analog signal can't be fully recover. There will be some frequency components to be discarded.

  

- ## Quantization

  When you have a sample, you should use binary number to represent them. N bits represent 2^N levels to be used. These levels can be uniform or non-uniform distributed.

  when the analog signal is continuous change, there will be a deviation compared to the sample value. This is called quantization error. This error is seemed as noise to the sample value, so we have signal-to-quantization-noise ratio (SQNR). 

  For uniform quantization, 
  $$
  SQNR = 6N + 10 log3*σ^2 / V^2
  $$

# JPEG

JPEG is a image compression standard. Making use of spatial redundancy of one picture.

Block: image is consisted by pixels, combine 8*8 pixels as a block.

In order to preserve the correlation between pixels, we use Zig-Zag scanning:

![1559592899045](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1559592899045.png)

Every Zig-Zag scanning can be one value of R or G or B or Y or Cb or Cr. We get a 8*8 matrix.

![img](https://thecodeway.com/blog/wp-content/uploads/2014/09/jpeg_032.gif)

Magic of DCT transform:

 DCT converts each frame/field of the video source from the spatial (2D) domain into the frequency (transform) domain. Like FFT, we get a matrix showing the frequency of one picture.

![1559593258426](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1559593258426.png)

![img](https://thecodeway.com/blog/wp-content/uploads/2014/09/jpeg_36.gif)

After DCT transformation, we get a matrix with low frequency and high frequency from left&up to right&down.

Going back to human's eye perception, we can discard high frequency of one picture and eyes don't feel much different. So, we use a quantization matrix to make high frequency to be zero.

![img](https://thecodeway.com/blog/wp-content/uploads/2014/09/jpeg_034.gif)

The quantization matrix determine the compression ratio. The remaining values have lots zeros and repetitions, we can use Huffman code to encode them.

- Huffman encoding

  ![1559595736449](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1559595736449.png)

Huffman encoding has a near-entropy coding result.

# Video compression

So far we know JPEG, it is used to compress image by removing spatial redundancy.  For video, it has spatial redundancy and temporal redundancy. We use JPEG to compress video from spatial redundancy (intra frame compression). 

There are another way to do intra frame compression. Again, relating to our eyes, eyes are insensitive with chroma. So we don't transmit chroma for every pixel. JPEG does compression within 8*8 pixels block, each block has Y matrix, Cb matrix, Cr matrix. Now we combine four blocks as a group, called micro-block, which is 16 * 16 pixels. We don't transmit Cb and Cr of every block, but follow special ratio: 4:4:4 or 4:2:2 or 4:2:0.

![img](https://img.huxiucdn.com/article/content/201809/19/092924641772.jpg?imageView2/2/w/1000/format/jpg/interlace/1/q/85)



Now we consider temporal redundancy (inter frame compression).

Like I mentioned, one short section of films looks like all the same picture. In this case, we don't transmit or store every picture. Instead, after we transmit one full frame (with intra frame compression), we just tell the different of second frame from the first one (motion compensation). By this way, we cut down the information we have to transmit. 



I-frame: intra coded frame, full JPEG frame

P-frame: predictive frame, using data from previous frame to form the whole frame.

B-frame: bi-predicted frame, using data from previous and following frames to construct the whole frame of it selves. 

GOP: Group Of Picture, from I-frame to next I-frame.

GOP means one group of related frames. Decoder can construct all the frame according to frames in one GOP. Therefore, only one full GOP arrive, the pictures will show up. That means the GOP size determine the delay of decoding.

- MPEG-profile and level

  profiles and levels define the ability of encoder and decoder must have.

  

# Wireless communication

Block diagram of communication link:

![1559601529345](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1559601529345.png)

The following description of this diagram is cited from professor's lecture:

The first block (formatter) is basically responsible for turning the source into (raw) digital format. The case of digital video, it performs the functions such as sampling, quantization and adding some synchronization signals. The format in which the data will be at the output of the formatter (video capture) is usually in SDI format. 

The next block compresses the raw video using a scheme such as MPEG. The out put at this point will consist of Transport Stream (TS) packets each 188 (204 if the RS code is used) bytes long. These packets are either formatted as an ASI or IP stream. Encryption may be used either for personal purposes or Digital Rights Management (DRM). In any case, the goal is to prevent an unauthorized person from using the video.

The Channel Encoding block is responsible for helping the receiver in detecting and possibly correcting the error caused by the channel noise or storage media defects (for example damaged video disk). A communication/broadcasting system may have several levels of error control coding. These may appear at different points in the chain. We have already talked about the use of Reed Solomon (RS) codes. The16 parity bytes added to each 188 byte TS packet helps the decoder to correct up to 8 bytes of error. The channel may be noisier and create more errors. Then either the power of the transmitter has to be increased or the stream be further encoded, i.e., more parities be added prior to transmission. A class of codes used is LDPC codes. These are very performant, easy to decode with manageable encoding complexity.

The role of the multiplexer is to put together two or more streams. This is needed if one broadcasts more than one program. Nowadays, the multiplexing function is more and more performed through IP Encapsulation. This means that several video streams are put together as a single IP stream. Each IP packet may then contain TS packets belonging to different video sources. A header added to each TS packet allows the de-multiplexer at the receiver side to separate the different programs. Note that the multiplexer at this point, i.e., before transmission although doing similar function should not be confused by multiplexing done at different points of the broadcast chain, e.g., the one used for adding video, audio and metadata files to form a complete video stream.

The last thing we will talk about is the modulator. A digital modulator consists of two parts: the Pulse Shaping part (one you may associate with a digital electronic designer) and a Radio Frequency section (the RF section that is designed by an RF engineer). The function of the pulse shaping circuit is to apply a filter to the outgoing information so that the adjacent bits do not overlap, i.e., there is no Inter-Symbol Interference (ISI) and a channel does not go beyond its allocated frequency slot causing interference to other channels (ICI). 

1. pulse shaping

   Why pulse shaping is needed? 

   Digital signal is binary data, square waveform. Its frequency spectrum extends from -∞ to +∞. If we use square waveform as baseband signal directly, we can make every symbol orthogonal, but there are two problems: 1, hard to control the time sample, 2, hard to modulate pre-hand. By pulse shaping, we can solve these two problems and reduce ISI.

   The method of pulse shaping is raise cosine filter.

   After filtering, the required bandwidth is determined: 
   $$
   W= Rs(1+β)
   $$
   
2. Modulation

   MPSK or MQAM

   Constellation mapping is a tool, a model, it is not a technic. After pulse shaping, forming a signal, then start to modulate. We may have two level modulation, intermediate frequency and radio frequency.

3. BER

   Different modulation with different SNR has different BER.

   The distance of mapping point determine the possibility of error, the nearer between two constellation points, the easier error will be made. At the same time, the higher SNR, the more concentration of mapping.

   ![1559604925351](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1559604925351.png) 

![1559604951144](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1559604951144.png)

The second plot has a higher SNR.

BER equation for different modulation:

![1559605184701](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1559605184701.png)

![1559605207536](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1559605207536.png)

# MPEG-TS

### What is TS?

TS specify a container that encapsulates elementary streams with error correction and synchronization. TS is a series of TS packets, these packets are typically 188 bytes long.

TS is used in unreliable channel, like terrestrial and satellite broadcasting. TS may contain multi program streams. Program stream is used reliable medium, like DVD, usually it has only one program.

Each packet is 188 Bytes long, it can be a table, or an elementary stream packet, depends on the PID.

![1559609451307](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1559609451307.png)

### What's inside of one packet?

PID: determine the type of this packet. If it is an elementary stream packet, PID determine which program it belongs to (from PMT).

![1559609581932](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1559609581932.png)

In one TS, decoder finds PID=0000 packet, which is PAT, from PAT, decoder can find what's the PID of PMT, then decoder knows every program and their elementary stream packets' PID.

Program Identifier PID: Each table or elementary stream in a transport stream is identified by a 13‐bit packet identifier PID.  A de-multiplexer extracts elementary streams from the transport stream in part by looking for packets identified by the same PID.

A TS with only one program in it, called Single Program TS, or SPTS, with multiple programs, called MPTS.

##### PSI—program specific information. There are 4 PSI tables: PAT, PMT, CAT, NIT.

##### PAT—program association table. list all the programs are available in the TS, and their PMT's PID. Decoder find one program's PMT PID, search this PID in TS, find PMT.

##### PMT—program map table. List all the elementary stream packets' PID, help decoder find all the elementary data of this program.

##### CAT—When scrambling is performed at TS level, CAT can help decoder find related stream (here is no sure).

##### PCR—program clock reference. Provide outside clock information of elementary packets in different program. It is one of reference clock for program. For example, voice packets and video packets of one program need PCR to be synchronized.

##### Null packet—When no data need sending, null packet is used to maintain bitrate.

![1559615722445](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1559615722445.png)

# Few Information theory

## Mutual information and Entropy

I(x;y) qualifies the amount of information an event provides about another event.

First, I don't know about X. The qualified "don't know" is H(x). Then you tell me what is Y, then the rest of "don't know about X" is H(x|y), that means I've known something about X. This "something" is mutual information I(x;y).

I(x;y)=H(x)-H(x|y)

![1559694511798](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1559694511798.png)

#### If X=Y, I(x;x) means when you tell me X, what I've known from X about X, I've known everything about X. So, I(x;x) is  the qualified information of X. That is H(X), the entropy of X!

If X and Y is independent, P(X,Y)=P(X)*P(Y), then, I(x;y)=0.

H(x|y) means the uncertainty of X given Y.

I(x;y)=I(y;x)=H(X)-H(X|Y)=H(Y)-H(Y|X) ≥0, that's because if you tell me y, the worst things is no help to know x, but it will never reduce my knowledge about x.

## Capacity of one channel

![1559695281239](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1559695281239.png)

Now we are on the right side of the channel. What we get from the channel is information Y. The uncertainty of X after we get Y is H(x|y), H(x)-H(x|y) is the amount of information carried through the channel. That is what we've known about X given Y, that is I(x;y). 

The capacity of channel is the max(I). When channel is perfect, we get Y=X, that means channel capacity reach the top: H(x)

In a AWGN channel
$$
C=W log(1+P/N)
$$

## Shannon limitation

We can transmit information with error free as long as our transmission rate is lower than C. In other words, it's impossible transmit higher rate than C with lower error rate. 

# Channel coding

Channel coding is used to correct the errors happen in the channel. When snr is fixed, then the capacity of channel is fixed, with error free. We can use channel coding to make the data error free under the capacity of  channel.

### RTP—Realtime Transport Protocol

RTP is used in internet. RTP is located in application layer. Multiple TS packets (188 Bytes) can be encapsulated into RTP packet, with a RTP header, then UTP header, IP header, framing.

###### CRC codes

CRC— Cyclic Redundancy Coding, used to detect errors, then 1) discard errors, or 2) ask to transmit again (ARQ) , Automatically Repeat request. The later one is more adaptive with channel condition, but not suitable for delay sensitive users. 

FEC—Forward Error Correction, using fixed parity rate to detect and correct error. Less adaptive, so, we need design proper FEC according channel condition. Convolution coding—output depending on current input and previous input. Decoding use Viterbi decoder. We have hard Viterbi decoder and soft Viterbi decoder.

Convolution coding has one problem: one error results more error at the same time. It turns memoryless AWGN channel into a burst error channel.

###### RS codes

Reed-Solomon code is used as concatenation coding scheme to deal with burst errors. First the source is encoded with RS as outer coding, then convolution code is used as inner coding. (Now inner coding LDPC is more popular)

RS code (N, K) over GF(2^m) can correct (N-K)/2 symbols' error, each symbol has m bits. It is not bits orientation, it is symbol orientation. Burst error happen in all bits of few symbol, RS code can correct up to m*(N-K)/2-1 burst errors.

From the RS code principle, for a 188 byte TS packet, we should use 255 (2^8-1) byte after encoding, means we add 67 byte parities. Too many parities, instead, we add 16 byte parity bits, and 51 byte zeros. Because outer coding is separated from data, we can omit these zeros after encoding, when we are decoding, we add zeros to decode, don't need to transmit these zeros. In reality, these zeros are conceptual, we don't need to actually add them.

###### LDPC codes

Low density parity-check code has best performance, near to the Shannon limitation. That means it can correct almost all the errors under the capacity of channel.

The point of LDPC is its check matrix, H matrix. H matrix is a sparse matrix, so its check point scatter in the whole codeword. In this case, the decoding error will not result in more decoding errors.  Its complexity is acceptable.  

In other words, regular codes errors are concentrated and errors will be spread. But LDPC code is scattered and errors will be correct, that's why it has a better performance.

# Link Budget

Simple block diagram of a TV broadcasting system

![1560092580417](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1560092580417.png)

Explanation:

Video and audio encoder is used to do compression. Data is used to do synchronization, etc.  

There are two mux actually, the first one is combine video, audio, and data to form a single program transport stream (SPTS). Second one is combine multiple programs, for example, sport channel, drama channel, movie channel, to form a multi program TS (MPTS). The second mux regenerate PSI and may also provide for PID Re‐mapping, Service Filtering.

FEC is channel encoding, ATSC 3.0 use BCH(outer) + LDPC(inner). 

Modulator convert bitstream into waveform, this waveform is baseband signal, means the frequency of wave is around zero.

Modulator has many small blocks. Interleaving is used to spread bits according to certain pattern, then recover them by de-interleaving. The object of this is combat against the fading by spread its influence. IFFT is a part of OFDM.

Block Upconverter BUC translating the signal to Ku‐band. A BUC include upconverter and an amplifier. This amplifier is called High power amplifier (HPA).

###### channel model

For this link budget, we only consider stationary and being installed high enough, so there is no reflection and blocking, scattering, refracting.



### Link Budget

First, link budget is important because

Transmitting power: Pt. The power of output of HPA, power density is Pr/4pi d^2. 

Gt is transmitter antenna gain. Gr is receiver antenna gain.

![1560627022314](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1560627022314.png)

Formula above comes from wiki, each element explanation check the wiki: link budget.

A simpler one is:![1560627280070](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1560627280070.png) not in dB.

Ls=(4pi d/λ)^2, is called space loss or path loss, represents the effect of the distance between the transmit and receive antennas. There are many kinds of loss in space. For example, pointing loss

L0 is other loss, such as cables and connectors. 

Now we get Pr, then Eb=Pr/Rb. in order to find Eb/N0, next we need find N0.

N0=KbT. T is noise temperature. The noise temperature of each component is T1, T2, T3, T4... The whole system equivalent noise temperature is Teq
$$
Teq=T1+T2/G1+T3/G1*G2+...
$$
T is one of quantified noise parameters of one component or system. Noise factor (F) is another quantity used to qualify noisy of one component or system.

T=(F-1)Tin  ; Tin = 290

Noise factor in dB is NF. NF=10log(F)=10log(1+T/290)

In communication, including broadcasting system, the receiver antenna is connected to the first component in the receiver side, the noise temperature of the receiver's antenna is Tant. So, the Tant and T1 are both the input of receiver side system. we have:
$$
Teq=Tant+T1+T2/G1+T3/G1*G2+...
$$
It's different for the active components (amplifier) and passive components. For passive components, L is the loss of signal power in the component. Then F=L, or T=(L-1)*290

After we get N0=Kb*T, we can calculate the Eb/N0, based on the modulation scheme, we can find the BER, then compared with the required BER, check if BER>=required BER, if not, we will increase the transmit power or decrease the bitrate, to meet the required BER.

### Satellite Link Budget

Satellite link is divided into two link: uplink and down link. So we have two link budget. Thera are also two SNR, Eb/N0(U) and Eb/N0(D). The overall SNR is Eb/N0(ov):
$$
(Eb/No(ov))^-1 = (Eb/No(u))^-1+(Eb/No(d))^-1
$$
The Ls of satellite link is different from terrestrial link, because wave have to go through different atmosphere layers.

EIRP: =Pt*Gt

Pr=EIRP*Gr/LoLs

![1560112751533](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1560112751533.png)

G/T is a figure of merit for uplink and down link.

# Layer Division Multiplexing (LDM)

LDM is adopted by ATSC 3.0, so I learned it.

Basically, LDM is constellation's combination.

![1560628012041](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1560628012041.png)

![1560639302038](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1560639302038.png)Before we do LDM, there are two constellation mapping for two layers, they are both regular constellation. Then we decrease the power of right one (enhance layer), do addition with the left one (core layer). Then we get the third one. After combining the total power of combined signals shall be normalized to unity in the power normalizer block.

![1560628736043](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1560628736043.png)

From the combined constellation, we can see the EL is like the noise of CL, make the point derivate from the correct mapping points. Therefore, the injection level determine the SNR of CL somehow. So the injection level should not be two small, it should be enough to let the CL be recoverable. In atsc guideline, it's recommended to have Injection level greater than SNRcl plus 3dB.

On the receiver side, the fist thing is demodulating and decoding the whole signal according to CL demod and decoding scheme, after receiver get the CL data, it will be encoded and modulated according to CL ModCod scheme, to regenerate the waveform of independent CL, then use the whole signal we receive subtract the independent CL waveform, then we will get the EL waveform with base noise. Then we do demodulation and decoding to the EL waveform, we get the EL data. The block diagram is the following figure:![1560631011216](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1560631011216.png)

![1560637695009](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1560637695009.png)

To calculate the required SNR changes, we use the following formula:![1560638343630](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1560638343630.png)

Right two SNRs are before combination, the required SNR. If we don't do LDM combination, the required SNR should be the right twos, they are the required SNR for previous standards. But because we combine two layers, one signal is noise for another signal. So, we need increase the SNR if we want to do LDM. The left two SNRs are the actual SNR when we receive the signal, they are greater than the right twos. That means we need to increase the signal power if we do LDM. When we calculate Pr, we use the sum of the left two SNRs.![1560639429900](C:\Users\hanfo\AppData\Roaming\Typora\typora-user-images\1560639429900.png)

By using LDM, we can provide multiple services using the same band width. like a double-decks bus, or 巴铁. [![Related image](https://img.pixers.pics/pho_wat(s3:700/FO/28/37/88/37/700_FO28378837_77974cb8a16d331057268852ddcea63a.jpg,700,467,cms:2018/10/5bd1b6b8d04b8_220x50-watermark.png,over,480,417,jpg)/wall-murals-empty-red-double-decker-on-street-in-london-england.jpg.jpg)](https://www.google.ca/url?sa=i&rct=j&q=&esrc=s&source=images&cd=&ved=2ahUKEwjJ4rW1y-ziAhUow1kKHfH0BCYQjRx6BAgBEAU&url=https%3A%2F%2Fpixers.uk%2Fwall-murals%2Fempty-red-double-decker-on-street-in-london-england-28378837&psig=AOvVaw2X-nvERw3-wNedtS-U0dF_&ust=1560725949564117)

[![Related image](http://img.mp.itc.cn/upload/20170703/e22b648c39334e1da2750a9bbe8b9c08_th.jpg)](https://www.google.ca/url?sa=i&rct=j&q=&esrc=s&source=images&cd=&cad=rja&uact=8&ved=2ahUKEwiEtJmEzuziAhVFq1kKHRwEAlgQjRx6BAgBEAU&url=%2Furl%3Fsa%3Di%26rct%3Dj%26q%3D%26esrc%3Ds%26source%3Dimages%26cd%3D%26ved%3D%26url%3Dhttp%3A%2F%2Fwww.sohu.com%2Fa%2F154077522_99916912%26psig%3DAOvVaw3IAPbHSnJ0I6BsJt2pVW2r%26ust%3D1560726083373626&psig=AOvVaw3IAPbHSnJ0I6BsJt2pVW2r&ust=1560726083373626)

FDM can be seen as multiple lanes of one road, TDM can be seen as different slots are occupied by different people's car, they move forward together. 

# SNR, BER, Bitrate

Wireless Communication is about the relation between these three things. Usually we have two of them, then find the third one. For example, we have SNR and BER, then we  can decide the modulation scheme, then the bitrate will be determined. If we have bitrate and BER, bitrate will decide the modulation, once modulation is determined, the BER and SNR have a direct relation. If  we have SNR and bitrate, bitrate decide the modulation, then we can find the BER according to the SNR. If then BER is not good enough, and we can't improve SNR, then we have to decrease modulation scheme, then the bitrate decreased.

When SNR is fixed, then the channel has a fixed Shannon limitation, whatever modulation is used, the Bitrate cannot exceed the Capacity of the channel with error free. That means when SNR is fixed, we can use more aggressive modulation, then we have a higher bitrate, but the BER will increase sharply, it's no use to use this aggressive modulation.

BER before channel decoding is determined by SNR and Modulation scheme. Channel coding is used to improve decoded BER, but the best result of  Channel coding is the Shannon limitation, that means, again, when the SNR is fixed, the capacity of channel is fixed. Conversely, when the SNR is decided, we choose the modulation which is the capacity of channel, then we cannot receive every bit error free, we still get some errors, by channel coding, LDPC for example, we can correct these errors. Channel coding is used to reach the Shannon limitation.

