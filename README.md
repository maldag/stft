# stft
Short time fourier transformation inspired by  Hristo Zhivomirov  on Matlab Central
# License
Copyright (c) 2015, Hristo Zhivomirov
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are
met:

    * Redistributions of source code must retain the above copyright
      notice, this list of conditions and the following disclaimer.
    * Redistributions in binary form must reproduce the above copyright
      notice, this list of conditions and the following disclaimer in
      the documentation and/or other materials provided with the distribution

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE
LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
POSSIBILITY OF SUCH DAMAGE.

# Example
```matlab
>>>>>>> 190b2cfb49b67b9d43e9739312df6ea680ccffca
clear, clc, close all

% load a .wav file
[x, fs] = wavread('track.wav');     % get the samples of the .wav file
x = x(:, 1);                        % get the first channel
xmax = max(abs(x));                 % find the maximum abs value
x = x/xmax;                         % scalling the signal

% define analysis parameters
xlen = length(x);                   % length of the signal
wlen = 1024;                        % window length (recomended to be power of 2)
h = wlen/4;                         % hop size (recomended to be power of 2)
nfft = 4096;                        % number of fft points (recomended to be power of 2)

% define the coherent amplification of the window
K = sum(hamming(wlen, 'periodic'))/wlen;

% perform STFT
[s, f, t] = stft(x, wlen, h, nfft, fs);

% take the amplitude of fft(x) and scale it, so not to be a
% function of the length of the window and its coherent amplification
s = abs(s)/wlen/K;

% correction of the DC & Nyquist component
if rem(nfft, 2)                     % odd nfft excludes Nyquist point
    st(2:end, :) = s(2:end, :).*2;
else                                % even nfft includes Nyquist point
    s(2:end-1, :) = s(2:end-1, :).*2;
end

% convert amplitude spectrum to dB (min = -120 dB)
s = 20*log10(s + 1e-6);

% plot the spectrogram
figure(1)
imagesc(t, f, s)
set(gca,'YDir','normal')
set(gca, 'FontName', 'Times New Roman', 'FontSize', 14)
xlabel('Time, s')
ylabel('Frequency, Hz')
title('Amplitude spectrogram of the signal')

handl = colorbar;
set(handl, 'FontName', 'Times New Roman', 'FontSize', 14)
ylabel(handl, 'Magnitude, dB')
