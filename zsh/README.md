## display git branches

```zsh
# Function to get the current git branch
function git_branch {
    git rev-parse --abbrev-ref HEAD 2>/dev/null
}

# Function to get the parent git folder name
function parent_git_folder_name {
    basename "$(git rev-parse --show-toplevel 2>/dev/null)"
}

# Function to get the path within the git repository
function git_relative_path {
    git rev-parse --show-prefix 2>/dev/null
}

# Define colors
autoload -U colors && colors

# Colors for different parts of the prompt
USER_COLOR="%{$fg[green]%}"
HOST_COLOR="%{$fg[cyan]%}"
DIR_COLOR="%{$(echo -e '\e[38;5;33m')%}"   # ANSI code for bold and bright blue
GIT_COLOR="%{$(echo -e '\e[38;5;196m')%}"  # ANSI code for bold and bright red
NO_COLOR="%{$reset_color%}"
CURRENT_DIR_COLOR="%{$fg[white]%}"

# Custom prompt function
function set_prompt {
    local git_branch=$(git_branch)
    local git_folder_name=$(parent_git_folder_name)
    local git_relative_path=$(git_relative_path)
    local dir

    if [[ -n $git_folder_name ]]; then
        dir="${git_folder_name}/${git_relative_path}"
    else
        dir=${PWD}
    fi

    if [[ -n $git_branch ]]; then
        PROMPT="${NO_COLOR}[${USER_COLOR}%n${NO_COLOR}@${HOST_COLOR}%m${NO_COLOR}] ${DIR_COLOR}[$dir] ${GIT_COLOR}($git_branch)${NO_COLOR} \$ "
    else
        PROMPT="${NO_COLOR}[${USER_COLOR}%n${NO_COLOR}@${HOST_COLOR}%m${NO_COLOR}] ${CURRENT_DIR_COLOR}[$dir] ${GIT_COLOR}()${NO_COLOR} \$ "
    fi
}
```

    # Set prompt
    precmd_functions+=(set_prompt)

