---
layout: single
categories: posts
tags: posts
title: Syncing local development with UNSW CSE servers without SSHFS
postage: true
labels: project
---

*<span style="font-size: 0.8em">A script to sync, edit, and back-up remote code locally without mouting a remote filesystem using a wrapper around scp to allow development on multiple remote projects simultaneously within a local development environment.</span>*

## Context

I have been using my terminal exclusively to code for the past few months (using vim). The big problem I find when I code on the UNSW CSE servers is that vim has an annoyingly noticeable latency when coding remotely and I don't have an easy way to sync my neovim setup between my laptop and CSE whenever I make a change. After looking into my options, I decided that the best option was to create my own script that was a bit of a mix between git and my own mini filesystem using scp. I've attached the entire script here with instructions for full use. Note that I have only used this script for a few weeks and so it may have a lot of bugs. Please let me know if you find a mistake or want me to add any extra features.

## Code

```bash
#!/bin/bash

# Define variables for paths and options
REMOTE_PATH=
LOCAL_PATH=$(pwd)
BACKUP_PATH="$LOCAL_PATH/.lp_remote_backup"
REPLACE_FILES=false
ZID=zXXXXXXX

# Define a function to search for the .lp_config file in parent directories
find_lp_config() {
  local dir="$1"
  while [ "$dir" != "/" ]; do
    if [ -f "$dir/.lp_config" ]; then
      REMOTE_PATH=$(cat "$dir/.lp_config")
      LOCAL_PATH="$dir"
      return 0
    fi
    dir=$(dirname "$dir")
  done
  return 1
}

# Check for command line arguments
while getopts ":rlb" opt; do
  case $opt in
    r)
      REMOTE_PATH="$2"
      # Create backup directory if it doesn't exist
      if [ ! -d "$BACKUP_PATH" ]; then
        mkdir "$BACKUP_PATH"
      fi
      # SCP pull from remote directory to local directory and create backup
      scp -r "$ZID@login.cse.unsw.edu.au:~/$REMOTE_PATH/*" "$LOCAL_PATH"
      cp -r "$LOCAL_PATH"/* "$BACKUP_PATH"
      echo "Loaded remote files"
      echo "Backup created in $BACKUP_PATH"
      # Save remote directory to .lp_config file
      echo "$REMOTE_PATH" > .lp_config
      exit 0
      ;;
    l)
      # Look for .lp_config file in current and parent directories
      if ! find_lp_config "$LOCAL_PATH"; then
        echo ".lp_config file not found"
        exit 1
      fi
      # SCP push from local directory to remote directory
      scp -r "$LOCAL_PATH"/* "$ZID@login.cse.unsw.edu.au:~/$REMOTE_PATH"

      echo "Pushed files remote"
      exit 0
      ;;
    b)
      # Prompt user before replacing files with backup
      read -n1 -p "Are you sure you want to replace local files with backup files? (y/n) " confirm
      echo ""
      if [ "$confirm" = "y" ]; then
        cp -r "$BACKUP_PATH/$REMOTE_PATH" "$LOCAL_PATH"
        echo "Files replaced with backup"
      fi
      exit 0
      ;;
    \?)
      echo "Invalid option: -$OPTARG" >&2
      exit 1
      ;;
    :)
      echo "Option -$OPTARG requires an argument." >&2
      exit 1
      ;;
  esac
done

# If no options are given, show usage information
echo "Usage: lp [-r REMOTE_PATH] [-l] [-b]"
echo "-r REMOTE_PATH: SCP pull from REMOTE_PATH to current directory and create backup"
echo "-l: SCP push from current directory to remote directory"
echo "-b: Replace current files with backup files"
exit 1

```

## Installation and Setup

Setting it up is really easy - simply create a directory anywhere you want called sp and then copy paste the above code into a file called sp within that directory. Add executable permissions by running the command `chmod +x sp` within the sp directory. Replace zXXXXXXX with your zID on the 8th line and save the file. Now, open either your bashrc or zshrc file depending on whether you are using Windows WSL, MacOS or Linux. For MacOS, you should open ~/.zshrc and for the other ones, open ~/.bashrc and then add the following to the bottom of the file:

```bash
export PATH="$HOME/Documents/sp:$PATH"
```

but replace Documents with the path to where you added the sp directory from the home directory. Restart your terminal and you're all set.

## Usage

The premise of sp is to take an existing directory from your remote CSE account and allow you to develop to it locally seamlessly. Let's say I want to edit `binary_tree.c` and `bfs.c` from the remote CSE directory `~/COMP2521/lab03/`. To do this, in my local setup, I'm going to go to a directory where I want to edit the remote files and then run the command `sp -r COMP2521/lab03`. This will copy all the files from that path in my CSE directory to my local directory where I executed the command. Then, I can edit the files locally as normal, add new files and directories, or remove any files or directories. Then, when I'm done editing everything, I can run `sp -l` and it will push eveverything from my local directory to wherever was last pulled from (in this case it would be ~/COMP2521/lab03 in my CSE directory).

### -r \<path\>

Pulls all files from the remote CSE directory located at ~/<path> into the current local directory. Sets the remote-point to <path> so that the next push will be back to the same remote directory.

### -l

Pushes all local files in the directory back to the remote location that the files were pulled for. Note that you can run this command in any child directory of the parent where `sp -r <path>` was run and it will push all files from every child directory recursively of the parent. Additionally, files that have been deleted locally will remain existing remotely, however, I may add a feature to optionally change this feature if requested.

### -b

Replaces the directory where the original `sp -r <path>` command was run with a backup version of the remote files that were pulled over. Note, this command exists to revert back to the remote backup and once running this command, the directory will look the same as when the original `sp -r <path>` was run.

## Tips and Tricks

1. Please setup an SSH key otherwise this entire program will be extremely tedious and slow as it will ask for your password every time you run a command.
2. You can work on multiple different projects at the same time but make sure you execute the initial `-r` command in a separate local directory for each local project.


### References

A portion of the above code was written with assistance from ChatGPT. The entire functionality, design, and the majority of the code was done by myself.

### Comments

If you want me to add any features or fix bugs, please request it via a comment or dm me. Additionally, if you would like me to post a vim setup guide for CSE students let me know as well.
