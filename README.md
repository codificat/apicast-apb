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

You must provide OpenShift's API URL and authentication token, as well as the
3scale management hostname and admin access token:

    TOKEN="$(oc whoami -t)"      # You must have logged in beforehand
    SERVER="https://openshift.example.com:443"   # Your OpenShift API
    APICAST_HOST="my-admin.3scale.net"  # Your 3scale management host
    APICAST_TOKEN="17ab...cafe"         # Your 3scale management token

with that, you can:

    docker run \
        -e OPENSHIFT_TARGET=${SERVER} \
        -e OPENSHIFT_TOKEN=${TOKEN} \
        apicast-apb provision \
        --extra-vars management_host=${APICAST_HOST} \
        --extra-vars access_token=${APICAST_TOKEN}

You can set values for any of the parameters of the APB with additional
`--extra-vars` arguments.
