+++
title = "Fictional statements of remorse with GPT-3 in the 1st and 3rd person"
author = ["Shane Mulligan"]
date = 2021-04-07T00:00:00+12:00
keywords = ["GPT-3", "openai", "emacs", "NLP"]
draft = false
+++

## Summary {#summary}

I use GPT-3 to generate fictional statements
of remorse.

It should be noted that this is only one such
way that GPT-3 will upheave legal process.


### RemorseBot (in the 1st person) {#remorsebot--in-the-1st-person}

<!-- Play on asciinema.com -->
<!-- <a title="asciinema recording" href="https://asciinema.org/a/iFH9YvDMVoM0aI2w28wmfc2Th" target="_blank"><img alt="asciinema recording" src="https://asciinema.org/a/iFH9YvDMVoM0aI2w28wmfc2Th.svg" /></a> -->
<!-- Play on the blog -->
<script src="https://asciinema.org/a/iFH9YvDMVoM0aI2w28wmfc2Th.js" id="asciicast-iFH9YvDMVoM0aI2w28wmfc2Th" async></script>


### RemorseBot (in the 3rd person) {#remorsebot--in-the-3rd-person}

<!-- Play on asciinema.com -->
<!-- <a title="asciinema recording" href="https://asciinema.org/a/UNgcBrZclb4ubP9GXDT99nRAa" target="_blank"><img alt="asciinema recording" src="https://asciinema.org/a/UNgcBrZclb4ubP9GXDT99nRAa.svg" /></a> -->
<!-- Play on the blog -->
<script src="https://asciinema.org/a/UNgcBrZclb4ubP9GXDT99nRAa.js" id="asciicast-UNgcBrZclb4ubP9GXDT99nRAa" async></script>


### Prompts {#prompts}

{{< highlight yaml "linenos=table, linenostart=1" >}}
title: "Statement of remorse"
# future-titles: ""
# aims: |+
# - More abstractive rewording
doc: ""
# aims: |+
# - More abstractive rewording
prompt-version: 1
# <:pp> defines a point where the following
# text is concatenated before the postprocessor
# is run.
# <1>, <2> etc. are where variables are substituted
prompt: |+
    The following documents to be submitted to
    the court prior to a defendants'
    sentencing. The documents are intended to
    convey the defendant's remorse.
    ###
    I am truly sorry for my inexcusable actions. I
    have caused real harm to the victims and have
    let down not only my family, but the community
    too. I want to do everything in my power to
    make things right.
    ###
# # Additional transformation of prompt after the template
# prompt-filter: "sed -z 's/\s\+$//'"
# # Trailing whitespace is always removed
# prompt-remove-trailing-whitespace: on
# myrc will select the completion engine using my config.
# This may be openi-complete or something else
engine: "myrc"
# if nothing is selected in myrc and openapi-complete is used
# by default, then openai should select this engine.
preferred-openai-engine: "davinci"
# 0.0 = /r/hadastroke
# 1.0 = /r/iamveryrandom
# Use 0.3-0.8
temperature: 0.8
max-tokens: 200
top-p: 1.0
# Not available yet: openai api completions.create --help
frequency-penalty: 0.5
# If I make presence-penalty 0 then it will get very terse
presence-penalty: 0.0
best-of: 1
# Only the first one will be used by the API,
# but the completer script will use the others.
# Currently the API can only accept one stop-sequence, but that may change.
stop-sequences:
# - "\n"
# - "\n\n"
# 2 hashes is more reliable as a stop sequence because
# sometimes the generation morphs from ### to ##
- "##"
inject-start-text: yes
inject-restart-text: yes
show-probabilities: off
# Cache the function by default when running the prompt function
cache: on
vars:
examples:
# Completion is for generating a company-mode completion function
# completion: on
# # default values for pen -- evaled
# # This is useful for completion commands.
# pen-defaults:
# - "(detect-language)"
# - "(pen-preceding-text)"
# These are elisp String->String functions and run from pen.el
# It probably runs earlier than the preprocessors shell scripts
pen-preprocessors:
- "pen-pf-correct-grammar"
# # A preprocessor filters the var at that position
# the current implementation of preprocessors is kinda slow and will add ~100ml per variable
# # This may be useful to distinguish a block of text, for example
# preprocessors:
# - "sed 's/^/- /"
# - "cat"
chomp-start: on
chomp-end: off
prefer-external: on
# This is an optional external command which may be used to perform the same task as the API.
# This can be used to train the prompt.
external: "generate-text-from-input.sh"
# This script returns a 0-1 decimal value representing the quality of the generated output.
quality-script: "my-quality-checker-for-this-prompt.sh"
# This script can be used to validate the output.
# If the output is accurate, the validation script returns exit code 1.
# The quality-script is sent to this script as the first argument.
validation-script: "my-validator-for-this-prompt.sh"
# Enable running conversation
conversation-mode: no
# Replace selected text
filter: no
# Keep stitching together until reaching this limit
# This allows a full response for answers which may need n*max-tokens to reach the stop-sequence.
stitch-max: 0
needs-work: no
n-test-runs: 5
# Prompt function aliases
# aliases:
# - "asktutor"
# postprocessor: "sed 's/- //' | uniqnosort"
# # Run it n times and combine the output
# n-collate: 10
# This for combining prompts:
# It might be, for example, summarize, or uniqnosort
# collation-postprocessor: "uniqnosort"
{{< /highlight >}}

{{< highlight yaml "linenos=table, linenostart=1" >}}
title: "Statement of remorse - third person"
# future-titles: ""
# aims: |+
# - More abstractive rewording
doc: ""
# aims: |+
# - More abstractive rewording
prompt-version: 1
# <:pp> defines a point where the following
# text is concatenated before the postprocessor
# is run.
# <1>, <2> etc. are where variables are substituted
prompt: |+
    The following documents to be submitted to
    the court prior to a defendants'
    sentencing. The documents are intended to
    convey the defendant's remorse.
    ###
    The defendant wishes to convey his genuine
    and sincere remorse for his actions. He
    acknowledges the emotional and physical
    pain he has caused and feels sick to his
    soul when he thinks about this.
    ###
# # Additional transformation of prompt after the template
# prompt-filter: "sed -z 's/\s\+$//'"
# # Trailing whitespace is always removed
# prompt-remove-trailing-whitespace: on
# myrc will select the completion engine using my config.
# This may be openi-complete or something else
engine: "myrc"
# if nothing is selected in myrc and openapi-complete is used
# by default, then openai should select this engine.
preferred-openai-engine: "davinci"
# 0.0 = /r/hadastroke
# 1.0 = /r/iamveryrandom
# Use 0.3-0.8
temperature: 0.8
max-tokens: 200
top-p: 1.0
# Not available yet: openai api completions.create --help
frequency-penalty: 0.5
# If I make presence-penalty 0 then it will get very terse
presence-penalty: 0.0
best-of: 1
# Only the first one will be used by the API,
# but the completer script will use the others.
# Currently the API can only accept one stop-sequence, but that may change.
stop-sequences:
# - "\n"
# - "\n\n"
# 2 hashes is more reliable as a stop sequence because
# sometimes the generation morphs from ### to ##
- "##"
inject-start-text: yes
inject-restart-text: yes
show-probabilities: off
# Cache the function by default when running the prompt function
cache: on
vars:
examples:
# Completion is for generating a company-mode completion function
# completion: on
# # default values for pen -- evaled
# # This is useful for completion commands.
# pen-defaults:
# - "(detect-language)"
# - "(pen-preceding-text)"
# These are elisp String->String functions and run from pen.el
# It probably runs earlier than the preprocessors shell scripts
pen-preprocessors:
- "pen-pf-correct-grammar"
# # A preprocessor filters the var at that position
# the current implementation of preprocessors is kinda slow and will add ~100ml per variable
# # This may be useful to distinguish a block of text, for example
# preprocessors:
# - "sed 's/^/- /"
# - "cat"
chomp-start: on
chomp-end: off
prefer-external: on
# This is an optional external command which may be used to perform the same task as the API.
# This can be used to train the prompt.
external: "generate-text-from-input.sh"
# This script returns a 0-1 decimal value representing the quality of the generated output.
quality-script: "my-quality-checker-for-this-prompt.sh"
# This script can be used to validate the output.
# If the output is accurate, the validation script returns exit code 1.
# The quality-script is sent to this script as the first argument.
validation-script: "my-validator-for-this-prompt.sh"
# Enable running conversation
conversation-mode: no
# Replace selected text
filter: no
# Keep stitching together until reaching this limit
# This allows a full response for answers which may need n*max-tokens to reach the stop-sequence.
stitch-max: 0
needs-work: no
n-test-runs: 5
# Prompt function aliases
# aliases:
# - "asktutor"
# postprocessor: "sed 's/- //' | uniqnosort"
# # Run it n times and combine the output
# n-collate: 10
# This for combining prompts:
# It might be, for example, summarize, or uniqnosort
# collation-postprocessor: "uniqnosort"
{{< /highlight >}}