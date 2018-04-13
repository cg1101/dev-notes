# port


## glossaries

### portdir

### portname


### pseudo-portname


### port-expressions

### port-url


### action


### targets
||activate       || - activate installed port||

archive         - create an archive of a port
archivefetch    - fetch archive of given ports if available
build           - build given port
cat             - print contents of a port file
cd              - change working directory to the one containing given port, for shell use
checksum        - compute the checksums of the distribution files of given port
clean           - clean the files used for building a port
configure       - runs configure process for port
contents        - list files installed by a port
deactivate      - deactivate installed port
dependents      - lists installed ports that depend on the port
deps            - lists dependencies
destroot        - installs a port to a temporary directory
diagnose        - detects common issues
dir             - displays the path to the directory containing port
distcheck       - check if distfiles haven't changed and can be fetched
distfiles       - display each distfile, its checksums and URLs used to fetch it
dmg             - creates an internet-enabled disk image containing an OS X package of portname
echo            - writes to stdout, often used to expand pseduo portnames
edit            - opens portfile of a given file in editor
exit            - exit the shell mode
extract         - extracts the distribution files
fetch           - fetches distribution files required to build given port
file            - display path to the portfile of given port
gohome          - loads the homepage of the given port
help            - show port help
info            - display meta-infomation
install         - install
installed       - show the installed version, variants and activation status of each (or all) port(s)
lint            - check a port for common problems
list            - list of all available port or list given ports
livecheck       - check if software hasn't been updated since the portfile was last modified
load            - provide a shortcut to using launchctl to load a port's daemon
location        - show install location of given port
log             - parses and shows log files for a port
logfile         - show log file path of given port
mdmg            - create an internet-enabled disk image containing an OS X metapackage of port and its dependencies
mirror          - create/update a local mirror of distfiles
mpkg            - create an OS X installer metapackage of given port and its dependencies
notes           - display notes for port
outdated        - list installed ports that need an upgrade
patch           - applies required patches to port's extracted files
pkg             - creates an OS X installer package of portname
platform        - print the OS version for which macports was built
provides        - determines which port owns a given file and can take relative path
quit            - same as exit
rdependents     - lists dependents recursively
rdeps           - lists depencies recursively, flags: --full, --index, --no-build
reclaim         - reclaim disk space by uninstalling inactive ports
reload          - shortcut launchctl to reload a daemon
rev-upgrade     - manually check broken binaries and rebuild ports containing broken binaries
search          - search for port
select          - selects version of a given group
selfupdate      - update macports system
setrequested    - mark a port requested
setunrequested  - alias of unsetrequested
space           - show the disk usage of the given ports
sync            - performs a sync operation from MacPorts rsync server
test            - run the test phase of a given port
unarchive       - extract the destroot of the given ports from a binary archive
uninstall       - uninstall
unload          - a shortcut to launchctl to unload a daemon
unsetrequested  - mark a port unrequested
upgrade         - upgrade a port and its dependencies (-n/-R)
url             - display URL for the path of the given portname
usage           - display a condensed usage summary
variants        - list the variants available for port
version         - display release number of installed macport infrastructure
work            - displays the path to the work directory of given port
