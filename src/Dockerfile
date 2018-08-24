#
# This is the NGINX Plus router for OpenShift Origin.
#
# The standard name for this image is openshift/origin-nginx-plus-router
#

FROM openshift/origin-cli:v3.11.0

# Download certificate and key from the customer portal (https://cs.nginx.com)
# and copy to the build context
RUN mkdir -p /etc/ssl/nginx/
COPY nginx-repo.crt /etc/ssl/nginx/
COPY nginx-repo.key /etc/ssl/nginx/

# Make sure the certificate and key have correct permissions
RUN chmod 644 /etc/ssl/nginx/*

# Configure NGINX Plus repo
RUN echo -e "[nginx]\nname=nginx plus repo" >> /etc/yum.repos.d/nginx-plus.repo && \
    echo "baseurl=https://plus-pkgs.nginx.com/centos/7.4/\$basearch/" >> /etc/yum.repos.d/nginx-plus.repo && \
    echo "sslclientcert=/etc/ssl/nginx/nginx-repo.crt" >> /etc/yum.repos.d/nginx-plus.repo && \
    echo "sslclientkey=/etc/ssl/nginx/nginx-repo.key" >> /etc/yum.repos.d/nginx-plus.repo && \
    echo -e "gpgcheck=0\nenabled=1" >> /etc/yum.repos.d/nginx-plus.repo

RUN yum install -y nginx-plus && \
    yum clean all && \
    mkdir -p /var/lib/nginx/router/{certs,cacerts} && \
    mkdir -p /var/lib/nginx/{conf,run,log,cache} && \
    touch /var/lib/nginx/conf/nginx.conf && \
    setcap 'cap_net_bind_service=ep' /usr/sbin/nginx && \
    chown -R :0 /var/lib/nginx && \
    chmod -R g+w /var/lib/nginx && \
    ln -sf /var/lib/nginx/log/error.log /var/log/nginx/error.log && \
    rm /etc/yum.repos.d/nginx-plus.repo && \
    rm -rf /etc/ssl/nginx

COPY . /var/lib/nginx/

LABEL io.k8s.display-name="OpenShift Origin NGINX Plus Router" \
      io.k8s.description="This is a component of OpenShift Origin and contains an NGINX Plus instance that automatically exposes services within the cluster through routes, and offers TLS termination, reencryption, or SNI-passthrough on ports 80 and 443."
USER 1001
EXPOSE 80 443
WORKDIR /var/lib/nginx/conf
ENV TEMPLATE_FILE=/var/lib/nginx/conf/nginx-config.template \
    RELOAD_SCRIPT=/var/lib/nginx/reload-nginx
ENTRYPOINT ["/usr/bin/openshift-router", "--working-dir=/var/lib/nginx/router"]
