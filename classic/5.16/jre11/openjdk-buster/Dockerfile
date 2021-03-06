FROM openjdk:11-jre-buster

RUN mkdir /usr/local/activemq

ENV PATH /usr/local/activemq/bin:$PATH

WORKDIR /usr/local/activemq

ENV ACTIVEMQ_MAJOR 5.16
ENV ACTIVEMQ_VERSION 5.16.1
ENV ACTIVEMQ_SHA512 f98c9da5dafa2dc291070fecd57b7fccd0954cc8b2be09abc77ea66745b89283358297f308ec30dd47efd3e0c9d4cffc3821ef429f8f94d6c8971bbe933809b2

RUN set -eux; \
  \
  savedAptMark="$(apt-mark showmanual)"; \
  apt-get update; \
  apt-get install -y --no-install-recommends \
    gnupg dirmngr wget \
  ; \
  \
  ddist() { \
    local f="$1"; shift; \
    local distFile="$1"; shift; \
    local mvnFile="${1:-}"; \
    local success=; \
    local distUrl=; \
    for distUrl in \
      "https://www.apache.org/dyn/closer.cgi?action=download&filename=$distFile" \
# if the version is outdated (or we're grabbing the .asc file), we might have to pull from the dist/archive :/
      "https://www-us.apache.org/dist/$distFile" \
      "https://www.apache.org/dist/$distFile" \
      "https://archive.apache.org/dist/$distFile" \
# if all else fails, let's try Maven html; https://mvnrepository.com/artifact/org.apache.activemq/activemq; https://repo1.maven.org/maven2/org/apache/activemq/activemq/)
# maven does not work, comepletely different SHA and file sizes, will investigate
#      ${mvnFile:+"https://repo1.maven.org/maven2/org/apache/activemq/apache-activemq/$mvnFile"} \
    ; do \
      if wget -O "$f" "$distUrl" --progress=dot:giga && [ -s "$f" ]; then \
        success=1; \
        break; \
      fi; \
    done; \
    [ -n "$success" ]; \
  }; \
  \
  ddist 'activemq.tar.gz' "activemq/$ACTIVEMQ_VERSION/apache-activemq-$ACTIVEMQ_VERSION-bin.tar.gz" "$ACTIVEMQ_VERSION/apache-activemq-$ACTIVEMQ_VERSION-bin.tar.gz"; \
  echo "$ACTIVEMQ_SHA512 *activemq.tar.gz" | sha512sum --strict --check -; \
  ddist 'activemq.tar.gz.asc' "activemq/$ACTIVEMQ_VERSION/apache-activemq-$ACTIVEMQ_VERSION-bin.tar.gz.asc" "$ACTIVEMQ_VERSION/apache-activemq-$ACTIVEMQ_VERSION.tar.gz.asc"; \
  export GNUPGHOME="$(mktemp -d)"; \
  wget -O KEYS http://www.apache.org/dist/activemq/KEYS --progress=dot:giga; \
  gpg --import KEYS; \
  gpg --verify activemq.tar.gz.asc activemq.tar.gz; \
  tar -xf activemq.tar.gz --strip-components=1; \
  rm -r activemq.tar.gz* KEYS /tmp/tmp*; \
  sed -i 's/127.0.0.1/0.0.0.0/' conf/jetty.xml

EXPOSE 8101 1099 44444 8181 8161

CMD ["./bin/activemq", "console"]
