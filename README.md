# Using ironSource.atom with Logstash & Docker
[![License][license-image]][license-url]

atom-logstash the way to connect Logstash with [ironSource.atom](http://www.ironsrc.com/data-flow-management) standing alone and with docker container.

- [Signup](https://atom.ironsrc.com/#/signup)
- [Documentation](https://github.com/ironSource/atom-logstash)

### Send an event to ironSource.Atom using Logstash

__1. Create a file named logstash.conf with following configuration:__
```html
# An input plugin enables a specific source of events to be read by Logstash.
input {

    # this example contains <file> as input, but logstash supports more:
    # https://www.elastic.co/guide/en/logstash/current/input-plugins.html

    file {
        # default codec is plain, for different codecs, read this:
        # https://www.elastic.co/guide/en/logstash/current/codec-plugins.html
        
        # path can be an array or string and can contain asterisk, like this:
        path => [ "/var/log/log-from-host/", "/var/log/*.log" ]
        path => "/data/mysql/mysql.log"
    }
}

# output plugin
output {
    http {
        url => "http://track.atom-data.io/"
        headers => {
            "x-ironsource-atom-sdk-type" => "logstash"
            "x-ironsource-atom-sdk-version" => "${SDK_VERSION:1.0.0}"
        }
        http_method => "post"
        format => "json"
        mapping => {
            "table" => "${STREAM}" # STREAM var is passed by the docker ENV
            "data" => "%{message}" # The msg that will be sent
        }
        workers => 5
        ssl_certificate_validation => false
    }

}
```

__2. Run logstash__
```bash
STREAM=<the name of your stream> logstash --allow-env -f logstash.conf
```

### Send events to ironSource.Atom using Logstash docker container & docker-compose

__1. Create docker-compose.yaml with the following content:__
```yaml
version: '2'
services:
  logstash:
    image: logstash:2.3
    command: bash -c "logstash --allow-env -f /etc/logstash/conf.d/logstash.conf"
    environment:
     STREAM: ${STREAM} # Your Atom Stream
     SDK_VERSION: 1.0.0
    volumes:
    ### Specify the logstash.conf file path on your host ###
    - <path to your logstash.conf file on your host>:/etc/logstash/conf.d/logstash.conf

    ### Specify the path of the files that you want logstash to use, can have multiple paths ###
    - /var/log/app-logs:/var/log/log-from-host
    # For more options: https://docs.docker.com/compose/compose-file/#volumes-volume-driver
    
    container_name: logstash-atom
```

__2. Run docker-compose:__
```bash
STREAM=<the name of your stream> docker-compose up (-d for detached)

OR

export STREAM=<the name of your stream>
docker-compose up -d
```
### Example

You can use our [example][example-url] for sending data to Atom.

### License
MIT

[license-image]: https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square
[license-url]: LICENSE
[example-url]: https://github.com/ironSource/atom-logstash/tree/master/example