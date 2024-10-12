# OneLiner ✍🏻

## This is a custom oneliner scripts used for bug bounty

#### 1. Gathering ips

* Below commands gather iprange using ASN number & usings prips we get ips

```
whois -h whois.radb.net  -- '-i origin AS397015' | grep -Eo "([0-9.]+){4}/[0-9]+" | uniq >> ASN_ips
for i in $(cat ASN_ips); do prips $i >> ips; done
```

#### 2. Gathering subdomains

```
bbot -t carvanadomainslist -f subdomain-enum -o subs -y
```

#### 3.Vhost bruteforce

* Gather all the ips of the organisation (eg: fbips)
* Gather all the domains of the organisation (eg: fbsubs)
* Run the below command

```
for i in $(cat ips);do ffuf -w subs -u https://$i -H "Host: FUZZ" -of csv -o $i.csv ; done
```

#### 4. Getting js files from urls

```
cat urls | ack '.*\.(js|js\?.*)$' > js.txt
```

#### 5. Naabu

```
naabu -top-ports 1000 -list subfinder_domain -o open_ports
```

#### 6. Dirsearch

```
python3 ~/dirsearch/dirsearch.py -w ~/wordlist/custm.txt -l domains -x 400-404 -o dir.txt -t 8
```

#### 7. ffuf

```
ffuf -u https://www.kohls.com/myaccount/FUZZ -b "AKA_GEO=US; akacd_www-kohls-com-mosaic-p2=2147483647~rv=46~id=ec71355a106105d1d905b95a6809e848;" -w /Users/faique/pentest/wordlist/jsp.txt -H "User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:127.0) Gecko/20100101 Firefox/127.0" -x http://127.0.0.1:8080
```

#### 8. Katana

* Normal crawling

```
katana -u domains -proxy http://127.0.0.1:8080 -fs fqdn
```

* Crawl with cookies & crawl js files as well

```
katana -u domains -proxy http://127.0.0.1:8080 -js-crawl -H cookie.txt -fs fqdn
```

#### 9. Finding secrets

* Using nuclei

```
cat jsurl | urldedupe | nuclei -t ~/nuclei-templates/http/exposures
```

* Using secretfinder

```
cat jsfiles.txt | while read url; do python3 ~/pentest/tools/secretfinder/SecretFinder.py -i $url -o cli; done
```

#### 10. XSS

* Get all urls from burpsuite & waybackurls
* Merge them in one file
* Remove all parmeter after ?
* Remove dupicate urls
* Run X8 script

```
~/go/bin/gf xss ~/pentest/Application/test/test_url | sed -E 's/([&?])[^&?]+=[^&]*//g' | sed 's/[?&]$//' | urldedupe

x8 -u x8url -w param -X GET -L -m 25 -o x8report -x http://127.0.0.1:8080 -H 'Cookie: test=test'

```

### 11. Nuclei
