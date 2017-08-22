Title: A quick intro to background and foreground command control in bash.
Date: 2016-01-04

Appending an & (ampersand) to any command run within bash will
background the process.

For example:

    for count in $(seq 1 10); do
        sleep 10m &
    done
    [11] 12775
    [12] 12776
    [13] 12777
    [14] 12778
    [15] 12779
    [16] 12780
    [17] 12781
    [18] 12782
    [19] 12783
    [20] 12784

Will background 10 processes which will simply sleep for 10
minutes. Bash will return a table of job ids and their PIDs.

To list a table which shows all background processes use the built-in
`jobs` command.

    $ jobs -l

You can also use ps to list the background processes by requesting the
processes which have the parent pid of your current bash shell.

    $ ps -O user --ppid $$

By using ps we can refer to important information on the processes.

To connect to one of the background processes, use fg and the job id
number.

    $ fg 11
    sleep 10m

To put job 11 back into the background you first have to suspend the
process by using ^Z (Control-Z) and then running bg specifying the job
id.

    $ fg 11
    sleep 10m
    ^Z
    [11]+  Stopped                 sleep 10m
    $ bg 11
    [11]+ sleep 10m &


To put fg, bg and ^Z to practical use, lets say you were copying a large
directory which was going to take a long time though you wanted to
regain control of your current shell.

Suspend the current command using ^Z.

    $ cp -a /media/backups/rsnapshot/hourly.0 /opt/restore
    ^Z
    [1]+  Stopped                 cp -a /media/backups/rsnapshot/hourly.0 /opt/restore

Background the task with bg referring to the job id in the table.

    $ bg 1
    [1]+ cp -a /media/backups/rsnapshot/hourly.0 /opt/restore &

Confirm the process is running with ps.

    $ ps -O user --ppid $$
    PID USER     S TTY          TIME COMMAND
    19496 root     D pts/11   00:00:06 cp -a /media/backups/rsnapshot/hourly.0 /opt/restore
