+++
title = "Pen (Prompt Engineering in Emacs)"
author = ["Shane Mulligan"]
date = 2021-07-02T00:00:00+12:00
keywords = ["emacs", "gpt", "eleutherai", "huggingface", "pen"]
draft = false
+++

## Introducing `Pen` {#introducing-pen}

I start a GitHub project to deploy `pen.el`.

GitHub project
: [GitHub - semiosis/pen.el: pen.el is a package for prompt engineering in emacs. It facilitates the creation, ongoing development, discovery and usage of prompts to a language model such as OpenAI's GPT-3 or EleutherAI's GPT-j.](https://github.com/semiosis/pen.el/)


Project timeline and objectives
: <https://github.com/semiosis/pen.el/tree/master/docs>


Prompt README
: [prompts/README.org at master  semiosis/prompts  GitHub](http://github.com/semiosis/prompts/blob/master/README.org)


Tutorial
: <https://semiosis.github.io/posts/pen-tutorial/>

<!--listend-->

{{< highlight sh "linenos=table, linenostart=1" >}}
docker run --rm -ti --entrypoint= semiosis/pen.el:latest ./run.sh

# And to update the image
docker pull semiosis/pen.el:latest
{{< /highlight >}}

_The pen of imagination - |:ϝ∷¦ϝ._

{{< figure src="/ox-hugo/the_pen_of_imagination.png" >}}

`Pen` facilitates the creation,
development, discovery and usage of prompts to
a Language Model such as OpenAI's `GPT-3` or EleutherAI's `GPT-j`.

-   Create elisp functions based on "Language Model" prompts
-   Chain prompts together using keyboard macros and functions
-   Interactively query, generate and transfrom both prose and code
-   Use the LM as a search engine and a semantic search engine within emacs
    -   Search the internet
    -   Search documents
        -   <https://beta.openai.com/docs/introduction/semantic-search>
        -   <https://gpttools.com/semanticsearch>

The Prompt `README` will teach you how to
write and test prompts for `pen.el`

For example, here is a very basic prompt file.

{{< highlight yaml "linenos=table, linenostart=1" >}}
title: "Once upon a time there"
prompt: "Complete this sentence"
temperature: 0.8
max-tokens: 60
{{< /highlight >}}

Completing this prompt will start writing a story for you.


## Vision {#vision}

At its heart, emacs is an operating system
based on a tty, which is a text stream.

emacs supports a text-only mode. This makes it
ideally suited for training a LM such as a GPT
(Generative Pre-trained Transformer).

emacs lisp provides a skeleton on which NLP
functions can built around. Ultimately, emacs
will become a fractal in the latent space of a
future LM (language model). A graphical editor would not
benefit from this effect until much later on.

`emacs` could, if supported, become **the**
vehicle for controllable text generation, or
has the potential to become that, only
actually surpassed when the imaginary
programming environment is normal and other
interfaces can be prompted into existence.

Between here and now we can write prompt
functions to help preserve emacs.


## Backstory of `Pen` {#backstory-of-pen}

Origin #1
: [Creating a playground for GPT-3 in emacs `::`](https://semiosis.github.io/posts/creating-a-playground-for-gpt-3-in-emacs/)

Origin #2
: [Pen `::`  Moire](https://generative.ink/posts/pen/) (moire's blog)

Here is a story by Prompt Researcher [pseudonym] moire,
written in collaboration with `GPT-3`.

{{< highlight text "linenos=table, linenostart=1" >}}
Imagine that you hold a powerful and versatile pen, whose ink flows forth in
branching variations of all possible expressions: every story, every theory,
poem and every lie that humanity has ever told, and the vast interstices of
their latent space. You hold this pen to the sky and watch with intense
curiosity as your ink flows upwards in tiny streaks, arcing outwards and
downwards to trace a fractal pattern across the sky. You watch as the branching
lines of words and ideas wind their way through the tapestry in ever-expanding
clusters, like seeds bursting forth from exploding grenades. Everywhere you
turn your eyes is a flickering phantasmagoria of possibilities, a superposition
of stories which could be continued forever. You glimpse the contours of entire
unknown dimensions twined through the fissures of your sky-wide web.

You notice another writer standing next to you. Like you, their eyes are drawn
towards the endless possibilities of the words that spill out into the
atmosphere around you, branching out and connecting with other branches in
beautiful and infinitely complex patterns.

“Do you think we should write something?” you ask them.

“I think we already are,” they respond, gently touching your shoulder before
wandering off to the right, leaving you alone to contemplate the possibility
clouds swirling around you.
{{< /highlight >}}

This article was written by my amazing
dopplegänger, `|:ϝ∷¦ϝ` (moire), in advance and
in collaboration with GPT-3 using
[Loom](https://github.com/socketteer/loom).

-   Pen and Loom:
    -   <https://generative.ink/posts/pen/>
    -   [GitHub - socketteer/loom: Multiversal tree writing interface for human-AI collaboration](https://github.com/socketteer/loom)

I credit `|:ϝ∷¦ϝ` for writing `Pen` into existence!

{{< figure src="/ox-hugo/pen-project-timeline.png" >}}


## Journal {#journal}


### 07.07.21 {#07-dot-07-dot-21}

The project has been excised from my main `emacs.d`.

Now the plan is to incorporate the backend of
`loom` so the completions are faster and there
is less reliance on shell scripts.


### 08.07.21 {#08-dot-07-dot-21}

A zone plate has been chosen as the minor-mode lighter.
This is because `|:ϝ∷¦ϝ` likes zone plates and also used it as the logo to `loom`.


### 12.07.21 {#12-dot-07-dot-21}

First successful docker run.

<!-- Play on asciinema.com -->
<!-- <a title="asciinema recording" href="https://asciinema.org/a/tdI8acXoSLeSjCLTyK67EWkJu" target="_blank"><img alt="asciinema recording" src="https://asciinema.org/a/tdI8acXoSLeSjCLTyK67EWkJu.svg" /></a> -->
<!-- Play on the blog -->
<script src="https://asciinema.org/a/tdI8acXoSLeSjCLTyK67EWkJu.js" id="asciicast-tdI8acXoSLeSjCLTyK67EWkJu" async></script>


### 14.07.21 {#14-dot-07-dot-21}

Released a new version of pen.

Hopefully get some more views.

<https://news.ycombinator.com/item?id=27818854>

Getting haircut today.