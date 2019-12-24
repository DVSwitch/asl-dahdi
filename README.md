DAHDI Telephony Interface Driver
=================================
Asterisk Development Team <asteriskteam@digium.com>
$Revision$, $Date$

DAHDI stands for Digium Asterisk Hardware Device Interface.

This package contains the kernel modules for DAHDI. For the required 
userspace tools see the package dahdi-tools.

Supported Hardware
------------------
Digital Cards
~~~~~~~~~~~~~
- wcte43x:
  * Digium TE435: PCI express quad-port T1/E1/J1
  * Digium TE436: PCI quad-port T1/E1/J1
  * Digium TE235: PCI express dual-port T1/E1/J1
  * Digium TE236: PCI dual-port T1/E1/J1
- wcte13xp:
  * Digium TE131: PCI express single-port T1/E1/J1
  * Digium TE133: PCI express single-port T1/E1/J1 with echocan
  * Digium TE132: PCI single-port T1/E1/J1
  * Digium TE134: PCI single-port T1/E1/J1 with echocan
- wct4xxp:
  * Digium TE205P/TE207P/TE210P/TE212P: PCI dual-port T1/E1/J1
  * Digium TE405P/TE407P/TE410P/TE412P: PCI quad-port T1/E1/J1
  * Digium TE220: PCI-Express dual-port T1/E1/J1
  * Digium TE420: PCI-Express quad-port T1/E1/J1
  * Digium TE820: PCI-Express eight-port T1/E1/J1
- wcte12xp:
  * Digium TE120P: PCI single-port T1/E1/J1
  * Digium TE121: PCI-Express single-port T1/E1/J1
  * Digium TE122: PCI single-port T1/E1/J1
- wcte11xp:
  * Digium TE110P: PCI single-port T1/E1/J1
- wct1xxp: 
  * Digium T100P: PCI single-port T1
  * Digium E100P: PCI single-port E1
- wcb4xxp:
  * Digium B410: PCI quad-port BRI
- tor2: Tormenta quad-span T1/E1 card from the Zapata Telephony project


Analog Cards
~~~~~~~~~~~~
- wcaxx:
  * Digium A8A: PCI up to 8 mixed FXS/FXO ports
  * Digium A8B: PCI express up to 8 mixed FXS/FXO ports
  * Digium A4A: PCI up to 4 mixed FXS/FXO ports
  * Digium A4B: PCI express up to 4 mixed FXS/FXO ports 
- wctdm24xxp: 
  * Digium TDM2400P/AEX2400: up to 24 analog ports
  * Digium TDM800P/AEX800: up to 8 analog ports
  * Digium TDM410P/AEX410: up to 4 analog ports
  * Digium Hx8 Series: Up to 8 analog or BRI ports
- wctdm:
  * Digium TDM400P: up to 4 analog ports
- xpp: Xorcom Astribank: a USB connected unit of up to 32 ports
  (including the digital BRI and E1/T1 modules)
- wcfxo: X100P, similar and clones. A simple single-port FXO card


Other Drivers
~~~~~~~~~~~~~
- pciradio: Zapata Telephony PCI Quad Radio Interface
- wctc4xxp: Digium hardware transcoder cards (also need dahdi_transcode)
- dahdi_dynamic_eth: TDM over Ethernet (TDMoE) driver. Requires dahdi_dynamic
- dahdi_dynamic_loc: Mirror a local span. Requires dahdi_dynamic

Installation
------------
If all is well, you just need to run the following:

  make
  make install

You'll need the utilities provided in the package dahdi-tools to 
configure DAHDI devices on your system.

If using `sudo` to build/install, you may need to add /sbin to your PATH.

If you still have problems, read further.


Build Requirements
~~~~~~~~~~~~~~~~~~
gcc and friends. Generally you will need to install the package gcc.
There may be cases where you will need a specific version of gcc to build
kernel modules.

Kernel Source / "Headers"
^^^^^^^^^^^^^^^^^^^^^^^^^
- Building DAHDI-linux requires a kernel build tree.
- This should basically be at least a partial kernel source tree and
  most importantly, the exact kernel .config file used for the build as
  well as several files generated at kernel build time.
- KERNEL_VERSION is the output of the command `uname -r`
- If you build your own kernel, you need to point to the exact kernel
  build tree. Luckily for you, this will typically be pointed by the
  symbolic link /lib/modules/KERNEL_VERSION/build which is the location
  zaptel checks by default.
- If you use a kernel from your distribution you will typically have a
  package with all the files required to build a kernel modules for your
  kernel image.
  * On Debian and Ubuntu this is
    +++ linux-headers-`uname -r` +++
  * On Fedora, RHEL and compatibles (e.g. CentOS) and SUSE this is
    the kernel-devel package. Or if you run kernel-smp or kernel-xen,
    you need kernel-smp-devel or kernel-xen-devel, respectively.
  * In some distributions (e.g.: in RHEL/CentOS, Fedora, Ubuntu) the
    installation of the kernel-devel / kernel-headers package will
    be of a version that is newer than the one you currently run. In
    such a case you may need to upgrade the kernel package itself as
    well and reboot.
- To point explicitly to a different build tree: set KSRC to the kernel
  source tree or KVERS to the exact kernel version (if "headers" are
  available for a different version). This parameter must be run on
  every calls to 'make' (e.g.: 'make clean', 'make install').

  make KVERS=2.6.18.Custom
  make KSRC=/home/tzafrir/kernels/linus


Kernel Configuration
^^^^^^^^^^^^^^^^^^^^
If you build a custom kernel, note the following configuration items:

- CONFIG_CRC_CCITT must be enabled ('y' or 'm'). On 2.6 kernels this can
  be selected These can be selected from the "Library Routines" submenu
  during kernel configuration via "make menuconfig".
- DAHDI will work if you disable module unloading. But you may need
  extra reboots.
- DAHDI needs the BKL (Big Kernel Lock). This may be annoying in
  >=2.6.37 kernels. Make sure you enable CONFIG_BKL on those kernels.


Installing to a Subtree
~~~~~~~~~~~~~~~~~~~~~~~
The following may be useful when testing the package or when preparing a
package for a binary distribution (such as an rpm package) installing
onto a subtree rather than on the real system. 

  make install DESTDIR=targetdir

This can be useful for any partial install target of the above (e.g:
install-modules or install-programs).

the targetdir must be an absolute path, at least if you install the
modules. To install to a relative path you can use something like:

  make install-modules DESTDIR=$PWD/target


Extra Modules
~~~~~~~~~~~~~
To build extra modules / modules directory not included in the DAHDI 
distribution, use the optional variables MODULES_EXTRA and
SUBDIRS_EXTRA:

  make MODULES_EXTRA="mod1 mod2"
  make MODULES_EXTRA="mod1 mod2" SUBDIRS_EXTRA="subdir1/ subdir1/"


Static Device Files
~~~~~~~~~~~~~~~~~~~
Userspace programs communicate with the DAHDI modules through special
device files under /dev/dahdi .  Those are normally created by udev, but
if you use an embedded-type system and don't wish to use udev, you can
generate them with the following script, from the source directory:

  ./build_tools/make_static_devs

This will generate the device files under /dev/dahdi . If you need to
generate them elsewhere (e.g. `tmp/newroot/dev/dahdi`) use the option -d
to the script:

  ./build_tools/make_static_devs -d tmp/newroot/dev/dahdi


DKMS
~~~~
DKMS, Dynamic Kernel Module Support, is a framework for building Linux
kernel modules. It is used, among others, by several distributions that
package the DAHDI kernel modules.

DKMS is designed to provide updates over drivers installed from original
kernel modules tree. Thus it installed modules into /lib/modules/updates
or /lib/modules/VERSION/updates . This is generally not an issue on
normal operation. However if you try to install DAHDI from source on
a system with DAHDI installed from DKMS this way (potentially of an
older version), be sure to remove the DKMS-installed modules from the
updates directory. If you're not sure, the following command will give
you a clue of the versions installed:

  find /lib/modules -name dahdi.ko


Installing the B410P drivers with mISDN
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
DAHDI includes the wcb4xxp driver for the B410P, however, support for the
B410P was historically provided by mISDN.  If you would like to use the mISDN
driver with the B410P, please comment out the wcb4xxp line in /etc/dahdi/modules.
This will prevent DAHDI from loading wcb4xxp which will conflict with the mISDN
driver.

To install the mISDN driver for the B410P, please see http://www.misdn.org for
more information, but the following sequence of steps is roughly equivalent to
'make b410p' from previous releases.

  wget http://www.misdn.org/downloads/releases/mISDN-1_1_8.tar.gz
  wget http://www.misdn.org/downloads/releases/mISDNuser-1_1_8.tar.gz
  tar xfz mISDN-1_1_8.tar.gz
  tar xfz mISDNuser-1_1_8.tar.gz
  pushd mISDN-1_1_8
  make install
  popd
  pushd mISDNuser-1_1_8
  make install
  popd
  /usr/sbin/misdn-init config

You will then also want to make sure /etc/init.d/misdn-init is started
automatically with either 'chkconfig --add misdn-init' or 'update-rc.d
misdn-init defaults 15 30' depending on your distribution.

NOTE:  At the time this was written, misdn-1.1.8 is not compatible the
2.6.25 kernel.  Please use a kernel version 2.6.25 or earlier.


OSLEC
~~~~~
http://www.rowetel.com/ucasterisk/oslec.html[OSLEC] is an 
Open Source Line Echo Canceller. It is currently in the staging subtree
of the mainline kernel and will hopefully be fully merged at around
version 2.6.29. The echo canceller module dahdi_echocan_oslec
provides a DAHDI echo canceller module that uses the code from OSLEC. As
OSLEC has not been accepted into mainline yet, its interface is not set
in stone and thus this driver may need to change. Thus it is not
built by default.

Luckily the structure of the dahdi-linux tree matches that of the kernel
tree. Hence you can basically copy drivers/staging/echo and place it
under driver/staging/echo . In fact, dahdi_echocan_oslec assumes that
this is where the oslec code lies. If it is elsewhere you'll need to fix
the #include line.

Thus for the moment, the simplest way to build OSLEC with dahdi is to
copy the directory `drivers/staging/echo` from a recent kernel tree (at
least 2.6.28-rc1) to the a subdirectory with the same name in the
dahdi-linux tree.

After doing that, you'll see the following when building (running
'make')

  ...
  CC [M] /home/tzafrir/dahdi-linux/drivers/dahdi/dahdi_echocan_oslec.o
  CC [M] /home/tzafrir/dahdi-linux/drivers/dahdi/../staging/echo/echo.o
  ...

As this is an experimental driver, problems building and using it should 
be reported on the 
https://lists.sourceforge.net/lists/listinfo/freetel-oslec[OSLEC mailing
list].

Alternatively you can also get the OSLEC code from the dahdi-linux-extra
GIT repository:

  git clone git://gitorious.org/dahdi-extra/dahdi-linux-extra.git
  cd dahdi-linux-extra
  git archive extra-2.6 drivers/staging | (cd ..; tar xf -)
  cd ..; rm -rf dahdi-linux-extra


Live Install
~~~~~~~~~~~~
In many cases you already have DAHDI installed on your system but would
like to try a different version. E.g. in order to check if the latest
version fixes a bug that your current system happens to have.

DAHDI-linux includes a script to automate the task of installing DAHDI
to a subtree and using it instead of the system copy. Module loading
through modprobe cannot be used. Thus the script pre-loads the required
modules with insmod (which requires some quesswork as for which modules
to load). It also sets PATH and other environment variables to make all
the commands do the right thing.

There is an extra mode of operation to copy all the required files to a
remote host and run things there, for those who don't like to test code
on thir build system.

Live Install: The Basics
^^^^^^^^^^^^^^^^^^^^^^^^
Basic operation is through running

  ./build_tools/live_dahdi

from the root directory of the dahdi-linux tree. Using DAHDI requires
dahdi-tools as well, and the script builds and installs dahdi-tools. By
default it assumes the tree of dahdi-tools is in the directory
'dahdi-tools' alongside the dahdi-linux tree.  If you want to checkout
the trunks from SVN, use:

  svn checkout http://svn.asterisk.org/svn/dahdi/linux/trunk dahdi-linux
  svn checkout http://svn.asterisk.org/svn/dahdi/tools/trunk dahdi-tools
  cd dahdi-linux

If the tools directory resides elsewhere, you'll need to edit
live/live.conf (see later on). The usage message of live_dahdi:

 Usage:                    equivalent of:
 live_dahdi configure      ./configure
 live_dahdi install        make install
 live_dahdi config         make config
 live_dahdi unload         /etc/init.d/dahdi stop
 live_dahdi load           /etc/init.d/dahdi start
 live_dahdi reload         /etc/init.d/dahdi restart
 live_dahdi xpp-firm       (Reset and load xpp firmware)
 live_dahdi rsync TARGET   (copy filea to /tmp/live in host TARGET)
 live_dahdi exec  COMMAND  (Run COMMAND in 'live' environment)

Normally you should run:

 ./build_tools/live_dahdi configure
 ./build_tools/live_dahdi install
 ./build_tools/live_dahdi config

to build and install everything. Up until now no real change was done.
This could actually be run by a non-root user. All files are installed
under the subdirectory live/ .

Reloading the modules (and restarting Asterisk) is done by:

 ./build_tools/live_dahdi reload

Note: this stops Asterisk, unloads the DAHDI modules, loads the DAHDI
modules from the live/ subdirectory, configures the system and re-starts
Asterisk. This *can* do damage to your system. Furthermore, the DAHDI
configuration is generated by dahdi_genconf. It can be influenced by
a genconf_parameters file. But it may or may not be what you want.

If you want to run a command in the environment of the live system, use
the command 'exec':

 ./build_tools/live_dahdi lsdahdi
 ./build_tools/live_dahdi dahdi_hardware -v

Note however:

 ./build_tools/live_dahdi dahdi_cfg -c live/etc/dahdi/system.conf

Live Install Remote
^^^^^^^^^^^^^^^^^^^
As mentioned above, live_dahdi can also copy all the live system files
to a remote system and run from there. This requires rsync installed on
both system and assumes you can connect to the remove system through
ssh.

  tzafrir@hilbert $ ./build_tools/live_dahdi rsync root@david
  root@david's password:
  <f+++++++++ live_dahdi
  cd+++++++++ live/
  <f+++++++++ live/live.conf
  cd+++++++++ live/dev/
  cd+++++++++ live/dev/dahdi/
  cd+++++++++ live/etc/
  cd+++++++++ live/etc/asterisk/
  cd+++++++++ live/etc/dahdi/
  <f+++++++++ live/etc/dahdi/genconf_parameters
  <f+++++++++ live/etc/dahdi/init.conf 
  ...

As you can see, it copies the script itselfand the whole live/
subdirectory. The target directory is /tmp/live on the target directory
(changing it should probably be simple, but I never needed that).

Then, on the remove computer:

  root@david:/tmp# ./live_dahdi reload


Configuring a Live Install
^^^^^^^^^^^^^^^^^^^^^^^^^^
The live_dahdi script reads a configuration file in 'live/live.conf' if
it exists. This file has the format of a shell script snippet:

 var1=value # a '#' sign begins a comment
 var2='value'

 # comments and empty lines are ignored
 var3="value"

The variables below can also be overriden from the environment:

 var1='value' ./build_tools/live_dahdi

===== LINUX_DIR
The relative path to the dahdi-linux tree. The default is '.' and normally
there's no reason to override it.

===== TOOLS_DIR
The relative path to the dahdi-tools tree. The default is 'dahdi-tools'.

===== XPP_SYNC
Set a syncing astribank. See xpp_sync(8). Default is 'auto'.

===== AST_SCRIPT
The command for an init.d script to start/stop Asterisk. Should accept 
'start' and 'stop' commands. Use 'true' if you want to make that a
no-op. Defaults to '/etc/init.d/asterisk' .

===== MODULES_LOAD
Manual list of modules. They will be loaded by insmod. If reside in a 
subdir, add it explicitly. Modules for the drivers that are detected on
the system will be added by the script. Default: 'dahdi
dahdi_echocan_mg2'

===== REMOVE_MODULES
A list of modules to remove when unloading. Will be resolved
recursively. The default is 'dahdi'. You may also want to have 'oslec'
or 'hpec' there if you use them.

===== KVERS
If you want to build DAHDI for a different kernel version than the one
currently running on the system (mostly useful for a remote install).
Note that you will normally need to export this, in order for this to
take effect on the 'make' command. In live/live.conf itself have the line:

  export KVERS="2.6.39-local"

===== KSRC
Alternatively, if you want to point to an exact kernel source tree,
set it with KSRC. Just like KVERS above, it needs to be explicitly exported.

  export KSRC="/usr/src/tree/linux"


Module Parameters
-----------------
The kernel modules can be configured through module parameters. Module
parameters can optionally be set at load time. They are normally set (if
needed) by a line in a file under /etc/modprobe.d/ or in the file
/etc/modprobe.conf.

Example line:

  options dahdi debug=1

The module parameters can normally be modified at runtime through sysfs:

  pungenday:~# cat /sys/module/dahdi/parameters/debug 
  0
  pungenday:~# echo 1 >/sys/module/dahdi/parameters/debug
  pungenday:~# cat /sys/module/dahdi/parameters/debug 
  1

Viewing and setting parameters that way is possible as of kernel 2.6 .
In kernels older than 2.6.10, the sysfs "files" for the parameters
reside directly under /sys/module/'module_name' .

Useful module parameters:

=== debug
(most modules)

Sets debug mode / debug level. With most modules 'debug' can be either
disabled (0, the default value) or enabled (any other value). 

wctdm and wcte1xp print several extra debugging messages if the value
of debug is more than 1.

Some modules have "debugging flags" bits - the value of debug is a
bitmask and several messages are printed if some bits are set:

To get a list of parameters supported by a module, use 

  modinfo module_name

Or, for a module you have just built:

  modinfo ./drivers/dahdi/module_name.ko

For the xpp modules this will also include the description and default
value of the module. You can find a list of useful xpp module parameters
in README.Astribank .


- wctdm24xxp:
  * 1: DEBUG_CARD
  * 2: DEBUG_ECHOCAN
- wct4xxp:
  * 1: DEBUG_MAIN
  * 2: DEBUG_DTMF
  * 4: DEBUG_REGS
  * 8: DEBUG_TSI
  * 16: DEBUG_ECHOCAN
  * 32: DEBUG_RBS
  * 64: DEBUG_FRAMER
- xpp: See also README.Astribank:
  * 1: GENERAL - General debug comments.
  * 2: PCM - PCM-related messages. Tend to flood logs.
  * 4: LEDS - Anything related to the LEDs status control. The driver
    produces a lot of messages when the option is enabled.
  * 8: SYNC - Synchronization related messages.
  * 16: SIGNAL - DAHDI signalling related messages.
  * 32: PROC - Messages related to the procfs interface.
  * 64: REGS - Reading and writing to chip registers. Tends to flood
        logs.
  * 128: DEVICES - Device instantiation, destruction and such.
  * 256 - COMMANDS - Protocol commands. Tends to flood logs.
  +
  +
  The script xpp_debug in the source tree can help settting them at run
  time.

=== deftaps
(dahdi)

The default size for the echo canceller. The number is in "taps", that
is "samples", 1/8 ms. The default is 64 - for a tail size of 8 ms.

Asterisk's chan_dahdi tends to pass its own value anyway, with a
different default size. So normally setting this doesn't change
anything.

=== max_pseudo_channels
(dahdi)

The maximum number of pseudo channels that dahdi will allow userspace to
create.  Pseudo channels are used when conferencing channels together.
The default is 512.

=== auto_assign_spans
(dahdi)

See <<_span_assignments,Span Assignments>> below.

XPP (Astribank) module parameters
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
==== debug
(all modules) - see above.

==== dahdi_autoreg
(xpp)

Deprecated. See dahdi.<<_auto_assign_spans,auto_assign_spans>> above.

Originally had a somewhat similar (but xpp-specific and more limited)
role to auto_assign_spans. For backward compatibility this variable is
still kept, but its value is unused. Astribanks will auto-register
with dahdi if auto_assign_spans is not set.

==== tools_rootdir
(xpp)

Defaults to /. Passed (as the variable DAHDI_TOOLS_ROOTDIR) to generated
events (which can be used in udev hooks). Also serves as the base of
the variable DAHDI_INIT_DIR (by default: $DAHDI_TOOLS_DIR/usr/share/dahdi).

==== initdir
(xpp)

Deprecated. Setting both initdir and tools_rootdir will generate an error.

A directory under tools_rootdir containing the initialization scripts.
The default is /usr/share/dahdi .
Setting this value could be useful if that location is inconvenient for you.

==== rx_tasklet
(xpp)

Enable (1) or disable (0) doing most of the packets processing in
separate tasklets. This should probably help on higher-end systems with
multiple Astribanks.

==== vmwi_ioctl
(xpd_fxs)

Does userspace support VMWI notification via ioctl? Default: 1 (yes).

Disable this (0) to have the driver attempt to detect the voicemail
message waiting indication status for this port from FSK messages
userspace (Asterisk) sends. Set the ports to use AC neon-lamp style
message waiting indication. The detection from the FSK messages takes
extra CPU cycles but is required with e.g. Asterisk < 1.6.0 .

Also note that in order for this parameter to take effect, it must be
set before the span is registered. This practically means that it
should be set through modprobe.d files.

See also Voicemail Indication in README.Astribank.

==== usb1
(xpp_usb)

Enable (1) or disable (0) support of USB1 devices. Disabled by default.

USB1 devices are not well-tested. It seems that they don't work at all
for Astribank BRI. Generally they should work with the current code, but
we expect the voice quality issues. Hence we would like to make it
very clear that you if you have a USB1 port (rather than a USB2 one, as
recommended) you will have to take an action to enable the device.

==== poll intervals
(various)

There are various values which the driver occasionally polls the
device for. For instance, the parameter poll_battery_interval for
xpd_fxo to poll the battery, in order to know if the telco line is
actually connected.

The value of those parameters is typically a number in milliseconds.
0 is used to disable polling. Under normal operation there should be
no reason to play with those parameters.

==== dtmf_detection
(xpd_fxs)

Enable (1) or disable (0) support of hardware DTMF detection by the
Astribank.

==== caller_id_style
(xpd_fxo)

Various types of caller ID signalling styles require knowing the PCM
even when the line is on-hook (which is usually a waste of CPU and
bandwidth). This parameter allows fine-tuning the behaviour here:

* 0 (default) - Don't pass extra PCM when on-hook.
* 1 ETSI-FSK: Wait for polarity reversal to come before a ring and
  then start passing PCM until the caller ID has been passed.
* 2 ETSI-DTMF: Always pass PCM and generate a DTMF if polarity reversal is
  detected before ring.
* 3 Passthrough: Always pass PCM as-is.

This parameter is read-only. It cannot be changed at run-time.

==== battery_threshold
(xpd_fxo)

Minimum voltage that shows there is battery. Defaults to 3. Normally you
should not need to change this, unless dealing with a funky PSTN
provider.

==== battery_debounce
(xpd_fxo)

Minimum interval (msec) for detection of battery off (as opposed to e.g.
a temporary power denial to signal a hangup). Defaults to 1000. As with
battery_threshold above, there's normally no need to tweak it.

==== use_polrev_firmware
(xpd_fxo)

Enable (1, default) or disable (0) support for polarity reversal
detection in the hardware. Only has effect with PIC_TYPE_2.hex rev. >=
11039 and with the initialization changes (init_card_2_30) in rev.
949aa49.

This parameter is read-only. It cannot be changed at run-time.


Internals
---------
DAHDI Device Files
~~~~~~~~~~~~~~~~~~
Userspace programs will usually interact with DAHDI through device
files under the /dev/dahdi directory (pedantically: character device files 
with major number 196) . Those device files can be generated statically
or dynamically through the udev system.

* /dev/dahdi/ctl (196:0) - a general device file for various information and
  control operations on the DAHDI channels.
* /dev/dahdi/chan/N/M - A device file for channel M in span N
  - Both N and M are zero padded 3 digit numbers
  - Both N and M start at 001
  - M is chanpos - numbering relative to the current span.
* /dev/dahdi/NNN (196:NNN) - for NNN in the range 1-249. A device file for
  DAHDI channel NNN. It can be used to read data from the channel
  and write data to the channel. It is not generated by default but may
  be generated as a symlink using udev rules.
* /dev/dahdi/transcode (196:250) - Used to connect to a DAHDI transcoding
  device.
* /dev/dahdi/timer (196:253) - Allows setting timers. Used anywhere?
* /dev/dahdi/channel (196:254) - Can be used to open an arbitrary DAHDI
  channel. This is an alternative to /dev/dahdi/NNN that is not limited to
  249 channels.
* /dev/dahdi/pseudo (196:255) - A timing-only device. Every time you open
  it, a new DAHDI channel is created. That DAHDI channel is "pseudo" -
  DAHDI receives no data in it, and only sends garbage data with the
  same timing as the DAHDI timing master device.


DAHDI Timing
~~~~~~~~~~~~
A PBX system should generally have a single clock. If you are connected to a
telephony provider via a digital interface (e.g: E1, T1) you should also
typically use the provider's clock (as you get through the interface). Hence
one important job of Asterisk is to provide timing to the PBX. 

DAHDI "ticks" once per millisecond (1000 times per second). On each tick every
active DAHDI channel reads and 8 bytes of data. Asterisk also uses this for
timing, through a DAHDI pseudo channel it opens.

However, not all PBX systems are connected to a telephony provider via a T1 or
similar connection. With an analog connection you are not synced to the other
party. And some systems don't have DAHDI hardware at all.  Even a digital card
may be used for other uses or is simply not connected to a provider. DAHDI
cards are also capable of providing timing from a clock on card. Cheap x100P
clone cards are sometimes used for that purpose.

If a hardware timing source either cannot be found or stops providing timing
during runtime, DAHDI will automatically use the host timer in order provide
timing.

You can check the DAHDI timing source with dahdi_test, which is a small
utility that is included with DAHDI. It runs in cycles. In each such cycle it
tries to read 8192 bytes, and sees how long it takes. If DAHDI is not loaded
or you don't have the device files, it will fail immediately. If you lack a
timing device it will hang forever in the first cycle. Otherwise it will just
give you in each cycle the percent of how close it was. Also try running it
with the option -v for a verbose output.


Spans and Channels
~~~~~~~~~~~~~~~~~~
DAHDI provides telephony *channels* to the userspace applications. 
Those channels are channels are incorporated into logical units called
*spans*.

With digital telephony adapters (e.g: E1 or T1), a span normally 
represents a single port. With analog telephony a span typically
represents a PCI adapter or a similar logical unit.

Both channels and spans are identified by enumerating numbers (beginning
with 1). The number of the channel is the lowest unused one when it is
generated, and ditto for spans.

There are up to 128 spans and 1024 channels. This is a hard-wired limit
(see dahdi/user.h . Several places throuout the code assume a channel
number fits in a 16 bits number). Channel and span numbers start at 1.


Span Assignments
~~~~~~~~~~~~~~~~
A DAHDI device (e.g. a PCI card) is represented within the DAHDI drivers
as a 'DAHDI device'. Normally (with auto_assign_spans=1 in the module
dahdi, which is the default), when a device is discovered and loaded,
it registers with the DAHDI core and its spans automatically become
available. However if you have more than one device, you may be
interested to set explicit spans and channels numbers for them. To use
manual span assigment, set 'auto_assign_spans' to 0 . e.g. in a file
under /etc/modprobe.d/ include the following line:

  options dahdi auto_assign_spans=0

You will then need to assign the spans manually at device startup. You
will need to assign a span number and channel numbers for each
available span on the system. On my test system I have one BRI PCI card
and one Astribank BRI+FXS:

  # grep . /sys/bus/dahdi_devices/devices/*/spantype
  /sys/bus/dahdi_devices/devices/astribanks:xbus-00/spantype:1:BRI
  /sys/bus/dahdi_devices/devices/astribanks:xbus-00/spantype:2:BRI
  /sys/bus/dahdi_devices/devices/astribanks:xbus-00/spantype:3:BRI
  /sys/bus/dahdi_devices/devices/astribanks:xbus-00/spantype:4:BRI
  /sys/bus/dahdi_devices/devices/astribanks:xbus-00/spantype:5:BRI
  /sys/bus/dahdi_devices/devices/astribanks:xbus-00/spantype:6:BRI
  /sys/bus/dahdi_devices/devices/astribanks:xbus-00/spantype:7:BRI
  /sys/bus/dahdi_devices/devices/astribanks:xbus-00/spantype:8:BRI
  /sys/bus/dahdi_devices/devices/astribanks:xbus-00/spantype:9:FXS
  /sys/bus/dahdi_devices/devices/pci:0000:00:09.0/spantype:1:TE
  /sys/bus/dahdi_devices/devices/pci:0000:00:09.0/spantype:2:TE
  /sys/bus/dahdi_devices/devices/pci:0000:00:09.0/spantype:3:NT
  /sys/bus/dahdi_devices/devices/pci:0000:00:09.0/spantype:4:NT

All spans here, except the FXS one, are BRI spans with 3 channels per span.

In order to assign a span, we write three numbers separated by colns to
the file 'assign_span' in the SysFS node

  local_num:span_num:base_chan_num

Thus:

  echo 9:5:10 >/sys/bus/dahdi_devices/devices/astribanks:xbus-00/assign_span
  echo 2:8:40 >/sys/bus/dahdi_devices/devices/pci:0000:00:09.0/assign_span
  echo 1:1:1  >/sys/bus/dahdi_devices/devices/astribanks:xbus-00/assign_span
  echo 4:6:20 >/sys/bus/dahdi_devices/devices/pci:0000:00:09.0/assign_span
  echo 3:2:5  >/sys/bus/dahdi_devices/devices/astribanks:xbus-00/assign_span

As you can see, the order of span numbers or local span number is
insignificant. However the order of channel numbers must be the same as
that of span numbers.

Which indeed produced:

  # head -n3 -q /proc/dahdi/*
  Span 1: XBUS-00/XPD-00 "Xorcom XPD [usb:LAB-0003].1: BRI_NT"

             1 XPP_BRI_NT/00/00/0
  Span 2: XBUS-00/XPD-02 "Xorcom XPD [usb:LAB-0003].3: BRI_TE"

             5 XPP_BRI_TE/00/02/0
  Span 5: XBUS-00/XPD-10 "Xorcom XPD [usb:LAB-0003].9: FXS" (MASTER)

            10 XPP_FXS/00/10/0
  Span 6: B4/0/4 "B4XXP (PCI) Card 0 Span 4" RED

            23 B4/0/4/1 YELLOW
  Span 8: B4/0/2 "B4XXP (PCI) Card 0 Span 2" RED

            40 B4/0/2/1 RED

Likewise spans can be unassigned by writing to the 'unassign-span'
"file".


Dynamic Spans
~~~~~~~~~~~~~
Dynamic spans are spans that are not represented by real hardware.
Currently there are two types of them:

tdmoe::
  TDM over Ethernet. A remote span is identified by an ethernet (MAC)
  address.

local::
  Generates a span that is actually a loopback to a different local
  span.

Modules that support the dynamic spans are typically loaded at the time
the ioctl DAHDI_DYNAMIC_CREATE is called. This is typically called by
dahdi_cfg when it has a line such as:

  dynamic,somename,params

in /etc/dahdi/system.conf . In that case it will typically try to load
(through modprobe) the modules dahdi_dynamic and
dahdi_dynamic_'somename'. It will then pass 'params' to it.

Dynamic spans are known to be tricky and are some of the least-tested
parts of DAHDI.


Echo Cancellers
~~~~~~~~~~~~~~~
(To be documented later)


Tone Zones
~~~~~~~~~~
(To be documented later)


PROCFS Interface: /proc/dahdi
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
A simple way to get the current list of spans and channels each span contains
is the files under /proc/dahdi . /proc/dahdi is generated by DAHDI as it
loads. As each span registers to DAHDI, a file under /proc/dahdi is created
for it. The name of that file is the number of that span.

Each file has a 1-line title for the span followed by a few optional
general counter lines, an empty line and then a line for each channel of
the span. 

The title line shows the number of the span, its name and title, and 
(potentially) the alarms in which it is.

The title shows the span number and name, followed by any alarms the
span may have: For example, here is the first span in my system (with no
alarms):

  Span 1: XBUS-00/XPD-00 "Xorcom XPD #0/0: FXS"

There are several extra optional keywords that may be added there:

(Master)::
  This span is the master span. See <<_dahdi_timing,DAHDI Timing>>.
ClockSource::
  The clock source among several spans that belong to a single E1/J1/T1
  card.
RED/YELLOW/RED/NOTOPEN/LOOP/RECOVERING::
  The span is in alarm

Following that line there may be some optional lines about IRQ misses,
timing slips and such, if there are any.

The channel line for each channel shows its channel number, name and the
actual signalling assigned to it through dahdi_cfg. Before being configured by
dahdi_cfg: This is DAHDI channel 2, whose name is 'XPP_FXS/0/0/1'.

           2 XPP_FXS/0/0/1

After being configured by dahdi_cfg: the signalling 'FXOLS' was added. FXS
channels have FXO signalling and vice versa:

           2 XPP_FXS/0/0/1 FXOLS

If the channel is in use (typically opened by Asterisk) then you will
see an extra '(In use)':

           2 XPP_FXS/0/0/1 FXOLS (In use)


SysFS Interface
~~~~~~~~~~~~~~~
DAHDI exposes several interfaces under the SysFS virtual file system.
SysFS represents kernel objects in nodes - directories. There properties
are often files. They may also contain other nodes or include symlinks
to other nodes.

Class DAHDI
^^^^^^^^^^^
Under /sys/class/dadhi there exists a node for the non-channel DAHDI
device file under /dev/dahdi. The name is 'dahdi!foo' for the file
'/dev/dahdi/foo' (udev translates exclamation marks to slashes). Those
nodes are not, for the most part, proper SysFS nodes, and don't include
any interesting properties. The files in this class `ctl`, `timer`,
`channel`, `pseudo` and (if exists) `transcode`.


Devices Bus
^^^^^^^^^^^
Each DAHDI device (a physical device, such as a PCI card) is represented
by a node under /sys/bus/dahdi_devices/devices named with the name of
its device.

Its attributes include:

===== /sys/bus/dahdi_devices/devices/DEVICE/assgin-span
Write-only attribute: this device's spans should now be assigned
("registered"). See section about <<_span_assignments>>.

===== /sys/bus/dahdi_devices/devices/DEVICE/auto-assign
Write-only attribute. Spans in the device auto-assign ("register" as in
the original interface). See section about <<_span_assignments>>.

===== /sys/bus/dahdi_devices/devices/DEVICE/hardware_id
A unique hardware-level identifier (e.g. serial number), if available.

===== /sys/bus/dahdi_devices/devices/DEVICE/manufacturer
The name of the manufacturer. Freeform-string.

===== /sys/bus/dahdi_devices/devices/DEVICE/registration_time
The time at which the device registered with the DAHDI core. Example
value: "0005634136.941901429".

===== /sys/bus/dahdi_devices/devices/DEVICE/spantype
A line for each available span: <num>:<type>. This has to be provided
here as in the case of manual assignment, userspace may need to know
it before span nodes are created.

===== /sys/bus/dahdi_devices/devices/DEVICE/spantype
Device type.

===== /sys/bus/dahdi_devices/devices/DEVICE/unassign-span
Write-only attribute: the span whose device-local number is written
should now be unassigned ("unregistered"). See section about
<<_span_assignments>>.


Spans Bus
^^^^^^^^^
Each DAHDI span is represented by a node under
/sys/bus/dahdi_spans/devices with the name 'span-N' (where N is the
number of the span). Spans of each device also reside under the node of
the device.

Useful attributes in the span node:

===== /sys/bus/dahdi_spans/devices/span-N/alarms
The alarms of the span. Currently this is a numeric representation.
This may change in the future.

===== /sys/bus/dahdi_spans/devices/span-N/basechan
The channel number of the first channel. The channel numbers of the
following channels are guaranteed to follow it.

===== /sys/bus/dahdi_spans/devices/span-N/channels
The number of the channels in the span.

===== /sys/bus/dahdi_spans/devices/span-N/desc
A free-form description of the span.

===== /sys/bus/dahdi_spans/devices/span-N/is_digital
1 if the span is digital, 0 if it isn't.

===== /sys/bus/dahdi_spans/devices/span-N/is_sync_master
1 if the span is the sync master, 0 if it isn't.

===== /sys/bus/dahdi_spans/devices/span-N/lbo
LBO setting for the channel.

===== /sys/bus/dahdi_spans/devices/span-N/lineconfig
The framing and coding of the span, for a digital span. Textual
represenation:

<B8ZS|AMI|HDB3>/<D4|ESF|CCS>[/CRC4]

===== /sys/bus/dahdi_spans/devices/span-N/local_spanno
The number of the span within the DAHDI device.

===== /sys/bus/dahdi_spans/devices/span-N/name
A concise name for this span.

===== /sys/bus/dahdi_spans/devices/span-N/spantype
A very short type string.

===== /sys/bus/dahdi_spans/devices/span-N/syncsrc
Current sync source.

==== sys/bus/dahdi_spans/drivers/generic_lowlevel/master_span
All spans in the bus are handled by a single driver. The driver has one
non-standard attribute: master_span. printing it shows the current DAHDI
master span writing a number to it forces setting this span as the master
span.


Channels Bus
^^^^^^^^^^^^
Each DAHDI channel is represented by a node under
/sys/bus/dahdi_channels/devices with the name 'dahdi!channels!N!M'
(where N is the number of the span and M is the number of the channel
in the span - chanpos). Channels of each span also reside under the node
of the span.

Useful attributes in the channel node (All attributed listed below are
read-only):

===== /sys/bus/dahdi_spans/devices/span-N/dahdi!channels!N!M/alarms
List of names of the current active alarms (space separated). Normally
(no alarms) empty. Example:

  RED YELLOW

===== /sys/bus/dahdi_spans/devices/span-N/dahdi!channels!N!M/blocksize
The block size set by userspace.

===== /sys/bus/dahdi_spans/devices/span-N/dahdi!channels!N!M/channo
The (global) channel number.

===== /sys/bus/dahdi_spans/devices/span-N/dahdi!channels!N!M/chanpos
The channel number within the span.

===== /sys/bus/dahdi_spans/devices/span-N/dahdi!channels!N!M/dev
Major and minor device numbers.

===== /sys/bus/dahdi_spans/devices/span-N/dahdi!channels!N!M/ec_factory
The name of the echo canceller to be used in the channel, if one is
configured. Example:

  MG2

===== /sys/bus/dahdi_spans/devices/span-N/dahdi!channels!N!M/ec_state
State of the echo canceller. ACTIVE: configured and inuse. INACTIVE
otherwise.

===== /sys/bus/dahdi_spans/devices/span-N/dahdi!channels!N!M/in_use
1 if the channel is in use (was opepend by userspace), 0 otherwise.

===== /sys/bus/dahdi_spans/devices/span-N/dahdi!channels!N!M/name
A name string for the channel

===== /sys/bus/dahdi_spans/devices/span-N/dahdi!channels!N!M/sig
The signalling types set for the channel. A space-separated list of
signalling types.

===== /sys/bus/dahdi_spans/devices/span-N/dahdi!channels!N!M/sigcap
The signalling types this channel may be configured to handle. A space-
separated list of signalling types.


User-space Interface
~~~~~~~~~~~~~~~~~~~~
User-space programs can only work with DAHDI channels. The basic
operation is 'read()' to read audio from the device and write() to write
audio to it. Audio can be encoded as either alaw (G.711a) or (m)ulaw
(G.711u). See the ioctl DAHDI_SETLAW.

While it is technically possible to use /dev/dahdi/NUMBER directly, this
will only work for channel numbers up to 249. Generally you should use:

  int channo = CHAN_NUMBER_TO_OPEN;
  int rc;
  int fd = open("/dev/dahdi/channel", O_RDRW, 0600);
  // Make sure fd >= 0
  rc = ioctl(fd, DAHDI_SPECIFY, &channo) < 0);
  // Make sure this rc >= 0

FIXME: there's more to tell about the user-space interface.


Configuration
~~~~~~~~~~~~~
Most of the configuration is applied from userspace using the tool
'dahdi_cfg' in the package dahdi_tools. This section will not cover the
specifics of that file. Rather it will cover the way configuration from
this file is applied. Also note that there are other methods to
configure DAHDI drivers: there are <<_module_parameters,Module
Parameters>>. The xpp driver have their own separate initialization
scripts and xpp.conf that arecovered in README.Astribank.

When a span is registered, it is considered "unconfigured". Only after
dahdi_cfg has been run to apply configuration, the span is ready to run.

Some of the configuration is handled by the DAHDI core. Some of it is
handled by callbacks, which are function pointers in the `struct
dahdi_span': 'spanconfig', 'chanconfig' and (in a way) 'startup'.

Dahdi_cfg starts by reading the configuration from the configuration
file ('/etc/dahdi/system.conf', by default), and creating a local
configuration to apply. If you use -v, at this stage it will pront the
configuration that is "about to be configured". Then it will start to
actually apply the configuration.

Before actually applying configuration, it destroys any existing
<<_dynamic_spans,dynamic spans>> and generates new ones (if so
configured. FIXME: what about running DAHDI_SPANCONFIG for new dynamic
spans?).

Next thing it will do is apply the parameters from *span* lines using
the ioctl DAHDI_SPANCONFIG. Some generic processing of parameters passed
in DAHDI_SPANCONFIG is done in the DAHDI core, in the callback function
spanconfig in , but most of it is left to 'spanconfig' callback of the
span (if it exists. This one typically does not exists on analog cards).

Now dahdi_cfg will ask each existing channel for its existing
configuration and apply configuration if configuration changes are
needed. Configuration is applied to the channels using the ioctl call
DAHDI_CHANCONFIG ioctl. As in the case of the span configuration, part
of it is applied by the DAHDI core, and then it is handed over to the
span's chanconfig callback. Typically all spans will have such a
callback.

<<_echo_cancellers,Echo cancellers>> and <<_tone_zones,tone-zones>> are
handled separately later. 

Once everything is done, the ioctl DAHDI_STARTUP is called for every
span. This is also translated to a call to the optional span callback
'startup'.

Finally the ioctl DAHDI_HDLC_RATE is called for every channel (that is:
if '56k' is not set for the channel, it will be explicitly set to the
standard HDLC rate of 64k).


Low-Level Drivers
~~~~~~~~~~~~~~~~~
Low-level drivers create spans ('struct dahdi_span'). They register the
spans with the DAHDI core using 'dahdi_device_register()'.

'struct dahdi_span' has a number of informative members that are updated
solely by the low-level driver:

name::
  A concise span name. E.g.: Foo/1
desc::
  A slightly longer span name.
spantype::
  Span type in text form.
manufacturer::
  Span's device manufacturer
devicetype::
  Span's device type
location::
  span device's location in system
irq::
  IRQ for this span's hardware
irqmisses::
  Interrupt misses
timingslips::
  Clock slips

There are various function pointers in the struct 'dahdi_span' which are
used by the DAHDI core to call the low-level driver. Most of them are
optional.

The following apply to a span:

setchunksize::
  FIXME: seems to be unused.

spanconfig::
  Basic span configuration (called from dahdi_cfg).
	
startup::
  Last minute initialization after the configuration was applied.
	
shutdown::
  Explicit shutdown (e.g. for dynamic spans). Normally not needed.
	
maint::
  Enable/disable maintinance mode (FIXME: document).

sync_tick::
  Get notified that the master span has ticked.

The following apply to a single channel.

chanconfig::
  Configure the channel (called from dahdi_cfg).

open::
  Channel was opened for read/write from user-space.

close::
  Channel was closed by user-space.

ioctl::
  Handle extra ioctls. Should return -ENOTTY if ioctl is not known to
  the channel

echocan_create::
  Create a custom echo canceller. Normally used for providing a hardware
  echo canceller. If NULL, the standard DAHDI echo canceller modules
  will be used.

rbsbits::
  Copy signalling bits to device. See below on signalling.

hooksig::
  Implement RBS-like signalling-handling. See below on signalling.

sethook::
  Handle signalling yourself. See below on signalling.

hdlc_hard_xmit::
  Used to tell an onboard HDLC controller that there is data ready to
  transmit.

audio_notify::
  (if DAHDI_AUDIO_NOTIFY is set) - be notified when the channel is (or
  isn't) in audio mode. Which may mean (for an ISDN B-channel) that its
  data need not be sent.

There are several alternative methods for a span to use for
signalling. One of them should be used.

Signalling: rbsbits
^^^^^^^^^^^^^^^^^^^
If the device is a CAS interface, the driver should copy the signalling
bits to and from the other side, and DAHDI will handle the signalling.
The driver just need to provide a 'rbsbits' and set DAHDI_FLAG_RBS in
the span->flags.

Note that 'rbs' (Robed Bits Signalling) here merely refers to the (up
to) 4 signalling bits of the channel. In T1 they are transmitted by
"robbing" bits from the channels and hence the name. In E1 they are
transmitted in a timeframe of their own.

The driver should then signal a change in the signalling bits in a
channel using dahdi_rbsbits().


Signalling: hooksig
^^^^^^^^^^^^^^^^^^^
If the device does not know about signalling bits, but has their
equivalents (i.e. can disconnect battery, detect off hook, generate
ring, etc directly) then the driver can specify a 'sethook' function and
set DAHDI_FLAG_RBS in span->flags. In that case DAHDI will call that
function whenever the signalling state changes.

The hooksig function is only used if the rbsbits function is not set.

The span should notify DAHDI of a change of signalling in a channel using
dahdi_hooksig().


Signalling: sethook
^^^^^^^^^^^^^^^^^^^
Alternatively, if DAHDI_FLAG_RBS is not set in the flags of the span (to
use either rbsbits or hooksig), the DAHDI core will try to call the
'sethook' function of the span (if it exists) to handle individual hook
states.

The span should then notify DAHDI of a change in the signalling state
using dahdi_sethook().

FIXME: anybody using this one?


ABI Compatibility
~~~~~~~~~~~~~~~~~
Like any other kernel code, DAHDI strives to maintain a stable interface to
userspace programs. The API of DAHDI to userspace programs, dahdi/user.h, has
remained backward-compatible for a long time and is expected to remain so in
the future. With the ABI (the bits themselves) things are slightly trickier.

DAHDI's interface to userspace is mostly ioctl(3) calls. Ioctl calls
are identified by a number that stems from various things, one of which
is the size of the data structure passed between the kernel and
userspace. 

Many of the DAHDI ioctl-s use some specific structs to pass information
between kernel and userspace. In some cases the need arose to pass a few
more data members in each call. Simply adding a new member to the struct
would have meant a new number for the ioctl, as its number depends on
the size of the data passed.

Thus we would add a new ioctl with the same base number and with the
original struct.

So suppose we had the following ioctl:
----------------------------------
struct dahdi_example {
	int sample;
}

#define DAHDI_EXAMPLE     _IOWR (DAHDI_CODE, 62, struct dahdi_example)
----------------------------------

And we want to add the field 'int onemore', we won't just add it to the
struct. We will do something that is more complex:
------------------------------------
/* The original, unchanged: */
struct dahdi_example_v1 {
	int sample;
}

/* The new struct: */
struct dahdi_example {
	int sample;
	int onemore;
}

#define DAHDI_EXAMPLE_V1  _IOWR(DAHDI_CODE, 62, struct dahdi_example_v1)
#define DAHDI_EXAMPLE     _IOWR(DAHDI_CODE, 62, struct dahdi_example)
------------------------------------
We actually have here two different ioctls: the old DAHDI_EXAMPLE would be
0xC004DA3E . DAHDI_EXAMPLE_V1 would have the same value. But the new value
of DAHDI_EXAMPLE would be 0xC008DA3E .

Programs built with the original dahdi/user.h (before the change) use the
original ioctl, whether or not the kernel code is actually of the newer
version. Thus in most cases there are no compatibility issues.

When can we have compatibility issues? If we have code built with the new
dahdi/user.h, but the loaded kernel code (modules) are of the older version.
Thus the userspace program will try to use the newer DAHDI_EXAMPLE (0xC008DA3E).
But the kernel code has no handler for that ioctl. The result: the error 25,
ENOTTY, which means "Inappropriate ioctl for device".

As a by-product of that method, for each interface change a new #define is
added. That definition is for the old version and thus it might appear
slightly confusing in the code, but it is useful for writing code that works
with all versions of DAHDI. 

Past Incompatibilities
^^^^^^^^^^^^^^^^^^^^^^
.DAHDI 2.3:
DAHDI_SPANINFO_V1 (extra members added). This will typically only be
used on ISDN (PRI/BRI) spans in Asterisk.

.DAHDI 2.2:
* DAHDI_GET_PARAMS_V1, DAHDI_GETCONF_V1, DAHDI_SETCONF_V1,
  DAHDI_GETGAINS_V1 ('direction' changed from 'R' to 'RW' to fix
  FreeBSD support).
* DAHDI_CONFDIAG_V1, DAHDI_CHANDIAG_V1 (fixed direction).


Alarm Types
~~~~~~~~~~~
An alarm indicates that a port is not available for some reason. Thus it
is probably not a good idea to try to call out through it.


Red Alarm
^^^^^^^^^
Your T1/E1 port will go into red alarm when it cannot maintain
synchronization with the remote switch.  A red alarm typically
indicates either a physical wiring problem, loss of connectivity, or a
framing and/or line-coding mismatch with the remote switch.  When your
T1/E1 port loses sync, it will transmit a yellow alarm to the remote
switch to indicate that it's having a problem receiving signal from
the remote switch.

The easy way to remember this is that the R in red stands for "right
here" and "receive"... indicating that we're having a problem right
here receiving the signal from the remote switch.


Yellow Alarm 
^^^^^^^^^^^^
(RAI -- Remote Alarm Indication)

Your T1/E1 port will go into yellow alarm when it receives a signal
from the remote switch that the port on that remote switch is in red
alarm. This essentially means that the remote switch is not able to
maintain sync with you, or is not receiving your transmission.

The easy way to remember this is that the Y in yellow stands for
"yonder"... indicating that the remote switch (over yonder) isn't able
to see what you're sending.


Blue Alarm
^^^^^^^^^^
(AIS -- Alarm Indication Signal)

Your T1/E1 port will go into blue alarm when it receives all unframed
1s on all timeslots from the remote switch.  This is a special signal
to indicate that the remote switch is having problems with its
upstream connection.  dahdi_tool and Asterisk don't correctly indicate
a blue alarm at this time.  The easy way to remember this is that
streams are blue, so a blue alarm indicates a problem upstream from
the switch you're connected to.


Recovering from Alarm
^^^^^^^^^^^^^^^^^^^^^
TODO: explain.


Loopback
^^^^^^^^
Not really an alarm. Indicates that a span is not available, as the port 
is in either a local or remote loopback mode.


Not Open
^^^^^^^^
Something is not connected. Used by e.g. the drivers of the Astribank to
indicate a span that belongs to a device that has been disconnected 
but is still being used by userspace programs and thus can't e
destroyed.


License
-------
This package is distributed under the terms of the GNU General Public License
Version 2, except for some components which are distributed under the terms of
the GNU Lesser General Public License Version 2.1. Both licenses are included
in this directory, and each file is clearly marked as to which license applies.

If you wish to use the DAHDI drivers in an application for which the license
terms are not appropriate (e.g. a proprietary embedded system), licenses under
more flexible terms can be readily obtained through Digium, Inc. at reasonable
cost.

Known Issues
------------

KB1 does not function when echocancel > 128
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
KB1 was not designed to function at greater than 128 taps, and if configured
this way, will result in the destruction of audio.  Ideally DAHDI would return
an error when a KB1 echocanceller is configured with greater than 128 taps.


Reporting Bugs
--------------
Please report bug and patches to the Asterisk bug tracker at
http://issues.asterisk.org in the "DAHDI-linux" category.

Links
-----
- http://asterisk.org/[] - The Asterisk PBX
- http://voip-info.org/[]
- http://voip-info.org/wiki/view/DAHDI[]
- http://docs.tzafrir.org.il/dahdi-linux/README.html[Up-to-date HTML version
  of this file]
