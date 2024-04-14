SPUTNIX USP FEC protocol general description
============================================

Motivation
----------

1. CubeSat/smallsat-friendly TM/TC protocol.
2. Strong and efficient FEC.
3. Variable frame length and structure to optimize request-response and
lockstep performance on half-duplex links.
4. Max. CCSDS-compatibility is a plus.
5. AX.25-compatibility is a plus.
6. Suitable for usage with low-power single-chip transceivers.
7. Simple enough to implement with open-source libs/tools without a lot of
coding.

Structure
----------

MSB first everywhere if not specified. The sequence is from the transmitter
perspective.

1. Preamble. >= 32 bits. 0x55555555 recommended.

2. Sync 64 bits 0x5072F64B2D90B1F5. Optimized for the first 32 bits being
detected by the single-chip transceiver in low-power receive mode. No
convolutional coding or scrambling for this field for the same reason.

3. PLS-code like DVB-S2. The code is exactly as per standard ETSI EN 302 307-1
clause 5.5.2 or CCSDS 131.2-B-1 clause 5.3.3. Used to switch frame length,
puncturing, etc. No convolutional coding or scrambling for this field.
Implemented values:  
0b0000000 - next is Reed-Solomon(255, 223), convolutional 1/2.  
0b0000001 - next is Reed-Solomon(80, 48), convolutional 1/2.  
No interleaving yet.

4. Reed-Solomon as per CCSDS 131.0-B-3 TM synchronization
and channel coding. Section 4. Dual basis.

5. Scrambling as per CCSDS 131.0-B-3 TM synchronization
and channel coding. Section 10.

6. Convolutional coding as per CCSDS 131.0-B-3 TM synchronization
and channel coding. Section 3.

7. Data payload inside FEC blocks. 2-byte EtherType-like tag used. To
encapsulate AX.25 the value 0x08FF used, like BPQ. The length field is 2-byte
little-endian, exact payload length, including AX.25 header. No bit-stuffing, no
HDLC, no CRC besides RS.

Requirements
-----------

1. gnuradio 3.10 (check branches for other versions)
2. gnuradio-osmosdr
3. gr-satellites