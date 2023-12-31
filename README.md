# .htaccess Security
Apache .htaccess file security config

## Contents

### No Directory Index
```apache
Options -Indexes
```

### .htaccess File Protection
```apache
<files ".htaccess">
    order allow,deny
    deny from all
</files>
```

### .htaccess File Strong Protection
```apache
<Files ~ "^.*\.([Hh][Tt][Aa])">
    order allow,deny
    deny from all
    satisfy all
</Files>
```

### LFI Bugs Patch
```apache
<IfModule mod_rewrite.c>
    RewriteCond %{QUERY_STRING} \=(\.\./\.\.//?)+ [OR]
    RewriteCond %{QUERY_STRING} \=(\.\.//\./?)+ [OR]
    RewriteCond %{QUERY_STRING} \=(\.\.\\\.\./?)+ [OR]
    RewriteCond %{QUERY_STRING} \=(\.\.\\\\\./?)+ [OR]
    RewriteCond %{QUERY_STRING} \/tmp\/sess_ [NC,OR]
    RewriteCond %{QUERY_STRING} php:\/\/filter\/read=convert\.base64-(en|de)code\/ [NC,OR]
</IfModule>
```

### XSS Bugs Patch
```apache
<IfModule mod_rewrite.c>
    RewriteCond %{QUERY_STRING} base64_encode.*\(.*\) [OR]
    RewriteCond %{QUERY_STRING} (\<|%3C).*script.*(\>|%3E) [NC,OR]
    RewriteCond %{QUERY_STRING} (\<|%3C).*iframe.*(\>|%3E) [NC,OR]
    RewriteCond %{QUERY_STRING} GLOBALS(=|\[|\%[0-9A-Z]{0,2}) [OR]
    RewriteCond %{QUERY_STRING} _REQUEST(=|\[|\%[0-9A-Z]{0,2})
    RewriteRule ^(.*)$ index_error.php [F,L]
    RewriteCond %{REQUEST_METHOD} ^(TRACE|TRACK)
    RewriteRule .* - [F]
</IfModule>
```

### SQL Bugs Patch
```apache
<IfModule mod_rewrite.c>
    RewriteCond %{QUERY_STRING} union([^a]*a)+ll([^s]*s)+elect [NC,OR]
    RewriteCond %{QUERY_STRING} (order).*(by).*(\%[0-9A-Z]{0,2}) [NC,OR]
    RewriteCond %{QUERY_STRING} (waitfor|delay|shutdown).*(nowait) [NC,OR]
    RewriteCond %{QUERY_STRING} (union|and|\^).*(select).*(ascii\(|bin\(|benchmark\(|cast\(|char\(|charset\(|collation\(|concat\(|concat_ws\(|table_schema) [NC,OR]
    RewriteCond %{QUERY_STRING} (union|and|\^).*(select).*(conv\(|convert\(|count\(|database\(|decode\(|diff\(|distinct\(|elt\(}encode\(|encrypt\(|extract\() [NC,OR]
    RewriteCond %{QUERY_STRING} (union|and|\^).*(select).*(field\(|floor\(|format\(|from|hex\(|if\(|in\(|information_schema|insert\(|instr\(|interval\(|lcase\() [NC,OR]
    RewriteCond %{QUERY_STRING} (union|and|\^).*(select).*(left\(|length\(|load_file\(|locate\(|lock\(|log\(|lower\(|lpad\(|ltrim\(|max\(|md5\(|mid\() [NC,OR]
    RewriteCond %{QUERY_STRING} (union|and|\^).*(select).*(mod\(|now\(|null\(|ord\(|password\(|position\(|quote\(|rand\(|repeat\(|replace\(|reverse\() [NC,OR]
    RewriteCond %{QUERY_STRING} (union|and|\^).*(select).*(right\(|rlike\(|row_count\(|rpad\(|rtrim\(|_set\(|schema\(|sha1\(|sha2\(|sleep\(|soundex\() [NC,OR]
    RewriteCond %{QUERY_STRING} (union|and|\^).*(select).*(space\(|strcmp\(|substr\(|substr_index\(|substring\(|sum\(|time\(|trim\(|truncate\(|ucase\() [NC,OR]
    RewriteCond %{QUERY_STRING} (union|and|\^).*(select).*(unhex\(|upper\(|_user\(|user\(|values\(|varchar\(|version\(|xor\() [NC,OR]
    RewriteCond %{QUERY_STRING} ^.*(;|<|>|’|”|\)|%0A|%0D|%22|%27|%3C|%3E|%00).*(/\*|union|select|insert|cast|set|declare|drop|update|md5|benchmark).* [NC,OR]
</IfModule>
```

### Anti Shell
```apache
<IfModule mod_rewrite.c>
    RewriteCond %{REQUEST_URI} .*((php|my)?shell|remview.*|phpremoteview.*|sshphp.*|pcom|nstview.*|c99|r57|b37|alfa|c100|x666|webadmin.*|phpget.*|phpwriter.*|fileditor.*|locus7.*|storm7.*)\.(p?s?x?htm?l?|txt|aspx?|cfml?|cgi|pl|php[3-9]{0,1}|jsp?|sql|xml) [NC,OR]
    RewriteCond %{REQUEST_METHOD} (GET|POST) [NC]
    RewriteCond %{QUERY_STRING} ^(.*)=/home(.+)?/(.*)/(.*)$ [OR]
    RewriteCond %{QUERY_STRING} ^work_dir=.*$ [OR]
    RewriteCond %{QUERY_STRING} ^command=.*&output.*$ [OR]
    RewriteCond %{QUERY_STRING} ^nts_[a-z0-9_]{0,10}=.*$ [OR]
    RewriteCond %{QUERY_STRING} ^c=(t|setup|codes)$ [OR]
    RewriteCond %{QUERY_STRING} ^act=((about|cmd|selfremove|chbd|trojan|backc|massbrowsersploit|exploits|grablogins|upload.*)|((chmod|f)&f=.*))$ [OR]
    RewriteCond %{QUERY_STRING} ^act=(ls|search|fsbuff|encoder|tools|processes|ftpquickbrute|security|sql|eval|update|feedback|cmd|gofile|mkfile)&d=.*$ [OR]
    RewriteCond %{QUERY_STRING} ^&?c=(l?v?i?&d=|v&fnot=|setup&ref=|l&r=|d&d=|tree&d|t&d=|e&d=|i&d=|codes|md5crack).*$ [OR]
    RewriteCond %{QUERY_STRING} ^(.*)([-_a-z]{1,15})=(ls|cd|cat|rm|mv|vim|chmod|chdir|mkdir|rmdir|pwd|clear|whoami|uname|tar|zip|unzip|tar|gzip|gunzip|grep|more|ln|umask|telnet|ssh|ftp|head|tail|which|mkmode|touch|logname|edit_file|search_text|find_text|php_eval|download_file|ftp_file_down|ftp_file_up|ftp_brute|mail_file|mysql|mysql_dump|db_query)([^a-zA-Z0-9].+)*$ [OR]
    RewriteCond %{QUERY_STRING} ^(.*)(wget|shell_exec|passthru|system|exec|popen|proc_open)(.*)$
</IfModule>
```

### Anti .pl .py .cgi
```apache
<IfModule mod_rewrite.c>
    RemoveHandler cgi-script .pl .py .cgi
    AddType text/plain .pl .py .cgi
</IfModule>
```

### Anti Scanner
```apache
RewriteEngine On
<IfModule mod_rewrite.c>
    RewriteCond %{HTTP_USER_AGENT} ^w3af.sourceforge.net [NC,OR]
    RewriteCond %{HTTP_USER_AGENT} dirbuster [NC,OR]
    RewriteCond %{HTTP_USER_AGENT} nikto [NC,OR]
    RewriteCond %{HTTP_USER_AGENT} SF [OR]
    RewriteCond %{HTTP_USER_AGENT} sqlmap [NC,OR]
    RewriteCond %{HTTP_USER_AGENT} fimap [NC,OR]
    RewriteCond %{HTTP_USER_AGENT} nessus [NC,OR]
    RewriteCond %{HTTP_USER_AGENT} whatweb [NC,OR]
    RewriteCond %{HTTP_USER_AGENT} Openvas [NC,OR]
    RewriteCond %{HTTP_USER_AGENT} jbrofuzz [NC,OR]
    RewriteCond %{HTTP_USER_AGENT} libwhisker [NC,OR]
    RewriteCond %{HTTP_USER_AGENT} webshag [NC,OR]
    RewriteCond %{HTTP:Acunetix-Product} ^WVS
    RewriteRule ^.* http://127.0.0.1/ [R=301,L]
</IfModule>
```

### Anti DDoS (CVE-2011-3192)
```apache
SetEnvIf Range (,.*?){5,} bad-range=1
RequestHeader unset Range env=bad-range
```

### Anti JavaScript
```apache
Header set X-Content-Security-Policy "allow 'self';"
```

## See also
- [6G Firewall](https://perishablepress.com/6g)

## Contribution
- [Discord](https://discord.gg/2JjvhAk)
