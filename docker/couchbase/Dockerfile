FROM couchbase:community-4.5.1
RUN ulimit -n 40960        # nofile: max number of open files
RUN ulimit -c 100000000    # core: max core file size
# RUN ulimit -l unlimited  # TODO: Doesn't work!