==============
Tar
==============

-----------------------
Compress
-----------------------



tar cvzf - /var/log/audit/audit.log | split --bytes=1MB - myfiles.tar.gz.

tar cvzf - /var/log/audit/audit.log | split --bytes=1KB - myfiles.tar.gz.


-----------------------
List content
-----------------------

.. code::
  
  cat myfiles.tar.gz.* |  tar -tz



-------------------------------
Extract on a different folder
-------------------------------

.. code::
  
  cat myfiles.tar.gz.* | tar xzv --directory=test - 



