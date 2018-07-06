
# Task Scheduler Failed To Launch Action

I have a scheduled task that kicks off a powershell that in turn imports data into a db on a daily basis. The task stopped working one day and all I could see in the error message was:

```
Task Scheduler failed to launch action "powershell" in instance "{2ed8098a-db0e-4b6d-bf51-b14fb58fc38e}" of task "\Import data". Additional Data: Error Value: 2147942402.
```

Tried a few suggestions from stackoverflow but nothing worked. At last I noticed a critical piece of information in the task window: 

|Last Run Result|
|:---:|
|(0x80070002)|

This error code was way more useful than the error messages found in the history tab. It translates to **FILE NOT FOUND**. Following the lead I eventually worked out that the environment variable %PATH% was corrupt therefore executables and commands such as **powershell** and **sqlcmd** were not recognized.

Once %PATH% was fixed the scheduled task started to work again.
