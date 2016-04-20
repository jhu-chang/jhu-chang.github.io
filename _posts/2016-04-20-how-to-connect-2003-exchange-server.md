---
layout: post
title: "How to connect 2003 exchange server"
category: "Doc"
tags: [windows,outlook,mac]
---

The latest Microsoft Office and Mac Mail app only support the exchange server version 2007, for old 2003 servers, you can only use the Microsoft Outlook 2003/2007 or Web mail, it is not convinient, to connect the 2003 exchange server, a proxy is needed:  [DavMail](http://davmail.sourceforge.net/)

DavMail
: a POP/IMAP/SMTP/Caldav/Carddav/LDAP exchange gateway allowing users to use any mail/calendar client (e.g. Thunderbird with Lightning or Apple iCal) with an Exchange server, even from the internet or behind a firewall through Outlook Web Access. DavMail now includes an LDAP gateway to Exchange global address book and user personal contacts to allow recipient address completion in mail compose window and full calendar support with attendees free/busy display.

: also supports the CardDav protocol to sync address books. This new feature is sponsored by French Defense / DGA through project Trustedbird

You can download [the latest version DavMail](http://davmail.sourceforge.net/download.html) for your platform, then

- [Configure the DavMail port](http://davmail.sourceforge.net/gettingstarted.html)

- [Configure the mail client](http://davmail.sourceforge.net/thunderbirdimapmailsetup.html)
    - Incomming: IMAP
    - Outcomming: SMTP