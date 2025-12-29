Jesper Jensen

I have updated the Hongkaide parser-script a bit, made it more robust against 'garbage' input, network-errors and other common problems.
- We can't have it crashing now, can we.. 😎
Also implemented a feature to empty [truncate] the database, mitigating a problem in the CPS - not being able to unload the digital contacts once imported (even though it makes no difference to the size of the codeplug-file that it is loaded with 20 MB of deadweight data).
Anyway.. With the new trunk feature you can produce a truncated database (CSV-file) with only one record - that you can load in stead of the full haystack. You can even specify and find your own DMR-ID for the record 😉
If you present the script with a ? you get the 'full' documentation 😎