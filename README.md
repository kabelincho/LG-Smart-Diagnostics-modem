# LG-Smart-Diagnostics-modem
Initially a demodulator for LG Smart Diagnostics acoustic signals


LG Smart Diagnostics on home appliances appears to be using 4-MFSK for transmitting data using tones over an acoustic link either directly to a smartphone application or via a telephony carrier to support centers. The symbol rate appears to be ~25Hz.

The necessity to have robust transmission even after potentially multiple encoding and transcoding stages, as common in modern voice telecomms has led LG to adopt a very robust an low bitrate form of signaling - FSK. 4 tones, 1.4kHz, 1.55kHz, 1.75kHz and 1.95kHz are used to transmit digital data. These frequencies fall confortably within narrowband voice transmission bandidth of ~3.5kHz. These are also out of the DTMF tone range in order not to interfere with the telephone network.

The tones used appear to be 4 consecutive full-tones in standard pitch tuning:
F6 - 1396.91Hz
G6 - 1567.98Hz
A6 - 1760.00Hz
C7 - 2093.00Hz

This may have the effect of making the modem sound more melodic.

On recording I have access to it appears that the ome appliance generates the desired tone as square wave, most probably PWM directly from an MCU pin, which is either directly or via a simple transistor amplifier fed to a lodspeaker - most likely a piezzo buzzer. A lto of the transmitted energy is wasted in harmonics, but this is most probably not an issue.

This combined with the slow sybol rate makes for a somewhat musical signal. The dirty chiptune signal may also result in voice codecs recognising these sequences as voice and transmitting them better. Many codecs should be able to support tones in order to transmit DTMF. As tese tones are above DTMF it may be possible that some codecs will not recognise them as tones correctly and therefore need the extra harmonic content.


## Domodulation
There are many ways to demodulte FSK signals. I have decided to use non-synchronous detection in the time domain, as it seems simplest and most straight forward to implement.
From looking at recording it is clear that there is a preamble sequence which we can use to synchronise frames to. We can do this either on the fill band or on subband signals.
The receiver will have 4 decimating bandpass filters for te 4 tones used. A delay-multiply-filter detector with early-late symbol syncronisation will be implemented on each sub band. Once the optimal symbol sample point is found, all 4 subband will be sampled and the highest magnitude band will be detected.
Demapping will assume standard Grey coding starting from the lowest frequency.
It is not known if Manchester coding is applied to the bitstream before mapping to reduce runlengths.


From LG promotional material it appears that there is no heavy error correction applid, since you may be asked to repeat the recording.
It also appears that the Smart Diagnostic interface is normally not operated in receive mode.
A full diganostic receptions lasts ~17s - so at the symbol rate this gives ~425 symbols or ~850 bits. This makes it highly likely that the transmitted data is heavily compressed before transmission and without much protocol overead.

## Why do this?
This all sounds like a lot of work for no gains, why do this?

It is a challange to look at an unknown signal, recognite the type and chose how to attack it. This is not too diffucult to do, but I have a soft spot for acoustic communications and I personally find this neat and also a lot more secure than having your washing machine connected to your home WIFI all the time and vaunerable to being hacked. And with just TX capabilities, this pretty much makes sure that no-one will hack your appliance.

And lastly, while I write signal processing algoritms in Matlab, C, and assembly on a daily basis, I have never really used python to do tis and this seemed like a fun project to get me started on python signal processing.



