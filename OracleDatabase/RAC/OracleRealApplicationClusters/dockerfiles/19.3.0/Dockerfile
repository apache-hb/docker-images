# Copyright (c) 2022, Oracle and/or its affiliates
# Licensed under the Universal Permissive License v 1.0 as shown at https://oss.oracle.com/licenses/upl/
#
# ORACLE DOCKERFILES PROJECT
# --------------------------
# This is the Dockerfile for Oracle Database 19c Release 3 Real Application Clusters
#
# REQUIRED FILES TO BUILD THIS IMAGE
# ----------------------------------
# (1) LINUX.X64_193000_grid_home.zip
# (2 LINUX.X64_193000_db_home.zip
#     Download Oracle Grid 19c Release 3 Enterprise Edition for Linux x64
#     Download Oracle Database 19c Release 3  Enterprise Edition for Linux x64
#     from http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html
#
# HOW TO BUILD THIS IMAGE
# -----------------------
# Run:
#      $ docker build -t oracle/database:19.3.0-rac .
#
# Pull base image
# ---------------

# hadolint ignore=DL3006,DL3025

ARG BASE_OL_IMAGE=oraclelinux:7-slim

# Pull base image
# ---------------
# hadolint ignore=DL3006,DL3025
FROM $BASE_OL_IMAGE AS base
ARG SLIMMING=false
# Labels
# ------
LABEL "provider"="Oracle"                                               \
      "issues"="https://github.com/oracle/docker-images/issues"         \
      "volume.setup.location1"="/opt/scripts"              \
      "volume.startup.location1"="/opt/scripts/startup"          \
      "port.listener"="1521"                                            \
      "port.oemexpress"="5500"

# Argument to control removal of components not needed after db software installation
ARG INSTALL_FILE_1="LINUX.X64_193000_grid_home.zip"
ARG INSTALL_FILE_2="LINUX.X64_193000_db_home.zip"
ARG DB_EDITION="EE"
ARG USER="root"
ARG WORKDIR="/rac-work-dir"

# Environment variables required for this build (do NOT change)
# -------------------------------------------------------------
# Linux Env Variable
# hadolint ignore=DL3044
ENV SETUP_LINUX_FILE="setupLinuxEnv.sh" \
    INSTALL_DIR=/opt/scripts \
# Grid Env variables
    GRID_BASE=/u01/app/grid \
    GRID_HOME=/u01/app/19.3.0/grid \
    INSTALL_FILE_1=$INSTALL_FILE_1 \
    GRID_INSTALL_RSP="gridsetup_19c.rsp" \
    GRID_SW_INSTALL_RSP="grid_sw_install_19c.rsp" \
    GRID_SETUP_FILE="setupGrid.sh" \
    FIXUP_PREQ_FILE="fixupPreq.sh" \
    INSTALL_GRID_BINARIES_FILE="installGridBinaries.sh" \
    INSTALL_GRID_PATCH="applyGridPatch.sh" \
    INVENTORY=/u01/app/oraInventory \
    CONFIGGRID="configGrid.sh"  \
    ADDNODE="AddNode.sh"   \
    DELNODE="DelNode.sh" \
    INITSH="initsh" \
    ADDNODE_RSP=""grid_addnode.rsp""  \
    SETUPSSH="setupSSH.expect"  \
    DOCKERORACLEINIT="dockeroracleinit" \
    GRID_USER_HOME="/home/grid" \
    SETUPGRIDENV="setupGridEnv.sh" \
    ASM_DISCOVERY_DIR="/dev" \
    RESET_OS_PASSWORD="resetOSPassword.sh" \
    MULTI_NODE_INSTALL="MultiNodeInstall.py" \
# RAC DB Env Variables
    DB_BASE=/u01/app/oracle \
    DB_HOME=/u01/app/oracle/product/19.3.0/dbhome_1 \
    INSTALL_FILE_2="$INSTALL_FILE_2" \
    DB_INSTALL_RSP="db_sw_install_19c.rsp" \
    DBCA_RSP="dbca_19c.rsp" \
    DB_SETUP_FILE="setupDB.sh" \
    PWD_FILE="setPassword.sh" \
    RUN_FILE="runOracle.sh" \
    STOP_FILE="stopOracle.sh" \
    ENABLE_RAC_FILE="enableRAC.sh" \
    CHECK_DB_FILE="checkDBStatus.sh" \
    USER_SCRIPTS_FILE="runUserScripts.sh" \
    REMOTE_LISTENER_FILE="remoteListener.sh" \
    INSTALL_DB_BINARIES_FILE="installDBBinaries.sh" \
    GRID_HOME_CLEANUP="GridHomeCleanup.sh" \
    ORACLE_HOME_CLEANUP="OracleHomeCleanup.sh" \
    DB_USER="oracle" \
    GRID_USER="grid" \
    WORKDIR=$WORKDIR \
    USER=$USER \
    SLIMMING=$SLIMMING \
# COMMON ENV Variable
   FUNCTIONS="functions.sh" \
   COMMON_SCRIPTS="/common_scripts" \
   CHECK_SPACE_FILE="checkSpace.sh" \
   RESET_FAILED_UNITS="resetFailedUnits.sh" \
   SET_CRONTAB="setCrontab.sh" \
   CRONTAB_ENTRY="crontabEntry" \
   EXPECT="/usr/bin/expect" \
   BIN="/usr/sbin" \
   container="true"

# Use second ENV so that variable get substituted
ENV  INSTALL_SCRIPTS=$INSTALL_DIR/install \
     PATH=/bin:/usr/bin:/sbin:/usr/sbin \
     SCRIPT_DIR=$INSTALL_DIR/startup \
     GRID_PATH=$GRID_HOME/bin:$GRID_HOME/OPatch/:$GRID_HOME/perl/bin:/usr/sbin:/bin:/sbin  \
     DB_PATH=$DB_HOME/bin:$DB_HOME/OPatch/:$DB_HOME/perl/bin:/usr/sbin:/bin:/sbin \
     GRID_LD_LIBRARY_PATH=$GRID_HOME/lib:/usr/lib:/lib \
     DB_LD_LIBRARY_PATH=$DB_HOME/lib:/usr/lib:/lib

#############################################
# -------------------------------------------
# Start new stage for installing the grid and DB for slim image
# -------------------------------------------
#############################################
FROM base AS rac-image-slim-false
ARG SLIMMING

COPY $SETUP_LINUX_FILE $GRID_SETUP_FILE $DB_SETUP_FILE $CHECK_SPACE_FILE $INSTALL_SCRIPTS/

RUN chmod 755 "$INSTALL_SCRIPTS"/*.sh  && \
    sync && \
    $INSTALL_DIR/install/$CHECK_SPACE_FILE && \
    $INSTALL_DIR/install/$SETUP_LINUX_FILE && \
    $INSTALL_DIR/install/$GRID_SETUP_FILE && \
    $INSTALL_DIR/install/$DB_SETUP_FILE && \
    rm -f "$INSTALL_DIR"/install/* && \
    sync

#############################################
# -------------------------------------------
# Start new stage for installing the grid and DB
# -------------------------------------------
#############################################
FROM base AS rac-image-slim-true
ARG SLIMMING

COPY $SETUP_LINUX_FILE $GRID_SETUP_FILE $DB_SETUP_FILE $CHECK_SPACE_FILE $INSTALL_SCRIPTS/

RUN chmod 755 "$INSTALL_SCRIPTS"/*.sh  && \
    sync && \
    $INSTALL_DIR/install/$CHECK_SPACE_FILE && \
    $INSTALL_DIR/install/$SETUP_LINUX_FILE && \
    $INSTALL_DIR/install/$GRID_SETUP_FILE && \
    $INSTALL_DIR/install/$DB_SETUP_FILE && \
    rm -f "$INSTALL_DIR"/install/* && \
    sync

#############################################
# -------------------------------------------
# Start new stage for installing the grid and DB
# -------------------------------------------
#############################################
# hadolint ignore=DL3006
FROM rac-image-slim-false AS builder
ARG SLIMMING

COPY $GRID_SW_INSTALL_RSP $INSTALL_GRID_PATCH $GRID_HOME_CLEANUP $ORACLE_HOME_CLEANUP $DB_INSTALL_RSP $INSTALL_DB_BINARIES_FILE $ENABLE_RAC_FILE $INSTALL_GRID_BINARIES_FILE $INSTALL_FILE_1* $INSTALL_FILE_2* $INSTALL_SCRIPTS/

RUN chmod 755 "${INSTALL_SCRIPTS}"/*.sh

## Install software if SLIMMING is false
# hadolint ignore=SC2086
RUN if [ "${SLIMMING}"x != "truex" ]; then \
      sed -e '/hard *memlock/s/^/#/g' -i /etc/security/limits.d/oracle-database-preinstall-19c.conf && \
      sed -e '/ *nofile /s/^/#/g' -i /etc/security/limits.d/oracle-database-preinstall-19c.conf && \
      su  $GRID_USER -c "$INSTALL_DIR/install/$INSTALL_GRID_BINARIES_FILE EE $PATCH_NUMBER" && \
      $INVENTORY/orainstRoot.sh && \
      $GRID_HOME/root.sh && \
      su  $DB_USER  -c  "$INSTALL_DIR/install/$INSTALL_DB_BINARIES_FILE EE" && \
      su  $DB_USER  -c  "$INSTALL_DIR/install/$ENABLE_RAC_FILE" && \
      $INVENTORY/orainstRoot.sh && \
      $DB_HOME/root.sh && \
      su  $GRID_USER -c "$INSTALL_SCRIPTS/$GRID_HOME_CLEANUP" && \
      su  $DB_USER -c "$INSTALL_SCRIPTS/$ORACLE_HOME_CLEANUP" && \
      :; \
    fi

RUN rm -f "${INSTALL_DIR}"/install/* && \
    sync
#############################################
FROM rac-image-slim-true AS builder-slim
ARG SLIMMING

COPY $FIXUP_PREQ_FILE $GRID_SW_INSTALL_RSP $INSTALL_GRID_PATCH $GRID_HOME_CLEANUP $ORACLE_HOME_CLEANUP $DB_INSTALL_RSP $INSTALL_DB_BINARIES_FILE $ENABLE_RAC_FILE $INSTALL_GRID_BINARIES_FILE $INSTALL_FILE_1* $INSTALL_FILE_2* $INSTALL_SCRIPTS/

RUN chmod 755 "${INSTALL_SCRIPTS}"/*.sh

## Install software if SLIMMING is false
# hadolint ignore=SC2086
RUN if [ "${SLIMMING}x" != 'truex' ]; then \
      sed -e '/hard *memlock/s/^/#/g' -i /etc/security/limits.d/oracle-database-preinstall-19c.conf && \
      sed -e '/ *nofile /s/^/#/g' -i /etc/security/limits.d/oracle-database-preinstall-19c.conf && \
      su  $GRID_USER -c "$INSTALL_DIR/install/$INSTALL_GRID_BINARIES_FILE EE $PATCH_NUMBER" && \
      $INVENTORY/orainstRoot.sh && \
      $GRID_HOME/root.sh && \
      su  $DB_USER  -c  "$INSTALL_DIR/install/$INSTALL_DB_BINARIES_FILE EE" && \
      su  $DB_USER  -c  "$INSTALL_DIR/install/$ENABLE_RAC_FILE" && \
      $INVENTORY/orainstRoot.sh && \
      $DB_HOME/root.sh && \
      su  $GRID_USER -c "$INSTALL_SCRIPTS/$GRID_HOME_CLEANUP" && \
      su  $DB_USER -c "$INSTALL_SCRIPTS/$ORACLE_HOME_CLEANUP" && \
      :; \
    fi

RUN rm -f "$INSTALL_DIR"/install/* && \
    sync
#############################################
# -------------------------------------------
# Start new image for grid & database runtime
# -------------------------------------------
#############################################
# hadolint ignore=DL3006
FROM rac-image-slim-false AS final

# Setup Scripts
COPY $FIXUP_PREQ_FILE $INSTALL_SCRIPTS/

COPY $RUN_FILE $INITSH $ADDNODE $ADDNODE_RSP $SETUPSSH $FUNCTIONS $CONFIGGRID $GRID_INSTALL_RSP $DBCA_RSP $PWD_FILE $CHECK_DB_FILE $USER_SCRIPTS_FILE $STOP_FILE $CHECK_DB_FILE $REMOTE_LISTENER_FILE $SETUPGRIDENV $DELNODE $RESET_FAILED_UNITS $RESET_OS_PASSWORD $MULTI_NODE_INSTALL $SET_CRONTAB $SCRIPT_DIR/

COPY --from=builder /u01 /u01

RUN if [ "${SLIMMING}x" != 'truex' ]; then \
       "$INVENTORY"/orainstRoot.sh && \
       "$GRID_HOME"/root.sh && \
       "$DB_HOME"/root.sh && \
       chmod 666 "${SCRIPT_DIR}"/*.rsp && \
       :; \
    fi && \
    chmod 755 $INSTALL_DIR/install/$FIXUP_PREQ_FILE && \
    "$INSTALL_DIR"/install/"$FIXUP_PREQ_FILE" && \
    sync && \
    chmod 755 "$SCRIPT_DIR"/*.sh && \
    chmod 666 "$SCRIPT_DIR"/*.rsp && \
    echo "nohup $SCRIPT_DIR/runOracle.sh &" >> /etc/rc.local && \
    rm -f /etc/rc.d/init.d/oracle-database-preinstall-19c-firstboot && \
    chmod +x /etc/rc.d/rc.local  && \
    cp "$SCRIPT_DIR"/"$INITSH" /usr/bin/"$INITSH" && \
    chmod 755 /usr/bin/"$INITSH" && \
    rm -f /etc/sysctl.d/99-oracle-database-preinstall-19c-sysctl.conf && \
    rm -f /etc/sysctl.d/99-sysctl.conf && \
    rm -f "$INSTALL_DIR"/install/* && \
    sync

USER ${USER}
VOLUME ["/common_scripts"]
WORKDIR $WORKDIR

# Define default command to start Oracle Grid and RAC Database setup.
# hadolint ignore=DL3006,DL3025
ENTRYPOINT "/usr/bin/$INITSH"
##########################################
FROM rac-image-slim-true AS final-slim

# Setup Scripts
COPY $FIXUP_PREQ_FILE $INSTALL_SCRIPTS/

COPY $RUN_FILE $INITSH $ADDNODE $ADDNODE_RSP $SETUPSSH $FUNCTIONS $CONFIGGRID $GRID_INSTALL_RSP $DBCA_RSP $PWD_FILE $CHECK_DB_FILE $USER_SCRIPTS_FILE $STOP_FILE $CHECK_DB_FILE $REMOTE_LISTENER_FILE $SETUPGRIDENV $DELNODE $RESET_FAILED_UNITS $RESET_OS_PASSWORD $MULTI_NODE_INSTALL $SET_CRONTAB $SCRIPT_DIR/

COPY --from=builder /u01 /u01

# hadolint ignore=SC2086
RUN if [ "${SLIMMING}x" != 'truex' ]; then \
       $INVENTORY/orainstRoot.sh && \
       $GRID_HOME/root.sh && \
       $DB_HOME/root.sh && \
       :; \
    fi && \
    chmod 755 $INSTALL_DIR/install/$FIXUP_PREQ_FILE && \
    $INSTALL_DIR/install/$FIXUP_PREQ_FILE && \
    sync && \
    chmod 755 $SCRIPT_DIR/*.sh && \
    chmod 666 $SCRIPT_DIR/*.rsp && \
    echo "nohup $SCRIPT_DIR/runOracle.sh &" >> /etc/rc.local && \
    rm -f /etc/rc.d/init.d/oracle-database-preinstall-19c-firstboot && \
    chmod +x /etc/rc.d/rc.local  && \
    cp $SCRIPT_DIR/$INITSH /usr/bin/$INITSH && \
    chmod 755 /usr/bin/$INITSH && \
    rm -f /etc/sysctl.d/99-oracle-database-preinstall-19c-sysctl.conf && \
    rm -f /etc/sysctl.d/99-sysctl.conf && \
    rm -f $INSTALL_DIR/install/* && \
    sync

USER ${USER}
VOLUME ["/common_scripts"]
WORKDIR $WORKDIR

# Define default command to start Oracle Grid and RAC Database setup.
# hadolint ignore=DL3025
ENTRYPOINT /usr/bin/$INITSH
