FROM openjdk:11-jre-buster

RUN mkdir /usr/local/activemq

WORKDIR /usr/local/activemq

ENV ACTIVEMQ_MAJOR 5.16
ENV ACTIVEMQ_VERSION 5.16.1

RUN set -eux; \
  \
  savedAptMark="$(apt-mark showmanual)"; \
  apt-get update; \
  apt-get install -y --no-install-recommends \
    gnupg dirmngr \
    wget ca-certificates \
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
      ${mvnFile:+"https://repo1.maven.org/maven2/org/apache/activemq/activemq/$mvnFile"} \
    ; do \
      if wget -O "$f" "$distUrl" --progress=dot:giga && [ -s "$f" ]; then \
        success=1; \
        break; \
      fi; \
    done; \
    [ -n "$success" ]; \
  }; \
  \
  ddist 'activemq.tar.gz' "activemq/$ACTIVEMQ_VERSION/apache-activemq-$ACTIVEMQ_VERSION-bin.tar.gz" "$ACTIVEMQ_VERSION/activemq-$ACTIVEMQ_VERSION-bin.tar.gz"; \
  tar -xf activemq.tar.gz; \
  rm activemq.tar.gz*; \
  mv apache-activemq-$ACTIVEMQ_VERSION/* .; \
  rmdir apache-activemq-$ACTIVEMQ_VERSION; \
  cd bin

EXPOSE 8101 1099 44444 8181

#CMD ["activemq start", "run"]
