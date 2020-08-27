Mainframe (z/os) FIM
 
•	FIM replication
•	Some of the key files on the mainframe that should be monitored are called SYS1.PARMLIB or the PARMLIB concatenation. This dataset contains lists of system parameter values that are used by most components of z/OS. If someone were to have clear access to the PARMLIB concatenation, they would essentially have unprecedented control over the mainframe
•	Due to obvious differences between mainframes and distributed systems, it is typically unreasonable to implement FIM by performing checksums as it would require impractical use of resources. Alternatively, the organization may be able to utilize third party or homegrown solutions to implement a SIEM agent on the mainframe to convert SMF records to SIEM-type syslogs (e.g., RFC 3264) within an LPAR and forward to the organizations distributed SIEM 
•	Logging information such as RACF, ACF2, Top Secret, address space, file accesses, TCP/ IP, FTP, CICS and DB2 DAM and other security event messages
 
•	Monitor the following:
•	Any malicious program intended to run on the mainframe has to be placed in a new or existing load library. Some load libraries have higher authority, such as those defined in the APF
i.	Monitor libraries in the APF list and monitor for additions to that list, either manually by editing the SYS1.PARMLIB system dataset or alternatively through operator commands
ii.	SMF records 14, 15, 18 and 42 are key record types that should be monitored for dataset allocations and PDS member additions, deletions and updates. Alerts should be generated for access to those system critical and other sensitive libraries
 
•	A program that is part of an APF library could effectively be an extension of the operating system. For example, it may be able to remove system blocks and even turn off logging. Any FIM solution should have clear understanding of all access to the APF-authorized library
i.	SMF types 14 and 15 record whenever a dataset is closed or processed by end of volume
ii.	SMF type type 17 records when a dataset is scratched and 18 audits renamed datasets
iii.	SMF type 42 will monitor dataset members and audit when one has been updated, renamed, or deleted
 
•	Any malicious program would have to be copied into a file on the mainframe and loaded from that file by the operating system before it can be executed. Creating the file and defining its catalog entries is fully audited by SMF and the user’s activity for the file is traceable...and may be an indication of malicious activity
i.	RACF SMF type 80 records both authorized and unauthorized access attempts as well as attempts to modify profiles
 
•	Event Correlation should be a consideration - for example, not all unauthorized attempts are malicious (e.g., keystroke error). On the other hand, not all authorized accesses are safe, but you might never know about the malicious ones without correlation.
i.	Real-time event messages from z/OS should be included in your correlation engine along with distributed systems  (e.g., mainframe user ID, failed password, and IP logs)
 
•	Another fundamental audit trail should be TCP/IP and 3270 connections (e.g., protocol of terminal emulation and green screens [even though the use of green screens is pretty minimal as most applications are now web based)
i.	A type 80 event records security issues, such as password violations and other denied resource access attempts. Other security systems such as ACF2 and Top Secret also use type 80 and 81 SMF records
ii.	Additionally TCP/IP activity through SMF 119 and 3270 connection should be monitored
 
•	IBM’s IND$FILE is the facility that lets a 3270 user download/upload a dataset between the emulator pc and the mainframe. (There are also other IND$FILE programs, but the reference here is just to IND$FILE that runs as a time sharing option, or TSO command.) Simply put, a TSO command provides access to shared mainframe resources such as CPU, memory and datasets. IND$FILE is still subject to the security constraints of RACF (and ACF2, Top Secret), but it has no audit trail. It does not provide any information to these mainframe security subsystems to track user and system interaction with z/OS
i.	Other than developing a  solution that writes an SMF for IND$FILE activity, there are few options for auditing IND$FILE’s ability to change, delete, or create a system file that contains a malicious operation
ii.	There may, however, be third-party applications which act as a wrapper that audits the usage of IND$FILE (e.g., writes an SMF record and calls to the SIEM agent solution with the following information for every IND$FILE transfer:
1.	User ID, name and Group 
2.	Terminal name and IP address
3.	Mainframe dataset name
4.	Time stamp
5.	Duration of transfer 
6.	Other parameters
 
•	Attempts to access and then exfiltrate data from tables that contain credit card data, social security numbers or other data from the mainframe should be traceable. Even if a user accesses a DB2 table but changes nothing there should still be a trail  of the access log (e.g., if there is an attempt purely to determine if the attacker would be detected and then try again later)
i.	As with other mainframe event messages there should be a record of the user accessing DB2. The DB2 audit facility can track this access, but it needs a means of sending the real-time event to your SIEM system. DB2 audit tracing can be used to monitor this type of access, even if the user had administrative privileges, which shows up in an IFCID number
ii.	There may be a third-party solution that monitors access for DB2 for all of the IFCID numbers as well as SMF records 100, 101, and 102. DB2 activity audited by these SMF records and IFCID numbers would have to be captured by this solution and converted to a SIEM ready syslog protocol

