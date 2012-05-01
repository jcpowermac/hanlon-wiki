# The Razor Logviewer Slice

As was stated previously, the `logviewer` slice is used to view the current Razor server logfile. It includes functionality that will allow the user to simply view the log file in its entirety (the default) or to tail or filter the log file by adding additional *sub-commands* to the basic `razor logviewer` command. In addition, these sub-commands can be chained (so that you can ask for a filtered tail of the current logfile or the tail of a filtered version of the logfile). We will provide more detail on the use of these sub-commands, below.

As is the case with most slices, adding the "help" sub-command to the basic `razor logviewer` command produces a usage line for the command:

    root@ubuntu-server:~# razor logviewer help
    
    Command help:  razor logviewer [tail [NLINES]] [filter EXPR]
    root@ubuntu-server:~# 

As you can see, the logviewer command supports two sub-commands as part of the slice. The `tail` sub-command will tail the current logfile (selecting NLINES from the end of the log). If the NLINES argument is not included in the command, then the logviewer will output the last 10 lines of the logfile (which is the same as the standard UNIX tail command). The `filter` sub-command can be used to filter the logfile, only selecting lines from the logfile that match the filter expression (which must be included as part of the filter sub-command). As was previously stated, these two sub-commands can be chained, but if they are chained the order in which they appear will likely be significant. Filtering the tail of a logfile (using a command like `razor logviewer tail 20 filter '{ ... }'`) will likely produce output that is quite different from tailing the filter of that same logfile (using a command like `razor logviewer filter '{ ... }' tail 20`). This isn't really difficult to see once you think about it, but we felt that it was important to emphasize this fact. When it comes to the order of the sub-commands in a `razor logviewer` command, order is significant.

We also would like to emphasize here that the `logviewer` slice does not currently provide a RESTful API. It was our thought that an ATOM-feed sort of mechanism might actually make more sense (where changes are pushed to a listener rather than having the listener pull changes using a RESTful API). For this reason, we've chosen to leave the RESTful API for the `logviewer` slice unimplemented (at least for now). We may revisit this question at a later date.