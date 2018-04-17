#### mapping ip
[ip](https://www.elastic.co/guide/en/elasticsearch/reference/current/ip.html)

[geo-ip](https://www.elastic.co/guide/en/elasticsearch/reference/current/geo-point.html)



filter {
    geoip {
        source => "clientip"
        target => "geoip"
        database => "/test/logstash-5.0.0/GeoLite2-City.mmdb"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
    }

    mutate {
        convert => [ "[geoip][coordinates]", "float"]
          }
    }
}
