![Vorbis Logo](./vorbis-logo.svg)

Custom LibVorbis Build
--------------------

Vorbis is a well-established free audio codec. It delivers better quality
than older codecs such as AC3 and MP3 and it supports much higher bit rates
for cases where you want to go beyond.

libvorbis is a C/C++ library that contains the reference encoder and
decoder for the format.


What's in this repository?
--------------------------

This `CMakeLists.txt` is a custom build that *blindly* assumes a modern
desktop Linux build environment / Windows Visual Studio environment
and skips all configure checks.

You should prefer the `CMakeLists.txt` that ships with libopus.

This one exists specifically for some of my "Nuclex" projects, where
I'm being pedantic about compiler settings. The `CMakeLists.txt` in this
repository takes all C/C++ compiler options from an external CMake include
file (`../../build-system/cmake/cplusplus.cmake`), thus making sure that
all libraries involved in my tools are compiled with the exact same compiler
settings and that i.e. PGO can be used from end to end.
