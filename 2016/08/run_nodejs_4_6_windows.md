# Run NodeJS 4 and 6 on Windows

If you ever want to have both NodeJS 4 and 6 versions on your computer, here is a trick to do it on Windows.

## First configuration

Open the Envrionment Variables configuration window.
Assuming you have installed NodeJS under the `C:\Program Files` directory,
add the `C:\Program Files\%NODE_HOME%` path to your `PATH` variable.

Then, add a `NODE_HOME` variable and set it to `nodejs`.

## Other version for NodeJS

When you will install NodeJS 6 for instance, set the installation folder to `C:\Program Files\nodejs6`.
Make sure to un-check the "Add to path" option in the NodeJS installer.

## All within the command line

Within the `cmd.exe`, run the `setx NODE_HOME nodejs6` command to set the new path to NodeJS 6.
Restart you shell and voilà!

To go back to NodeJS 4, do run the same command but with `nodejs` as the new value for `NODE_HOME`.
