  --------------------
  Sudoers
  --------------------


  .. [Ref1]  https://www.hostinger.com/tutorials/sudo-and-the-sudoers-file/

  .. code::

      Cmnd_Alias AUDIT = /home/admin/RobotScripts/Audit/audit.sh
      Cmnd_Alias UPDATE = /home/admin/RobotScripts/Install/update.sh
      Cmnd_Alias ROBOT = AUDIT, UPDATE
      %wheel  ALL=(ALL)       NOPASSWD: ROBOT

