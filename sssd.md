## Introduction
SSSD is an acronym for System Security Services Daemon. It is the client component of centralized identity management solutions such as FreeIPA, 389 Directory Server, Microsoft Active Directory, OpenLDAP and other directory servers. The client serves and caches the information stored in the remote directory server and provides identity, authentication and authorization services to the host machine.

## Why do you need SSSD
Did you ever wonder how the same login works on all of the computers in the school lab? That’s because they are joined to a domain that’s part of an identity management solution. A centralized, redundant, and secure set of servers to manage users, groups, policies and more.

## Understanding SSSD and its benefits
The System Security Services Daemon (SSSD) is a system service to access remote directories and authentication mechanisms. The following chapters outline how SSSD works, what are the benefits of using it, how the configuration files are processed, as well as what identity and authentication providers you can configure.

## How SSSD works 

The System Security Services Daemon (SSSD) is a system service that allows you to access remote directories and authentication mechanisms. You can connect a local system, an SSSD client, to an external back-end system, a provider.

For example:
An LDAP directory
An Identity Management (IdM) domain
An Active Directory (AD) domain
A Kerberos realm

SSSD works in two stages:

It connects the client to a remote provider to retrieve identity and authentication information.
It uses the obtained authentication information to create a local cache of users and credentials on the client.
Users on the local system are then able to authenticate using the user accounts stored in the remote provider.

SSSD does not create user accounts on the local system. However, SSSD can be configured to create home directories for IdM users. Once created, an IdM user home directory and its contents on the client are not deleted when the user logs out.

This is not necessary when you have a handful of machines to manage but when scaled up to tens, hundreds or thousands then SSSD becomes an important Linux directory-client component. You certainly can force the new guy to go to each machine to create a user, update their password, remove a user. However, that’s pretty mean and a waste of time when there is a much easier way of doing things.

## Benefits of using SSSD 
Using the System Security Services Daemon (SSSD) provides multiple benefits regarding user identity retrieval and user authentication.

Offline authentication
SSSD optionally keeps a cache of user identities and credentials retrieved from remote providers. In this setup, a user - provided they have already authenticated once against the remote provider at the start of the session - can successfully authenticate to resources even if the remote provider or the client are offline.

With SSSD, it is not necessary to maintain both a central account and a local user account for offline authentication. The conditions are:
In a particular session, the user must have logged in at least once: the client must be connected to the remote provider when the user logs in for the first time.

Caching must be enabled in SSSD.
Without SSSD, remote users often have multiple user accounts. For example, to connect to a virtual private network (VPN), remote users have one account for the local system and another account for the VPN system. In this scenario, you must first authenticate on the private network to fetch the user from the remote server and cache the user credentials locally.

With SSSD, thanks to caching and offline authentication, remote users can connect to network resources simply by authenticating to their local machine. SSSD then maintains their network credentials.

Reduced load on identity and authentication providers
When requesting information, the clients first check the local SSSD cache. SSSD contacts the remote providers only if the information is not available in the cache.

## Identity management solutions
The community project FreeIPA is an Identity Management solution. It’s also known as the Red Hat IdM or simply IPA. FreeIPA uses 389 Directory Server as its database. Directory servers contain objects like a user object. That user object contains user information stored in LDAP attributes and it is accessible on the network through the LDAP (Lightweight Directory Access Protocol) protocol.

Microsoft Windows Server implements a directory service named Microsoft Active Directory Domain Services, abbreviated as Active Directory or AD. Active Directory is another identity management solution that is specifically designed for Windows. It also uses an LDAP server and has many similarities with IPA.

Beside FreeIPA and Active Directory, SSSD can also integrate to other identity solutions using the LDAP provider (for pure LDAP servers) and the Kerberos provider (for Kerberos authentication instead of plain passwords). It can also directly integrate with local users using the files provider.

## SSSD Architecture
Simply put, the main SSSD purpose is to store data from the remote database in a local cache and then serve this data to the target user (application). Keeping the cache up to date and valid is a difficult task and to do that SSSD consists of multiple components (processes and libraries) that talk to each other through various inter-process communication techniques. 

## How can I use sssd to integrate with AD.
You need to use `adcli` or `realmd` to integrate a Linux machine with AD the command is `realm join domain_name -U aduser`

## How can start sssd
`systemctl start sss` in case if you are failing to start sssd try to start sssd in debug mode using `sssd -i d7` here you can serach for errors.Troubleshoot sssd failure or failed to start sssd involves several steps first try to start sssd in debug mode using `sssd -i d7`. Some comon issue related to sssd start up involves `krb5_kt_start_seq_get failed: Key table file /etc/krb5.keytab not found` which indicates the keytab file is absent. Check using `klist -kte` if you have the keytab file. `sssd.service: main process exited, code=exited, status=4/NOPERMISSION` indicates sssd is failing to start due to permission issues check if you have proper permisison set on sssd.conf it should be owned by root and permission should be 600. 

## How can I troubleshoot LDAP user login issues using sssd
If you are failed to login with sssd first try to check if you can lookup the LDAP user using `id ldapuser` if it fails enable sssd debugging by adding `debug_level =9` under all section of the sssd.conf and restart sssd and reproduce the issue post reproducing the issue check `/var/log/sssd`. If the logins take too long or the time to execute id $username takes too long. First, make sure to understand what does id username do. Do you really care about its performance? Chances are you’re more interested in id -G performance.Check out the ignore_group_members options in the sssd.conf(5) manual page.Some users improved their SSSD performance a lot by mounting the cache into tmpfs. Slow login or slow lookup is common in sssd if the LDAP user is a member of large no. of groups say for example 500 groups. you can use `ignore_group_member = false` Normally the most data-intensive operation is downloading the groups including their members. Usually, we are interested in what groups a user is a member of (id aduser@ad_domain) as the initial step rather than what members do specific groups include (getent group adgroup@ad_domain). Setting the ignore_group_members option to True makes all groups appear as empty, thus downloading only information about the group objects themselves and not their members, providing a significant performance boost. Please note that id aduser@ad_domain would still return all the correct groups! If you are not able to login with LDAP user try to enable sssd debugging by adding `debug_level =9` under the `[$domain]` section of the sssd.conf followed by sssd restart and reproduce the issue. Post reproducing the issue check the sssd_be or sssd_pam.log


## Troubleshooting Basics
SSSD provides two major features - obtaining information about users and authenticating users. Each of these hook into different system APIs and should be viewed separately. However, a successful authentication can only be performed when the information about a user can be retrieved, so if authentication doesn’t work in your case, please make sure you can at least obtain info from about the user with getent passwd $user and id.

## SSSD debug logs
Each SSSD process is represented by a section in the sssd.conf config file. To enable debugging persistently across SSSD service restarts, put the directive debug_level=N, where N typically stands for a number between 1 and 10 into the particular section. Debug levels up to 3 should log mostly failures and anything above level 8 provides a large number of log messages. Level 6 might be a good starting point for debugging problems. You can also use the sss_debuglevel(8) tool to enable debugging on the fly without having to restart the daemon.

On Fedora/RHEL, debug logs are stored under /var/log/sssd. There is one log file per SSSD process. The services (also called responders) log into a log file called sssd_$service, for example NSS responder logs to /var/log/sssd/sssd_nss.log. Domain sections log to files called sssd_$domainname.log. The short-lived helper processes also log to their own log files, such as ldap_child.log or krb5_child.log. KCM logs to the file sssd_kcm.log.

## Troubleshooting Identity Information
Before diving into the SSSD logs and config files it is very beneficial to know what the SSSD Architecture looks like. Please note the examples of the DEBUG messages are subject to change in future SSSD versions.

General tips
To avoid SSSD caching, it is often useful to reproduce the bugs with an empty cache or at least invalid cache. However, keep in mind that also the cached credentials are stored in the cache! Do not remove the cache files if your system is offline and it relies on SSSD authentication!

Before sending the logs and/or config files to a publicly-accessible space, such as mailing lists or bug trackers, check the files for any sensitive information.

Please only send log files relevant to the occurrence of the issue. Issues in log files that are mega- or gigabytes large are more likely to be skipped

Unless the problem you’re trying to diagnose is related to enumeration, always reproduce the issue with enumerate=false. Triaging logs with enumeration enabled obfuscates the normal data flow.

## Troubleshooting Backend
A backend, often also called data provider, is an SSSD child process. This process talks to LDAP server, performs different lookup queries and stores the results in the cache. The SSSD Cache is a local database containing identity and authentication information which may be reused later to speed up answering client queries. SSSD backend also performs online authentication against LDAP or Kerberos and applies access and password policy to the user that is about to log in.

## SSD process is terminated by own WATCHDOG

(Fri Apr 14 15:07:19 2023) system1 sssd[sssd]: Child [1277] ('SSSDdomain':'%BE_SSSDdomain') was terminated by own WATCHDOG. Consult corresponding logs to figure out the reason.

The above log indicates that the sssd_be process was blocked too long on something that was longer than 3*10 seconds(the default value of timeout is 10 sec). In order to find which operation is blocking sssd_be, enable sssd debugging by adding debug_level = 9 under all sections of the /etc/sssd/sssd.conf file, especially under the [$domain] section, and wait for the issue to reoccur. Once the issue is observed, take the timestamp of the ... was terminated by own WATCHDOG message and then spot the last operation before the timestamp in /var/log/sssd/sssd_$domain.log. In the above example, sssd_be is getting killed, but there could be some other situation where sssd_nss or sssd_pam processes could get killed by watchdog. In this case, the troubleshooting process will be the same, but the respective sssd log needs to be checked instead of the /var/log/sssd/sssd_$domain.log file. Increasing the timeout values may serve as a workaround, however finding the root cause of the watchdog termination may be important. On a side note if the processing of group membership is very slow then also we can observe sssd_be killed by watchdog.

## sdap_async_sys_connect request failed

sdap_async_sys_connect request failed occurs if sssd is not able to connect to the LDAP server within 6 seconds. This could be an issue with DNS or the network. Validate the DNS SRV records; if SRV records are not working, hardcoding the AD/LDAP server may help here. For an example, if id_provider = ad is being used then hardcoding of AD servers can be done as: add ad_server = ad1.example.com, ad2.example.com under the [$domain] section of the /etc/sssd/sssd.conf. If the network is slow or ldap_network_timeout is reached, then consider increasing the value of ldap_network_timeout which is set to 6 seconds by default.

## krb5_auth_timeout or krb5_child_timeout reached

(Fri Apr 14 16:37:19 2023) [sssd[be[example.com]]] [krb5_child_timeout] (0x0040): Timeout for child [23514] reached. In case KDC is distant or network is slow you may consider increasing value of krb5_auth_timeout.

The above error is self-explanatory when trying to connect to the KDC server, but the KDC server is responding very slowly due to some reason. One of them could be an issue with the firewall or a slow network. As a workaround, consider increasing the value of krb5_auth_timeout which is 6 seconds by default.

## SSSD is offline/Backend is offline

SSSD is going offline because it cannot establish a connection to the LDAP server, but the cause could vary. It may be a DNS issue where SRV records are not resolving. It may be a connection issue when the remote server is unreachable because it is behind a firewall, etc. If the DNS or network issue is intermittent then enable authenticate against cache in SSSD. Set cache_credentials = True under the [$domain] section of the /etc/sssd/sssd.conf then user logins should still work if the credentials are already present in the cache. For more details refer to Authenticate against cache in SSSD.

SSSD service can fail to start due to multiple reasons. i.e:
- Check for any typos in sssd configuration files especially under /etc/sssd/conf.d/ directory and in /etc/sssd/sssd.conf
- Correct the typos and restart sssd service. If it fails, try removing any customized sssd configuration under /etc/sssd/conf.d/ directory.
- Make sure the permissions of `/etc/sssd/sssd.conf`, `/var/log/sssd` directory are correct.
- Check if any module is missing. Detailed information can be obtained by running SSSD in daemon mode with debug `sssd -d9 -i`
