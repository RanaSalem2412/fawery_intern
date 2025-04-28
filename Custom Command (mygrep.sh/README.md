# Custom Grep Command (mygrep.sh)
### About
#### This script replicates basic grep functionality with support for:
##### Case-insensitive search
##### Showing line numbers (-n option)
##### Inverted matches (-v option)
##### Help menu (--help option)
##### Combining options like -vn or -nv
## Setup
#### 1. Create a Test File
```
vim testfile.txt
```
#### Content of testfile.txt
```
Hello world
This is a test
another test line
HELLO AGAIN
Don't match this line
Testing one two three
```
#### 2. Create the Script
```
vim mygrep.sh
```
#### Script Content
```
#!/bin/bash

# Help function
function show_help {
  echo "Usage: ./mygrep.sh [options] search_string filename"
  echo "Options:"
  echo "  -n        Show line numbers"
  echo "  -v        Invert match (show lines that do NOT match)"
  echo "  --help    Show this help message"
  exit 0
}

# Check if no arguments provided
if [[ $# -lt 1 ]]; then
  echo "Error: Not enough arguments."
  show_help
fi

# Variables
show_line_numbers=false
invert_match=false

# Parse options manually
while [[ "$1" == -* ]]; do
  if [[ "$1" == "--help" ]]; then
    show_help
  fi

  # Handle combined short options like -vn
  option="${1:1}"  # Remove leading '-'
  for (( i=0; i<${#option}; i++ )); do
    char="${option:$i:1}"
    case "$char" in
      n) show_line_numbers=true ;;
      v) invert_match=true ;;
      *)
        echo "Error: Unknown option -$char"
        show_help
        ;;
    esac
  done
  shift
done

# Now $1 = search string, $2 = filename
if [[ $# -lt 2 ]]; then
  echo "Error: Missing search string or filename."
  show_help
fi

search_string=$1
file=$2

# Check if file exists
if [[ ! -f "$file" ]]; then
  echo "Error: File '$file' not found!"
  exit 1
fi

# Main logic
line_number=0
while IFS= read -r line
do
  ((line_number++))
  if [[ "$invert_match" == false ]]; then
    if echo "$line" | grep -i -q "$search_string"; then
      if [[ "$show_line_numbers" == true ]]; then
        echo "${line_number}:$line"
      else
        echo "$line"
      fi
    fi
  else
    if ! echo "$line" | grep -i -q "$search_string"; then
      if [[ "$show_line_numbers" == true ]]; then
        echo "${line_number}:$line"
      else
        echo "$line"
      fi
    fi
  fi
done < "$file"
```
### How to Run
#### Make the script executable first:
```
chmod +x mygrep.sh
```
#### Then run any of the following commands:
```
./mygrep.sh hello testfile.txt
./mygrep.sh -n hello testfile.txt
./mygrep.sh -v -n hello testfile.txt
./mygrep.sh -vn hello testfile.txt
./mygrep.sh -v testfile.txt      # (Expect an error about missing search string)
./mygrep.sh --help
```
## Reflective Section
### 1. How the Script Handles Arguments and Options
#### The script manually parses options without using getopts.
#### It supports combined options (e.g., -vn).
#### After parsing options, it expects two remaining arguments: the search string and the filename.
#### If the arguments are missing, or the file does not exist, the script displays an appropriate error message.
#### Based on the flags:
##### -n adds line numbers to the output.
##### -v inverts the search (shows lines that do not match).
##### The search is case-insensitive by default using grep -i.
### 2. How to Extend the Script (Regex / Additional Options)
#### To support more options like:
##### Regex: No major changes needed. grep handles regex by default.
##### -i (case-insensitive) optional: Introduce a new flag to optionally enable -i, instead of making it always on.
##### -c (count matches): Detect the -c flag and modify the logic to count matching lines instead of printing them.
##### -l (list filenames only): Detect the -l flag to list only filenames if matches are found.
##### This would require expanding the option parsing logic and adjusting the execution block.
### 3. What Was the Hardest Part
#### The most challenging part was correctly handling combined options like -vn and -nv.
#### It required manually parsing each character after the dash (-) and setting corresponding flags.
#### Also, ensuring proper error handling for missing parameters or invalid options was tricky but essential for a good user experience.
### Bonus
#### Supports --help to display usage instructions.
#### Properly handles combined options.
#### Provides helpful error messages for incorrect usage.





 
