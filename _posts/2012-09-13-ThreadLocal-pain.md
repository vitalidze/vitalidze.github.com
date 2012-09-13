---
layout: post
tags : [java, threadlocal]
---
{% include JB/setup %}

In our application we use several session factories, one per each database connection. Sessions are always created by thread local current-session context (we use default `org.hibernate.context.ThreadLocalSessionContext` implementation). The transactions are managed manually. If the transaction was opened but didn't closed then that particular thread will have a session in the thread-local variable. When session factory gets closed the reference remains in that thread local, which produces a memory leak.

As for now I've found [the only hardcore solution](http://blog.igorminar.com/2009/03/identifying-threadlocal-memory-leaks-in.html) for such issue. So when session factory gets closed walk through all thread locals and clean up.
