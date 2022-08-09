FROM registry.access.redhat.com/ubi8/ubi-minimal
USER root
ADD run.sh /
#COPY user-seed.conf /tmp
COPY inputs.conf /tmp
COPY outputs.conf /tmp
#COPY server.conf /tmp

COPY splunkforwarder-8.2.4-87e2dda940d1-linux-2.6-x86_64.rpm /tmp
RUN microdnf --setopt=install_weak_deps=0 --setopt=tsflags=nodocs install -y ca-certificates libaio \
    && microdnf update \
	&& microdnf install -y libsemanage shadow-utils findutils procps wget \
	&& microdnf reinstall tzdata -y \
	&& microdnf clean all \
    && rpm -i /tmp/splunkforwarder-8.2.4-87e2dda940d1-linux-2.6-x86_64.rpm \
    && rm /tmp/splunkforwarder-8.2.4-87e2dda940d1-linux-2.6-x86_64.rpm \
	&& ln -fs /usr/share/zoneinfo/America/New_York /etc/localtime \
    && mkdir -p /host && chown splunk:splunk /host \
    && mkdir -p /opt/splunkforwarder/var/{lib,run,spool}/splunk \
    && mkdir -p /opt/splunkforwarder/var/lib/splunk/{authDb,hashDb} \
    && mkdir -p /opt/splunkforwarder/var/run/splunk/appserver/{i18n,modules/static/css} \
    && mkdir -p /opt/splunkforwarder/var/run/splunk/{upload,search_telemetry} \
    && mkdir -p /opt/splunkforwarder/var/spool/dirmoncache \
    && mkdir -p /opt/splunkforwarder/etc/apps/osd_monitored_logs/{local,metadata} \
    && mkdir -p /opt/splunkforwarder/etc/apps/splunkauth/{default,local,metadata} \
    && chown -R splunk:splunk /opt/splunkforwarder \
    && chown -R splunk:splunk /run.sh \
    && chmod +x /run.sh \
	#&& chown -R splunk:splunk $SPLUNK_HOME \
    && sed -i 's/\.maxBackupIndex=5/\.maxBackupIndex=1/g' /opt/splunkforwarder/etc/log.cfg \
    && sed -i 's/\.maxFileSize=25000000/\.maxFileSize=250000/g' /opt/splunkforwarder/etc/log.cfg

#chmod +x /splunkforwarder/bin/scripts/*.sh \
# && groupadd -r splunk \
#    && useradd -r -m -g splunk splunk \	
#RUN echo "%sudo ALL=NOPASSWD:ALL" >> /etc/sudoers \
#    && chown -R splunk:splunk $SPLUNK_HOME 

RUN cd /opt/splunkforwarder/bin \
	&& ./splunk start --no-prompt --accept-license --login -auth deloitte:deloitte \
	&& ./splunk add user deloitte -role Admin -password deloitte -full-name dc 

#RUN cp /tmp/* /opt/splunkforwarder/etc/system/local/

RUN cd /opt/splunkforwarder/bin \
#	&& ./splunk login -auth deloitte:deloitte \
#	&& ./splunk search 'index=_internal | fields _time | head 1 ' -auth 'admin:password' \
	&& cp /tmp/inputs.conf /opt/splunkforwarder/etc/system/local \
	&& cp /tmp/outputs.conf /opt/splunkforwarder/etc/system/local \
#	&& ./splunk add forward-server 10.231.34.255:8089 \ 
#	&& cp /tmp/user-seed.conf etc/system/default/user-seed.conf \
#	&& cp /tmp/outputs.conf /opt/splunkforwarder/etc/system/local \
	&& mkdir -p /opt/splunkforwarder/logs \
#	&& ./splunk add monitor /opt/splunkforwarder/logs \
#	&& ./splunk start
	

#USER splunk
WORKDIR /opt/splunkforwarder
ENTRYPOINT ["/run.sh"]
#CMD [ "/run.sh" ]