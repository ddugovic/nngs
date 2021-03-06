-------------------------------------------------------------------
-- NNGS SERVER README
-------------------------------------------------------------------

The files contained in this tarball comprise the No Name Go Server
(NNGS) source code.

For licencing information, see the file COPYING.


>> CONFIGURATION-METHOD HAS CHANGED!

Most of the configuration items for the NNGS server are now
kept in a single nngs.cnf -file.
Running ./configure basically just establishes some defaults.

This will allow you to reconfigure things without recompiling, and
recompiling/installing without changing your configuration.

Some of the configuration items have to do with files and the directory
where they are expected to be. Pointing to wrong or nonexistant files will
of course produce bizar results. Feel free to shoot yourself in the foot.

At startup, if the configfile is not found, nngs will *create* it, with
the current (compiled in) default values in it.  Edit and enjoy...

After processing the configfile, nngs will write the file 'written.cnf'
into the current directory. You can use this file to verify the values that
the program actually uses. The format is the same as the conffile.

--------------------------------------
-- CONFIGURATION COMPILATION AND INSTALLATION.
--------------------------------------

NNGS needs mlrate, even if you don't use it.
Unfortunately, the original (pem's) mlrate
is not autoconfd/automaked. If you can't get your hands on an
AC/AM's copy, you will need to move all of it's
source into a src/ subdirectory, and you may need some
minor changes. Sorry!


Step 1:

Configure and compile mlrate *before* NNGS.  Here's how:
(current version numbers may be different...)

    cd mlrate
    ./configure
    make
    cd ..

_READ_ the README file in the mlrate directory.  It contains much useful
information on how to set up the ratings system to work properly.

step 2: 
unpack tarball and create a symlink to the mlrate directory.

    ./configure --prefix=/home/nngs
    make
    make install

The 'make install' command installs the server and all basic
help files necessary for its functionality.
All files needed by the server are installed under the --prefix directory
which must be writeable by (the user that runs) the NNGS binary.
Once running, the server will store its data (players, games, logfile)
under this prefix, too.
Some of these files may grow over time, as the server runs. You might need to
periodically check and prune these directories.

To run and test it:

    mkdir $prefix/nngs && cd $prefix/nngs
    mkdir -p cgames games info lists messages news players/{a..z} problems spool stats
    $prefix/bin/nngs &
    telnet localhost 9696

--------------------------------------------
-- STEPS FOR EXISTING INSTALLATIONS
--------------------------------------------

If you already have (an older version of) NNGS:
* the steps for installing the new version are _basically the same_
  as for a first install.

* Just make sure you use the same --prefix=/whatever/dir/you/want
  flag for the configuration as you did on the first installation.

* The installation will *not* overwrite existing files.
  (except for the nngs - binary, of course) .

* A new version of the server *may* introduce new items in the conffile.
  To track these:
  1) put (a copy of) your conffile in a safe place (or rename it)
  2) compile the new NNGS
  3) shutdown the (old) server
  4) install the new NNGS
  5) start the new NNGS from a (current) directory, where *no*
     nngs.cnf file is present.
     (this will cause NNGS to *create* a nngs.cnf -file)
  6) Check the differences between the old<-->new configfile.
  7) edit your configfile.
     (probably just the old one, with only a few lines inserted/added)
  8) of course, you'll have to stop and restart NNGS to put
     the changes in effect. (NNGS does *not* understand SIGHUP)
  9) check the 'written.cnf' file again, to see the config that the
     server actually uses ;-)
 10) If the new server complains that it cannot find the player-files,
     it probably expects them somewhere else: check the pathnames!

--------------------------------------------
-- DATA FILES
--------------------------------------------

Data and logfiles live in $prefix/share/nngs/*

You can change these locations, but you'll have to reflect the change
in your conffile. See below under alternate locations.

To avoid damage to customised data, some files are not installed automatically
by the 'make install'.
You'll need to install them manually.

In $prefix/nngs/ladder you will find two example ladder
files.  Copy them (or remame them) to remove the '.example'

In $prefix/nngs/messages you will find default message
files for login, logout, etc.  Rename them to remove the 'default.'
part to activate them.

--------------------------------------------
-- SETTING UP ALTERNATE LOCATIONS.
--------------------------------------------

On startup, nngs will try to read it's config from nngs.cnf in
the current directory (or specfied with -c pathname)
The config file mechanism enables you to change file locations and
other things without recompiling. You can also use separate locations 
for data and the nngs binary. Or put the logfile in /var/.
(but I would advise you *not* to make too wild choices ;-)

The easiest way to set things up is:
1) do a configure&compile&and install of nngs
2) start the binary, and kill it.
3) edit the nngs.cnf, which will have been written in the current directory.
   The config file contains enough comment to be self-explanatory.
4) Future versions will probably add more items to the conffile.
   Please inspect the nngs.cnf (or written.cnf) for possible additions.

--------------------------------------------
-- RUNNING NNGS FROM INSIDE A CHROOT() 'JAIL'
--------------------------------------------

If you want nngs to run in a chroot()ed environment ('jail')
, you'll have to do some manual tuning.

*************************************************
*** NOTE: if you don't know what chroot() is, ***
*** you will probably *not* want it!          ***
*************************************************

1) the chroot() systemcall itself requires root privileges.
   nngs starts as root, then
   - chdir()s to its cage
   - chroot()s
   fork()s twice
   - , and changes gid and uid.
   Currently, it does *not* 
   - close filedescriptors,
   - change process group,
   - detach from its CTTY.
   Just the two forks should do.

2) Mail cannot be sent fom a chroot()ed program using /usr/bin/mail et.al.
   (unless a copy exists in the jail, which will also need the loader, and
   part of the /lib/*so, and maybe more)
   If you configure the mailer to be SMTP (by setting mail_program=SMTP),
   a built-in mailer is used. This will only need gethostbyname(), which
   relies on /etc/hosts and/or /etc/resolv.conf. These files need to be
   present in the jail, or mail won't work.

3) For safety: if anything fails (chroot, setuid, ...) the program will
   refuse to work and will exit() before doing anything useful.

4) Maybe (?depending on platform?) other files (/dev/zero?) are needed.

5) As an aid in debugging, a file "written.cnf" is written inside the
   chroot() -jail. The pathnames in this file will be relative to the
   chroot() directory. (the prefix is snipped) You can use this to check
   NNGS's assumptions about where things should be located.

6) YMMV

---------------------------------------------------
-- USERS AND ADMINS
---------------------------------------------------

In nngs-1.1.22, an admin-account is automatically added 
if it is not found at server startup.
**********************************************
** this admin-account has NO PASSWORD       **
** you'll have to SET A PASSWORD IMMEDIATELY**
**********************************************

(in older versions: When you install NNGS for the first time, you'll need to manually
create a user with 'admin' - rights. This can be ANY valid name.
Once there is an admin-account, it can be used to grant admin-rights
to other users.)

NOTE: the admin-status is stored in TWO places: the userfile proper,
and a lists/admin - file. You'l need both.
NOTE2: a running NNGS-server may have most of it's files cached.
editing the underlying files may not have the desired effects.
To be safe: stop the server before editing the files.

There already is a user 'admin' in the lists/admins.default -file
; you'll need to:
1) create a user named 'admin' by logging in and using
   the 'register' -command, and logging out.
2a) stop the server
2b) manually edit the user file for 'admin' in .../players/a/admin:
   change the line:
VARS: 1:4:0:1:0:1:1:0 :0:1:1:0:1:1:0:0 :0:0:0:0:0:0:0:0 :0:4:1:3:90:19:10:25 :33:0:0:33
   to:
VARS: 1:4:0:1:0:1:1:0 :0:1:1:0:1:1:0:0 :0:100:0:0:0:0:0:0 :0:4:1:3:90:19:10:25 :33:0:0:33
   (this field is the adminLevel)---------^^^
3a) restart the server
3b) on the next logon, the 'admin' account will have unlimited rights.


>> MAJOR CHANGES

Major changes since the beginning of this project:
- autoconf and automake so it compiles easily on many platforms, even windoze
- clean source tree
... all help files, admin files etc. included
... a 'make install' will install a working server
... all data dirs organized into one place ($prefix/nngs)
... all game and player dirs created on install
- nngs local malloc() and salloc() removed
- mlrate cleaned up
- (most) format strings taken out of the code
- internationalization
... chinese (messages and help files) (courtesy of LGS)
... german (messages only)
... others to follow
- added some caching, to reduce reading/writing of playerdb to/from disk.
- changed clock resolution for games to 0.1 sec.
- moved configuration into .cnf file.
- removed (most of) the compiled-in filenames.
- added chroot() support.
- experimental UDP port, to be used by a PHP/web interface.
