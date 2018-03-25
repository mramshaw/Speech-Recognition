# Speech Recognition with Python

I stumbled across this great tutorial, so why not try it out?

    https://realpython.com/python-speech-recognition/

As recommended, we will use [SpeechRecognition](https://github.com/Uberi/speech_recognition).

After setting up my own repo, I found the author's:

    https://github.com/realpython/python-speech-recognition

## Prerequisites

#### For microphone use

1. Check for `pyaudio`:

    $ pip list --format=legacy | grep pyaudio

[The next step is for linux; check the [pyaudio requirements](http://people.csail.mit.edu/hubert/pyaudio/#downloads) first.]

2. Install `portaudio19-dev`:

    $ sudo apt-get install portaudio19-dev

3. Install  `pyaudio`:

    $ pip install pyaudio

#### For speech recognition

1. Check for `pyaudio`:

    $ pip list --format=legacy | grep SpeechRecognition

2. Install `SpeechRecognition`:

    $ pip install --user SpeechRecognition

3. Verify:

    ``` Python
    >>> import speech_recognition as sr
    >>> sr.__version__
    '3.8.1'
    >>>
    ```

## Speech Engine

The tutorial uses __Google Web Speech API__, however it looks like installing [PocketSphinx](https://cmusphinx.github.io/) would be pretty easy.

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

[Slightly different from the tutorial's 'the snail smell of old gear vendors' and 'still smell of old beer vendors'.]

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

[Pretty much the same as 'the snail smell like old Beer Mongers'.]

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

## And finallly, the guessing game

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

## To Do

- [ ] Retry with PocketSphinx
