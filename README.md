ninjatracing
============

Convert .ninja_log files to chrome's about:tracing format.

Idea from Nick Carter, initial implementation from Richard Smith.

    $ ./ninjatracing
    Converts one (or several) .ninja_log files into chrome's about:tracing format

    Usage:
        ninja -C $BUILDDIR
        ninjatracing $BUILDDIR/.ninja_log > trace.json

    By default this will show build timing results for the most recent (possibly
    incremental) build. To show build timing results for every target, whether
    built in the last build or previously, use --showall. Note that this will
    overlap multiple builds and will thus exaggerate build parallelism.

    (When using --showall if you don't have time for a clean build, at least run
    `ninja -C $BUILDDIR -t recompact` first to remove no-longer-built targets.)