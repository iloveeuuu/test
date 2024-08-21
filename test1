#!/usr/bin/env python3
# takeover - subdomain takeover finder
# coded by M'hamed (@m4ll0k) Outaadi
# edited by edoardottt (https://github.com/edoardottt/takeover)

import os
import json
import requests
import urllib.parse
import concurrent.futures as thread
import urllib3
import getopt
import sys
import re


r = "\033[1;31m"
g = "\033[1;32m"
y = "\033[1;33m"
b = "\033[1;34m"
r_ = "\033[0;31m"
g_ = "\033[0;32m"
y_ = "\033[0;33m"
b_ = "\033[0;34m"
e = "\033[0m"

global _output
_output = []
global k_
k_ = {
    "domain": None,
    "threads": 1,
    "d_list": None,
    "proxy": None,
    "output": None,
    "timeout": None,
    "process": False,
    "user_agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/537.36 \
(KHTML, like Gecko) Chrome/76.0.3809.36 Safari/537.36",
    "verbose": False,
    "dict_len": 0,
}


# index/lenght * 100
def PERCENT(x, y):
    return float(x) / float(y) * 100


services = {
    "AWS/S3": {"error": r"The specified bucket does not exist"},
    "BitBucket": {"error": r"Repository not found"},
    "Github": {
        "error": r"There isn\\\'t a Github Pages site here\.|a Github Pages site here"
    },
    "Shopify": {"error": r"Sorry\, this shop is currently unavailable\."},
    "Fastly": {"error": r"Fastly error\: unknown domain\:"},
    "Ghost": {
        "error": r"The thing you were looking for is no longer here\, or never was"
    },
    "Heroku": {
        "error": r"no-such-app.html|<title>no such app</title>|herokucdn.com/error-pages/no-such-app.html|No such app"
    },
    "Pantheon": {
        "error": r"The gods are wise, but do not know of the site which you seek|404 error unknown site"
    },
    "Tumbler": {
        "error": r"Whatever you were looking for doesn\\\'t currently exist at this address."
    },
    "Wordpress": {"error": r"Do you want to register"},
    "TeamWork": {"error": r"Oops - We didn\'t find your site."},
    "Helpjuice": {"error": r"We could not find what you\'re looking for."},
    "Helpscout": {"error": r"No settings were found for this company:"},
    "Cargo": {"error": r"<title>404 &mdash; File not found</title>"},
    "Uservoice": {"error": r"This UserVoice subdomain is currently available"},
    "Surge.sh": {"error": r"project not found"},
    "Intercom": {
        "error": r"This page is reserved for artistic dogs\.|Uh oh\. That page doesn\'t exist</h1>"
    },
    "Webflow": {
        "error": r"<p class=\"description\">The page you are looking for doesn\'t exist or has been \
moved.</p>|The page you are looking for doesn\'t exist or has been moved"
    },
    "Kajabi": {"error": r"<h1>The page you were looking for doesn\'t exist.</h1>"},
    "Thinkific": {
        "error": r"You may have mistyped the address or the page may have moved."
    },
    "Tave": {"error": r"<h1>Error 404: Page Not Found</h1>"},
    "Wishpond": {"error": r"<h1>https://www.wishpond.com/404?campaign=true"},
    "Aftership": {
        "error": r"Oops.</h2><p class=\"text-muted text-tight\">The page you\'re looking for doesn\'t exist."
    },
    "Aha": {"error": r"There is no portal here \.\.\. sending you back to Aha!"},
    "Tictail": {
        "error": r"to target URL: <a href=\"https://tictail.com|Start selling on Tictail."
    },
    "Brightcove": {"error": r"<p class=\"bc-gallery-error-code\">Error Code: 404</p>"},
    "Bigcartel": {"error": r"<h1>Oops! We couldn&#8217;t find that page.</h1>"},
    "ActiveCampaign": {"error": r"alt=\"LIGHTTPD - fly light.\""},
    "Campaignmonitor": {
        "error": r"Double check the URL or <a href=\"mailto:help@createsend.com|Trying to access your account"
    },
    "Acquia": {
        "error": r"The site you are looking for could not be found.|If you are an Acquia Cloud \
customer and expect to see your site at this address|Web Site Not Found"
    },
    "Proposify": {
        "error": r"If you need immediate assistance, please contact <a href=\"mailto:support@proposify.biz"
    },
    "Simplebooklet": {
        "error": r"We can\'t find this <a href=\"https://simplebooklet.com"
    },
    "GetResponse": {
        "error": r"With GetResponse Landing Pages, lead generation has never been easier"
    },
    "Vend": {"error": r"Looks like you\'ve traveled too far into cyberspace."},
    "Jetbrains": {"error": r"is not a registered InCloud YouTrack."},
    "Smartling": {"error": r"Domain is not configured"},
    "Pingdom": {"error": r"pingdom|Sorry, couldn\'t find the status page"},
    "Tilda": {"error": r"Domain has been assigned|Please renew your subscription"},
    "Surveygizmo": {"error": r"data-html-name"},
    "Mashery": {"error": r"Unrecognized domain <strong>|Unrecognized domain"},
    "Divio": {"error": r"Application not responding"},
    "feedpress": {"error": r"The feed has not been found."},
    "Readme.io": {"error": r"Project doesnt exist... yet!"},
    "statuspage": {"error": r"You are being <a href=\'https>"},
    "zendesk": {"error": r"Help Center Closed"},
    "worksites.net": {"error": r"Hello! Sorry, but the webs>"},
    "Agile CRM": {"error": r"this page is no longer available"},
    "Anima": {
        "error": r"try refreshing in a minute|this is your website and you've just created it"
    },
    "Fly.io": {"error": r"404 Not Found"},
    "Gemfury": {"error": r"This page could not be found"},
    "HatenaBlog": {"error": r"404 Blog is not found"},
    "Kinsta": {"error": r"No Site For Domain"},
    "LaunchRock": {
        "error": r"It looks like you may have taken a wrong turn somewhere|worry...it happens to all of us"
    },
    "Ngrok": {"error": r"ngrok.io not found"},
    "SmartJobBoard": {
        "error": r"This job board website is either expired or its domain name is invalid"
    },
    "Strikingly": {"error": r"page not found"},
    "Tumblr": {
        "error": r"Whatever you were looking for doesn\'t currently exist at this address"
    },
    "Uberflip": {
        "error": r"hub domain\, The URL you\'ve accessed does not provide a hub"
    },
    "Unbounce": {"error": r"The requested URL was not found on this server"},
    "Uptimerobot": {"error": r"page not found"},
}


def plus(string):
    print("{0}[ + ]{1} {2}".format(g, e, string))


def warn(string, exit=not 1):
    print("{0}[ ! ]{1} {2}".format(r, e, string))
    if exit:
        sys.exit()


def info(string):
    print("{0}[ i ]{1} {2}".format(y, e, string))


def _info():
    return "{0}[ i ]{1} ".format(y, e)


def err(string):
    print(r"  |= [REGEX]: {0}{1}{2}".format(y_, string, e))


def request(domain, proxy, timeout, user_agent):
    url = checkurl(domain)
    timeout = timeout
    proxies = {"http": proxy, "https": proxy}
    redirect = True
    headers = {"User-Agent": user_agent}
    try:
        req = requests.packages.urllib3.disable_warnings(
            urllib3.exceptions.InsecureRequestWarning
        )
        req = requests.get(
            url=url,
            headers=headers,
            verify=False,
            allow_redirects=redirect,
            timeout=int(timeout) if timeout is not None else None,
            proxies=proxies if proxy is not None else None,
        )
        return req.status_code, req.content
    except requests.exceptions.ProxyError:
        warn("Invalid Proxy (Format: http(s)://host:port)")
    except requests.exceptions.ReadTimeout:
        info("Read Timeout!")
    except requests.exceptions.ConnectionError:
        info("Connection Error!")
    except Exception:
        pass


def _print(a, l, _d_):
    plus("{0} ({1:.2f}%)".format(_d_, PERCENT(int(a), int(l))))


def match(error, data):
    rgx = re.compile(r"{0}".format(error))
    try:
        return rgx.search(data.decode("utf-8", "ignore").strip())
    except Exception:
        pass


def check(_url):
    try:
        if "http://" not in _url and "https://" not in _url:
            _url = "http://{0}".format(_url)
        domain = urllib.parse.urlparse(_url).netloc
        return domain
    except Exception:
        pass


def checkurl(_url):
    try:
        if "http://" not in _url and "https://" not in _url:
            _url = "http://{0}".format(_url)
        return _url
    except Exception:
        pass


def output(data, filename, mode):
    global _output
    if filename is not None:
        with open(filename, mode) as file:
            file.write("{0}\n".format(data))
    else:
        _output.append(data)


def runner(index, domains, proxy, timeout, user_agent, process):
    for _d_ in domains:
        if process:
            _print(index[0], index[1], _d_)
        try:
            status_code, content = request(_d_, proxy, timeout, user_agent)
            if status_code != 200:
                pass
            else:
                for service, values in services.items():
                    _ = match(values["error"], content)
                    if _:
                        output("[TAKEOVER]: {0} -> {1}".format(_d_, service), k_["output"], "a")
                        if k_["verbose"]:
                            err(_.group(0))
        except Exception:
            pass
        index[0] += 1


def f_thread(domains, threads, proxy, timeout, user_agent, process):
    split = lambda lst, sz: [lst[i : i + sz] for i in range(0, len(lst), sz)]
    index = [1, len(domains)]
    domains = split(domains, int(len(domains) / int(threads)))
    with thread.ThreadPoolExecutor(max_workers=int(threads)) as executor:
        future_to = {
            executor.submit(
                runner, index, domains[index], proxy, timeout, user_agent, process
            ): index
            for index in range(len(domains))
        }
        for future in thread.as_completed(future_to):
            future_to[future]
            try:
                future.result()
            except Exception:
                pass


def main():
    global k_
    if len(sys.argv) < 2:
        print(
            """{0}Subdomain Takeover{1}
Usage:
  python3 takeover.py [options]

Options:
  -d domain        Set domain to test
  -t threads       Set threads number (default: 1)
  -l file          Set list of domains
  -p proxy         Set proxy (example: http(s)://host:port)
  -o output        Set filename to save output
  -T timeout       Set request timeout
  -k               Enable percentage process
  -u user_agent    Set User-Agent (default: browser)
  -v               Set verbose mode
  -h               Show this help message
        """.format(
                y_, e
            )
        )
    try:
        opts, _ = getopt.getopt(
            sys.argv[1:],
            "d:t:l:p:o:T:k:u:vh",
            [
                "domain=",
                "threads=",
                "list=",
                "proxy=",
                "output=",
                "timeout=",
                "process",
                "user_agent=",
                "verbose",
            ],
        )
    except getopt.GetoptError as err:
        warn("{0}".format(err))
        sys.exit()
    for o, a in opts:
        if o in ("-d", "--domain"):
            k_["domain"] = a
        elif o in ("-t", "--threads"):
            k_["threads"] = a
        elif o in ("-l", "--list"):
            k_["d_list"] = a
        elif o in ("-p", "--proxy"):
            k_["proxy"] = a
        elif o in ("-o", "--output"):
            k_["output"] = a
        elif o in ("-T", "--timeout"):
            k_["timeout"] = a
        elif o in ("-k", "--process"):
            k_["process"] = True
        elif o in ("-u", "--user_agent"):
            k_["user_agent"] = a
        elif o in ("-v", "--verbose"):
            k_["verbose"] = True
        elif o in ("-h"):
            print(
                """{0}Subdomain Takeover{1}
Usage:
  python3 takeover.py [options]

Options:
  -d domain        Set domain to test
  -t threads       Set threads number (default: 1)
  -l file          Set list of domains
  -p proxy         Set proxy (example: http(s)://host:port)
  -o output        Set filename to save output
  -T timeout       Set request timeout
  -k               Enable percentage process
  -u user_agent    Set User-Agent (default: browser)
  -v               Set verbose mode
  -h               Show this help message
        """.format(
                    y_, e
                )
            )
            sys.exit()
    try:
        if k_["domain"] is not None and k_["d_list"] is None:
            domains = [k_["domain"]]
        elif k_["domain"] is None and k_["d_list"] is not None:
            domains = open(k_["d_list"], "r").read().strip().split("\n")
            k_["dict_len"] = len(domains)
        elif k_["domain"] is not None and k_["d_list"] is not None:
            domains = [k_["domain"]]
        else:
            warn("Set --domain or --list options!", exit=1)
        f_thread(
            domains,
            k_["threads"],
            k_["proxy"],
            k_["timeout"],
            k_["user_agent"],
            k_["process"],
        )
    except KeyboardInterrupt:
        warn("Stopped!", exit=1)
    except IOError:
        warn("File not found!")


if __name__ == "__main__":
    main()

