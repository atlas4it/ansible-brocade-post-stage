# ansible-brocade-post-stage

This Ansible module runs common commands that are required / recommended to complete the staging and setup of a Brocade Switch (tested against FastIron ICX family only so far!).  Specifically, this module will generate SSH keys, copy over a specified bootrom file, and also synchronize the software image in the Primary Flash to the Secondary Flash.

This module uses Pexpect with telnet to connect to the device because Brocade devices do not have SSH enabled nor have any SSH keys generated by default

Files:

- ansible.cfg contains an example Ansible config file
- hosts file is an example hosts file that defines icx6450 and icx6610 switch IP addresses
- example playbook itself is in playbooks/ directory as I believe this is normal practice for Ansible
- the modules are under playbooks/library/ -- again I believe this is normal practice for Ansible

Directory structure is such that (I believe) cloning the repo should allow it to be run directly with 'ansible-playbook playbooks/brocade-post-stage.yml'




Notes:

- on my Mac with how Python was installed via Homebrew, python is actually at /usr/local/bin/python rather than the normal /usr/bin/python -- the repo has /usr/bin/python as I believe that should be the default.  Mac users may or may not need to change the python file to match their location for python

- the Log Directory is mandatory right now, and its location and ability to be created is highly machine dependent, so please modify as required in the Playbook for each Play.  The repo uses /tmp/log/


OUTSTANDING ISSUES:

- For reasons I've yet to figure out, the first run through the ICX6610 Bootrom task, it seems to successfully download the bootrom and yet still throws a TFTP error code (at least with my system), however if you rerun the task a second time it works fine
- I have seen where occasionally a Copy Flash Secondary will report as failed on an ICX6450 stack -- not sure if the size of stack matters, however I think it is a parsing issue with a line wrap or something with Pexpect, as the expected text response is in the log file however Pexpect doesn't seem to parse it properly.  Not sure of a better way to handle that.  For now if one fails this task I check the debug log to see if it actually copied or not -- in various runs against maybe a total of 20 stacks or so (not all at once), I think I have seen it maybe twice.