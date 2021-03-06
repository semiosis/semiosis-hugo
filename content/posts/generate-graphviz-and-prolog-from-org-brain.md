+++
title = "Generate graphviz and prolog from org-brain"
author = ["Shane Mulligan"]
date = 2021-05-06T00:00:00+12:00
keywords = ["graphviz", "prolog", "emacs"]
draft = false
+++

## Summary {#summary}

I would like to be creating graphs
interactively with `org-brain` and then using
them to generate graphviz, plantuml and
mermaid diagrams, and also knowledge bases with prolog.

Why Prolog? Prolog is incredibly useful for
querying KBs. If I plan on working for a large
corporation with their own ontologies and
knowledge graphs, then I will want to be
skilled with Prolog.


## Graphviz {#graphviz}

<!-- Play on asciinema.com -->
<!-- <a title="asciinema recording" href="https://asciinema.org/a/9CPWDAd1ZR4azTOxyXEosNOUr" target="_blank"><img alt="asciinema recording" src="https://asciinema.org/a/9CPWDAd1ZR4azTOxyXEosNOUr.svg" /></a> -->
<!-- Play on the blog -->
<script src="https://asciinema.org/a/9CPWDAd1ZR4azTOxyXEosNOUr.js" id="asciicast-9CPWDAd1ZR4azTOxyXEosNOUr" async></script>


### A generated graphviz `neato` diagram {#a-generated-graphviz-neato-diagram}

This has been generated from an `org-brain`.

It shows only the headings.

{{< figure src="/ox-hugo/brain-billboard-gv.png" >}}


### elisp {#elisp}

{{< highlight emacs-lisp "linenos=table, linenostart=1" >}}
(defvar org-brain-visited-ht)

(defun org-brain-recursive-associates-visited (entry max-level &optional func)
  "Return a tree of ENTRY and its (grand)associates up to MAX-LEVEL.
Apply FUNC to each tree member. FUNC is a function which takes an
entry as the only argument. If FUNC is nil or omitted, get the
raw entry data.
Also stop descending if a node has been visited before.
"
  (let ((en (org-brain-entry-name entry)))
    (if (not (and (ht-contains? org-brain-visited-ht (sxhash-equal en))))
        (cons (funcall (or func #'identity) entry)
              (when (> max-level 0)
                (ht-set org-brain-visited-ht (sxhash-equal en) t)
                (mapcar (lambda (x) (org-brain-recursive-associates-visited x (1- max-level) func))
                        (org-brain-associates entry)))))))

(defun org-brain-to-dot-associates (&optional depth)
  (interactive (list (string-to-int (read-string-hist "depth: " "5" nil 5))))

  (if (not depth)
      (setq depth 5))

  (let* ((recurfun 'org-brain-recursive-associates-visited)
         (tre
          (-flatten
           (progn
             (setq org-brain-visited-ht (make-hash-table))
             (funcall recurfun
                      (org-brain-current-entry) depth
                      (lambda (e)
                        (let* ((n (org-brain-entry-name e))
                               (fs (mapcar 'org-brain-entry-name (org-brain-friends e)))
                               (cs (mapcar 'org-brain-entry-name (org-brain-children e))))
                          (list
                           ;; (concat "[" n "]")
                           (if fs
                               (loop for f in fs
                                     collect
                                     (list (concat (e/q n) " -> " (e/q f))
                                           (concat (e/q f) " -> " (e/q n))))
                             ;; "FRIENDS"
                             )
                           (if cs
                               (loop for c in cs
                                     collect
                                     (concat (e/q n) " -> " (e/q c)))
                             ;; "CHILDREN"
                             )))
                        ;; (list (org-brain-entry-name e)
                        ;;       (org-brain-friends e))
                        ))))))

    (nbfs (snc "uniqnosort" (list2str tre)) nil 'graphviz-dot-mode))

  ;; (etv (pp-to-string (org-brain-recursive-children (org-brain-current-entry) 10 'org-brain-entry-name)))

  ;; (loop for c in t)
  )

(defun dot-digraph ()
  (interactive)
  (let* ((td (snc "td"))
         (f (snc (concat "cd " (e/q td) "; dot-digraph -norg") (region-or-buffer-string)))
         (fp (concat td f)))
    (my/copy fp)
    (if (interactive-p)
        (if (yn "show?")
            (sps (cmd "o" fp))))))

(defun neato-digraph ()
  (interactive)
  (let* ((td (snc "td"))
         (f (snc (concat "cd " (e/q td) "; neato-digraph -norg") (region-or-buffer-string)))
         (fp (concat td f)))
    (my/copy fp)
    (if (interactive-p)
        (if (yn "show?")
            (sps (cmd "o" fp))))))

(provide 'my-graphviz)
{{< /highlight >}}


### shell {#shell}

`dot-digraph`

{{< highlight bash "linenos=table, linenostart=1" >}}
#!/bin/bash
export TTY

read -r -d '' dotcode <<HEREDOC
# graph [style=filled,color=lightgrey,label=subgraph];
graph [style=filled,color=lightgrey];
# node [style=filled,color=white,shape=circle,fontsize=8,fixedsize=true,width=0.9];
node [style=filled,fillcolor=white,shape=circle];
# edge [fontsize=8];
# rankdir=LR;
# size="6,6";
HEREDOC

{
    echo "digraph Q {"
    echo "$dotcode"
    awk1
    echo "}"
} | dot-png "$@"
{{< /highlight >}}

`dot-png`

{{< highlight bash "linenos=table, linenostart=1" >}}
#!/bin/bash
export TTY

( hs "$(basename "$0")" "$@" "#" "<==" "$(ps -o comm= $PPID)" 0</dev/null ) &>/dev/null

do_org_output=y
while [ $# -gt 0 ]; do opt="$1"; case "$opt" in
    "") { shift; }; ;;
    -norg) {
        do_org_output=n
        shift
    }
    ;;

    -org) {
        do_org_output=y
        shift
    }
    ;;

    *) break;
esac; done

is_tty() {
    [[ -t 1 ]]
}

stdin_exists() {
    ! [ -t 0 ]
}

tf_dot="$(tf dot)"
# trap "rm \"$tf_dot\" 2>/dev/null" 0

# cat > "$tf_dot"

fn=$(basename "$tf_dot")
dn=$(dirname "$tf_dot")
ext="${fn##*.}"
mant="${fn%.*}"


nf="${mant}.png"
# echo "$nf" | tv &>/dev/null

if test -n "$1"; then
    nf="$1.png"
    shift
fi

if is_tty; then
    dot -q -T png "$@" "$tf_dot" > "$nf"
else
    # echo "$nf" | tv &>/dev/null
    dot -q -T png "$@" "$tf_dot" > "$nf"
fi

if test "$do_org_output" = "y"; then
    echo -n "[[file:$nf]]"
else
    echo -n "$nf"
fi
{{< /highlight >}}


## Prolog {#prolog}


## Mermaid {#mermaid}


## PlantUML {#plantuml}