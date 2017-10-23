BPM Library: Log
================

Add logging capabilities to your programs. Send messages to `stdout`, `stderr`, a log file and `syslog`. Have debug messages display only for select components or hide them entirely with an environment variable.


Installation
============

Add to your `bpm.ini` file the following dependency.

    [dependencies]
    0=log

Run `bpm install` to add the library. Finally, use it in your scripts.

    #!/usr/bin/env bash
    . bpm
    bpm::include log

    log::debug "This is a debug message and will not normally be shown."
    log::debug "Set the DEBUG environment variable to see it."
    log::warn "Some recoverable problem happened."


API
===


[//]: # (AUTOGENERATED FROM liblog - START)

`log::debug()`
--------------

This logging function helps diagnose errors or provide additional details about what's happening in your scripts.  Use this to log every action that is performed.  Debug output can be enabled by setting the `DEBUG` environment variable.  When enabled, debug output is written to `stderr` and are also passed to `log::toFile` so they could be written to a log file. This also will colorize the output when the `LOG_COLOR` environment variable is set to a non-empty string.

* $@         - The message to log.  Arguments are joined into one line.
* $DEBUG     - Controls if logging is enabled and where it's enabled.
* $LOG_COLOR - When set to non-empty string, ANSI color is enabled.

When the DEBUG environment variable is not set, debug is disabled.  If DEBUG is set to `*`, `all` or `true`, this always logs.  In all other cases, DEBUG is assumed to be either a name of a function or a list of function names that should have debug logging enabled.  When FUNCNAME lists one of these functions, logging will happen.  This means you can turn on logging for one function and logging is enabled for that function and for all code that the function will execute, so child functions will also write debug output.

Writing to `stderr` is intentional.  This way you can enable debugging and still get valid output captured.

Examples

    # Forcibly disable all logging.
    DEBUG= ./thing

    # Enable all debugging (two methods).
    DEBUG=true ./thing
    DEBUG="*" ./thing

    # Enable logging for a function named "doMagic".
    DEBUG=doMagic ./thing

    # Enable logging for the functions "rainbow", "pony" and "unicorn".
    DEBUG="rainbow pony unicorn" ./thing

    # Enable debug logging for all subsequent commands.
    export DEBUG=true
    ./thing1
    ./thing2

Returns nothing.


`log::error()`
--------------

Logging function for errors.  Use this to log error messages right before you exit the program or return a failure.  All messages are written to `stderr`. Log messages are also passed to the `log::toFile` function to be logged to a file.  (See `log::toFile` for information about log files.) This also will colorize the output when the `LOG_COLOR` environment variable is set to a non-empty string.

* $@         - The message to log.  Arguments are joined into one line.
* $LOG_COLOR - Colorizes output when set to a non-empty string.

Examples

     log::error "Could not find some vital thing"

     exit 1

Returns nothing.


`log::info()`
-------------

Writes an informational message to the logging system.  Informational messages are written to `stdout` unless `$LOG_QUIET` is set to a non-empty value.  The message is also sent to `log::toFile` for possible output to a log file.  See `log::toFile` for information regarding logfiles.  When `$LOG_COLOR` is set to a non-empty string, the output is also colorized.

* $@          - The message to write.  Arguments are joined into one line.
* $LOG_COLOR  - When set to a non-empty value, the output is colorized.
* $_LOG_QUIET - When set to a non-empty value, the output is suppressed.

Returns nothing.


`log::toFile()`
---------------

Write a log message to a file or to a logging system.  Used by `log::debug`, `log::info`, `log::warn` and `log::error`.  Should not be used directly.

* $1       - Log level. One of ERROR WARN INFO DEBUG.
* $2       - The message to write.
* $LOGFILE - Destination for log messages.

Uses the environment variable `$LOGFILE` to determine where to log messages.  Will write a timestamped messsage to that file, creating the file if necessary.

If `$LOGFILE` starts with "syslog:", this will use the `logger` command to write a line to syslogd.  The facility defaults to user, but you can use any facility; for example you can use the mail facility by setting `LOGFILE="syslog:mail"`.  Additionally you can set a the tag by adding a second ":" followed by the desired tag; for example you can set the tag to myapp by setting `LOGFILE="syslog::myapp".

Otherwise, `$LOGFILE` should be set to a filename.  Messages are prefixed with a timestamp.

Examples

    # Send output to /var/log/messages
    LOGFILE=/var/log/messages ./your-command

    # Send messages to /var/log/syslog using the daemon facility
    LOGFILE=syslog:daemon ./my-background-thing

    # Send messages to syslog using the "user" facility (default) and
    # tagging them with "awesome"
    LOGFILE=syslog::awesome

Returns nothing.


`log::warn()`
-------------

Logging for warnings, such as when problems are detected and they aren't severe enough to warrant an error and termination of the function.  Warning messages are always written to `stderr`.  See `log::toFile` for information about writing log messages to files.  This also colorizes the output when `$LOG_COLOR` is set to a non-empty value.

* $@         - The text to log.  Arguments are appended into a single line.
* $LOG_COLOR - When set to a non-empty string the output is colorized.

Examples

    if [[ -f /some/file ]]; then
        log::warn "File exists when it should not."
        rm /some/file
    fi

Returns nothing.

[//]: # (AUTOGENERATED FROM liblog - END)


License
=======

This project is placed under an [MIT License](LICENSE.md).