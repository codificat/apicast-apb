# Ansible Playbook Bundle for 3scale gateway (APIcast)

An Ansible Playbook Bundle (APB) to deploy the
[3scale gateway (APIcast)](https://support.3scale.net) to OpenShift via the
Ansible Service Broker.

## Plans

There is just one plan: `defalut`.

## Parameters

Name                  | Requred | Default    | Description                                          |
----------------------|---------|------------|------------------------------------------------------|
management_host       | Yes     | -          | 3scale management hostname, e.g. my-admin.3scale.net |
access_token          | Yes     | -          | 3scale management access token                       |
deploy_environment    | Yes     | production | Deployment environment {sandbox, production}         |
config_loader         | Yes     | boot       | When to load config {boot, lazy}                     |
backend_cache_handler | Yes     | strict     | How to handle cache auth {strict, resilient}         |
resolver              | No      | -          | DNS Resolver for openresty                           |
log_level             | Yes     | notice     | Log level                                            |
oauth_tokens_ttl      | Yes     | 604800     | TTL of oauth tokens created (seconds)                |
service_list          | No      | -          | Subset of services to run (CSV, eg 42, 1337)         |
config_cache          | No      | -          | TTL of the cache for downloaded config (seconds)     |
redis_url             | No      | -          | Redis URL. Required for OAuth2 integration           |
management_scope      | Yes     | status     | Scope of the mgmt API {disabled, status, debug}      |
tls_verify            | Yes     | true       | Enable TLS/SSL peer verification                     |
response_codes        | Yes     | false      | Enable logging response codes to 3scale              |
path_routing          | Yes     | false      | Enable path routing (experimental)                   |
config_file           | No      | -          | Override JSON config file for the gateway            |

## Environment variables

For convenience, some of the parameters above can be set/overriden via
environment variables on the APB container. The environment variable names are
in general the same in all upper case letters, preceeded by `APICAST`.

Here's a list of recognized environment variables:

- APICAST_RESOLVER
- APICAST_SERVICE_LIST
- APICAST_CONFIG_CACHE
- APICAST_REDIS_URL
- APICAST_CONFIG_FILE
- NAMESPACE

There's also a special env var `APICAST_NAME` (default `apicast`) which sets the
base name of the objects created by the APB: deployment configuration, service,
secrets.

## How to Test the APIcast gateway APB

### Using the apb tool

From within the APB's directory you can run:

    apb run --project myproject

The tool will interactively ask you for the values of the different parameters.
Note that you must at least provide the details for 3scale management API via
the `management_host` and `access_token` parameters.

### Using Docker

You must provide OpenShift's API URL and authentication token, as well as the
3scale management hostname and admin access token:

    APICAST_HOST="my-admin.3scale.net"  # Your 3scale management host
    APICAST_TOKEN="17ab...cafe"         # Your 3scale management token

with that, you can:

    docker run -u $(id -u) -v ~/.kube:/opt/apb/.kube:Z \
        apicast-apb provision \
        -e management_host=${APICAST_HOST} \
        -e access_token=${APICAST_TOKEN}

You can set values for any of the parameters of the APB with additional `-e`
arguments. For example you might want to specify `-e namespace=myproject` (the
default target project is `apicast`).
