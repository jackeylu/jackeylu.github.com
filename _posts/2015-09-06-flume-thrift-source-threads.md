% java.lang.OutOfMemoryError for flume Thrift Source.
% Jackey Lyu
% 2015/9/6

# The reason.

If the conf file for flume use the thrift source, and the `threads`
was not set, then the default `threads` would be the Integer.MAX_VALUE.

But the implementation of thread pool `TThreadedSelectorServer` has some problem.

In the source code of flume-ng-core, 
the thread pool in  `flume-ng-core/src/main/java/org/apache/flume/source/ThriftSource.java` 
is as follow,
```Java
  private TServer getTThreadedSelectorServer() {
    ...

      if (maxThreads == 0) {
        sourceService = Executors.newCachedThreadPool(threadFactory);
      } else {
        sourceService = Executors.newFixedThreadPool(maxThreads, threadFactory);
      }

      ...
```

and the configuration for `maxThreads` in `configure` is,
```Java
@Override
  public void configure(Context context) {
   ...

    try {
      maxThreads = context.getInteger(CONFIG_THREADS, 0);
      maxThreads = (maxThreads <= 0) ? Integer.MAX_VALUE : maxThreads;
    } catch (NumberFormatException e) {
      logger.warn("Thrift source\'s \"threads\" property must specify an " +
        "integer value: " + context.getString(CONFIG_THREADS));
    }

    ...
```

From these two codes, we can figure that, if the user didn't set the `threads` parameter,
then, the `maxThreads` would be first set as 0, and then as it's `<=0`, it would be 
set as Integer.maxThreads finally.
So the `sourceService` would be a `FixedThreadPool`, but the pool is too large that OutOfMemoryError
will occur.


# How to solve it?

To avoid it, you have to set the `threads` parameter by hand.
Or patch your code like the issue's patch file in <https://issues.apache.org/jira/browse/FLUME-2785>.
