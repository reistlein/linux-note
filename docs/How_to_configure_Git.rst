==========================
How to configure git
==========================

-------------------------------
Configure git username & email
-------------------------------

git config --global user.name "Your Name"
git config --global user.email "youremail@yourdomain.com"
git config --list
The command saves the values in the global configuration file, ~/.gitconfig (ref [#]_)

.. [#] https://linuxize.com/post/how-to-configure-git-username-and-email/


-------------------------------
List remote repositories
-------------------------------

git remote -v  

-------------------------------
Sign commit
-------------------------------

You can use an SSH key to sign commits and tags (ref [#]_ )

.. code::

    git config --global gpg.format ssh

    git config --global user.signingkey "$(cat ~/.ssh/id_ecdsa_sk.pub )"


.. [#] https://docs.github.com/en/authentication/managing-commit-signature-verification/telling-git-about-your-signing-key#telling-git-about-your-ssh-key