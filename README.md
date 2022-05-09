# Agartha { LFI | RCE | Auth | SQLi | Http-Js }
Agartha is a penetration testing tool which creates dynamic payload lists and user access matrix to reveal injection flaws and authentication/authorization issues. There are many different attack payloads exists, but Agartha creates run-time, systematic and vendor-neutral payloads with many different possibilities and bypassing methods. It also draws attention to user session and URL relationships, which makes easy to find user access violations. And additionally, it converts Http requests to JavaScript to help digging up XSS issues. In summary:


- **Payload Generator**: It creates payloads/wordlists for different attacks.
	- **Directory Traversal/Local File Inclusion**: It creates file dictionary lists with various encoding and escaping characters.
	- **Remote Code Execution**: It creates command dictionary lists for both unix and windows environments with different combinations.
	- **SQL Injection**: It creates Stacked Queries, Boolean-Based, Union-Based, Time-Based and Order-Based SQLi wordlist for various databases to help finding vulnerable spots.
- **Authorization Matrix**: It creates an access role matrix based on user sessions and URL lists to determine authorization/authentication related access violation issues.
- And **Http Request to JavaScript Converter**: It converts Http requests to JavaScript code to be useful for further XSS exploitation and more.<br/><br/>


Here is a small tutorial how to use.
## Installation
<!---You can install Agartha through official Burp Store automatically, from Burp menu 'Extender > BApp Store'.-->

For manual installation, you should download 'jython' file first, and then:
- Burp Menu > Extender > Options > Python Environment > Locate jython standalone jar file
- Burp Menu > Extender > Extensions > Add > Extension Type: Python > Select file: 'agartha.py'
- After, you will see 'Agartha' tab in the main window and it will be also registered the right click, under 'Extensions > Agartha {LFI|RCE|Auth|SQLi|Http-Js}'.<br/><br/>

## Directory Traversal/Local File Inclusion
It both supports unix and windows file systems. You can generate any wordlists dynamically for the path you want. You just need to supply a file path and that's all. 

**'Depth'** is representation of how deep the wordlist should be. You can generate wordlists 'till' or 'equal to' this value.

**'Waf Bypass'** asks for if you want to include all bypass features; like null bytes, different encoding, etc.

<img width="1000" alt="Directory Traversal/Local File Inclusion wordlist" src="https://user-images.githubusercontent.com/50321735/152050458-84c29e84-6e12-486b-99d2-fcf220791798.png"><br/><br/>


## Remote Code Execution
It creates command execution dynamic wordlists for the command you supply. It combines different separators and terminators for unix and windows environments together.

**'URL Encoding'** encodes dictionary output.

<img width="1000" alt="Remote Code Execution wordlist" src="https://user-images.githubusercontent.com/50321735/161567724-f73bfea2-1386-447d-b81b-7b811e13e426.png"><br/><br/>

## SQL Injection
It generates payloads for Stacked Queries, Boolean-Based, Union-Based, Time-Based, Order-Based SQLi attacks, and you do not need to supply any inputs. You just pick what type of SQLi attacks and databases you want, then it will generate a wordlist with different combinations. 

**'URL Encoding'** encodes dictionary output.

**'Waf Bypass'** asks for if you want to include all bypass features; like null bytes, different encoding, etc.

**'Union-Based'** and **'Order-Based'** ask for how deep the payload should be. The default value is 5.

And the rest is related with database and attack types.

<img width="1000" alt="SQL Injection wordlist" src="https://user-images.githubusercontent.com/50321735/161572348-eb5d610d-cb81-45c7-a9a3-796f823b0032.png"><br/><br/>


## Authorization Matrix
This part focuses on user session and URLs relationships to determine access violations. The tool will visit all URLs from pre-defined user sessions and fill the table with all Http responses. It is a kind of access matrix and helps to find out authentication/authorization issues. Afterwards we will see what user can access what page contents.
- **User session name**: You can right click on any request and send it 'Agartha Panel' to define user sessions.
- **URL Addresses** user can visit: You can use Burp's spider feature or any sitemap generators. You may need to provide different URLs for different users.


<img width="1000" alt="Authorization Matrix, sending http req" src="https://user-images.githubusercontent.com/50321735/152217672-353b42a8-bb06-4e92-b9af-3f4e487ab1fd.png">


After sending Http request to Agartha, the panel will fill some fields in the tool. 
1. What's username for the session you provide. You can add up to 4 different users and each user will have a different color to make it more readable.
2. User's request header and all user related URL visits will be based on it.
3. URL addresses the user can visit. You can create this list with manual effort or automatic tools, like spiders, sitemap generators, etc, and do not forget to remove logout links.
4. All URLs you supply will be in here. Also user cells will be colored, if the URL belongs to her/him.
5. Http requests and responses without authentication. All session cookies, tokens and parameters will be removed form Http calls.
6. Http requests and responses with the user session you define in the first step. Cell titles show Http response codes and response lengths. 
7. Just click the cell you want to examine and Http details will be shown in here.


<img width="1000" alt="Role Matrix" src="https://user-images.githubusercontent.com/50321735/152227189-9e4b93df-de26-438e-ac1c-1aabcaf1ff56.png">


After clicking 'RUN', the tool will fill user and URL matrix with different colors. Besides the user colors, you will see orange, yellow and red cells. The URL address does not belong to the user and the cell color is:
- Yellow, because the response returns 'HTTP 302' with authentication/authorization concerns
- Orange, because the response returns 'HTTP 200' but different content length, with authentication/authorization concerns
- Red, because the response returns 'HTTP 200' and same content length, with authentication/authorization concerns

It will be quite similar, even if we add more users and any authorization concerns will be highlighted in the same way. 

You may also notice, it support only one Http request method and user session at the same time, because it processes bulk requests and it is not possible to provide different header options for each calls. But you may play with 'GET/POST' methods to see response differences.<br/><br/>


## Http Request to JavaScript Converter
The feature is for converting Http requests to JavaScript code. It can be useful to dig up further XSS issues and bypass header restrictions, like CSP, CORS.

To access it, right click any Http Request, Extensions, 'Agartha', and 'Copy as JavaScript'.

<img width="1000" alt="Http Request to JavaScript Converter" src="https://user-images.githubusercontent.com/50321735/152224405-d10b78a2-9b18-44a9-a991-5b9c451c7253.png">

It will automatically save it to your clipboard with some remarks. For example:
```
Http request with minimum header paramaters in JavaScript:
	<script>var xhr=new XMLHttpRequest();xhr.open('POST','http://vm:80/dvwa/login.php');xhr.withCredentials=true;xhr.setRequestHeader('Content-type','application/x-www-form-urlencoded');xhr.send('username=admin&password=password&Login=Login');</script>

Http request with all header paramaters in JavaScript:
	<script>var xhr=new XMLHttpRequest();xhr.open('POST','http://vm:80/dvwa/login.php');xhr.withCredentials=true;xhr.setRequestHeader('Host',' vm');xhr.setRequestHeader('User-Agent',' Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:97.0) Gecko/20100101 Firefox/97.0');xhr.setRequestHeader('Accept',' */*');xhr.setRequestHeader('Accept-Language',' en-US,en;q=0.5');xhr.setRequestHeader('Accept-Encoding',' gzip, deflate');xhr.setRequestHeader('Content-type',' application/x-www-form-urlencoded');xhr.setRequestHeader('Content-Length',' 44');xhr.setRequestHeader('Origin',' http://vm');xhr.setRequestHeader('Connection',' close');xhr.setRequestHeader('Referer',' http://vm/dvwa/login.php');xhr.send('username=admin&password=password&Login=Login');</script>

For redirection, please also add this code before '</script>' tag:
	xhr.onreadystatechange=function(){if (this.status===302){var location=this.getResponseHeader('Location');return ajax.call(this,location);}};
```
Please note that, the JavaScript code will be called over original user session and many header fields will be filled automatically. In some cases, the server may require some header field mandatory, and therefore you may need to modify the code for an adjustment.
