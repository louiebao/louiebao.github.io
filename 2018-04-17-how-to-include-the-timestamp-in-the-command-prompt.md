# How  to include the timestamp in the command prompt

**EDIT:** This is an old post. This trick is later incorporated into the posh-git setup.

I find myself working from the command prompt quite often and I always have the need to capture the screen shots for demonstration or auditing purposes, so it is vital to incorporate the timestamp in the command prompt.

Let me show you how it is done. Open up a command window and type:

```
PROMPT=$D$S$T$H$H$H$_$P$G
```

Depending on your locale and system settings, you'd see something like:

```
Tue 17/04/2018 16:19:48
c:\>
```

To make the timestamp a permanent feature, simply add it to the environment variables.
