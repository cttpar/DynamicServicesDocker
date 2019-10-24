FROM debian:stretch-slim

MAINTAINER ramity "github.com/ramity"

# Update repos
RUN apt-get update

# Install chromium
RUN apt-get install -y chromium

CMD chromium --remote-debugging-address=0.0.0.0\
             --remote-debugging-port=6969\
             --no-sandbox\
             --headless\
             --window-size="1920,1080"\
             --disable-extensions\
             --disable-gpu
