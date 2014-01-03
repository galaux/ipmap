ipmap
=====

Extract IP from Unix log files and display them on a Google Map instance. This can be usefull to display a map of IP addresses from which a Unix box is used along with several info such as the login used of whatever one could extract from a given log file.

Files
=====
- `ipmapgen:` Python 3 script used to parse a given log file and extract info such as an IP address (compulsory) and any other info that can be identified by a regular expression. Extracted info is group by IP addresses that are then checked against an online GeoIp service (Default service can be overriden). Result is then stored as JSON (along with extracted remaining info if set by user). This script should be copied to `/usr/local/bin` or any other directory but preferably in `$PATH` and should be given execution rights.
- `cron_ipmapgen`: Example cron script running the previous script and fixing read rights in case the HTTP server used to display the result is run with a different user. Needs to know the path of the previous script and the path of a JSON output file.
- `index.html`: HTML/Javascript served by an HTTP server. Needs to know the path of the local JSON output file.

Usage
=====
    usage: ipmapgen [-h] -I infile -O outfile -r regexp [-c count] [-i pos]
                    [-u geoip_url]

    Generate a JSON file of geographical coordinates based on a log file

    optional arguments:
      -h, --help            show this help message and exit
      -I infile, --in infile
                            Input log file to parse
      -O outfile, --out outfile
                            Output file to store generated JSON (overwritten)
      -r regexp, --regexp regexp
                            A Python 3 valid regexp holding at least one capture
                            group for an IP address. If more than one capture
                            group are provided, then you must specify '-c' and
                            '-i'. Supplementary capture groups will be use to
                            group matching IP addresses
      -c count, --capture-count count
                            Count of capture group in the provided 'regexp'
      -i pos, --ip-capture-pos pos
                            Position of the IP address capture group in 'regexp'
      -u geoip_url, --url geoip_url
                            Override default URL of the GeoIP service to use. Use
                            '%' to specify IP position

Example `ipmapgen`
==================
    ipmapgen --in  /var/log/auth.log \
             --out /tmp/geoip.json \
             --regexp ' Accepted [a-zA-Z]+ for ([a-zA-Z]+) from ([0-9\.]+)' \
             --capture-count  2 \     # 'There are 2 capture groups in the provided regexp'
             --ip-capture-pos 2       # 'Among capture groups, the second points at the IP address'
