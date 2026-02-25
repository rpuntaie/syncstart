=======================================
syncstart(1) Version 1.1.2 \| syncstart
=======================================

SYNOPSIS
========

Command line help::

    usage: syncstart [options] in1 in2
    
    CLI program to compute timing offset (seconds) of media file 1 (in1)
    in referenceto media file 2 (in2) using their audio or video streams.
    ffmpeg is required.
      
    
    positional arguments:
      in1                   Offset media file.
      in2                   Reference media file.
    
    options:
      -h, --help            show this help message and exit
      --version             show program's version number and exit
      -v, --video           Compare video streams. (audio is default)
      -b BEGIN, --begin BEGIN
                            Begin comparison X seconds into the inputs. (default: 0)
      -t TAKE, --take TAKE  Take X seconds of the inputs to look at. (default: 20)
      -n, --normalize       Normalizes audio/video values from each stream.
      -d, --denoise         Reduces audio/video noise in each stream.
      -l LOWPASS, --lowpass LOWPASS
                            Audio option: Discards frequencies above the specified Hz, e.g., 300. 0 == off (default)
      -c, --crop            Video option: Crop to 4:3. Helpful when aspect ratios differ.
      -s, --show            Suppress "show diagrams", in case you are confident.
      -q, --quiet           Suppresses standard output except for the CSV result. Output will be:
                            file_to_advance,seconds_to_advance


DESCRIPTION
===========


The steps taken by ``syncstart``:

- get the maximum audio sample frequency or video frame rate among the inputs using ffprobe
- process and extract sample audio/video clips using ffmpeg with some default and optional filters
- read the two clips into a 1D array and apply optional z-score normalization
- compute offset via correlation using scipy ifft/fft
- print ffmpeg/ffprobe output (optional)
- show diagrams to allow MANUAL correction using ZOOM (optional)
- print result as human readable, or print and return result as CSV

MANUAL correction with ZOOM:

- at the checkbox on the top right check or uncheck to make the statement true
- turn on ZOOM with the magnifying glass on the bottom left
- draw a rectangle that stretches between two X that should coincide
  (Y is not relevant but don't make it 0)
- read the final correction from the top right or in the final output

Requirements:

- ffmpeg and ffprobe installed
- Python3 with tk (tk is separate on Ubuntu: python3-tk)

References:

- https://ffmpeg.org/ffmpeg-all.html
- https://dsp.stackexchange.com/questions/736/how-do-i-implement-cross-correlation-to-prove-two-audio-files-are-similar
- https://dsp.stackexchange.com/questions/18846/map-time-difference-between-two-similar-videos

Within Python:

from syncstart import file_offset




INSTALLATION
============

To install for user only, do::

    pip install --user syncstart

Or activate a virtualenv and do::

    pip install syncstart

To make syncstart an executable in your PATH on Windows, do:

    # Install pip if you don't have it
    py -m ensurepip
    # Upgrade and ensure pip prefers official wheels
    py -m pip install --upgrade pip setuptools wheel
    # Install/update pipx
    py -m pip install --user --upgrade pipx
    py -m pipx ensurepath
    pipx install syncstart
    # Upgrade numpy inside pipx venv
    pipx runpip syncstart install --upgrade numpy
    # Test syncstart.exe by reading the version
    syncstart --version

EXAMPLES
--------

::

    # compute audio offset with default settings:
    syncstart from_s10.m4a from_gopro.m4p
    
    # compute audio offset using first 10 seconds with denoising, normalization and a 300 Hz lowpass filter:
    syncstart -t 10 -dnl 300 video1.mp4 video2.mkv
    
    # compute video offset using first 20 seconds, don\[aq]t show plots, only output final result:
    syncstart -vsq video1.mp4 video2.mkv
    
    # compute video offset using seconds 15 to 25 with denoising, cropping and normalization:
    syncstart -b 15 -t 10 -vdcn video1.mp4 video2.mkv

License
-------

MIT

