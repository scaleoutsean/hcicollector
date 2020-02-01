# Dockerfile for Grafana container
#
# https://hub.docker.com/r/grafana/grafana/tags
FROM grafana/grafana:6.5.1
ENV GF_SECURITY_DISABLE_GRAVATAR=true
ENV GF_SERVER_PROTOCOL=http
# For HTTPS, change server protocol to HTTPS and ensure required certs are available
# COPY certs/ /etc/ssl/certs/
# ENV GF_SERVER_CERT_FILE = 
# ENV GF_SERVER_CERT_KEY = 
ENV GF_SERVER_HTTP_PORT=3000

RUN mkdir -p /var/lib/grafana/dashboards
COPY provisioning /etc/grafana/provisioning/
COPY dashboards /var/lib/grafana/dashboards/
