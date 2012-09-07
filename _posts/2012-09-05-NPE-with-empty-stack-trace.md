Every day gives the more pleasant problems. This time it's the magic of the -XX:-OmitStackTraceInFastThrow VM flag. I don't know, maybe someone faced problem when JIT decides that exception is thrown too often in same place and replaces it with empty stacktrace-less stub in all next occurrencies. I believe that kind of optimization was directed to leverage costs of stack trace reterieval.

However, in our case the exception fellt to infrequent:

2012-08-22 14:29:52,911 FATAL: java.lang.NullPointerException
2012-08-22 14:30:01,578 FATAL: java.lang.NullPointerException
2012-08-22 14:30:10,303 FATAL: java.lang.NullPointerException

Then JIT starts replacing NPE object with static stub for a week (!) of application worktime. Hell yeah! You rock, JIT!
