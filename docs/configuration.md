# NGINX Plus Router Configuration

NGINX Plus Router can be customized using environment variables or annotations.

## Environment Variables

NGINX Plus Router supports the majority of the standard Router environment variables, described in [this document](https://docs.openshift.org/latest/architecture/networking/routes.html#env-variables).

The NGINX Plus Router supports the following standard Router environment variables:
* `DEFAULT_CERTIFICATE`
* `EXTENDED_VALIDATION`
* `NAMESPACE_LABELS`
* `RELOAD_SCRIPT`
* `ROUTER_ALLOWED_DOMAINS`
* `ROUTER_DEFAULT_CLIENT_TIMEOUT`
* `ROUTER_DEFAULT_CONNECT_TIMEOUT`
* `ROUTER_DEFAULT_SERVER_TIMEOUT`
* `ROUTER_DEFAULT_TUNNEL_TIMEOUT`
* `ROUTER_DENIED_DOMAINS`
* `ROUTER_ENABLE_INGRESS`
* `ROUTER_LISTEN_ADDR`
* `ROUTER_LOG_LEVEL`
* `ROUTER_MAX_CONNECTIONS`
* `ROUTER_OVERRIDE_HOSTNAME`
* `ROUTER_SERVICE_HTTPS_PORT`
* `ROUTER_SERVICE_HTTP_PORT`
* `ROUTER_SERVICE_NAME`
* `ROUTER_CANONICAL_HOSTNAME`
* `ROUTER_SERVICE_NAMESPACE`
* `ROUTER_SERVICE_SNI_PORT`
* `ROUTER_SLOWLORIS_HTTP_KEEPALIVE`
* `ROUTER_SUBDOMAIN`
* `ROUTER_SYSLOG_ADDRESS`
* `ROUTE_LABELS`
* `RELOAD_INTERVAL`
* `ROUTER_USE_PROXY_PROTOCOL`
* `ROUTER_ALLOW_WILDCARD_ROUTES`
* `ROUTER_DISABLE_NAMESPACE_OWNERSHIP_CHECK`

The following standard environment variables are supported, but have some differences comparing with the default Router:
* `DEFAULT_CERTIFICATE_DIR`. The default value is `/etc/pki/tls/private`.
* `ROUTER_TCP_BALANCE_SCHEME`. Supported values are `round_robin`, `least_conn` and `ip_hash`. The default is `round_robin`.
* `ROUTER_LOAD_BALANCE_ALGORITHM`. Supported values are `round_robin`, `least_conn` and `ip_hash`. The default is `round_robin`.
* `ROUTER_METRICS_TYPE`. The value must be empty.
* `ROUTER_SYSLOG_FORMAT`. Specifies the log format for a server that routes HTTP(s) traffic.
* `TEMPLATE_FILE`. The default value is `/var/lib/nginx/conf/nginx-config.template`.


The following standard environment variables are not available:
* `DEFAULT_CERTIFICATE_PATH`
* `DROP_SYN_DURING_RESTART`
* `ROUTER_BACKEND_CHECK_INTERVAL`
* `ROUTER_BACKEND_PROCESS_ENDPOINTS`
* `ROUTER_CLIENT_FIN_TIMEOUT`
* `ROUTER_COOKIE_NAME`
* `ROUTER_COMPRESSION_MIME`
* `ROUTER_DEFAULT_SERVER_FIN_TIMEOUT`
* `ROUTER_ENABLE_COMPRESSION`
* `ROUTER_SERVICE_NO_SNI_PORT`
* `ROUTER_SLOWLORIS_TIMEOUT`
* `STATS_PASSWORD`
* `STATS_USERNAME`
* `ROUTER_STRICT_SNI`


The NGINX Plus Router includes the following additional environment variables:
* `ROUTER_SERVICE_UNREACHABLE_PORT`. For Routes with zero endpoints, the Router sends passthrough connections to `127.0.0.1:<ROUTER_SERVICE_UNREACHABLE_PORT>`. The Router assumes that nothing is running on that port. The default is `10446`. 
* `ROUTER_PROXY_PROTOCOL_TRUSTED_SOURCE`. Configures trusted sources of client connections when PROXY PROTOCOL is enabled (using `ROUTER_USE_PROXY_PROTOCOL` variable). The default is `0.0.0.0/0`, which means any source is trusted. Configure this variable with the value of the IP address or the subnet of outgoing connections of your external load balancer that uses PROXY PROTOCOL. When the load balancer is not trusted, the client IP address is not extracted using the PROXY PROTOCOL and the IP address of the load balancer is used as the client IP.   
* `ROUTER_SERVICE_INTERNAL_PASSTHROUGH_PORT`. Specifies the port of the helper server that routes passthrough connections.  The default is `10447`. 
* `ROUTER_SYSLOG_FORMAT_FOR_PASSTHROUGH`. Specifies the log format for the passthrough server -- the server, which handles both HTTPS and passthrough connections, before forwarding them to the Router internal helper servers.
* `ROUTER_SYSLOG_FORMAT_FOR_INTERNAL_PASSTHROUGH`. Specifies the log format for the internal server that routes passthrough connections.
* `ROUTER_SERVICE_503_SERVER_PORT`. Specifies the port of the helper server, which serves 503 error pages.
* `ROUTER_SERVICE_PASSTHROUGH_PORT`. Specifies the port of the passthrough server -- the server, which handles both HTTPS and passthrough connections, before forwarding them to the Router internal helper servers. The default is `443` and equal to `ROUTER_SERVICE_HTTPS_PORT`. However, `ROUTER_SERVICE_PASSTHROUGH_PORT` and `ROUTER_SERVICE_HTTPS_PORT` can be different. In that case the server on the passthrough port will only handle passthrough connections and the server on the HTTPS port will only handle HTTPS connections. 

## Annotations

The NGINX Plus Router supports the following annotations:
* `nginx.router.openshift.io/balance`. Configures a load balancing method. The supported values are `round_robin`, `least_conn` and `ip_hash`. The default values is configured using `ROUTER_LOAD_BALANCE_ALGORITHM` or `ROUTER_TCP_BALANCE_SCHEME` environment variables.
* `nginx.router.openshift.io/keepalive`. Activates the cache for connections between NGINX Plus and upstream servers. The default is `0`, which means the cache is not activated. Not applicable for passthrough routes. See http://nginx.org/en/docs/http/ngx_http_upstream_module.html#keepalive for more details.
* `nginx.router.openshift.io/websocket`. Enables Websocket. The default is `false`.
* `nginx.router.openshift.io/grpc`. Enables gRPC. The default is `false`.

## Configuring Edge Termination

If edge termination is enabled for a host in one route, it will be enabled in all other routes that reference that host as well. Thus, you only need to configure edge termination in one route. This behavior is different from the default Router behavior, where you would have to enable edge termination in every route. 

If you still want to configure edge termination in every route that reference a particular host, please note that for all those routes the NGINX Plus Router will use edge termination settings, such as termination policy and the certificate, from a route with a name that is alphabetically last among those routes.
