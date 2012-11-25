---
layout: post
tags : [shell, bash, du, progress, bar]
---
{% include JB/setup %}

Bash coding is a mistreious thing I can't get used at. The syntax is awful, it doesn't look like anything that I use in my every day work.

Today, I'm doing an upgrade of our support desk tool. Their upgrade tool makes a mandatory backup. It's size on our installation in ~46GB, so when waiting I've decided to write an shell script that will check an upgrade file to reach the pre-defined size, display progress bar and size in MB.

The following things I've learned during writing:

* to read the output of command right to the variable - use back quotes \`. The key is usually at the top lef corner of the main keyboard right below the 'Esc' button. I almost haven't used this button before, so this is like a new discovery for me :)

* arithmetic operations in shell go in double brackets and should be preceeded by the $ symbol, like:
	
		a=1
		$b = $(( a + 1))

* the for cycle may be defined either as an quick sequence

		for i in {0..5}

I couldn't use the variables here neither for the lower bound nor for the upper, so given up with it. The second style is an 'arithmetic' style:

		for (( expr1; expr2; expr3 ))

The variables may be defined in a java (or c++) friendly way, so that's my choice.

* To display progress bar the easiest (and maybe the only) way is to rewrite the same line by prining the caret return symbol each time at the end of progress bar.

Here is the code that I've got:

	#!/bin/bash
	to_monitor=$1
	target_gb=$2
	read_mb=0
	while [ $read_mb -le $(( $target_gb*1024 )) ]
	do
	        read_mb=`du -sm $to_monitor | cut -f 1`
	        read_gb=$(( $read_mb/1024 ))
	        echo -ne '[ '
	        for (( gb=0; gb<$target_gb; gb++ ))
                	do
                        	if [ $read_gb -ge $gb ]
                        	then
	                                echo -ne '#';
                        	else
	                                echo -ne ' '
                        	fi
                	done
        	perc=$(( $read_mb*100/($target_gb*1024) ))
        	echo -ne " $perc%] $read_mb of $(($target_gb*1024))\r"
        	sleep 1
	done