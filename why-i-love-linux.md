Why i love Linux

there are a lot of articles and blog entrie all over the web about why people love GNU/Linux. for me, i also in love with it. strictly when it comes to GNU. in my case, i love how i can do things, complicated things in Linux more easily than a fellow developer can do in a microsoft windows operating system for example. but one might say it is the matter of time and the skills one has. here is the story of me, Linux and an unsual event followed by my Conclusion as well.

<!-- Content Breaker -->

## Iranian Presidential Election 2013

two days ago, it was the presidential election in Iran and i decided to follow the result. as usual, it was a little annoying to follow it through the web sites because *when i want a simple result, it should be a simple result* :D so i digged in one of them and found out that it is using an ajax request to get the result from it's servers. it was simple javascript file wich they put the results in array variables. so i decided to write down a simple bash script to do the job and show me the results i wanted. you can see the javascript file content below (the [sourcefile](http://election.farsnews.com/electioninfo.js) may be removed any moments):

	lastTotal=36704156;
	lastCounted=35458747;
	lastFailed=1245409;

	lastJalili=4168946;
	lastRezaei=3884412;
	lastRohani=18613329;
	lastQarazi=446015;
	lastQalibaf=6077292;
	lastVelayati=2268753;

	categoryarray=('00:00', '06:05', '07:07', '08:03', '08:16', '09:07', '09:45', '10:43', '11:51', '12:27', '14:16', '15:51', '16:12', '19:05', '20:21');
	noneparty=(0, 14, 14, 13, 14, 12, 12, 12, Infinity, 14, 14, 13, 13, 13, 12);
	party1=(0, 35, 39, 38, 36, 36, 37, 36, Infinity, 34, 34, 34, 35, 34, 35);
	party2=(0, 47, 47, 49, 50, 52, 50, 51, Infinity, 52, 52, 53, 53, 53, 52);

the job was to get the file, store it somewhere and parse it for the information i wanted. simple job and due to the lack of time, i had to do it ninja-style :D in order to that first i had to get the file for future parsing:

## get the File

	TMP_FILE="/tmp/election"
	wget -q http://election.farsnews.com/electioninfo.js -O $TMP_FILE

## parse the information and print it

i made some changes to real code to make it better for tutorial porpuses too:

	TOTAL=$(cat $TMP_FILE | grep "lastTotal" | grep -o "[0-9]\+")
	COUNTED=$(cat $TMP_FILE | grep "lastCounted" | grep -o "[0-9]\+")
	FAILED=$(cat $TMP_FILE | grep "lastFailed" | grep -o "[0-9]\+")
	...
	echo "Total: $TOTAL"
	echo "Counted: $COUNTED failed: $FAILED"

you can see the [real code](https://gist.github.com/bijanebrahimi/5787282/6e34109fbce58b1ad2c875bbbeec1a8f7581570d). the result was good. i even ran the script using `watch` command which made me able to see the fresh results every minute just to be aware of the latest events

	watch -n 60 ./election.sh

since i was working on cli on that time, i ignored sending notification on desktop but that was just available by one line of code :D the thing was i didn't really liked the way i parsed the code. it was nasty and un-optimized but like i told you, it was a ninja-bash script just to do the job for at most one day. well, till i made the time and found out there is yet a better way. i realized the syntax of javascript file is similar to shell scripting style but with a little changes. for instance, the white spaces beside the equator operator is not allowed in bash scripting and bash arrays are defined with parenthesis not brackets. so what i really should do is to do the changes to javascript file to make it a valid bash script file and load via `source` command. that way, the javascript variables would be available in bash script without any more parsing the text. it was pretty much easy

	wget -qO- http://election.farsnews.com/electioninfo.js | sed 's/var //' | sed 's/ = /=/' | sed "s/\[/\(/" | sed "s/\]/\)/" > $TMP_FILE
	source "$TMP_FILE"

what i did was removing the `var` keyword and whitespaces beside the `=` operator and replaced the brackets with parenthesis and that was it. with calling `source` command at the second line, the variables was loaded and were avaiable in the script right away:

	echo "last Update: "${categoryarray[${#categoryarray[@]}-1]}
	echo "$lastTotal ($lastCounted, $lastFailed)"

you can see the [final code](https://gist.github.com/bijanebrahimi/5787282/5de70f7af56e056bf263b4ed2eef084d888dfc29)

# Conclusion?

back to the question. why do i love linux? i love Linux because

* i can do things much easier in linux because there are many small Free and Opensource programs available that can be put together like a puzzle to do a much complicated job. it's a real Lego for developers

* it makes me a smarter person by dealing with problems directly because it's Free and Open Source. i (will) have a much deeper knowledge about linux and how it works than a microsoft professional windows user for example.

* But, the main reason i love Linux: we people cooperate on/by GNU/Linux. in the time i was writing the script, there were some other people working on the same problem. all of them shared their work with me and everybody else. here is an [Android app](http://beygi.org/ElectionResault92.apk) written almost simultaneously with me. even a [fork](https://gist.github.com/esahekmat/5787246) from my first code :) and here is a later [another script](https://gist.github.com/arashm/5788351) written in ruby and in fact, looking at my friend's ruby code, made me realize that i can import the javascript code as a bash file with small modification despite the fact that he didn't actually do it. **cooperative works leads to creative works** and that's the lesson Big companies should learn about opensource and freesoftware that leads to why i love Linux.

you can download the markdown source of this blog entry from [here](why-i-love-linux.md)
