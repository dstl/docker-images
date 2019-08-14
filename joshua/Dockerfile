FROM ubuntu:16.04

# Update, patch and install dependencies
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get upgrade -f -y
RUN apt-get install -y git openjdk-8-jdk maven

# Build Joshua
RUN git clone https://github.com/apache/incubator-joshua /opt/bin/joshua
RUN export JOSHUA=/opt/bin/joshua && echo "export JOSHUA=$JOSHUA" >> ~/.bashrc

WORKDIR /opt/bin/joshua
RUN mvn package && bash download-deps.sh

# Set Entrypoint
ARG mem=8G
ENV javaHeap=${mem}

VOLUME ["/opt/bin/joshua/model"]
ENTRYPOINT java -Xmx${javaHeap} -Dfile.encoding=utf8 -Djava.library.path=./lib -cp ./target/joshua-*-jar-with-dependencies.jar org.apache.joshua.decoder.JoshuaDecoder -c /opt/bin/joshua/joshua.config -server-port 5674 -server-type http
EXPOSE 5674
