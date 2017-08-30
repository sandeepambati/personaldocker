FROM openjdk:8

RUN echo $JAVA_HOME
RUN java -version

RUN set -x \
    && apt-get update --quiet \
    && apt-get install --quiet --yes --no-install-recommends libtcnative-1 xmlstarlet sudo \
    && apt-get clean
