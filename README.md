libvmod_maxminddb
=================

Varnish 4 vmod for loading maxminddb (geoip2)

Requirement:
=================
Packages: build-essential libtool libvarnishapi-dev python-docutils libmaxminddb-dev libmaxminddb

Build:
=================
You need libmaxminddb in order to build this.

```
 ./autogen.sh
 ./configure  VMOD_DIR=/usr/lib/varnish/vmods/
 make
 make install
```

Usage:
=================
```
vcl 4.0;
import maxminddb;

backend default {
    .host = "127.0.0.1";
    .port = "8080";
}

sub vcl_init{
  maxminddb.init_db("/path/to/GeoLite2-City.mmdb");
}

sub vcl_recv {

  if(maxminddb.query_isIP6(req.http.X-Forwarded-For)){
        set req.http.client.ip = maxminddb.query_extractIP6(req.http.X-Forwarded-For);
  }
	

  set req.http.continentcode = maxminddb.query_continent(client.ip);
  set req.http.countrycode = maxminddb.query_country(client.ip);
  set req.http.state = maxminddb.query_state(client.ip);
  set req.http.city = maxminddb.query_city(client.ip);
  set req.http.postalcode = maxminddb.query_postalcode(client.ip);


  maxminddb.init_db("/path/to/GeoLite2-ASN.mmdb");
  set req.http.ASN = maxminddb.query_ASN(client.ip);

# If you have extracted the client IP from X-Forwarded-For(XFF), then you can pass 

		std.ip(req.http.client.ip,"0.0.0.0")  

  as the argument to above functions, where
	
  req.http.client.ip --> is the variable in which client IP is stored but as 
  			 String data type, since it is a part of XFF.
  	

}

```
