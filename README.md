## 对原项目进行修改
项目可集成到openwrt中或在Linux下直接使用，需要使用请自行编译


This is the README file for ppp-2.4, a package which implements the
Point-to-Point Protocol (PPP) to provide Internet connections over
serial lines.


Introduction.
*************

The Point-to-Point Protocol (PPP) provides a standard way to establish
a network connection over a serial link.  At present, this package
supports IP and IPV6 and the protocols layered above them, such as TCP
and UDP.  The Linux port of this package also has support for IPX.

This PPP implementation consists of two parts:

- Kernel code, which establishes a network interface and passes
packets between the serial port, the kernel networking code and the
PPP daemon (pppd).  This code is implemented using STREAMS modules on
Solaris, and as a line discipline under Linux.

- The PPP daemon (pppd), which negotiates with the peer to establish
the link and sets up the ppp network interface.  Pppd includes support
for authentication, so you can control which other systems may make a
PPP connection and what IP addresses they may use.

The platforms supported by this package are Linux and Solaris.  I have
code for NeXTStep, FreeBSD, SunOS 4.x, SVR4, Tru64 (Digital Unix), AIX
and Ultrix but no active maintainers for these platforms.  Code for
all of these except AIX is included in the ppp-2.3.11 release.

The kernel code for Linux is no longer distributed with this package,
since the relevant kernel code is in the official Linux kernel source
(and has been for many years) and is included in all reasonably modern
Linux distributions.  The Linux kernel code supports using PPP over
things other than serial ports, such as PPP over Ethernet and PPP over
ATM.


Installation.
*************

The file SETUP contains general information about setting up your
system for using PPP.  There is also a README file for each supported
system, which contains more specific details for installing PPP on
that system.  The supported systems, and the corresponding README
files, are:

	Linux				README.linux
	Solaris				README.sol2

In each case you start by running the ./configure script.  This works
out which operating system you are using and creates the appropriate
makefiles.  You then run `make' to compile the user-level code, and
(as root) `make install' to install the user-level programs pppd, chat
and pppstats.

N.B. Since 2.3.0, leaving the permitted IP addresses column of the
pap-secrets or chap-secrets file empty means that no addresses are
permitted.  You need to put a "*" in that column to allow the peer to
use any IP address.  (This only applies where the peer is
authenticating itself to you, of course.)


What's new in ppp-2.4.7.
************************

* Fixed a potential security issue in parsing option files (CVE-2014-3158).

* There is a new "stop-bits" option, which takes an argument of 1 or 2,
  indicating the number of stop bits to use for async serial ports.

* Various bug fixes.


What was new in ppp-2.4.6.
**************************

* Man page updates.

* Several bug fixes.

* Options files can now set and unset environment variables for
  scripts.

* The timeout for chat scripts can now be taken from an environment
  variable.

* There is a new option, master_detach, which allows pppd to detach
  from the controlling terminal when it is the multilink bundle master
  but its own link has terminated, even if the nodetach option has
  been given.


What was new in ppp-2.4.5.
**************************

* Under Linux, pppd can now operate in a mode where it doesn't request
  the peer's IP address, as some peers refuse to supply an IP address.
  Since Linux supports device routes as well as gateway routes, it's
  possible to have no remote IP address assigned to the ppp interface
  and still route traffic over it.

* Pppd now works better with 3G modems that do strange things such as
  sending IPCP Configure-Naks with the same values over and over again.

* The PPP over L2TP plugin is included, which works with the pppol2tp
  PPP channel code in the Linux kernel.  This allows pppd to be used
  to set up tunnels using the Layer 2 Tunneling Protocol.

* A new 'enable-session' option has been added, which enables session
  accounting via PAM or wtwp/wtmpx, as appropriate.  See the pppd man
  page for details.

* Several bugs have been fixed.


What was new in ppp-2.4.4.
**************************

* Pppd will now run /etc/ppp/ip-pre-up, if it exists, after creating
  the ppp interface and configuring its IP addresses but before
  bringing it up.  This can be used, for example, for adding firewall
  rules for the interface.

* Lots of bugs fixed, particularly in the area of demand-dialled and
  persistent connections.

* The rp-pppoe plugin now accepts any interface name (that isn't an
  existing pppd option name) without putting "nic-" on the front of
  it, not just eth*, nas*, tap* and br*.


What was new in ppp-2.4.3.
**************************

* The configure script now accepts --prefix and --sysconfdir options.
  These default to /usr/local and /etc.  If you want pppd put in
  /usr/sbin as before, use ./configure --prefix=/usr.

* Doing `make install' no longer puts example configuration files in
  /etc/ppp.  Use `make install-etcppp' if you want that.

* The code has been updated to work with version 0.8.3 of libpcap.
  Unfortunately the libpcap maintainers removed support for the
  "inbound" and "outbound" keywords on PPP links, meaning that if you
  link pppd with libpcap-0.8.3, you can't use those keywords in the
  active-filter and pass-filter expressions.  The support has been
  reinstated in the CVS version and should be in future libpcap
  releases.  If you need the in/outbound keywords, use a later release
  than 0.8.3, or get the CVS version from http://www.tcpdump.org.

* There is a new option, child-timeout, which sets the length of time
  that pppd will wait for child processes (such as the command
  specified with the pty option) to exit before exiting itself.  It
  defaults to 5 seconds.  After the timeout, pppd will send a SIGTERM
  to any remaining child processes and exit.  A value of 0 means no
  timeout.

* Various bugs have been fixed, including some CBCP packet parsing
  bugs that could lead to the peer being able to crash pppd if CBCP
  support is enabled.

* Various fixes and enhancements to the radius and rp-pppoe plugins
  have been added.

* There is a new winbind plugin, from Andrew Bartlet of the Samba
  team, which provides the ability to authenticate the peer against an
  NT domain controller using MS-CHAP or MS-CHAPV2.

* There is a new pppoatm plugin, by various authors, sent in by David
  Woodhouse.

* The multilink code has been substantially reworked.  The first pppd
  for a bundle still controls the ppp interface, but it doesn't exit
  until all the links in the bundle have terminated.  If the first
  pppd is signalled to exit, it signals all the other pppds
  controlling links in the bundle.

* The TDB code has been updated to the latest version.  This should
  eliminate the problem that some people have seen where the database
  file (/var/run/pppd.tdb) keeps on growing.  Unfortunately, however,
  the new code uses an incompatible database format.  For this reason,
  pppd now uses /var/run/pppd2.tdb as the database filename.


What was new in ppp-2.4.2.
**************************

* The CHAP code has been rewritten.  Pppd now has support for MS-CHAP
  V1 and V2 authentication, both as server and client.  The new CHAP
  code is cleaner than the old code and avoids some copyright problems
  that existed in the old code.

* MPPE (Microsoft Point-to-Point Encryption) support has been added,
  although the current implementation shouldn't be considered
  completely secure.  (There is no assurance that the current code
  won't ever transmit an unencrypted packet.)

* James Carlson's implementation of the Extensible Authentication
  Protocol (EAP) has been added.

* Support for the Encryption Control Protocol (ECP) has been added.

* Some new plug-ins have been included:
  - A plug-in for kernel-mode PPPoE (PPP over Ethernet)
  - A plug-in for supplying the PAP password over a pipe from another
    process
  - A plug-in for authenticating using a Radius server.

* Updates and bug-fixes for the Solaris port.

* The CBCP (Call Back Control Protocol) code has been updated.  There
  are new options `remotenumber' and `allow-number'.

* Extra hooks for plugins to use have been added.

* There is now a `maxoctets' option, which causes pppd to terminate
  the link once the number of bytes passed on the link exceeds a given
  value.

* There are now options to control whether pppd can use the IPCP
  IP-Address and IP-Addresses options: `ipcp-no-address' and
  `ipcp-no-addresses'.

* Fixed several bugs, including potential buffer overflows in chat.


What was new in ppp-2.4.1.
**************************

* Pppd can now print out the set of options that are in effect.  The
  new `dump' option causes pppd to print out the option values after
  option parsing is complete.  The `dryrun' option causes pppd to
  print the options and then exit.

* The option parsing code has been fixed so that options in the
  per-tty options file are parsed correctly, and don't override values
  from the command line in most cases.

* The plugin option now looks in /usr/lib/pppd/<pppd-version> (for
  example, /usr/lib/pppd/2.4.1b1) for shared objects for plugins if
  there is no slash in the plugin name.

* When loading a plugin, pppd will now check the version of pppd for
  which the plugin was compiled, and refuse to load it if it is
  different to pppd's version string.  To enable this, the plugin
  source needs to #include "pppd.h" and have a line saying:
	char pppd_version[] = VERSION;

* There is a bug in zlib, discovered by James Carlson, which can cause
  kernel memory corruption if Deflate is used with the lowest setting,
  8.  As a workaround pppd will now insist on using at least 9.

* Pppd should compile on Solaris and SunOS again.

* Pppd should now set the MTU correctly on demand-dialled interfaces.


What was new in ppp-2.4.0.
**************************

* Multilink: this package now allows you to combine multiple serial
  links into one logical link or `bundle', for increased bandwidth and
  reduced latency.  This is currently only supported under the
  2.4.x and later Linux kernels.

* All the pppd processes running on a system now write information
  into a common database.  I used the `tdb' code from samba for this.

* New hooks have been added.

For a list of the changes made during the 2.3 series releases of this
package, see the Changes-2.3 file.


Compression methods.
********************

This package supports two packet compression methods: Deflate and
BSD-Compress.  Other compression methods which are in common use
include Predictor, LZS, and MPPC.  These methods are not supported for
two reasons - they are patent-encumbered, and they cause some packets
to expand slightly, which pppd doesn't currently allow for.
BSD-Compress and Deflate (which uses the same algorithm as gzip) don't
ever expand packets.


Contacts.
*********

The comp.protocols.ppp newsgroup is a useful place to get help if you
have trouble getting your ppp connections to work.  Please do not send
me questions of the form "please help me get connected to my ISP" -
I'm sorry, but I simply do not have the time to answer all the
questions like this that I get.

If you find bugs in this package, please report them to the maintainer
for the port for the operating system you are using:

Linux			Paul Mackerras <paulus@samba.org>
Solaris			James Carlson <carlson@workingcode.com>


Copyrights:
***********

All of the code can be freely used and redistributed.  The individual
source files each have their own copyright and permission notice.
Pppd, pppstats and pppdump are under BSD-style notices.  Some of the
pppd plugins are GPL'd.  Chat is public domain.


Distribution:
*************

The primary site for releases of this software is:

	ftp://ftp.samba.org/pub/ppp/


