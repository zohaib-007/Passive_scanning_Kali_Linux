# Complete Guide to Using the WHOIS Tool on Kali Linux
WHOIS is an essential command-line utility used during the footprinting and information-gathering phases of a penetration test. 
It queries global databases to retrieve registration details for internet resources, such as domain names and IP address blocks.

## Step 1: Install the WHOIS Tool

Kali Linux typically comes with whois pre-installed. If you are using a minimal installation or need to restore it, use the following 
commands in your terminal:

* Update your system package repository 

                                                    sudo apt update
                                                               
* Install the WHOIS utility package
       
                                                sudo apt install whois -y

## Step 2: Perform a Basic Domain Lookup

To gather information on a specific domain name, pass the target domain directly as the command argument.

                                                 whois wikipedia.org

<p align="center">
  <img src="wikipedia.png">
</p>

                                                
### Analyzing the Output:
The database will return a detailed plain-text report. Key fields to extract and analyze include:
* Domain Name: wikipedia.org
* Registrar: MarkMonitor Inc. (The company where the domain was purchased)
* Creation Date: 2001-01-13 (Tells you how long the target infrastructure has existed)
* Registry Expiry Date: 2027-01-13 (The next timeline milestone for the domain renewal)
* Name Servers:
	* ns0.wikimedia.org
	* ns1.wikimedia.org
	* ns2.wikimedia.org


## Step 3: Perform an IP Address Lookup

WHOIS can trace network block ownership and identify hosting providers by scanning individual IP addresses rather than domain names.
Query a public IP address: 

                                                   whois 8.8.8.8
<p align="center">
  <img src="whois_ip.png">
</p>


### Analyzing the Real-World Output
* **NetRange:** 8.8.8.0 - 8.8.8.255 (The specific block range assigned)
* **CIDR:** 8.8.8.0/24 (The routing prefix for the network block)
* **OrgName:** Google LLC (The legal organization operating the infrastructure)
* **Country:** US (The country where the network block is registered)


## Step 4: Advanced Operations
WHOIS outputs are often massive and difficult to scan within a single terminal window. Use standard Linux stream redirections and 
pipes to filter and save your threat intelligence data efficiently.

* **Export Findings to a Report File**

                                   whois wikipedia.org > wikipedia_whois_report.txt
                                   
Save the query results directly into a text document for documentation or documentation logs.

* **View Large Outputs Page-by-Page**

                                               whois wikipedia.org | less
                                               
Pipe your results directly into less to scroll up and down using your keyboard arrow keys. Press q for exit.


* **Target Specific Information Strings**

                                      whois wikipedia.org | grep -i "Expiry Date"
                        
<p align="center">
  <img src="expiry.png">
</p>

Filter out the noise and pull specific parameters by piping the output into grep


## Notes on Modern WHOIS & Privacy

When running lookups on commercial domains, you will frequently notice fields like Registrant Name, Email, or Phone show values 
like "REDACTED FOR PRIVACY".

* **Data Regulations:** Modern privacy frameworks like GDPR restrict registrars from publicly publishing personal contact details 
by default.

* **Privacy Proxies:** Domain owners often use privacy guard proxies to mask their actual physical identities behind the registrar's 
corporate credentials.



# Complete Guide to Using theHarvester on Kali Linux

theHarvester is a powerful open-source intelligence (OSINT) tool pre-installed on Kali Linux. It is designed to gather emails, subdomains, hosts, employee names, open ports, and banners from different public sources like search engines, PGP key servers, and SHODAN databases. It is an essential tool during the passive reconnaissance phase of a penetration test.

## Step 1: Install or Update theHarvester
While theHarvester comes pre-installed on standard Kali Linux distributions, it is highly recommended to update it to ensure all API connectors and search scraping modules are functioning correctly.
**1\. Open your terminal and run the following command to update your package lists**

                                                sudo apt update

**2\. Install or update the tool**

                                         sudo apt install theharvester -y
   
**3\. Verify the installation and view the available command arguments**

                                               theHarvester -h

<p align="center">
  <img src="https://example.com">
</p>

## Step 2: Core Command Syntax

The basic syntax for running a query with theHarvester relies on three core flags:

                                      theHarvester -d [domain] -l [limit] -b [source]
									  
* **-d:** Specifies the target domain or company name you want to investigate.
* **-l:** Limits the number of search results to work through (lower numbers are faster; higher numbers provide deeper scans).
* **-b:** Defines the public data source (e.g., bing, duckduckgo, linkedin, yahoo, or all).


## Step 3: Run a Real-Life Reconnaissance Scan
Let us use a live public entity (kali.org) to demonstrate a real-world passive footprinting scan.
1. Execute the following command to scan for public information using the DuckDuckGo search engine, limiting the analysis to the top 200 results

                                          theHarvester -d kali.org -l 200 -b duckduckgo

**Analyzing the Real-World Output:**

<p align="center">
  <img src="https://example.com">
</p>

The tool will print a structured text report directly into your terminal. Keep an eye out for these key sections:
* **Target Domain Evaluation:** Confirms kali.org is being analyzed.
* **Searching Source:** Indicates it is actively scraping results from DuckDuckGo.
* **Emails Found:** Displays public email addresses linked to the domain (e.g., devel@kali.org, info@kali.org).
* **Hosts/Subdomains Found:** Lists discovered subdomains paired with their resolved IP addresses:
    * **bugs.kali.org** -> 192.0.2.45
	* **pkg.kali.org** -> 192.0.2.98
	* **images.kali.org** -> 192.0.2.112


## Step 4: Advanced Scans and Multi-Source Scraping

To get a comprehensive view of a target's attack surface, you can aggregate data from multiple public platforms or extract business employee networks.

**1\. Broad Multi-Source Scan**
Instead of using a single search engine, use all to pull records across every built-in free module simultaneously

                                      theHarvester -d kali.org -l 500 -b all
<p align="center">
  <img src="https://example.com">
</p>


**2\. Harvest Employee Professional Profiles**
You can target professional networks to map out corporate identity structures and employee naming conventions by passing linkedin as the data source.

                                     theHarvester -d kali.org -l 300 -b linkedin

## Save Findings to an XML/JSON Report File

Passive reconnaissance reports can grow quite large. Use the -f flag to export your output cleanly into both an XML and HTML file format for structured documentation.

                            theHarvester -d kali.org -l 200 -b bing -f kali_recon_report


**[!NOTE] Notes On API Keys and Rate Limiting:**
When running comprehensive or frequent scans, you may notice that some advanced engines (like GitHub, Shodan, or Hunter.io) require authentication or throw error blocks.

   * **Free vs. Paid Modules:** Basic engines like Bing, DuckDuckGo, and Baidu work out of the box. Passive scanners like Shodan, Censys, or VirusTotal require an API key.
   * **Adding API Keys:** You can add your custom API tokens directly to theHarvester config file located on your Kali system at /etc/theHarvester/api-keys.yaml using a text editor like nano
     
                                       sudo nano /etc/theHarvester/api-keys.yaml

  * **Rate Limiting:** If you scan too aggressively with a high -l value, public search engines might temporarily block your IP address with CAPTCHAs. Keep limits reasonable or use a proxy/VPN.
  * 



							
