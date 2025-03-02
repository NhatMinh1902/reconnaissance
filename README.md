# WEB HACKING RECONNAISSANCE

##  Manually Walking Through the Target

> Before we dive into anything else, it will help to first manually walk throug the application to learn more about it. Try to uncover every feature in the application that users can access by browsing through every page and clicking every link. Access the functionalities that you don’t usually use. 

## Google Dorking

> In fact, advanced Google searches are a powerful technique that hackers often use to perform recon. Hackers call this Google dorking. For the average Joe, Google is just a text search tool for finding images, videos, and web pages. But for the hacker, Google can be a means of discovering valuable information such as hidden admin portals, unlocked password files, and leaked authentication keys.

### site

- Tells Google to show you results from a certain site only. This will help you quickly find the most reputable source on the topic that you are researching.
   
    ```
    print site:python.org
    ```
### inurl

- Searches for pages with a URL that match the search string. It’s a powerful way to search for vulnerable pages on a particular website.
- Let’s say you’ve read a blog post about how the existence of a page called `/course/jumpto`.php on a website could indicate that it’s vulnerable to remote code execution. You can check if the vulnerability exists on your target by searching inurl:

    ```
    inurl:"/course/jumpto.php" site:example.com
    ```

### intitle

- Finds specific strings in a page’s title. This is useful because it allows you to find pages that contain a particular type of content.
- . For example, file-listing pages on web servers often have index of in their titles.

    ```
    intitle:"index of" site:example.com
    ```
    
### link

- Searches for web pages that contain links to a specified URL. You can use this to find documentation about obscure technologies or vulnerabilities.

    ```
    link:"https://en.wikipedia.org/wiki/ReDoS"
    ```

### filetype

- Searches for pages with a specific file extension. This is an incredible tool for hacking; hackers often use it to locate files on their target sites that might be sensitive, such as log and password files. For example, this query searches for log files, which often have the

    ```
    filetype:log site:example.com
    ```

### Wildcard (*)

- You can use the wildcard operator (*) within searches to mean any character or series of characters




> You can use advanced search engine options in many more ways to make your work more efficient.

- look for all of a company’s subdomains by searching as follows:

    ```
    site:*.example.com
    ```

- You can also look for special endpoints that can lead to vulnerabilities. 
    - **Kibana** is a data visualization tool that displays server operation data such as server logs, debug messages, and server status. A compromised **Kibana** instance can allow attackers to collect extensive information about a site’s operation. Many Kibana dashboards run under the path `app/kibana`.

    ```
    site:example.com inurl:app/kibana
    ```

- Google can find company resources hosted by a third party online, such as Amazon S3 buckets
    
    ```
    site:s3.amazonaws.com COMPANY_NAME
    ```


