### Using ironSource.atom with logstash

__Create file logstash.conf with following configurations:__
```html
input {
    file {
        path => "< absolute path to file with your input data >"
    }
}

filter{}

output {
    http {
        url => "http://track.atom-data.io/"
        http_method => "post"
        format => "json"
        mapping => {
            "table" => "< name of your table in atom cluster>"
            "data" => "%{message}"
            "auth" => "<THE SHA256 of the data using a pre-shared key>"
        }
        workers => 5
        ssl_certificate_validation => false
    }

}
```
* You should configure your logstash to produce  each %{message} as JSON object or array of JSON objects. 
* Each object has to represent your data structure the same as you have in your atom table.

__Copy your logstash.conf into working directory.__

__Go to working directory.__
```bash
cd <path to you directory>
```
__Run logstash__
```bash
logstash -f logstash.conf
```


### Connect to ironSource Atom with Logstash using docker-compose

__Create file logstash.conf with following configurations:__
```html
input {
    file {
        path => "</etc/logstash/file.txt>"
    }
}

filter{}

output {
    http {
        url => "http://track.atom-data.io/"
        http_method => "post"
        format => "json"
        mapping => {
            "table" => "< name of your table in atom cluster>"
            "data" => "%{message}"
            "auth" => "<THE SHA256 of the data using a pre-shared key>"
        }
        workers => 5
        ssl_certificate_validation => false
    }

}
```
* You should configure your logstash to produce  each %{message} as JSON object or array of JSON objects. 
* Each object has to represent your data structure the same as you have in your atom table.

__Create docker-compose.yaml with the following content:__
```html
version: '2'
services:
  logstash:
    image: logstash 2.3
    command: bash -c "logstash -f /etc/logstash/conf.d/logstash.conf"
    volumes: 
    - <absolute path to your log stash.conf file on your server>:/etc/logstash/conf.d/logstash.conf
    - < absolute path to file with your input data >:/etc/logstash/file.txt
    container_name: logstash-atom
```
__Put you docker-compose.yaml into your working directory__

__Go to working directory:__
```bash
cd <path to you directory>
```
__Run docker-compose:__
```bash
docker-compose up -d
```
