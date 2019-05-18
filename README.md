# Speech Recognition with Python

[![Known Vulnerabilities](http://snyk.io/test/github/mramshaw/Speech-Recognition/badge.svg?style=plastic&targetFile=requirements.txt)](http://snyk.io/test/github/mramshaw/Speech-Recognition?style=plastic&targetFile=requirements.txt)

I stumbled across this great tutorial, so why not try it out?

    http://realpython.com/python-speech-recognition/

As recommended, we will use [SpeechRecognition](http://github.com/Uberi/speech_recognition).

After setting up my own repo, I found the author's:

    http://github.com/realpython/python-speech-recognition

![Raspberry](images/favicon.png)

This also works with Raspberry Pi (using Python 3).

## Contents

The contents are as follows:

* [Prerequisites](#prerequisites)
    * [For microphone use](#for-microphone-use)
    * [Optional: monotonic (for Python 2)](#optional-monotonic-for-python-2)
    * [For speech recognition](#for-speech-recognition)
* [Speech Engine](#speech-engine)
    * [Smoke Test](#smoke-test)
    * [Ambient Noise](#ambient-noise)
* [Speech testing](#speech-testing)
* [And finally, the guessing game](#and-finally-the-guessing-game)
* [Raspberry Pi](#raspberry-pi)
* [To Do](#to-do)

## Prerequisites

Python 3 and `pip` installed (Python 2 is scheduled for End-of-life, although the instructions and
code have been tested with Python 2 and an approprate `requirements` file for Python 2 is provided).

#### For microphone use

1. Check for `pyaudio`:

    ``` Python
    >>> import pyaudio as pa
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    ImportError: No module named pyaudio
    >>>
    ```

[The next step is for linux; check the [pyaudio requirements](http://people.csail.mit.edu/hubert/pyaudio/#downloads) first.]

2. Install `portaudio19-dev`:

    ```
    $ sudo apt-get install portaudio19-dev
    ```

3. Install `pyaudio`:

    ```
    $ pip install --user pyaudio
    ```

4. Verify installation:

    ``` Python
    >>> import pyaudio as pa
    >>> pa.__version__
    '0.2.11'
    >>>
    ```

#### Optional: monotonic (for Python 2)

[SpeechRecognition](http://github.com/Uberi/speech_recognition#monotonic-for-python-2-for-faster-operations-in-some-functions-on-python-2)
recommends installing [monotonic](http://pypi.python.org/pypi/monotonic) for Python 2 users.

1. Check for `monotonic`:

    ```
    $ pip list --format=freeze | grep monotonic
    ```

2. Install `monotonic`:

    ```
    $ pip install --user monotonic
    ```

3. Verify installation:

    ```
    $ pip list --format=freeze | grep monotonic
    monotonic==1.4
    $
    ```

#### For speech recognition

SpeechRecognition can be used as a _sound recorder_:

    http://github.com/Uberi/speech_recognition/blob/master/examples/write_audio.py

This is probably fine for occasional use - but there are better options available.

1. Check for `SpeechRecognition`:

    ```
    $ pip list --format=freeze | grep SpeechRecognition
    ```

2. Install `SpeechRecognition`:

    ```
    $ pip install --user SpeechRecognition
    ```

3. Verify:

    ``` Python
    >>> import speech_recognition as sr
    >>> sr.__version__
    '3.8.1'
    >>>
    ```


## Speech Engine

The tutorial uses the __Google Web Speech API__, however installing [PocketSphinx](http://cmusphinx.github.io/)
(which can work offline) is fairly easy.

[Snowboy](http://snowboy.kitt.ai/) (which can also work offline) is an option for Hotword Detection, but perhaps
unsuitable for speech recognition (SpeechRecognition tellingly refers to Snowboy as "Snowboy Hotword Detection").

For another online option, there is [Wit.ai](http://github.com/wit-ai/pywit) (which also has a [Node.js SDK](http://github.com/wit-ai/node-wit)).

#### Smoke Test

The final step may take a few seconds to execute:

``` Python
>>> import speech_recognition as sr
>>> r = sr.Recognizer()
>>> harvard = sr.AudioFile('audio_files/harvard.wav')
>>> with harvard as source:
...     audio = r.record(source)
... 
>>> type(audio)
<class 'speech_recognition.AudioData'>
>>> r.recognize_google(audio)
u'the stale smell of old beer lingers it takes heat to bring out the odor a cold dip restores health and zest a salt pickle taste fine with ham tacos al Pastore are my favorite a zestful food is the hot cross bun'
>>> 
```

#### Ambient Noise

``` Python
>>> jackhammer = sr.AudioFile('audio_files/jackhammer.wav')
>>> with jackhammer as source:
...     audio = r.record(source)
... 
>>> r.recognize_google(audio)
u'the snail smell of old beer drinkers'
>>> with jackhammer as source:
...     r.adjust_for_ambient_noise(source)
...     audio = r.record(source)
... 
>>> r.recognize_google(audio)
u'still smell old gear vendors'
>>> 
```

[Slightly different from the tutorial's `the snail smell of old gear vendors` and `still smell of old beer vendors`.]

And:

``` Python
>>> with jackhammer as source:
...     r.adjust_for_ambient_noise(source, duration=0.5)
...     audio = r.record(source)
... 
>>> r.recognize_google(audio)
u'the snail smell like old beermongers'
>>>
```

[Pretty much the same as `the snail smell like old Beer Mongers`.]


## Speech testing

Using the speech recognition module:

    $ python -m speech_recognition
    A moment of silence, please...
    Set minimum energy threshold to 259.109953712
    Say something!
    Got it! Now to recognize it...
    You said hello hello
    Got it! Now to recognize it...
    You said the rain in Spain
    Say something!
    ^C$

And:

``` Python
>>> with mic as source:
...     audio = r.listen(source)
... 
>>> r.recognize_google(audio)
u'Shazam'
>>>
```

And, as stated in the article, a loud hand-clap generates an exception:

``` Python
>>> with mic as source:
...     audio = r.listen(source)
... 
>>> r.recognize_google(audio)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/home/owner/.local/lib/python2.7/site-packages/speech_recognition/__init__.py", line 858, in recognize_google
    if not isinstance(actual_result, dict) or len(actual_result.get("alternative", [])) == 0: raise UnknownValueError()
speech_recognition.UnknownValueError
>>>
```


## And finally, the guessing game

Run the guessing game as follows:

    $ python guessing_game.py
    I'm thinking of one of these words:
    apple, banana, grape, orange, mango, lemon
    You have 3 tries to guess which one.
    
    Guess 1. Speak!
    You said: banana
    Incorrect. Try again.
    
    Guess 2. Speak!
    You said: Orange
    Incorrect. Try again.
    
    Guess 3. Speak!
    You said: mango
    Sorry, you lose!
    I was thinking of 'apple'.
    $


## Raspberry Pi

![Raspberry Pi](images/little_pi.png)

After hooking up a Raspberry Pi with a Logitech 4000 webcam (for its microphone)
and configuring with AlsaMixer, everything worked pretty much as expected with
Python 3.

There were some installation stumbles, but `sudo apt-get update` fixed them.

It turned out that `flac` was required so it was also installed.


## To Do

- [x] Add original License (this is probably 'fair use' but better safe than sorry)
- [x] Add `monotonic` as an optional component for Python 2
- [x] Retry with PocketSphinx (works offline)
- [x] Retry with [Snowboy](http://snowboy.kitt.ai/) (works offline)
- [ ] Retry with [Wit.ai](http://github.com/wit-ai/pywit) (which also has a [Node.js SDK](http://github.com/wit-ai/node-wit))
- [x] Try with Raspberry Pi (works nicely)
- [x] Update for recent versions of `pip`
- [x] Update code to conform to `pylint`, `pycodestyle` and `pydocstyle`
- [x] Update `requirements` files to fix Snyk.io quibbles
- [x] Update code for Python 3
- [x] Add table of Contents
