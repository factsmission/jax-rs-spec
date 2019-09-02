# JAX-RS-mod-qs Specification

This repository contains JAX-RS-mod-qs specification sources.

JAX-RS-mod-qs is a fork of the JAX-RS specification that allows for more powerful content negotiation, we hope that the modification will become part of future releases of JAX-RS.

In the original specification the q-value in the Accept-Header only indicates the preference, with this modification the q-value indicates the degree of preference. With the original specification `audio/*; q=0.2, audio/basic` only expresses that `audio/basic` is preferred over any other audio-format and the implementation must return `audio/basic` whenever it is capable of producing it. With this modified version the request header `audio/*; q=0.2, audio/basic` means "I prefer audio/basic, but send me any audio type if it is the best available after an 80% markdown in quality", the imlementation must send `audio/enhanced` if its producible quality - as expressed by the qs-value in the @Produces annotation - is more than 5 time higher than the producible quality for `audio/basic`.

## Background

The HTTP RFC 7231 defines the q-value as “relative ‘weight’ to the preference”.  The two examples provided in section 5.3.2 imply two different interpretations of what that means. The first interpretation the weight is relative to the quality in which the server can provide a specified format while in the second example the weight only indicates a relative preference. The JAX-RS specification interprets the q value and defines the qs value only as a sorting key to determine the preference over other formats in the same set.

I argue that following example included in RFC 7231 provides the better interpretation of the q-value and this would suggest a more powerful definition of the qs value:

        Accept: audio/*; q=0.2, audio/basic

    is interpreted as "I prefer audio/basic, but send me any audio type
    if it is the best available after an 80% markdown in quality".

By this interpretation a definition of qs would suggest itself by which qs is not merely a tertiary sorting criteria but an indicator of the quality in which a response in that format can be produced. So for example if the server can produce audio/basic; qs=0.1 and audio/extended; qs=1 the server should return audio/extended as after an 80% markdown in quality it is the best available format.

To achieve this, in Section 3.8 step 7 the secondary key for sorting M is defined as the product of q and qs and q is used as tertiary key, anaogous changes have been made in section 3.7.2.
