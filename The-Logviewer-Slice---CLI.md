# The Razor Logviewer Slice - CLI

As was stated previously, the *logviewer* slice is used to view the current Razor server logfile. It includes functionality that will allow the user to simply view the log file in its entirety (the default) or to tail or filter the log file by adding additional sub-commands to the basic `razor logviewer` command. In addition, these sub-commands can be chained (so that you can ask for a filtered tail of the current logfile or the tail of a filtered version of the logfile). We will provide more detail on the use of these sub-commands, below.

As is the case with most slices, adding the *help* sub-command to the basic `razor logviewer` command produces a usage line for the command:

    root@ubuntu-server:~# razor logviewer help
    
    Command help:  razor logviewer [tail [NLINES]] [filter EXPR]
    root@ubuntu-server:~# 

As you can see, the logviewer command supports two sub-commands as part of the slice. The *tail* sub-command will tail the current logfile (selecting NLINES from the end of the log). If the NLINES argument is not included in the command, then the logviewer will output the last 10 lines of the logfile (which is the same as the standard UNIX tail command). The *filter* sub-command can be used to filter the logfile, only selecting lines from the logfile that match the filter expression (which must be included as part of the filter sub-command). As was previously stated, these two sub-commands can be chained, but if they are chained the order in which they appear will likely be significant. Filtering the tail of a logfile, using a command like `razor logviewer tail 20 filter '{ ... }'`, will likely produce output that is quite different from tailing the filter of that same logfile, using a command like `razor logviewer filter '{ ... }' tail 20`. The reasoning behind the significance of order between these two sub-commands isn't really difficult to see once you think about it, but we felt that this fact needed to be emphasized.

We also would like to emphasize here that the *logviewer* slice does not currently provide a RESTful API. It was our thought that an ATOM-feed sort of mechanism might actually make more sense (where changes are pushed to a listener rather than having the listener pull changes using a RESTful API). For this reason, we've chosen to leave the RESTful API for the *logviewer* slice unimplemented (at least for now). We may revisit this question at a later date.

## Filter Expressions

You may have noticed that in our two example commands that included a *filter* subcommand (above), we left the filter expression out (replacing it with an ellipsis). This is because we felt that the filter expressions themselves deserved a bit of discussion/definition before we showed some real-world examples. The basic *logviewer* filter expression can include one or more of the following components:

- **log_level** &ndash; Used to filter the matching logfile lines based on the log-level of the message (ERROR, INFO, DEBUG, etc.)
- **class_name** &ndash; Used to filter the matching logfile lines based on the name of the class where the line was logged from
- **method_name** &ndash; Used to filter the matching logfile lines based on the name of the method where the line was logged from
- **elapsed_time** &ndash; Used to filter restrict the matching logfile lines to just those that were logged within the time window specified by this argument (see below for more information on the options supported for the format of this argument's value).
- **log_message** &ndash; Used to filter the matching logfile lines based on the contents of the log message itself

These components are specified in the form of a JSON-string that represents a hash-map of name/value pairs. The names are the component names shown above, while the values represent Ruby-style regular expressions that should be used to determine if a line matches or not. The only exception to this rule is that the value for the elapsed_time component is actually a string that defines a time window prior to the current time. This value consists of a number, possibly followed by a single-letter suffix that represents that units of time that should be used in defining a time window based on that number. The currently supported list of values are:

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

If no suffix is included, the time units are assumed to be seconds. If this elapsed_time component is included in the filter specification, then the specified time is used to determine how far back into the past the time window extends, and only log lines that occur **after** that time will successfully pass through the filter represented by that filter specification. 

Just to show an example of how these components can be combined to form a filter specification, here's an example *logviewer* command that will show all lines from the current Razor logfile that were output from the *mk_checkin* method of the *Engine* class that include the string *000C29291C95* as part of the log message and that were logged within the past 20 minutes.

    razor logviewer filter '{"class_name":"Engine", "method_name":"mk_checkin", "log_message":"000C29291C95", "elapsed_time":"20m"}'

It should be noted that if any of these components are not included in the filter specification, then it is as if a wildcard argument that matches any value was passed as the value for the corresponding component. If no filter specification is included as part of the *filter* sub-command, that is an error (and a usage line will be output to help the user with their syntax).

## Other Examples

To close out our discussion of the logviewer, we thought it might be helpful to show a few examples of commands (and their output). First, an example of viewing the entire logfile:

    root@ubuntu-server:~# razor logviewer 
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

Note, that we've truncated this output to keep things short and easy to read. The actual `razor logviewer` output was almost 2900 lines long in this case. As was stated previously, if we don't want to view all 2900 lines of that file, we could simply tail the output as follows:

    root@ubuntu-server:~# razor logviewer tail 10
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

As you can see, there has been no truncation of the output in this case, we've just selected the last 10 lines of this logfile using the *tail* command. We could also use a filter expression to create a shorter output. In this case, let's use the expression we showed when we were discussing the format of these filter expressions (above):

    root@ubuntu-server:~# razor logviewer filter '{"class_name":"Engine", "method_name":"mk_checkin", "log_message":"000C29291C95", "elapsed_time":"20m"}'
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

As you can see, above, this expression actually matched 20 lines in this case, one for each checkin made by the node with a UUID of 000C29291C95 (in this case). We could tail this and get only the last 10 lines, regardless of how many matched a particular expression. An example of this is something like the following:

    root@ubuntu-server:~# razor logviewer filter '{"class_name":"Engine", "method_name":"mk_checkin", "log_message":"000C29291C95", "elapsed_time":"20m"}' tail 10
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

in this case, we selected the last 10 lines from the output shown previously. If we reverse the order of these two "chained sub-commands", we'll see a much different output:

    root@ubuntu-server:~# razor logviewer tail 500 filter '{"class_name":"Engine", "method_name":"mk_checkin", "log_message":"000C29291C95", "elapsed_time":"20m"}'
    D, [2012-05-01T11:16:54.411133 #18582] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:17:54.465077 #18609] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    D, [2012-05-01T11:18:54.751716 #18623] DEBUG -- ProjectRazor::Engine#mk_checkin: Node 000C29291C95 checkin accepted
    root@ubuntu-server:~#

As you can see, this only gives us three matching lines from the logfile, in spite of the fact that we've increased the number of lines included in the tail part of the command from 10 to 500. This is because, in the second command we're tailing the last 500 lines of the logfile and then searching for matching lines in the result, whereas in the first command we filtered the entire logfile to find the lines that matched, then selected the last 10 lines from that filtered set (actually it's more complicated than that, but you can think of it that way...if you are curious, see the note, below, on how we efficiently tail this filtered set of lines).

So, that shows all of the possibilities for the logviewer slice, from viewing the entire log file (the default), to tailing the logfile, to filtering the logfile, to tailing the filtered logfile, to filtering the tailed logfile. All are possible using the *logviewer* slice's CLI.

### On efficiency in tailing a filtered logfile:

It might seem at first glance, that the easiest way to perform this operation would be as we (simplistically) described it in our examples, above. Unfortunately, we have no assurance as to the size of the logfile in question (it could be quite large), so loading all of the matching lines into memory and then tailing the result of that set of lines really isn't a very efficient (or even reasonable) approach to this problem.

Keeping that in mind, we adapted the method that we use to tail the logfile efficiently in order to get a tail of the lines that match a given filter specification. In the case of tailing the logfile, we actually don't read the entire file. Instead, we "seek" to the end of the file, then read the file backwards in chunks (4096 byte chunks on our case) and concatenate the chunks that are read together until the resulting (larger, typically) concatenated chunk of data has (at least) enough lines to satisfy the user's request. Once this many lines are found, we parse the result (converting the chunk that has been read into lines) and then grab the last NLINES lines from that chunk (returning it as an array to the caller, where it is printed out to the command line). This mechanism was actually quite easy to adapt to our use.

For the tail of the filtered log, we simply insert a filter operation in the middle of the process we just described. As is the case with the tail of the logfile, we start by seeking to the end of the file and reading backwards in chunks from the end of the file. Also, as was the case with tailing the logfile, we continue to concatentate the chunks we read together until we get enough lines to satisfy the user's request. Once we've read that many lines, we parse the resulting concatenated chunk of data (converting it into lines), and then apply our filter to the resulting array. The lines that match are "pre-pended" to a "matching_lines" array which will be returned to the caller. If the number of lines is still insufficient, we adjust how many lines we are still looking for and re-start the process of reading backwards through the logfile in chunks.

This process stops when one of the following criteria are met:

- we find enough matching lines to satisfy the user's request, **or**
- we find a chunk who's first line is prior to the defined time window (if any), **or**
- we reach the start of the file

In the second or third cases (where we find a chunk who's first line is outside of the defined time window or where we reach the start of the file), we continue to process the chunks that have been read (so far), and return whatever results we have (which may be fewer lines than were requested in the *tail* subcommand.

Obviously, the worst-case scenario for this approach is if we have to read the entire file to find enough lines to satisfy the user's request (in that case, reading from the beginning might have been more efficient), but that's typically not the way that this command will be used, so we've chosen to make the process of filtering, then tailing the results to obtain a small number of matching lines more efficient. If the other case (tailing a lot of lines from the filtered result) is more to the user's liking, then just filtering the logfile and piping that output to the UNIX tail (or head) commands may make more sense.
