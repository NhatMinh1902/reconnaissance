# Scope Discovery

> Let’s now dive into recon itself. First, always verify the target’s scope. A program’s scope on its policy page specifies which subdomains, products, and applications you’re allowed to attack. Carefully verify which of the company’s assets are in scope to avoid overstepping boundaries during the recon and hacking process.

## WHOIS and Reverse WHOIS

- When companies or individuals register a domain name, they need to supply identifying information, such as their mailing address, phone number, and email address, to a domain registrar.Anyone can then query this information by using the `whois` command.

    ```
    whois facebook.com
    ```
- This information is not always available, as some organizations and individuals use a service called domain privacy, in which a third-party service provider replaces the user’s information with that of a forwarding service. 
- Reverse WHOIS is extremely useful for finding obscure or internal domains not otherwise disclosed to the public. Use a public reverse WHOIS tool like [ViewDNS.info](https://viewdns.info/reversewhois/) to conduct this search.
- WHOIS and reverse WHOIS will give you a good set of top-level domains to work with.

## IP Addresses

- Another way of discovering your target’s top-level domains is to locate IP addresses. Find the IP address of a domain you know by running the `nslookup` command.

    ```
    $ nslookup facebook.com
    Server: 192.168.0.1
    Address: 192.168.0.1#53
    Non-authoritative answer:
    Name: facebook.com
    Address: 157.240.2.35
    ```
- Once you’ve found the IP address of the known domain, perform a **reverse** IP lookup. Reverse IP searches look for domains hosted on the same server, given an IP or domain(use ViewDNS.info).

- Also run the whois command on an IP address, and then see if the target has a dedicated IP range by checking the NetRange field. An IP range is a block of IP addresses that all belong to the same organization.

    ```
    NetRange:       157.240.0.0 - 157.240.255.255
    CIDR:           157.240.0.0/16
    NetName:        THEFA-3
    NetHandle:      NET-157-240-0-0-1
    Parent:         NET157 (NET-157-0-0-0-0)
    NetType:        Direct Allocation
    OriginAS:       
    Organization:   Facebook, Inc. (THEFA-3)
    RegDate:        2015-05-14
    Updated:        2021-12-14
    Ref:            https://rdap.arin.net/registry/ip/157.240.0.0

    OrgName:        Facebook, Inc.
    OrgId:          THEFA-3
    Address:        1601 Willow Rd.
    City:           Menlo Park
    StateProv:      CA
    PostalCode:     94025
    Country:        US
    RegDate:        2004-08-11
    Updated:        2024-02-14
    Ref:            https://rdap.arin.net/registry/entity/THEFA-3

    OrgTechHandle: OPERA82-ARIN
    OrgTechName:   Operations
    OrgTechPhone:  +1-650-543-4800 
    OrgTechEmail:  domain@facebook.com
    OrgTechRef:    https://rdap.arin.net/registry/entity/OPERA82-ARIN

    OrgAbuseHandle: OPERA82-ARIN
    OrgAbuseName:   Operations
    OrgAbusePhone:  +1-650-543-4800 
    OrgAbuseEmail:  domain@facebook.com
    OrgAbuseRef:    https://rdap.arin.net/registry/entity/OPERA82-ARIN

    ```

- To figure out if a company owns a dedicated IP range, run several IP-toASN translations to see if the IP addresses map to a single ASN.

    ```
    $ whois -h whois.cymru.com 157.240.2.20
    AS | IP | AS Name
    32934 | 157.240.2.20 | FACEBOOK, US
    $ whois -h whois.cymru.com 157.240.2.27
    AS | IP | AS Name
    32934 | 157.240.2.27 | FACEBOOK, US
    $ whois -h whois.cymru.com 157.240.2.35
    AS | IP | AS Name
    32934 | 157.240.2.35 | FACEBOOK, US
    ```

- The `-h` flag in the whois command sets the WHOIS server to retrieve information from.
-  `whois.cymru.com` is a database that translates IPs to ASNs.

- . From the following output, we can deduce that any IP within the `157.240.2.21 to 157.240.2.34` range probably belongs to Facebook.

## Certificate Parsing

- Another way of finding hosts is to take advantage of the **Secure Sockets Layer (SSL)** certificates used to encrypt web traffic. An SSL certificate’s **Subject Alternative Name** field lets certificate owners specify additional hostnames that use the same certificate, so you can find those hostnames by parsing this field. Use online databases like [crt.sh](https://crt.sh), Censys, and Cert Spotter to find certificates for a domain. 

    ```
    https://crt.sh/?q=facebook.com&output=json
    ```

## Subdomain Enumeration

> After finding as many domains on the target as possible, locate as many subdomains on those domains as you can. Each subdomain represents a new angle for attacking the network. The best way to enumerate subdomains is to use automation.

- Tools like `Sublist3r`, `SubBrute`, `Amass`, and `Gobuster` can enumerate subdomains automatically with a variety of wordlists and strategies.
    - Sublist3r works by querying search engines and online subdomain databases.    - SubBrute is a brute-forcing tool that guesses possible subdomains until it finds real ones.
    - s. Amass uses a combination of DNS zone transfers, certificate parsing, search engines, and subdomain databases to find subdomains.

- To use many subdomain enumeration tools, you need to feed the program a wordlist of terms likely to appear in subdomains.
    - Daniel Miessler’s SecLists at https://github.com/danielmiessler/SecLists/ is a pretty extensive one.
    - use a wordlist generation tool like [Commonspeak2](https://github.com/assetnote/commonspeak2/) to generate wordlists based on the most current internet data.

- Command to remove duplicate items from a set of two wordlists:
    ```
    sort -u wordlist1.txt wordlist2.txt
    # -u option tells sort to return only unique items in the sorted list
    ```
- Gobuster is a tool for brute-forcing to discover subdomains, directories, and files on target web servers. Its DNS mode is used for subdomain bruteforcing.
    ```
    gobuster dns -d target_domain -w wordlist
    # use the flag -d to specify the domain you want to brute-force
    # -w to specify the wordlist
    ```

- A good tool for automating this process is [Altdns](https://github.com/infosec-au/altdns/), which discovers subdomains with names that are permutations of other subdomain names.

- Also look for subdomains of subdomains. EX: `dev.example.com`, you might find subdomains like `1.dev.example.com`.

## Service Enumeration
- Nmap: link git
- With Shodan, you can discover the presence of webcams, web servers, or even power plants based on criteria such as hostnames or IP addresses.

- Alternatives to Shodan include Censys and Project Sonar. Combine the information you gather from different databases for the best results. With these databases, you might also find your target’s IP addresses, certificates, and software versions.

## Directory Brute-Forcing

> The next thing you can do to discover more of the site’s attack surface is brute-force the directories of the web servers you’ve found. Finding directories on servers is valuable, because through them, you might discover hidden admin panels, configuration files, password files, outdated functionalities, database copies, and source code files. Directory brute-forcing can sometimes allow you to directly take over a server!

- Even if you can’t find any immediate exploits, directory information often tells you about the structure and technology of an application.

-  Using `Dirsearch` or `Gobuster` for directory brute-forcing. These tools use wordlists to construct URLs, and then request these URLs from a web server.
    - **Status code = 200**  the directory or file exists.  This means you can browse to the page and see what Web Hacking Reconnaissance the application is hosting there
    - **Status code = 404** the directory or file doesn’t exist.
    - **Status code = 403** it exists but is protected. Examine **403** pages carefully to see if you can bypass the protection to access the content.

    ```
    ./dirsearch.py -u scanme.nmap.org -e php
    # -u flag specifies the hostname
    # e -e flag specifies the file extension to use when constructing URLs
    ```

- Gobuster’s Dir mode is used to find additional content on a specific domain or subdomain. This includes hidden directories and files.
    -  the `-u` flag to specify the domain or subdomain you want to brute-force.
    - `-w` to specify the wordlist you want to use
    ```
    gobuster dir -u target_url -w wordlist
    ```

- Use a screenshot tool like [EyeWitness](https://github.com/FortyNorthSecurity/EyeWitness/) or [Snapper](https://github.com/dxa4481/Snapper/) to automatically verify that a page is hosted on each location.

- EyeWitness accepts a list of URLs and takes screenshots of each page.
- In a photo gallery app, you can quickly skim these to find the interesting-looking ones. Keep an eye out for hidden services, such as developer or admin panels, directory listing pages, analytics pages, and pages that look outdated and illmaintained. T ese are all common places for vulnerabilities to manifest.

## Spidering the Site
