
=================
System Hardening
=================


Authoritative sources of high quality.



* NIST (National Institute of Standards and Technology)  [#1]_
* CIS (Center for Internet Security)
* ANSI (French National Agency for the Security of Information System  [#2]_)
* STIGs (Security Technical Implementation Guides)

.. [#1] https://www.cisecurity.org/cis-benchmarks/
.. [#2]  https://www.ssi.gouv.fr/en/publications/  


----------------
Automatic scan
----------------

Multiple security auditing tools exist, Lynis is an open-source security auditing tool recommanded by linux security expert (on may 22) 

.. [#] https://linuxsecurity.expert/tools/lynis/

----------------
Checklist
----------------

A lot of ressource are available on the CIS web site.
Details benchmarks are available for all major distribution.
Furthermore a distribution independent report is also provided.
Download of report required to provide its identity and email address but is free and accessible world wide.

#. Principle of hardening [#3]
#. Check list of University of Texas base from CIS report [#4]
#. CIS report: details report of 500 to 700 pages [#1]

.. [#3] https://linuxsecurity.expert/checklists/linux-security-and-system-hardening

.. [#4] https://security.utexas.edu/os-hardening-checklist/linux-7#5



----------------
Compile binaries
----------------

As the recommandation is to avoid installing compilator and additional depencies
it is recommanded to use an other VM to compile the source code and then copy the compiled binaries into the target VM.

https://www.vagrantup.com/docs/providers/virtualbox/boxes

sudo apt-get install linux-headers-$(uname -r) build-essential dkms