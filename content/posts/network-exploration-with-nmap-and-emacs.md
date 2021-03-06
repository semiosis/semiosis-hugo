+++
title = "Network exploration with nmap and emacs"
author = ["Shane Mulligan"]
date = 2021-05-05T00:00:00+12:00
keywords = ["nmap", "emacs", "tooling", "ops", "infra"]
draft = false
+++

## Summary {#summary}

I create some scripts based on `nmap` for network exploration.
I then make user interfaces for their output based on `tabulated-list-mode`.
I create some bindings to operate on the tabulated `nmap` output.


## Demo {#demo}

<!-- Play on asciinema.com -->
<!-- <a title="asciinema recording" href="https://asciinema.org/a/hEn3dOYCxpfi0YNqZppQuesq6" target="_blank"><img alt="asciinema recording" src="https://asciinema.org/a/hEn3dOYCxpfi0YNqZppQuesq6.svg" /></a> -->
<!-- Play on the blog -->
<script src="https://asciinema.org/a/hEn3dOYCxpfi0YNqZppQuesq6.js" id="asciicast-hEn3dOYCxpfi0YNqZppQuesq6" async></script>


## Scripts {#scripts}


### `nmap` wrapper {#nmap-wrapper}

{{< highlight bash "linenos=table, linenostart=1" >}}
#!/bin/bash
export TTY

( hs "$(basename "$0")" "$@" "#" "<==" "$(ps -o comm= $PPID)" 0</dev/null ) &>/dev/null

# https://hackertarget.com/nmap-cheatsheet-a-quick-reference-guide/

while [ $# -gt 0 ]; do opt="$1"; case "$opt" in
    "") { shift; }; ;;
    -csv) {
        csv_output=y
        shift
    }
    ;;

    *) break;
esac; done

test -f "/usr/bin/nmap" && : "${bin:="/usr/bin/nmap"}"

test -f "$bin" || agi nmap &>/dev/null

if test "$csv_output" = "y"; then
    out="$(0</dev/null tf csv)"
    "$bin" -oX "$out" "$@" &>/dev/null
    cat "$out" | nmap-xml2csv | pavd # pa -E "tf csv | xa fpvd"
else
    "$bin" "$@"  | pavd # pa -E "tf csv | xa fpvd"
fi
{{< /highlight >}}


### `nmap-s` {#nmap-s}

Scripts for `nmap`.

Symlink to `list-current-subnet-computers-details`.

{{< highlight bash "linenos=table, linenostart=1" >}}
#!/bin/bash
export TTY

sn="$(basename "$0")"

( hs "$(basename "$0")" "$@" 0</dev/null ) &>/dev/null

list_ip_range_for_subnet() {
    cidr="$1"
    : ${cidr:="192.168.1.0/24"}

    # $HOME/notes2018/ws/ip-networking/cidr.org

    nmap -sL -n "$cidr" | grep 'Nmap scan report for' | cut -f 5 -d ' '
    return 0
}

list_connected_raspberry_pi_dcbc() {
    nmap-s list_all_ssh_ips_for_subnet 192.168.1.1/24
}

list_current_subnet_computers_details() {
    oci -t 600 -E "$(cmd-nice nmap -csv -sT -F "$(i gateway)/24")" | pavd

    # n-list-open-ports -F localhost
    # list-ips-for-current-network
}

list_ips_for_current_network() {
    # list_all_ssh_ips_for_subnet "$(i gateway)/24"
    list_all_ips_for_subnet "$(i gateway)/24"
}

list_all_ips_for_subnet() {
    cidr="$1"
    : ${cidr:="$(ci -t 600 i gateway)/24"}

    nmap -sn "$cidr" | sed 1d | paste -s -d' \n' | sed '$d' | grep "Host is up" | rosie-ips

    # nmap -sn "$cidr" | aatr "\n\n" "tr '\n' _ | eipct -E $(aqf "grep -q open") | tr _ '\n'" | rosie-ips
}

list_all_ssh_ips_for_subnet() {
    while [ $# -gt 0 ]; do opt="$1"; case "$opt" in
        "") { shift; }; ;;
        -p) {
            port="$2"
            shift
            shift
        }
        ;;

        *) break;
    esac; done

    : ${port:="22"}

    cidr="$1"
    : ${cidr:="$(ci -t 600 i gateway)/24"}

    nmap -p "$port" "$cidr" | aatr "\n\n" "tr '\n' _ | eipct -E $(aqf "grep -q open") | tr _ '\n'" | rosie-ips
}

nmap_s() {
    eval "$@"
}

f_from_sn() {
    tr - _
}

eval "$(printf -- "%s" "$sn" | tr - _)" "$@"
{{< /highlight >}}


## emacs lisp {#emacs-lisp}


### Add to `my-server-suggest.el`. {#add-to-my-server-suggest-dot-el-dot}

Article explaining this
: [Auto-suggest tooling to handle ports on a network // Bodacious Blog](https://mullikine.github.io/posts/auto-suggest-tooling-to-handle-ports-on-a-network/)

<!--listend-->

{{< highlight emacs-lisp "linenos=table, linenostart=1" >}}
(defset server-command-tuples '((22 . ((sps (cmd "zrepl" "ssh" "-o" "BatchMode=no"
                                                 hn "-p" port))
                                       (call-interactively 'sql-mysql)))
                                (80 . ((chrome (concat "http://" hn ":" port))))
                                (443 . ((chrome (concat "https://" hn ":" port))))
                                (3306 . ((call-interactively 'connect-to-mysql)
                                         (call-interactively 'sql-mysql)))
                                (5432 . ((connect-to-postgres hn port "admin" "main" "admin")
                                         (connect-to-postgres hn port "ahungry" "ahungry" "ahungry")
                                         (call-interactively 'connect-to-postgres)
                                         (call-interactively 'sql-postgres)))))
{{< /highlight >}}


### Add `subnetscan` to the tabulated list mode {#add-subnetscan-to-the-tabulated-list-mode}

{{< highlight emacs-lisp "linenos=table, linenostart=1" >}}
(defset my-tablist-mode-tuples
  '(("list-venv-dirs-csv" . ("venv" t "30 40 20"))
    ("n-list-open-ports" . ("ports" t))
    ("mygit-tablist" . ("mygit" t))
    ("list-current-subnet-computers-details" . ("subnetscan" t))
    ("arp-details" . ("arp" t "20 20 20 20 20 20"))
    ("list-aws-iam-policies-csv" . ("aws-policies" t "30 80"))
    ("oci prompts-details -csv" . ("prompts" t "30 30 20 10 15 15 15 10"))
    ("upd list-aws-iam-users-csv" . ("aws-users" t "20 60 20"))))
{{< /highlight >}}


### Create functions for the `subnetscan` `tabulated-list` mode {#create-functions-for-the-subnetscan-tabulated-list-mode}

{{< highlight emacs-lisp "linenos=table, linenostart=1" >}}
(defun server-suggest-subnet-scan (hn)
  (interactive (list tablist-selected-id))
  (if tablist-selected-id
      (server-suggest tablist-selected-id)))

(define-key subnetscan-tablist-mode-map (kbd "'") 'server-suggest-subnet-scan)
{{< /highlight >}}


## `pathfinder` {#pathfinder}

Project code
: <https://github.com/TKCERT/pathfinder>

Pathfinder will make a dot graph out of `nmap`'s traceroute output.

I script around pathfinder to get it to generate an ASCII dot graph from a list of hostnames.

{{< highlight sh "linenos=table, linenostart=1" >}}
pathfinder mullikine.github.io semiosis.github.io
{{< /highlight >}}


### `demo` {#demo}

<!-- Play on asciinema.com -->
<!-- <a title="asciinema recording" href="https://asciinema.org/a/LH9r7DsTKvlOdNJZtcceO6V6Q" target="_blank"><img alt="asciinema recording" src="https://asciinema.org/a/LH9r7DsTKvlOdNJZtcceO6V6Q.svg" /></a> -->
<!-- Play on the blog -->
<script src="https://asciinema.org/a/LH9r7DsTKvlOdNJZtcceO6V6Q.js" id="asciicast-LH9r7DsTKvlOdNJZtcceO6V6Q" async></script>


### `scripts` {#scripts}

{{< highlight bash "linenos=table, linenostart=1" >}}
#!/bin/bash
export TTY

( hs "$(basename "$0")" "$@" "#" "<==" "$(ps -o comm= $PPID)" 0</dev/null ) &>/dev/null

while [ $# -gt 0 ]; do opt="$1"; case "$opt" in
    "") { shift; }; ;;
    -a) {
        all=y
        shift
    }
    ;;

    *) break;
esac; done

hn="$1"
test -n "$hn" || exit 1

dir="$MYGIT/TKCERT/pathfinder"

test -d "$dir" || {
    cd "$(gc "https://github.com/TKCERT/pathfinder")"
    sudo apt-get install graphviz graphviz-dev
    plf Graph::Easy # cpan
    py i pygraphviz
    py i untangle
} &>/dev/null

test -d "$dir" || exit 1

cd "$dir"

nm_fp="$(oci msudo nmap -xml -sn --traceroute "$@" | tf xml)"

pf_dot="$(tf dot)"

# sps v "$nm_fp"
#exit

{
    if test "$all" = "y"; then
        mypython pathfinder.py --destIpToNetwork -i "$nm_fp" -o "$pf_dot"
    else
        mypython pathfinder.py -i "$nm_fp" -o "$pf_dot"
    fi
}

cat "${pf_dot}.dot" | show-dot | pavs
{{< /highlight >}}