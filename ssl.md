http://www.sslshopper.com/article-free-ssl-certificates-from-a-free-certificate-authority.html
[基于 OpenSSL 的 CA 建立及证书签发](http://rhythm-zju.blog.163.com/blog/static/310042008015115718637/)
[pound](http://www.apsis.ch/pound/)
The file should be in PEM format. The OpenSSL command to generate a self-signed certificate in the correct format would be something like:

        openssl req -x509 -newkey rsa:1024 -keyout test.pem -out test.pem -days 365 -nodes
Note the '-nodes' flag - it's important!
[Why no SSL](https://www.varnish-cache.org/docs/trunk/phk/ssl.html)

http://singlemindconsulting.com/blog/christoler/2010/8/setting-pressflow-and-varnish-work-http-and-https/
[Apache HTTPS笔记](http://www.berlinix.com/apache_https.html)

http://httpd.apache.org/docs/2.2/ssl/ssl_faq.html#selfcert

	You now have to send this Certificate Signing Request (CSR) to a Certifying Authority (CA) to be signed. Once the CSR has been signed, you will have a real Certificate, which can be used by Apache. You can have a CSR signed by a commercial CA, or you can create your own CA to sign it.
	Commercial CAs usually ask you to post the CSR into a web form, pay for the signing, and then send a signed Certificate, which you can store in a server.crt file. For more information about commercial CAs see the following locations:
