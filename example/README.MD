### To run this example:
```bash
git clone https://github.com/ironSource/atom-logstash.git atom-logstash
cd atom-logstash/example
STREAM=YOUR_STREAM_NAME PWD=$(pwd) AUTH=YOUR_PRE_SHARED_AUTH_KEY logstash --verbose --allow-env -f logstash.conf
```