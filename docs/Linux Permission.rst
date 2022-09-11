
=====================
Linux permissions
=====================

Comment from redhat article [#1]_  and man [#2]_

-------------------
Standard permission
-------------------

^^^^^^^^^^^^^^^^
Chmod using mode
^^^^^^^^^^^^^^^^

.. code::

    chmod ug+rw file.txt

Where:

* Who - represents identities: u,g,o,a (user, group, other, all)
* What - represents actions: +, -, = (add, remove, set exact)
* Which - represents access levels: r, w, x (read, write, execute)

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Chmod using octal permission
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


.. code::

    chmod 0750 file.txt

+---------+------+-------+-------+
| special | user | group | other |
+---------+------+-------+-------+
| 0       | 7    | 5     | 0     |
+---------+------+-------+-------+

* 7 = 4 + 2 +1 
* no permission = 0
* read permission should be set, add = 4
* write permission should be set, add 2
* execute permission should be set, add 1

Omitted digits are assumed to be leading zeros, it means that

.. code::

    chmod 0750 file.txt
    #is identical as
    chmod 750 file.txt

The forms of chmod with 3 digit is the most common one where no special permission is set.
However be caution that chmod with a single digit is still valid !

.. code::

    chmod 5 file.txt
    #same behavior of
    chmod 005 file.txt

-----------------------------
Setting special permissions
-----------------------------

chmod 2770 <file or folder>

* Start at 0
* SUID = 4
* SGID = 2
* Sticky = 1

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
SUID: user + s (pecial) = 1
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Commonly noted as SUID, the special permission for the user access level has a single function: A file with SUID always executes as the user who owns the file

Now, to see this in a practical light, let's look at the /usr/bin/passwd command. This command, by default, has the SUID permission set

On modern terminal: the file is highlighted in red 

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
SGID: group + s (pecial) = 2
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Commonly noted as SGID, this special permission has a couple of functions:

* If set on a file, it allows the file to be executed as the group that owns the file (similar to SUID)
* If set on a directory, any files created in the directory will have their group ownership set to that of the directory owner

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Sticky other + t (sticky) = 4
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The last special permission has been dubbed the "sticky bit." 
At the directory level, it restricts file deletion. 
Only the owner (and root) of a file can remove the file within that directory.
A common example of this is the /tmp directory:

.. [#1] https://www.redhat.com/sysadmin/suid-sgid-sticky-bit
.. [#2] https://linux.die.net/man/1/chmod