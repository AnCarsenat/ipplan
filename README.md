# IPplan - IP Address Management and Tracking

## Revision History
- Revision 4.81 2007-01-23 Revised by: re
- Revision 2.91 2002-05-17 Revised by: re

IPplan is a free (GPL), web-based, multilingual IP address management and tracking tool written in PHP 4, simplifying the administration of your IP address space. IPplan goes beyond IP address management by including DNS administration, configuration file management, circuit management (customizable via templates), and storing of hardware information (customizable via templates). IPplan can handle a single network or cater to multiple networks and customers with overlapping address space. See the introduction section for more.

---

## Table of Contents
1. [Introduction](#introduction)
   - [Copyright Information](#copyright-information)
   - [Disclaimer](#disclaimer)
   - [New Versions](#new-versions)
   - [Credits](#credits)
   - [Feedback](#feedback)
   - [Translations](#translations)
2. [Requirements](#requirements)
   - [Databases](#databases)
   - [Additional features](#additional-features)
3. [Installation](#installation)
   - [Customization](#customization)
4. [Downloads, bugs, and forums](#downloads-bugs-and-forums)
   - [Screenshots](#screenshots)
5. [Mode of operation](#mode-of-operation)
   - [Services company](#services-company)
   - [ISP](#isp)
6. [Concepts](#concepts)
   - [Deployment strategy](#deployment-strategy)
   - [Linking addresses](#linking-addresses)
7. [Administration](#administration)
   - [Admin user](#admin-user)
   - [Customer access](#customer-access)
   - [Subnet access](#subnet-access)
   - [Group authority boundaries](#group-authority-boundaries)
8. [Circuit administration, host configuration data, and asset information](#circuit-administration-host-configuration-data-and-asset-information)
9. [Device configuration file management](#device-configuration-file-management)
10. [DNS administration](#dns-administration)
    - [Handling exported zones](#handling-exported-zones)
    - [Automatic updating of zone records](#automatic-updating-of-zone-records)
11. [Dealing with registrars](#dealing-with-registrars)
12. [Searching](#searching)
    - [Searching for individual address details](#searching-for-individual-address-details)
    - [Searching areas and ranges](#searching-areas-and-ranges)
13. [Config file](#config-file)
14. [Importing data](#importing-data)
    - [TAB delimited data](#tab-delimited-data)
    - [Importing using NMAP](#importing-using-nmap)
15. [Templates](#templates)
    - [Customer, Subnet, DNS, and IP address templates](#customer-subnet-dns-and-ip-address-templates)
    - [Registrar templates](#registrar-templates)
16. [DHCP](#dhcp)
17. [Triggers](#triggers)
18. [External command line poller](#external-command-line-poller)
19. [IP address request system](#ip-address-request-system)
20. [Authentication schemes](#authentication-schemes)
21. [Problems](#problems)
22. [Limitations](#limitations)
23. [Questions and Answers (FAQ)](#questions-and-answers-faq)

---

## 1. Introduction

IPplan is a web-based, multilingual IP address management and tracking tool based on PHP 4, simplifying the administration of your IP address space. IPplan can handle a single network or cater to multiple networks with overlapping address space.

Current functionality includes:
- Internationalization
- Importing network definitions from routing tables
- Importing definitions from TAB delimited files and NMAP's XML format
- Multiple administrators with different access profiles (per group, allowing access per customer, per network, etc.)
- Define address space authority boundaries per group
- Finding free address space across a range
- Split and join networks to make them smaller and larger - IP definitions remain intact
- Display overlapping address space between networks
- Search capabilities
- An audit log - contents before and after change are logged
- Statistics
- Keeping track of and sending SWIP/registrar information
- DNS administration (forward and reverse zones, import existing zones via zone transfer)
- Template system to extend IPplan to contain site-specific information like circuit data, host configuration data, asset information
- Device configuration file management
- External stylesheet to change display look
- Triggers - every user event can call a user-defined function - useful to execute backend DNS scripts
- External poller - scan subnets for active addresses to gather usage statistics
- IP address request system - allows users to request static IP addresses from the database

Two authentication methods are available: either IPplan's own internal authentication scheme or alternatively use any external Apache authentication module. This includes single sign-on systems like SiteMinder or your own scheme based on LDAP or any other Apache compatible system.

---

### 1.1. Copyright Information

This document is copyrighted (c) 2002 Richard E and is distributed under the terms of the Linux Documentation Project (LDP) license, stated below.

Unless otherwise stated, Linux HOWTO documents are copyrighted by their respective authors. Linux HOWTO documents may be reproduced and distributed in whole or in part, in any medium physical or electronic, as long as this copyright notice is retained on all copies. Commercial redistribution is allowed and encouraged; however, the author would like to be notified of any such distributions.

All translations, derivative works, or aggregate works incorporating any Linux HOWTO documents must be covered under this copyright notice. That is, you may not produce a derivative work from a HOWTO and impose additional restrictions on its distribution. Exceptions to these rules may be granted under certain conditions; please contact the Linux HOWTO coordinator at the address given below.

---

### 1.2. Disclaimer

No liability for the contents of this documents can be accepted. Use the concepts, examples, and other content at your own risk. As this is a new edition of this document, there may be errors and inaccuracies that may, of course, be damaging to your system. Proceed with caution, and although this is highly unlikely, the author(s) do not take any responsibility for that.

All copyrights are held by their respective owners unless specifically noted otherwise. The use of a term in this document should not be regarded as affecting the validity of any trademark or service mark.

Naming of particular products or brands should not be seen as endorsements.

**Warning:** It is strongly recommended to make a backup of your system before major installation or upgrades and to back up at regular intervals.

---

### 1.3. New Versions

See the CHANGELOG file for more information.

---

### 1.4. Credits

Thanks to ValueHunt Inc. for the use of their layout class used for rendering all HTML pages.

Thanks to AdoDB for the use of their generic database abstraction class.

Thanks to Vex for their Visual Editor for XML used to generate the IPplan documentation.

Thanks to The PHP Layers Menu System for their menu system.

---

### 1.5. Feedback

Feedback is most certainly welcome for this document. Without your submissions and input, this document wouldn't exist. Please send your additions, comments, and criticisms to the following email address: <ipplan@gmail.com>.

---

### 1.6. Translations

See the INSTALL and TRANSLATIONS files on how to enable multilingual support and how to do a translation to your own language. Doing a translation does not require any programming experience. Current languages supported are English, Bulgarian, French - Auto Translation, German - Auto Translation, Italian - Auto Translation, Norwegian - Auto Translation, Portuguese - Auto Translation, and Spanish - Auto Translation.

- Nickola Kolev for the Bulgarian translation - nikky at mnet.bg.
- Conrado Pinto Rebessi for the Brazilian translation - conradopinto at yahoo.com.br
- Tadashi Jokagi for the Japanese translation - elf2000 at users.sourceforge.net
- Vladimir Leshchenko for the Russian translation - worker at smtn.stavropol.ru

---

## 2. Requirements

IPplan requires a working web server installation. Currently, the Apache web server is preferred, but PHP as an ISAPI or CGI module on IIS works too - follow the appropriate installation instructions in the IPplan directory (INSTALL-IIS+MSSQL). Apache works just fine on Windows platforms too. For installing Apache on a Windows platform, follow [these instructions](http://httpd.apache.org/docs/windows.html). Or you can use [AppServ](http://www.appservnetwork.com/) or [WampServer](http://www.wampserver.com/en/index.php) which are complete installation packages for Apache, MySQL, and PHP for Windows - just add IPplan by following the installation instructions in the IPPLAN-WINDOWS file (part of IPPlan).

---

### 2.1. Databases

IPplan requires a working database installation. The following databases currently work:
- MySQL 3.23.15 or higher (preferred)
- PostgreSQL 7.1 or higher
- Oracle 9i or higher (SQL99)
- Microsoft SQL server (both 7 and 2000)

The following may work but are untested: Sybase. In fact, any database that supports SQL99 compliant joins, particularly LEFT JOIN, should work. See the limitations section below for more.

The web scripting language PHP 4.1 or higher must also be installed as a module in Apache (NOT as a CGI). PHP must have the preferred database driver compiled in and enabled. See the respective web sites and installation documents for more detail. IPplan works just fine with a combination of the Apache web server and PHP on a Windows platform - just read the relevant installation instructions for Windows carefully.

**Tip:** IPplan is also known to work in a distributed, replicated MySQL environment with multiple database servers. See [www.oreilly.com](http://www.oreilly.com/) for more information.

---

### 2.2. Additional features

To enable SNMP support, you will require the ucd-snmp package installed and configured in your environment. This must also be activated in the PHP configuration. SNMP support is only required if you wish to read routing tables directly from routers.

---

## 3. Installation

Follow the instructions for your platform and database in the INSTALL files in the IPplan directory.

---

### 3.1. Customization

IPplan is customizable in many ways. See the sections on templates, triggers, and pollers. You can also extend the menu system to include your own custom menus for other systems at your site - see the config.php file for an example.

---

## 4. Downloads, bugs, and forums

You can report bugs, contribute to forums, and download it [here](http://sourceforge.net/projects/iptrack) and look at the latest [TODO](http://cvs.sourceforge.net/viewcvs.py/iptrack/ipplan/TODO?view=markup) and [CHANGELOG](http://cvs.sourceforge.net/viewcvs.py/iptrack/ipplan/CHANGELOG?view=markup).

---

### 4.1. Screenshots

You can find some screenshots [here](file://localhost/home/richarde/public_html/iptrackdev/ipplan/screenshots).

---

## 5. Mode of operation

There are two modes of operation, one can be classified as a services company and the other as an ISP.

---

### 5.1. Services company

As a services company your primary use of IPplan will be to manage individual IP address records and the address plan of one or more customers.

---

### 5.2. ISP

In ISP mode, you will assign blocks of IP address space to your customers. In this mode, you will not be concerned at all with individual IP address records and how the customer breaks down his assigned address space. When you operate as an ISP, you may also generate SWIP/registrar entries, which are only useful if you deal directly with ARIN or any other registrar. (SWIP is enabled in the config.php file, see ARIN [tutorial](http://www.arin.net/minutes/tutorials/swipit.htm) for more details). All the relevant SWIP/registrar information is entered when the customer is created.

When using this mode, I suggest creating a dummy customer that holds all the allocated address space from your regional registrar (ARIN?) already broken up into the various blocks that you will eventually assign to your customers. All these blocks should be called "free" to allow them to be found using the "Find free" menu option. Once you are ready to assign a block, create a new customer with all the relevant SWIP/registrar information completed, go to your dummy customer and move a block of address space to the newly created customer, and finally generate a SWIP/registrar entry for the new block. In this mode, areas and ranges are not too relevant except for the dummy customer (see concepts below). You may also need to create a template for your registrar in the templates directory. If you have done this, feel free to contribute it to IPplan.

---

## 6. Concepts

The flow of address management is based on the creation of areas, then ranges which belong to areas, and finally, subnets which belong to ranges. Actually, only subnets are required, but on large networks, it makes logical sense to group the network into areas to ease administration and to reduce routing updates on the network. There is a jpeg drawing included with the distribution that graphically shows these relationships. The methodology employed borrows significantly from OSPF routing concepts which are explained more fully [here](http://www.cisco.com/univercd/cc/td/doc/cisintwk/ito_doc/ospf.htm).

---

### 6.1. Deployment strategy

So in a new installation, first create the areas, then create ranges adding them to areas, and finally create subnets. Searching is now a simple matter of selecting an area which will display all the ranges for the area, or selecting no area and simply selecting a range from the total list of ranges, or simply selecting a base network address.

**Note:** Within a customer or autonomous system, no overlaps of address space are allowed. This follows standard IP addressing rules. You can have overlapping ranges/aggregates, but the default behavior of ranges also prevents overlaps. This can be changed in the config.php file.

To handle challenges like NAT or other overlapping address spaces, you will be required to create multiple autonomous systems. See 'Searching' below how to see information across multiple autonomous systems.

---

### 6.2. Linking addresses

IP address records can be linked together. This allows one address or multiple addresses to reference another address or addresses. Using this feature allows for the referencing of NATed addresses or having a link to a loopback address of a device. Linking is done on the IP address details page by completing the "Linked address" field. Once the field is completed, you can follow the link. The link also appears on subnet summary pages.

You can also link many addresses in one go by choosing multiple addresses in the "Select multiple addresses to do a bulk change" window, then completing the "User" field as follows:
