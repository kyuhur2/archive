## display git branches

    parse_git_branch() {
        git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'
    }

    export PS1="\u@\h \[\e[32m\]\w \[\e[91m\]\$(parse_git_branch)\[\e[00m\]$ "

## recursively print and format repo as a tree

    print_tree() {
        git ls-tree -r --name-only HEAD | tree --fromfile
    }
