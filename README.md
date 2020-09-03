# Unimap
Scan only once by IP address and reduce scan times with Nmap for large amounts of data. Unimap is an abbreviation of "Unique Nmap *Scan*".

# Why?
If you have plans to run an Nmap to a whole organization you need to consideer that surely tens, hundreds or even thousands of subdomains will point to the same IP address and there will come a point where it becomes almost impossible to continue scanning. Also your IP may end up blocked due to multiple scans to the same remote host address among other things.

# How?
Unimap uses its own technology to initially resolve the IP addresses of all subdomains, once this process is finished, it creates a vector with the unique IP addresses and launches a parallel scan with Nmap according to the number of threads that the user has configured, at the same time, it analyzes the data from the files created with Nmap to find out which information corresponds to each IP. Finally, Unimap relates the information of each of the IPs associated with the subdomains. So, for example, if you have 50 subdomains that point to the same IP, you will only do one Nmap scan but you will have all the data associated with each of the subdomains at the same time, in a large scan it saves days or weeks.

# Installation

You need to have Rust installed, then run:

```
$ git clone https://github.com/Edu4rdSHL/unimap.git && cd unimap
$ cargo build --release
$ sudo ./target/release/unimap
```

# Usage

**Unimap requires root permissions to launch [Nmap TCP SYN (Stealth) Scan](https://nmap.org/book/synscan.html), we use it for accuracy and performance reasons.** See the [nmap.rs](https://github.com/Edu4rdSHL/unimap/blob/43b878e0d1ae74dc44d0ec51ce16a23fc6896a61/src/nmap.rs#L166) module.

* Flags doesn't require/accept values.

|Flag|Description|
|-----------|------------|
|--fast-scan|Use fast scanning for ports (no version detection) and just scan the top 1000 ports.|
|-o, --output| Write to an output file. The name of the output file will be unimap-log-$date.|

* Options require values.

|Option|Description|
|-----------|------------|
|--resolvers|Path to a file (or files) containing a list of DNS IP address. If no specified then 1.6k of built-in valid DNS servers from public-dns.info are used.|
|-f, --file|Use a list of targets writen in a file as input.|
|--iport|Initial port if you want to scan a custom range.|
|--lport|Last port if you want to scan a custom range.|
|--logs-dir|Path to save the CSV data of the process. Usefull when doing automated scans with the -o option.|
|--min-rate| Controls [Nmap's --min-rate](https://nmap.org/book/man-performance.html). Default to 30000.|
|-t, --target|Host if you want to scan only one and extract the interesting data.|
|--threads|Number of threads. **Have in mind that the same number of Nmap scans will be started at time.** Default to 30.|
|-u, --unique-output|Write output to an specified filename.|

# Examples

1. `$ sudo unimap -f targets.txt -u log.csv` performs an full scan and write output to log.csv.
2. `$ sudo unimap -f targets.txt --fast-scan -o` performs an fast scan and save the logfile to logs/ folder.
3. `$ sudo unimap -f targets.txt --iport 1 --lport 1000 --min-rate 1000` scan ports from 1-1000 doing service and versions detection (if you want a fast scan use the --fast-scan flag) with an min-rate of 1000.

# Found a bug?
Open an [issue](https://github.com/Edu4rdSHL/unimap).
