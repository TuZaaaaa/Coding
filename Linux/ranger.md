# ranger

## generate default config 
ranger --copy-config=all
at home/.config/ranger

## file introduce
- command
	- commands.py
	- commads_full.py
- config
	- rc.conf
	- rifle.conf
		- defult open application
- scope.sh
	- preview script


## command
g

## return from ranger to terminal
shift + s
> back to current directory

## use custom ranger.rc
export RANGER_LOAD_DEFAULT_RC=FALSE

## hot key
map V console shell vim%space
map f console scout -ftsea%space --> find file
map ytv console shell youtube-dl -ic%space
input filename --> create a file
cw --> change word --> rename
y yank --> copy
a --> apped prefix end 
A --> apped suffix end
dd --> cut
space --> select / cancel
v --> select all / cancel
bulkrename --> batch rename
zh --> show hidden file
dU --> show size
q --> quit
o --> sort file
/ --> search file
r --> open with_
[ ] --> move parent directory
H L --> go back/forward in history
w --> task view
- dd cancel task
t -- > * mark

## config
set vcs_aware true --> show git status

- preview image in terminal
install w3m
set preview image
method w3m

- previed more
