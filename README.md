# Using ironSource.atom with logstash and Docker
[![License][license-image]]



### Using ironSource.atom with logstash

#### Tracking events
__Confuguring logstash__
Create logstash.conf file with following text
```html
input {
 file {
   codec => "json"
   path => "/log_source_folder/fileneme.log"
} 

}

output {

 http {
   url => "http://track.atom-data.io/"
   http_method => "post"
   mapping => {
      "table" => "atomsource.stream.adress"
      "data" => "%{message}"
      "auth" => "Your Auth Key"

}
#workers => 5
ssl_certificate_validation => false
}

}
```
### License
MIT

[license-image]: https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square
