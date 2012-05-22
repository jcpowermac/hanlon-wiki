# The Razor Log Slice - CLI

The *log* slice is used to view the current Razor server's logfile. It allows the user to simply view the log file in its entirety (the default) or to tail or filter the log file by adding additional sub-commands to the `razor log` command. In addition, these sub-commands can be chained (for example, a user can ask for a filtered tail of the current logfile or the tail of a filtered version of the logfile). See below for more detail on the use of these sub-commands.

As with most slices, adding the `help` sub-command to `razor log` produces a usage line for the command:

    root@ubuntu-server:~# razor log help
    
    Command help:  razor log [tail [NLINES]] [filter EXPR]
    root@ubuntu-server:~# 

As you can see, the `log` command supports two sub-commands as part of the slice. The `tail` sub-command will tail the current logfile (selecting NLINES from the end of the log). If the NLINES argument is not included in the command, then the log will output the last 10 lines of the logfile (as in the standard UNIX `tail` command). The `filter` sub-command can be used to filter the logfile, only selecting lines from the logfile that match the filter expression (which must be included as part of the filter sub-command). These two sub-commands can be chained, but if they are chained the order in which they appear will likely be significant. Logically enough, filtering the tail of a logfile, using a command like `razor log tail 20 filter '{ ... }'`, will likely produce output that is quite different from tailing the filter of that same logfile, using a command like `razor log filter '{ ... }' tail 20`.

It should be noted that the `log` slice does not currently provide a RESTful API. The reasoning is that an ATOM-feed sort of mechanism makes more sense (where changes are pushed to a listener rather than having the listener pull changes using a RESTful API). Consequently, the RESTful API for the *log* slice is not implemented (at least for now).

## Filter Expressions

The above example commands that included a `filter` subcommand (above) left out the filter expression (replacing it with an ellipsis). This is because the filter expressions themselves require some discussion and definition before real-world examples can be useful. To begin with, the basic `log` filter expression can include one or more of the following components:

- `**log_level**` &ndash; filters the matching logfile lines based on the log-level of the message (ERROR, INFO, DEBUG, etc.)
- `**class_name**` &ndash; filters the matching logfile lines based on the name of the class where the line was logged from
- `**method_name**` &ndash; filters the matching logfile lines based on the name of the method where the line was logged from
- `**elapsed_time**` &ndash; restricts the matching logfile lines to just those that were logged within the time window specified by this argument (see below for more information on the format options for this argument's value).
- `**log_message**` &ndash; filters the matching logfile lines based on the contents of the log message itself

These components are specified using a JSON-string that represents a hash-map of name/value pairs. The names are the component names shown above, while the values represent Ruby-style regular expressions that should be used to determine if a line matches or not. The only exception to this rule is that the value for the `elapsed_time` component is actually a string that defines a time window prior to the current time. This value consists of a number, possibly followed by a single-letter suffix that represents the units of time to be used in defining the desired time window. The currently supported list of values are:

<table>
    <tr>
        <th>Suffix</td>
        <th>Units</td>
    </tr>
    <tr>
        <td>s</td>
        <td>seconds</td>
    </tr>
    <tr>
        <td>m</td>
        <td>minutes</td>
    </tr>
    <tr>
        <td>h</td>
        <td>hours</td>
    </tr>
    <tr>
        <td>d</td>
        <td>days</td>
    </tr>
</table>

If no suffix is included, the time unit defaults to seconds. If the `elapsed_time` component is included in the filter criteria, then the specified time is used to determine how far back into the past the time window extends, and only log lines that occur **after** that time will successfully pass through the filter. 

As an example of how these components can be combined to form filter criteria, here's an example *log* command that will show all lines from the current Razor logfile that were output from the *mk_checkin* method of the *Engine* class that include the string *000C29291C95* as part of the log message and that were logged within the past 20 minutes.

    razor log filter '{"class_name":"Engine", "method_name":"mk_checkin", "log_message":"000C29291C95", "elapsed_time":"20m"}'

It should be noted that if any of these components are not included in the filter criteria, the slice will treat that component as a wildcard. If no filter criteria at all are included as part of the *filter* sub-command, an error will be returned (and a usage line will be output to help the user with their syntax).

## Other Examples

Lastly, it might be helpful to show a few examples of `log` commands (and their output). First, an example showing the entire logfile:

    root@ubuntu-server:~# razor log 
     ...
    D, [2012-05-01T11:07:43.222156 #18402] DEBUG -- ProjectRazor::Persist::MongoPlugin#is_db_selected?: Is ProjectRazor DB selected?(false)
    D, [2012-05-01T11:07:43.222184 #18402] DEBUG -- ProjectRazor::Persist::Controller#is_connected?: Checking connection (false)
    D, [2012-05-01T11:07:43.222238 #18402] DEBUG -- ProjectRazor::Persist::MongoPlugin#is_db_selected?: Is ProjectRazor DB selected?(false)
    D, [2012-05-01T11:07:43.222265 #18402] DEBUG -- ProjectRazor::Persist::Controller#is_connected?: Checking if DB is selected(false)
    D, [2012-05-01T11:07:43.222321 #18402] DEBUG -- ProjectRazor::Persist::MongoPlugin#is_db_selected?: Is ProjectRazor DB selected?(false)
    D, [2012-05-01T11:07:43.222375 #18402] DEBUG -- ProjectRazor::Persist::Controller#connect_database: Connecting to database(127.0.0.1:@config.persist_port) with timeout(10)
    D, [2012-05-01T11:07:43.222420 #18402] DEBUG -- ProjectRazor::Persist::MongoPlugin#connect: Connecting to MongoDB (127.0.0.1:27017) with timeout (10)
    D, [2012-05-01T11:07:43.224831 #18402] DEBUG -- ProjectRazor::Persist::MongoPlugin#is_db_selected?: Is ProjectRazor DB selected?(true)
    D, [2012-05-01T11:07:43.225018 #18402] DEBUG -- ProjectRazor::Persist::Controller#is_connected?: Checking if DB is selected(true)
    D, [2012-05-01T11:07:43.225226 #18402] DEBUG -- ProjectRazor::Persist::MongoPlugin#is_db_selected?: Is ProjectRazor DB selected?(true)
    root@ubuntu-server:~#

Note, that this output is truncated this output to keep things short and easy to read. The actual `razor log` output was almost 2900 lines long in this case. If you don't wish to view all 2900 lines of that file, simply tail the output as follows:

    root@ubuntu-server:~# razor log tail 10
    D, [2012-05-01T11:11:03.013335 #18479] DEBUG -- ProjectRazor::Persist::MongoPlugin#is_db_selected?: Is ProjectRazor DB selected?(false)
    D, [2012-05-01T11:11:03.013364 #18479] DEBUG -- ProjectRazor::Persist::Controller#is_connected?: Checking connection (false)
    D, [2012-05-01T11:11:03.013418 #18479] DEBUG -- ProjectRazor::Persist::MongoPlugin#is_db_selected?: Is ProjectRazor DB selected?(false)
    D, [2012-05-01T11:11:03.013462 #18479] DEBUG -- ProjectRazor::Persist::Controller#is_connected?: Checking if DB is selected(false)
    D, [2012-05-01T11:11:03.013504 #18479] DEBUG -- ProjectRazor::Persist::MongoPlugin#is_db_selected?: Is ProjectRazor DB selected?(false)
    D, [2012-05-01T11:11:03.013555 #18479] DEBUG -- ProjectRazor::Persist::Controller#connect_database: Connecting to database(127.0.0.1:@config.persist_port) with timeout(10)
    D, [2012-05-01T11:11:03.013601 #18479] DEBUG -- ProjectRazor::Persist::MongoPlugin#connect: Connecting to MongoDB (127.0.0.1:27017) with timeout (10)
    D, [2012-05-01T11:11:03.016010 #18479] DEBUG -- ProjectRazor::Persist::MongoPlugin#is_db_selected?: Is ProjectRazor DB selected?(true)
    D, [2012-05-01T11:11:03.016195 #18479] DEBUG -- ProjectRazor::Persist::Controller#is_connected?: Checking if DB is selected(true)
    D, [2012-05-01T11:11:03.016390 #18479] DEBUG -- ProjectRazor::Persist::MongoPlugin#is_db_selected?: Is ProjectRazor DB selected?(true)
    root@ubuntu-server:~#

In this case, using the `tail` command has just selected the last 10 lines of this logfile. A filter expression could be used to create even shorter output. For example, using the expression shown above returns:

    root@ubuntu-server:~# razor log filter '{"class_name":"Engine", "method_name":"mk_checkin", "log_message":"000C29291C95", "elapsed_time":"20m"}'
    D, [2012-05-01T10:53:52.930492 #18183] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T10:54:53.021584 #18197] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T10:55:53.048763 #18211] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T10:56:53.528274 #18225] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T10:57:53.174352 #18239] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T10:58:53.235623 #18253] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T10:59:53.297431 #18267] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:00:53.342661 #18281] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:01:53.432356 #18295] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:02:53.485060 #18309] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:03:53.560955 #18323] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:04:53.620811 #18337] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:05:53.732717 #18351] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:06:53.745645 #18365] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:07:53.790159 #18413] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:08:53.893051 #18428] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:09:53.958093 #18452] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:10:54.007124 #18466] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:11:54.149992 #18490] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:12:54.121999 #18504] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    root@ubuntu-server:~# 

As you can see, this expression actually matched 20 lines, one for each checkin made by the node with a UUID of 000C29291C95. This expression could be tailed to return only the last 10 lines, regardless of how many matched a particular expression:

    root@ubuntu-server:~# razor log filter '{"class_name":"Engine", "method_name":"mk_checkin", "log_message":"000C29291C95", "elapsed_time":"20m"}' tail 10
    D, [2012-05-01T11:06:53.745645 #18365] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:07:53.790159 #18413] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:09:53.958093 #18452] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:10:54.007124 #18466] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:11:54.149992 #18490] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:12:54.121999 #18504] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:13:54.468110 #18530] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:14:54.298483 #18544] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:15:54.349332 #18568] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:16:54.411133 #18582] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    root@ubuntu-server:~#

In this case, only the last 10 lines of the previous output are shown. If the order of these two "chained sub-commands" is reversed, the resulting output is quite different:

    root@ubuntu-server:~# razor log tail 500 filter '{"class_name":"Engine", "method_name":"mk_checkin", "log_message":"000C29291C95", "elapsed_time":"20m"}'
    D, [2012-05-01T11:16:54.411133 #18582] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:17:54.465077 #18609] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:18:54.751716 #18623] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    root@ubuntu-server:~#

This returns only three matching lines from the logfile, in spite of the fact that the number of lines included in the tail part of the command increased from 10 to 500. This is because the second command tails the last 500 lines of the logfile and then searches those lines for matches, whereas the first command initially filters the entire logfile to find matches, then selected the last 10 lines from those matches (the process is actually a little more complicated, see the note below on how to efficiently tail this filtered set of lines).

### On Efficiency in Tailing a Filtered Logfile:

At first glance it may seem that the easiest way to tail a filtered logfile would be as (simplistically) described in the examples above. However, there is no immediate way to know to the size of the logfile in question. If it is large, loading all of the matching lines into memory and then tailing the result of that set of lines is not a very efficient (or even reasonable) approach to this problem.

Keeping that in mind, the method used to tail the logfile efficiently must be revised. Rather than read the entire file, we "seek" to the end of the file, then read the file backwards in chunks (in this case 4096 bytes) and concatenate the chunks that are read together until the resulting (larger, typically) concatenated chunk of data has (at least) enough lines to satisfy the user's request. Once this many lines are found, the result is parsed (converting the chunk that has been read into lines) and then tailed to return the last NLINES lines from that chunk (returning it as an array to the caller, where it is printed out to the command line).

For the tail of the filtered log, a filter operation is inserted into the middle of the process just described. As with the tail of the logfile, the process starts by seeking to the end of the file and then reading backwards in chunks. Similarly, the read chunks are concatenated until there are enough lines to satisfy the user's request. Once that many lines have been read, the resulting concatenated chunk of data is parsed (converting it into lines) The desired filter is then applied to the resulting array. The lines that match are "pre-pended" to a "matching_lines" array which will be returned to the caller. If the number of lines is still insufficient, the number of lines desired is adusted and the process of reading backwards through the logfile in chunks starts again.

This process stops when one of the following criteria are met:

1. enough matching lines are found to satisfy the user's request, **or**
2. a chunk is found whose first line is prior to the defined time window (if any), **or**
3. the start of the file is reached

In the second or third cases, the chunks that have been read (so far) continue to be processed, and then return whatever results exist (which may be fewer lines than were requested in the *tail* subcommand).

Obviously, the worst-case scenario for this approach is if the entire file needs to be read in order to find enough lines to satisfy the user's request. In that case, reading from the beginning might have been more efficient, but that's typically not the way this command will be used. Consequently, the process of filtering, then tailing the results to obtain a small number of matching lines has been made more efficient. If the other case (tailing a lot of lines from the filtered result) better suits the user's needs, then just filtering the logfile and piping that output to the UNIX tail (or head) commands may be more useful.
