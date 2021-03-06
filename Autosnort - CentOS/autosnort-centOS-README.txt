###############################
Installation Instructions
###############################

1. copy the autosnort-centOS-mm-dd-yyyy.sh script to root's home directory (/root) from the autosnort-master/autosnort - CentOS directory
2. decide which interface you would like to install there are five choices:
snortreport
BASE
aanval
snorby
remote syslog
3. Copy the shell script named after the interface you wish to install from autosnort-master/autosnort - CentOS/ directory and place it in /root along with the autosnort-CentOS-mm-dd-yyyy.sh script (example: if you want to install snorby, copy the snort-CentOS.sh script to /root along with autosnort-centOS-mm-dd-yyyy.sh script
4. Run the autosnort-centOS-mm-dd-yyyy.sh script:
as root:
cd /root;bash autosnort-centOS-mm-dd-yyyy.sh
alternatively:
cd /root;chmod u+x autosnort-centOS-mm-dd-yyyy.sh;./autosnort-centOS-mm-dd-yyyy.sh
via sudo:
cd /root;sudo bash autosnort-centOS-mm-dd-yyyy.sh
5. The script will prompt you as it needs answers from you. Answer the questions and before you know it, the installation is done.

###############################
autosnort-centOS Release Notes
##############################

Current Release: autosnort-centOS-03-31-14.sh

So, this all started when a user notified me that gen-msg.map is no longer provided by rule tarballs, and as a result barnyard2 wouldn't start when installed by autosnort. I made a few changes to autosnort, so sit tight:

-autosnort-centOS script changes:

--removed the choice to install a VRT rule tarball from the filesystem. There are a few reasons for this:
1) the sid-msg.map included in the rule tarball doesn't contain everything needed. To generate a good, valid sid-msg.map the user would need to pull the perl script from oinkmaster to do this, or download pulled pork to do this for them anyway.
2) pulledpork is the recommended tool for managing snort rulesets, and has been for some amount of time. If users really want to enable all snort rules at once, pulledpork supports it via enablesid.conf and regex magic.
3) one less question to ask the users during setup, a long stretch of time where the script does what it needs to without user intervention... (hint of things to come..)
--Shared Object rule stubs are now included in the snort.rules file; so_rules.rules is no longer generated, but the so_rules directory is still there. Pulled Pork seperates out all Shared Object rules and organized them by category. to find your Shared Object rules, you can use the expressions "GID:3" to search by category, or the expression "engine shared" to list all shared object rules in the snort.rules file.

snorby script changes:
-Experienced a problem with rvm not downloading properly on Debian's wget because the certificate did not match the name of the site for rvm.io. As much as I detest --no-check-certificates (It's like I'm really using HTTP!). While this isn't a bug, it's more of an evil preventative action.

bug fixes:
--gen-msg.map is copied out of the /usr/src/$snortver/etc now (so the untarred directory of the version of snort the script downloads), since rule tarballs no longer contain gen-msg.map. Thanks to David Earp for notifying me of this issue.
--Snort 2.9.6.0 has been the production release of snort for a while now, long enough for me to actually verify that the Shared Object rule generation works. spoilers: it sort-of, but not really worked. This has been fixed.
---The SO files would be put into the right place, but the rule stubs (SO rule headers) wouldn't be generated. Pulledpork actually executes snort to generate the SO rule stubs, but because snort would read the config, not find things where the config said they were, would summarily barf with no rule stubs. Fixed the script by generating some dummy files before pulled pork is ran, and placing files where pulled pork expects them.

##################
Previous Releases
##################

autosnort-03-23-CentOS.sh

It's finally time for CentOS autosnort and the module shell scripts to get a facelift. To all of my faithful users, my apologies for having taken so long.

Release Notes:
-autosnort-centOS script changes:
--Added in a check to see if the epel-release RPM package is already there; If it is, the script no longer exits. Special Thanks to keithmccammon and darkshade for providing sample code for a fix, and bringing the issue to my attention. Again, sorry it took so damned long!
--Removed all instances of catting and piping to grep. Grep is capable of reading from files directly. Results in a slight efficiency increase.
--Lots of minor changes to formatting and output given to the user to make things a little more user-friendly.
--Upgraded to use pullpork 0.7.0
--- In addition to download the standard VRT rule release, pulledpork now downloads the Snort community rules, and the ip blacklist from the VRT labs site
--- pulledpork now generates sid-msg.map version 2 output
--Efficiency enhancement: The entire pulledpork routine only runs pulledpork once, making the rule setup phase significantly faster.

-snorby-centOS script changes:
--Lots of minor formatting changes
--Removed all instances of catting and piping to grep. Grep files directly now.
--the rvm (Ruby Version Manager) tool download and installation is now torn down a bit more cleanly.
--fixed an issue reported on Autosnort-Ubuntu in which bundler would bomb. The work-around? run bundler twice. once with --no-deployment, then in --deployment. To my knowledge this is still broken (snorby issue #323). This makes snorby installation a bit longer, but you can still have your shinies, kids. Thanks to Gualty for reporting this issue.
--Git clone changed to https (took me long enough...) Special Thanks to pinglord for reporting this issue via Github.
--Changed permissions of database.yml and snorby_config.yml to be readable only by the www-data user (0400). Everything still works just fine.
--Stopped using a2ensite and a2dissite. Debian doesn't use Apache 2.4 yet, which seems to be very pickly about only allowing users to use a2ensite and a2dissite on files ending in ".conf", but I've seen in it Ubuntu, and I'm nipping this in the bud now. Renamed the /etc/apache2/sites-available/snorby file to snorby.conf, and now handle manually creation/removal of symlinks to sites-available. This is to ensure compatibility between older versions of Apache and newer versions (2.4+)

snortreport-centOS script changes:
--Very minor formatting changes
--Snort Report version upgraded to 1.3.4 from 1.3.3
--Unfortunately still has to enable short_open tags. Let me explain. short_open tags are using "<?" instead of "<?php" and "<?=" instead of php echo. "<?" causes problems in parsing XML, it's going to be deprecated, and it's an indicator of sloppy php code. I can't code in PHP to save my life and even I've figured this much out. "<?=" however doesn't cause any conflicts, and in fact php 5.4+ support this shortcut even if short open tags is turned off in php.ini. The problem is that CentOS uses a pre 5.4 version of php. Can't fix this with the version of php in the standard and/or epel repository.
--Ensured compatibility with new and old versions of Apache by making a very minor change the sed line that modifies DocumentRoot
--Upgraded jpgraph version to 3.05.
--The script now changes ownership for jpgraph and snortreport directories to be owned by www-data user and group (recursively)
--Changed file permissions on srconf.php. This file is now only readable by the www-data user (0400). Everything still works just fine.

-syslog_full-centOS script changes:
--Minor formatting changes.
--Removed all "cat" commands for slightly more efficient shell script.

BASE-ubuntu and Aanval-ubtu script changes
--Very minor formatting changes
--Ensured compatibility with new and old versions of Apache by making a very minor change the sed line that modifies DocumentRoot


Bug Fixes:
-autosnort-centOS:
--Significantly improved the OS version check at the start of the script.

snorby-centOS:
--The script show now properly download the latest ruby 1.9.x release, like it used to
--Clone of snorby from github is now https only
--Resolved autosnort issue 20 (snorby issue 323) via work-around that involves running bundler --no-deployment, followed by running bundler again with --deployment. It now takes bundler twice as long to perform its installation, but for now, this is the only viable work-around I've found until Mephux/Snorby team closes out this issue.

autosnort-centOS-08-18-2013.sh

- Updated the entire look and feel of the main autosnort installation script. CentOS/RHEL users now have the metasploit like prompts just like the Debian and Ubuntu users. Only things the user should be aware of are printed to the screen now:
-- Status updates are in blue
-- Notifications are in yellow
-- Successful modifications are in green 
-- Unsuccessful modifcations/installations are in red 
- The installations scripts no longer spew output all over your screen buffer. Thanks to neat trick I picked up from stack exchange, the output of every major command is saved in two separate log files in /var/log:
--/var/log/autosnort_install.log -- contains output from all the major commands ran from the main autosnort installation script
--/var/log/[interfacename]_install.log -- contains output from all the major commands ran from the interface installation script for the interface you chose to install. 
- The main autosnort installation script and all the web interface installation scripts have been updated with the new metasploit-like prompts and the background logging. This includes:
--autosnort-CentOS-08-18-2013.sh
--snortreport-CentOS.sh
--base-CentOS.sh
--aanval-CentOS.sh
--syslog_full-CentOS.sh
--snorby-CentOS.sh
- All web interface installation scripts for RHEL derivatives have had their DocumentRoot and Directory directives reconfigured to serve out the web interface of your choice. This means all you have to do is point your web browser to the ip address of your sensor's management interface, and provided you reconfigured ip tables to allow port 80 inbound (system-configure-firewall-tui), your web interface will pop up automatically.
- All web interface installation scripts for RHEL-based distros are 100% compatible with SELinux
- All web interface installation scripts for RHEL-based distros have had the ownership of DocumentRoot changed to the apache user and group
-Fixed minor grammatical and syntactical errors littered throughout the script.     


autosnort-centOS-04-21-2013.sh

Release Notes:

- Added support for output interface BASE and syslog_full format

-- The installation of BASE is very straightforward
-- Upon system reboot navigate to http://[ip address]/base to begin the setup
-- page 1 verifies that requisite packages are in place
-- when asked where adodb is located, enter "/usr/share/php/adodb"
-- when asked for credentials for the database and its location:
database name: snort
database host: localhost
database port: (leave blank or enter 3306)
database username: snort
database passwrd: [snort database user's password]
-- on the authentication page, if you want users to enter a password to review events, do so. Otherwise, click continue.
-- on the next page, click the "BASE AG" button for BASE to modify the database.

- Regarding syslog_full format
-- this is NOT fully tested. It has only been tested against Splunk. As time goes on, I may test with other SIEMS (e.g. graylog2 or just raw syslog) as required or requested (submit a feature request via github!)
-- Ensure 514/udp outbound is open on the sensor's management interface
-- Ensure 514/udp inbound is open on the SIEM
-- you can use tcpdump (tcpdump -i eth0 port 514) to verify that events are being sent out, as well as on the SIEM to see if events are making it to the SIEM
- if syslog_full format is chosen, output to mysql is disabled.

- The script has been modified to generate a new barnyard2.conf on the fly as opposed to using sed to modify the .conf file provided with the source. The barnyard2.conf file provided with barnyard2's source code is copied to /usr/local/snort/etc as barnyard2.conf.orig in the event it is needed in the future (e.g. modify output settings, etc.)
- Of course, the output interface menu has been modified to include BASE and syslog_full


autosnort-centOS-04-14-2013.sh

Release Notes:

- No new major functionality changes

Bug Fixes:

- Fixed a bug observed with Barnyard 2. Apparently specifying an argument on the command line as well as via its .conf file causes Barnyard2 to crash with a FATAL ERROR stating you can't do this anymore. Not sure when this change was implemented, but I've modified this version of autosnort as well as the CentOS snortbarn script to reflect this change. As a direct result of this the sid-msg.map and gen-msg.map files are specified via the barnyard2.conf file and not via the command line -S and -G options any longer.

- Fixed a bug in the line that runs ifconfig via rc.local to prime the snort sniffing interface at boot; the entire line was being echoed to the screen instead of the ifconfig command being echoed and appended to rc.local like it should have been.

autosnort-centOS-03-30-2013.sh

Release notes:

- This new release of autosnort for CentOS introduces support for an additional web front-end: Tactical Flex's Aanval Console!

- Please note that for this release, autosnort does not yet support some of the more advanced snort-related features for Aanval just yet (such as rule and/or policy management). At this stage, this is just to register the snort functionality with Aanval and get intrusion events reported to the Aanval Console.

- With this release I'm trying to make autosnort a bit more modular, instead of having it be one gigantic, monolithic shell script. This was a design choice I made to make it easier to troubleshoot issues with Autosnort and add on functionality. The first things I decided to break off from the main script was installation of different front ends. You'll notice there are two smaller shell scripts that accompany the main shell script:

--aanval-centOS.sh // installs aanval
--snortreport-centOS.sh // installs snort report

- Place these scripts in root's home directory (/root) along with the main autosnort script. /root is where the main script expects to find the child scripts. If the child scripts aren't there, the web front-end installation section of autosnort will fail to run until the child shell script is present in /root.

- As a part of the installation for Aanval on CentOS for users running SELinux, be aware that the script modifies SELinux to allow the httpd process to perform database connections (127.0.0.1:3306), and r/w access to /var/www/html/aanval and its subdirectories. You should NOT have to disable SELinux to run Aanval with these changes, just like you didn't have to with snortreport. Remember: disable SELinux is NEVER the solution!

Aanval Post-Setup notes:

- It is highly advised that you reboot your system before continuing to the aanval web console to configure Aanval to talk to snort. I ran into a problem prior to rebooting where the aanval console would not recognize that the php mysql module did exist and was loaded until the system was rebooted.

- During the initial setup, aanval will want to know the name of the aanvaldb user and password.

	Username:snort
	Password:password you gave the snort database user during the autosnort installation

- Aanval has a set of processes that are used to bring events over from the snort database that barnyard2 will dump to, and bring them over to the aanvaldb that aanval reads from. The console interface will let you know if they are not running. To start them:
	1. Navigate to /var/www/aanval/apps
	2. Run idsBackground.pl -start
	
- I plan on adding an rc.local entry that will do this for you in the near future!

- In order for Aanval to manage events for your snort sensor you need to enable it on the aanval console. click the gear symbol in the lower corner of the web interface. This will bring you to a page called configuration. Click the "Settings" option under the "Snort Module" section. On the next page, check the enabled checkbox and enter the information for the snort database:

	1. Database name: snort
	2. Database hostname: localhost
	3. Database username: snort
	4. Database password: the password you assigned to the snort database user during autosnort installation
	5. click update.
	6. Click on the gear symbol in the lower corner again. Under the "Snort Module" section, this time select "Sensor Configuration"
	7. Click the enabled checkbox
	8. Fill out the other fields except the SMT ID as you see fit (you can leave fields blank if you want)
	9. Click update
	10. The page will re-load with a new checkbox for "User Permissions". Select this checkbox. The page will automatically reload
	11. Click the house symbol at the top of the page to return to the Aanval home page.
	
- It may take a few minute for intrusion events to show up on the aanval interface. Be patient, they'll start coming in shortly!

- For more guidance and information specific to aanval, pay the folks at Tactical FLEX a visit at aanval.com. Community support site and Aanval wiki are free to use and will provide you with everything I used to integrate Aanval into Autosnort.


bug fixes:

- Changed how the main script performs the OS check. no longer checks for the sub-version of CentOS when running (e.g. 6.x), just checks to see if the primary OS version number is 6. Script was flagging CentOS 6.4 as an unsupported operating system for this script.

other notes:

- Finally got around to creating an init script for autosnort. For now, only CentOS/Redhat variant users have an init script (that will change soon). This init script can be used to replace /etc/rc.local as the primary method of starting up snort and barnyard2, and includes the added bonus of allow you to start/stop/restart snort and barnyard2 without requiring a reboot or sourcing /etc/rc.local if you need to make changes to snort or barnyard2. To add this script to CentOS 6.x perform the following tasks as root (or via sudo/root permissions)

	1. Copy the snortbarn script to /etc/init.d
	2. Edit the variables near the top of the script to suit your snort installation (the only variable that you should need to modify is the snort_iface variable if you installed snort/barnyard2 via autosnort)
	3. Make the snortbarn script is executable for the root user (chmod 700 snortbarn)
	4. Run chkconfig --add snortbarn
	5. Remove the entries for ifconfig, snort, and barnyard2 from /etc/rc.local (note: you may want to make a backup of the rc.local script in case you run into bugs/problems with the init script!)
	6. Kill your current snort/barnyard processes that ran from rc.local (killall snort && killall barnyard2)
	7. Run the command "service snortbarn start"
	8. check the process list to ensure that snort and barnyard2 are running after calling the init script. ( "ps -ef | grep snort" will return snort and barnyard2, if either/both processes are running. If only one process or the other is visible, something is wrong)

- Troubleshooting: 
--I'm not entirely sure why but there are CRLF/LF formatting problems with this script. If you get a bunch of errors stating that a file/command doesn't exist, try running dos2unix on the file to resolve the CRLF/LF errors.

--If you install the init script and upon reboot find that only the snort process is running, it is because the init script for snortbarn ran BEFORE the init script for mysqld ran. To Determine when mysqld is configured to run it its runlevels, check /etc/init.d/mysqld. You'll want to pay attention to this line in particular:

	# chkconfig: - 64 36
--The first number, 64 indicates what number the rc startup script will get on startup. Linux rc scripts determine what services run or are killed on a particular run level. Every rc script as a K for Kill order, an S for Start order, followed by a number and the name of the symlinked script from /etc/init.d. RC scripts are read in numeric order. So if the rc script for snortbarn has an S number lower than 64, it will run before mysqld. snort will start up fine, but barnyard fails because it has no database to connect to.

--To remedy this, you can modify the /etc/init.d/mysqld script to have a lower number than the snortbarn script in any of the /etc/rc[2-5].d directories, or modify the snortbarn script to have a higher number than the mysqld directory. This is a little confusing, so let's look at an example:

	run this command: ls -al /etc/rc?.d/S*snortbarn

--This command shows you each runlevel snortbarn is configured to start on.

	now, run this command: ls -al /etc/rc?.d/S*mysqld

--This command shows you the runlevels mysqld is set to start on. Don't worry about how many results you get.

--If mysqld's S## number is higher than snortbarn's number, the mysqld process will not be running before snort and barnyard are configured to run. No database running means barnyard2 won't run. Let's say snortbarn had a number of 63, and mysqld has a number of 64. edit /etc/init.d/mysqld and change the chkconfig to something like this:

	# chkconfig: - 62 36

	save your changes and run chkconfig --add mysqld. This should fix the problem. 

-If problems still persist, report them!

- Recieved a couple of notifications that users cannot connect to the web server running on their sensor post-reboot after the installation is done. This is because CentOS ships with a firewall that essentially blocks everything but 22/tcp (ssh) inbound by default. To remedy this, run the command "system-configure-firewall-tui" and enable access for "WWW" (80/tcp), and everything should work fine.

- Previous releases are available in the Previous_Rel directory, in the event that you find a bug with the current release and cannot wait for a fix to be made available.

Hello autosnort users. This is a README the CentOS autosnort build with Pulled Pork integration.

- The biggest change in functionality you will notice is the pulled pork integration using pulled pork for rule management has a few requirements:

	1. You need to have a valid oink code. register on snort.org as a registered user, or if you have a VRT subscription, the VRT oink code you have should work fine
	2. You'll need http and https access to labs.snort.org and snort.org to download snort.conf (from labs.snort.org) and rules via pulled pork (snort.org)

- Be aware that if there is a new release of snort, and you do not have a VRT subscription, you will be limited to snort rules for the previous version of snort for 30 days. That means that only the text-based rules will work. SO RULES DESIGNED FOR A PREVIOUS VERSION OF SNORT WILL NOT WORK ON A NEWER SNORT RELEASE.

- If you choose to have autosnort download rules for the previous snort version via pulled pork, pulled pork is configured to process text rules ONLY to prevent Shared Object compatibility problems.

- short_open_tag as well as SELinux configuration can be automatically performed for you through the script. --short_open_tag ON is required for httpd to be able to make sense of the short php tags. 
--SELinux reconfiguration is necessary for SELinux to allow httpd to access /var/www/html/snortreport files as required.

other notes:

- A lot of fault tolerance improvements in the code -- the script will no longer blindly plow forward if you give it invalid input. If you give the script something invalid or something that doesn't make sense the script loops through the routine until it gets input that makes sense.


as always, I can be contacted via twitter:
@da_667

or via e-mail:
deusexmachina667@gmail.com

Regards,

DA