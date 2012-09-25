The log slice can be used to view the details of the log file that is being used by a given Razor instance.  There is no current method for viewing an aggregate log view of the output from multiple Razor instances, nor does this slice provide the ability to view the contents of older versions of the log file.  Razor does maintain a set of historical log files, and rotates the current log into the historical log over time (this process of log file rotation results in aging out the oldest log files from the historical log over time).

## The log CLI

Here is a high-level summary of the commands that are available via the log CLI:
```bash
Usage: razor log (options...)
   -t, --tail NLINES                The number of lines to tail.
   -l, --log-level LOG_LEVEL        The log level pattern/value.
   -c, --class-name CLASS_NAME      The class name pattern/value.
   -m, --method-name METHOD_NAME    The method name pattern/value.
   -g, --log-message LOG_LEVEL      The log message pattern/value.
   -e, --elapsed-time LOG_LEVEL     The elapsed time limit (for messages).
   -n, --filter-before-tail         Apply filter before tailing (default).
   -r, --tail-before-filter         Apply tail before filtering
```
As can be seen from the high-level summary, options are available that allow for filtering of the log file based on based on patterns or values for the log level, class name where the log message was generated, method name where the log message was generated, and/or the contents of the log message.  There is even the an option that allows for filtering of log messages based on the time that has elapsed since the log message was posted to the log file.

The patterns that can be specified for the log level, class name, method name, and log message values take the form of either Ruby regular expressions or fixed strings that must be matched for a log message to pass through the filter being applied to the Razor log.  The elapsed time value must be of the form of an integer number, which may be followed by a single-character suffix that defines the units of the elapsed time window for the filter (eg. '30' or '30m').  The supported single-character suffix values (and their corresponding units) are as follows:

    s -- seconds (the default if no suffix is included)
    m -- minutes
    h -- hours
    d -- days

Finally, the user can define a number of lines to 'tail' from the log file (or from the filtered log file), and can also specify whether the filter should be applied before or after the tail operation (the default is to filter and then tail the filtered log, but sometimes the opposite order is desirable).

## The log RESTful API

The log slice does not support a RESTful API, and any attempts to access the resources from this slice via REST will result in a 'ProjectRazor::Error::Slice::NotImplemented' error being thrown (this error is mapped into an HTTP 403 error code).