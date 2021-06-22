# GitLab initial interview question/response

## Question 1
Write a Ruby or Bash script that will print usernames of all users on a Linux
system together with their home directories. Here's some example output:
<code>
gitlab:/home/gitlab

nobody:/nonexistent
</code>

As you can see, each line is a concatenation of a username, the colon character
(:), and the home directory path for that username. Your script should output
such a line for each user on the system.
Next, write a crontab entry that accomplishes the following:
<ul>
<li>Runs once every hour.</li>
<li>Takes the output of your above script and converts it to an MD5 hash.</li>
<li>Stores the MD5 hash into the <code>/var/log/current_users</code> file.</li>
<li>On subsequent runs, if the MD5 sum changes, it should log this change in
the <code>/var/log/user_changes</code> file with the message, DATE TIME
changes occurred, replacing DATE and TIME with appropriate
values. Make sure to replace the old MD5 hash in
<code>/var/log/current_users</code> file with the new MD5 hash.</li>
</ul>
Both the script and crontab entry should be provided for the answer to be
complete.

## Solution 1

**CRONTAB ENTRY**

<code>0	*	*	*	*	~/monitor_user_list_changes.sh	# every hour</code>

<code>
<p># The bash script: save below in a file called ~/monitor_user_list_changes.sh</p>

<p>#!/bin/bash</p>

<p># +--------------------------------------------------------------------------------+</p>
<p># | GitLab initial interview question/response                                     |</p>
<p># +--------------------------------------------------------------------------------+</p>
<p># | 9th Feb, 2021                                                                  |</p>
<p># +--------------------------------------------------------------------------------+</p>

<p>current_users=/var/log/current_users</p>
<p>user_changes=/var/log/user_changes</p>
<p>cur_md5=$(cut -d ":" -f 1,6 /etc/passwd | md5sum)</p>

<p># file exists and has data in it</p>
<p>if [[ -f "$current_users" ]] && [[ -s "$current_users" ]]; then</p>
<p>	old_md5=$(cat $current_users)</p>
<p>	echo $cur_md5 >$current_users</p>
<p>	cur_md5=$(cat $current_users)</p>
<p>	echo "If executed: old_md5=${old_md5}"</p>
<p>	echo "If executed: cur_md5=${cur_md5}"</p>
<p>	if [[ $cur_md5 != $old_md5 ]]; then</p>
<p>		date +'%D %T changes occurred' >>$user_changes</p>
<p>	fi</p>
<p>else</p>
<p>	# file doesn't exist, create an empty file</p>
<p>	echo $cur_md5 >$current_users</p>
<p>	echo "else executed ${cur_md5}"</p>
<p>fi</p>
</code>
