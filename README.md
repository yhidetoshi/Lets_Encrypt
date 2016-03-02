### CentOS6.7にLet’s Encryptを導入する手順

CentOS6だとpython2.6がデフォルトインストールされているため
python2.7以上をインストールする。

ptyhon2.7のインストール方法:https://github.com/yhidetoshi/python2.6_to_2.7

```
# cd /usr/local/src/
# git clone https://github.com/letsencrypt/letsencrypt.git


vim /usr/bin/virtualenv
#!/usr/bin/python
↓
#!/usr/bin/python2.6

# pip install virtualenv
# yum  -y install bzip2-devel 
# cd letsencrypt
# ./letsencrypt-auto --help
# ./letsencrypt-auto --renew certonly -a standalone --server https://acme-v01.api.letsencrypt.org/directory \
 --agree-dev-preview -d <domain_name>
 
/etc/letsencrypt/live/<domain_name>に証明書が生成される
→ cert.pem  chain.pem  fullchain.pem  privkey.pem

# service nginx stop
```

##### NginxのSSL化
nginxのconfファイルに下記を設定してSSL化
```
listen 443 ssl;
server_name <domain_name>;
ssl_certificate /etc/letsencrypt/live/<domain_name>/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/<domain_name>/privkey.pem;
```

設定したらnginxを起動する

Lets Encryptの証明書の有効期限は90日なので毎日1日午前1時に自動更新

crontab -l
```
00 01 01 * * /usr/bin/nginx restart && /usr/local/src/letsencrypt/letsencrypt-auto certonly -d <domain_name> -d www.<domain_name> --renew-by-default && /usr/bin/nginx restart
```

