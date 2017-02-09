#!/bin/sh

JVM_OPTS="-Xmx128m -Xms128m"

# This should be changed if you use Play sessions
PLAY_SECRET=none

CONFIG="-Dplay.crypto.secret=$PLAY_SECRET -Dlagom.cluster.join-self=on"

DIR=$(dirname $0)

java -cp "$DIR/../lib/*" $JAVA_OPTS $CONFIG play.core.server.ProdServerStart
