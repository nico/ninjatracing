ninjatracing
============

Convert .ninja_log files to chrome's about:tracing format.

Idea from Nick Carter, initial implementation from Richard Smith.

    $ ./ninjatracing
    Converts one (or several) .ninja_log files into chrome's about:tracing format

    Usage:
        rm $BUILDDIR/.ninja_log && ninja -C $BUILDDIR
        ninjatracing $BUILDDIR/.ninja_log > trace.json

    (If you don't have time for a clean build, at least run
    `ninja -C $BUILDDIR -t recompact` first.)
