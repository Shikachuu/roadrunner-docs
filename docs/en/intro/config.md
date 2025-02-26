```yaml
######################################################################################
#                       THIS IS SAMPLE OF THE CONFIGURATION                          #
# IT'S NOT A DEFAULT CONFIGURATION, IT'S JUST A REFERENCE TO ALL OPTIONS AND PLUGINS #
#       MORE DOCS CAN BE FOUND HERE: <https://roadrunner.dev/docs/intro-config>      #
######################################################################################

# Production usage guide: https://roadrunner.dev/docs/beep-beep-production

# Hint: RR will replace any config options using reference to environment variables,
# eg.: `option_key: ${ENVIRONMENT_VARIABLE_NAME}`.

# Important: TCP port numbers for each plugin (rpc, http, etc) must be unique!

# RR configuration version
version: "2.7"

# Remote Procedures Calling (docs: https://roadrunner.dev/docs/beep-beep-rpc)
# Is used for connecting to RoadRunner server from your PHP workers.
rpc:
  # TCP address:port for listening.
  #
  # Default: "tcp://127.0.0.1:6001"
  listen: tcp://127.0.0.1:6001

# Application server settings (docs: https://roadrunner.dev/docs/php-worker)
server:
  #[SINCE 2.6]
  on_init:
    # Command to execute before the main server's command
    #
    # This option is required if using on_init
    command: "any php or script here"

    # Script execute timeout
    #
    # Default: 60s [60m, 60h], if used w/o units its means - NANOSECONDS.
    exec_timeout: 20s

    # Environment variables for the worker processes.
    #
    # Default: <empty map>
    env:
      - SOME_KEY: "SOME_VALUE"
      - SOME_KEY2: "SOME_VALUE2"
  # Worker starting command, with any required arguments.
  #
  # This option is required.
  command: "php psr-worker.php"

  # Username (not UID) for the worker processes. An empty value means to use the RR process user.
  #
  # Default: ""
  user: ""

  # Group name (not GID) for the worker processes. An empty value means to use the RR process user.
  #
  # Default: ""
  group: ""

  # Environment variables for the worker processes.
  #
  # Default: <empty map>
  env:
    - SOME_KEY: "SOME_VALUE"
    - SOME_KEY2: "SOME_VALUE2"

  # Worker relay can be: "pipes", TCP (eg.: tcp://127.0.0.1:6002), or socket (eg.: unix:///var/run/rr.sock).
  #
  # Default: "pipes"
  relay: pipes

  # Timeout for relay connection establishing (only for socket and TCP port relay).
  #
  # Default: 60s
  relay_timeout: 60s

# Logging settings (docs: https://roadrunner.dev/docs/beep-beep-logging)
logs:
  # Logging mode can be "development", "production" or "raw". Do not forget to change this value for production environment.
  #
  # Development mode (which makes DPanicLevel logs panic), uses a console encoder, writes to standard error, and
  # disables sampling. Stacktraces are automatically included on logs of WarnLevel and above.
  #
  # Default: "development"
  mode: development

  # Logging level can be "panic", "error", "warn", "info", "debug".
  #
  # Default: "debug"
  level: debug

  # Encoding format can be "console" or "json" (last is preferred for production usage).
  #
  # Default: "console"
  encoding: console

  # Log line ending
  #
  # Default: "\n"
  line_ending: "\n"

  # Output can be file (eg.: "/var/log/rr_errors.log"), "stderr" or "stdout".
  #
  # Default: "stderr"
  output: stderr

  # Errors only output can be file (eg.: "/var/log/rr_errors.log"), "stderr" or "stdout".
  #
  # Default: "stderr"
  err_output: stderr

  # File logger options
  #
  # Default: null
  file_logger_options:

    # Path to the file
    #
    # Default: It uses <processname>-lumberjack.log name in the os tempdir if empty.
    log_output: "/tmp/my.log"

    # Max file size in MB
    #
    # Default: 100
    max_size: 100

    # max_age is the maximum number of days to retain old log files based on the timestamp encoded in their filename.
    #
    # Default: 1 (day)
    max_age: 1

    # max_backups is the maximum number of old log files to retain.
    #
    # Default: retain all (if set to 0)
    max_backups : 5

    # Compress determines if the rotated log files should be compressed using gzip.
    #
    # Default: false
    compress: false

  # You can configure each plugin log messages individually (key is plugin name, and value is logging options in same
  # format as above).
  #
  # Default: <empty map>
  channels:
    http:
      mode: development
      level: panic
      encoding: console
      output: stdout
      err_output: stderr
    server:
      mode: production
      level: info
      encoding: json
      output: stdout
      err_output: stdout
    rpc:
      mode: raw
      level: debug
      encoding: console
      output: stderr
      err_output: stdout

# Workflow and activity mesh service.
#
# Drop this section for temporal feature disabling.
temporal:
  # Address of temporal server.
  #
  # Default: "127.0.0.1:7233"
  address: 127.0.0.1:7233

  # Sticky cache size. Sticky workflow execution is the affinity
  # between workflow tasks of a specific workflow execution to a specific worker. The benefit of sticky execution is that
  # the workflow does not have to reconstruct state by replaying history from the beginning. The cache is shared between
  # workers running within same process. This must be called before any worker is started. If not called, the default
  # size of 10K (which may change) will be used.
  #
  # Default: 10_000
  cache_size: 10000

  # Namespace name for this client to work with
  #
  # Default: default
  namespace: default

  # Internal temporal communication protocol, can be "proto" or "json".
  #
  # Default: "proto"
  codec: proto

  # Debugging level (only for "json" codec). Set 0 for nothing, 1 for "normal", and 2 for colorized messages.
  #
  # Default: 0
  debug_level: 2

  # Temporal metrics
  #
  # Optional section
  metrics:
    # Server metrics address
    #
    # Required for the production. Default: 127.0.0.1:9091, for the metrics 127.0.0.1:9091/metrics
    address: 127.0.0.1:9091
    # Metrics type
    #
    # Default: "summary". Supported values: summary, histogram
    type: "summary"

    # Temporal metrics prefix
    #
    # Default: (empty)
    prefix: "foobar"

  # Activities pool settings.
  activities:
    # Debug mode for the pool. In this mode, pool will not pre-allocate the worker. Worker (only 1, num_workers ignored) will be allocated right after the request arrived.
    #
    # Default: false
    debug: false

    # Override server's command
    #
    # Default: empty
    command: "php my-super-app.php"

    # How many worker processes will be started. Zero (or nothing) means the number of logical CPUs.
    #
    # Default: 0
    num_workers: 0

    # Maximal count of worker executions. Zero (or nothing) means no limit.
    #
    # Default: 0
    max_jobs: 64

    # Timeout for worker allocation. Zero means no limit.
    #
    # Default: 60s
    allocate_timeout: 60s

    # Timeout for worker destroying before process killing. Zero means no limit.
    #
    # Default: 60s
    destroy_timeout: 60s

    # Supervisor is used to control http workers (previous name was "limit", docs:
    # https://roadrunner.dev/docs/php-limit). "Soft" limits will not interrupt current request processing. "Hard"
    # limit on the contrary - interrupts the execution of the request.
    supervisor:
      # How often to check the state of the workers.
      #
      # Default: 1s
      watch_tick: 1s

      # Maximum time worker is allowed to live (soft limit). Zero means no limit.
      #
      # Default: 0s
      ttl: 0s

      # How long worker can spend in IDLE mode after first using (soft limit). Zero means no limit.
      #
      # Default: 0s
      idle_ttl: 10s

      # Maximal worker memory usage in megabytes (soft limit). Zero means no limit.
      #
      # Default: 0
      max_worker_memory: 128

      # Maximal job lifetime (hard limit). Zero means no limit.
      #
      # Default: 0s
      exec_ttl: 60s


# KV plugin settings. Available drivers: boltdb, redis, memcached, memory.
# Any number of sections can be defined here.
kv:
  # User defined name of the section
  #
  # Default: none
  boltdb-south:
    # Driver which should be used for the storage
    #
    # This option is required.
    driver: boltdb
    # Local configuration section
    #
    # This option is required to use local section, otherwise (boltdb-south) global configuration will be used.
    config:
      # File name for the DB
      #
      # Default: "rr.db"
      file: "rr.db"
      # Access permission for the DB file.
      #
      # Default: "0777"
      permissions: 0777
      # TTL keys check interval in seconds. It's safe to use 1 second here, but can be a little costly to performance.
      #
      # Default: "60" seconds
      interval: 40

  # User defined name of the section (us-cental-kv used as example)
  #
  # Default: none
  us-central-kv:
    # Driver which should be used for the storage
    #
    # Default: none
    driver: memcached
    # Local configuration section
    #
    # This option is required to use local section, otherwise (us-central-kv) global configuration will be used.
    config:
      # Driver specific section. Address of the memcached node.
      #
      # Default: "localhost:11211"
      addr: [ "localhost:11211" ]

  # User defined name of the section
  #
  # Default: none
  fast-kv-fr:
    # Driver which should be used for the storage.
    #
    # Default: none
    driver: redis
    # Redis specific section. If one address provided - single node client will be used.
    #
    #
    # UniversalClient is an abstract client which - based on the provided options -
    # can connect to either clusters, or sentinel-backed failover instances
    # or simple single-instance servers. This can be useful for testing
    # cluster-specific applications locally.
    # if the number of addrs is 1 and master_name is empty, a single-node redis Client will be returned
    # if the number of addrs is two or more, a ClusterClient will be returned

    # Local configuration section
    #
    # This option is required to use local section, otherwise (fast-kv-fr) global configuration will be used.
    config:
      addrs:
        - "localhost:6379"
        # if a MasterName is passed a sentinel-backed FailoverClient will be returned
      master_name: ""
      username: ""
      password: ""
      db: 0
      sentinel_password: ""
      route_by_latency: false
      route_randomly: false
      dial_timeout: 0 # accepted values [1s, 5m, 3h]
      max_retries: 1
      min_retry_backoff: 0 # accepted values [1s, 5m, 3h]
      max_retry_backoff: 0 # accepted values [1s, 5m, 3h]
      pool_size: 0
      min_idle_conns: 0
      max_conn_age: 0 # accepted values [1s, 5m, 3h]
      read_timeout: 0 # accepted values [1s, 5m, 3h]
      write_timeout: 0 # accepted values [1s, 5m, 3h]
      pool_timeout: 0 # accepted values [1s, 5m, 3h]
      idle_timeout: 0 # accepted values [1s, 5m, 3h]
      idle_check_freq: 0 # accepted values [1s, 5m, 3h]
      read_only: false

  # User defined name of the section
  #
  # Default: none
  local-memory:
    # In memory driver specific section
    #
    # Default: none
    driver: memory
    # Local configuration section
    #
    # This option is required to use local section, otherwise (local-memory) global configuration will be used.
    config:
      # TTL check interval in seconds
      #
      # Default: 60 seconds
      interval: 1

# Service plugin settings
service:
  # User defined service name
  #
  # Default: none, required
  some_service_1:
    # Command to execute. Can be any command here which can be executed.
    #
    # Default: none, required.
    command: php tests/plugins/service/test_files/loop.php

    # Env variables for the process
    #
    # Default: empty
    env:
      foo: "BAR"
      foo2: "BAR2"

    # Number of copies (processes) to start per command.
    #
    # Default: 1
    process_num: 1

    # Allowed time before stop.
    #
    # Default: 0 (infinity), can be 1s, 2m, 2h (seconds, minutes, hours)
    exec_timeout: 0

    # Remain process after exit. In other words, restart process after exit with any exit code.
    #
    # Default: "false"
    remain_after_exit: true

    # Number of seconds to wait before process restart.
    #
    # Default: 30
    restart_sec: 1

  # User defined service name
  #
  # Default: none, required
  some_service_2:
    # Command to execute. Can be any command here which can be executed.
    #
    # Default: none, required.
    command: "binary"

    # Env variables for the process
    #
    # Default: empty
    env:
      foo: "BAR"
      foo2: "BAR2"

    # Number of copies (processes) to start per command.
    #
    # Default: 1
    process_num: 1

    # Allowed time before stop.
    #
    # Default: 0 (infinity), can be 1s, 2m, 2h (seconds, minutes, hours)
    exec_timeout: 0

    # Remain process after exit. In other words, restart process after exit with any exit code.
    #
    # Default: "false"
    remain_after_exit: true

    # Number of seconds to wait before process restart.
    #
    # Default: 30
    restart_sec: 1

# HTTP plugin settings.
http:
  # Host and port to listen on (eg.: `127.0.0.1:8080`).
  #
  # This option is required.
  address: 127.0.0.1:8080

  # override http error code for the internal RR errors
  #
  # Default: 500
  internal_error_code: 505

  # HTTP access logs
  #
  # Default: false
  access_logs: false

  # Maximal incoming request size in megabytes. Zero means no limit.
  #
  # Default: 0
  max_request_size: 256

  # Middlewares for the http plugin, order is important. Allowed values is: "headers", "gzip", "static", "websockets", "sendfile",  [SINCE 2.6] -> "new_relic", [SINCE 2.6] -> "http_metrics", [SINCE 2.7] -> "cache"
  #
  # Default value: []
  middleware: [ "headers", "gzip" ]

  # Allow incoming requests only from the following subnets (https://en.wikipedia.org/wiki/Reserved_IP_addresses).
  #
  # Default: ["10.0.0.0/8", "127.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16",  "::1/128", "fc00::/7", "fe80::/10"]
  trusted_subnets:
    [
        "10.0.0.0/8",
        "127.0.0.0/8",
        "172.16.0.0/12",
        "192.168.0.0/16",
        "::1/128",
        "fc00::/7",
        "fe80::/10",
    ]

  # [SINCE 2.10] OpenTelemetry middleware
  otel:
    # Use insecure endpoint (http) or insecure gRPC
    #
    # Default: false
    insecure: true

    # Use gzip to compress the spans
    #
    # Default: false
    compress: false

    # Client to send the spans
    #
    # Default: http. Possible values: `http`, `grpc`
    client: http

    # Provides functionality to emit telemetry to consumers
    #
    # Default: otlp. Possible values: otlp (used for new_relic, datadog), zipkin or stdout
    exporter: otlp

    # Used for the http client to override the default URL
    #
    # Default: empty
    custom_url: ""

    # User's service name
    #
    # Default: RoadRunner
    service_name: "rr_test"

    # User's service version
    #
    # Default: RoadRunner
    service_version: "1.0.0"

    # Consumer's endpoint
    #
    # Default: localhost:4318
    endpoint: "127.0.0.1:4318"

  # [SINCE 2.6] New Relic middleware
  new_relic:
    # Application name.
    #
    # Error if empty. NEW_RELIC_APP_NAME env variable should be set if the app_name key is empty. If both empty - error.
    app_name: "app"

    # Licence key
    #
    # Error if empty. NEW_RELIC_LICENSE_KEY env variable should be set if the license_key key is empty. If both empty - error.
    license_key: "key"

  # RFC 7234 (partially) RR Cache middleware
  cache:
    # Cache driver
    #
    # Default: memory. Available drivers: memory
    driver: memory

    # Methods to cache
    #
    # According to the RFC, we can cache only GET, HEAD or POST requests. Currently, only GET method supported.
    cache_methods: ["GET", "HEAD", "POST"]

    # Configuration for the driver
    #
    # Empty for the memory
    config: {}

  # File uploading settings.
  uploads:
    # Directory for file uploads. Empty value means to use $TEMP based on your OS.
    #
    # Default: ""
    dir: "/tmp"

    # Deny files with the following extensions to upload.
    #
    # Default: [".php", ".exe", ".bat"]
    forbid: [ ".php", ".exe", ".bat", ".sh" ]

    # [SINCE 2.6] Allow files with the following extensions to upload
    #
    # Default: empty
    allow: [ ".html", ".aaa" ]

  # Settings for "headers" middleware (docs: https://roadrunner.dev/docs/http-headers).
  headers:
    # Allows to control CORS headers. Additional headers "Vary: Origin", "Vary: Access-Control-Request-Method",
    # "Vary: Access-Control-Request-Headers" will be added to the server responses. Drop this section for this
    # feature disabling.
    cors:
      # Controls "Access-Control-Allow-Origin" header value (docs: https://mzl.la/2OgD4Qf).
      #
      # Default: ""
      allowed_origin: "*"

      # Controls "Access-Control-Allow-Headers" header value (docs: https://mzl.la/2OzDVvk).
      #
      # Default: ""
      allowed_headers: "*"

      # Controls "Access-Control-Allow-Methods" header value (docs: https://mzl.la/3lbwyXf).
      #
      # Default: ""
      allowed_methods: "GET,POST,PUT,DELETE"

      # Controls "Access-Control-Allow-Credentials" header value (docs: https://mzl.la/3ekJGaY).
      #
      # Default: false
      allow_credentials: true

      # Controls "Access-Control-Expose-Headers" header value (docs: https://mzl.la/3qAqgkF).
      #
      # Default: ""
      exposed_headers: "Cache-Control,Content-Language,Content-Type,Expires,Last-Modified,Pragma"

      # Controls "Access-Control-Max-Age" header value in seconds (docs: https://mzl.la/2PCSdvt).
      #
      # Default: 0
      max_age: 600

    # Automatically add headers to every request passed to PHP.
    #
    # Default: <empty map>
    request:
      input: "custom-header"

    # Automatically add headers to every response.
    #
    # Default: <empty map>
    response:
      X-Powered-By: "RoadRunner"

  # Settings for "static" middleware (docs: https://roadrunner.dev/docs/http-static).
  static:
    # Path to the directory to serve
    #
    # Default: "." (current)
    dir: "."

    # File patterns to forbid
    #
    # Default: empty
    forbid: [ "" ]

    # Etag calculation (base on the body CRC32)
    #
    # Default: false
    calculate_etag: false

    # Weak etag calculation (based only on the content-length CRC32)
    #
    # Default: false
    weak: false

    # Patterns to allow
    #
    # Default: empty
    allow: [ ".txt", ".php" ]

    # Request headers
    #
    # Default: empty
    request:
      input: "custom-header"

    # Response headers
    #
    # Default: empty
    response:
      output: "output-header"

  # Workers pool settings.
  pool:
    # Debug mode for the pool. In this mode, pool will not pre-allocate the worker. Worker (only 1, num_workers ignored) will be allocated right after the request arrived.
    #
    # Default: false
    debug: false

    # Override server's command
    #
    # Default: empty
    command: "php my-super-app.php"

    # How many worker processes will be started. Zero (or nothing) means the number of logical CPUs.
    #
    # Default: 0
    num_workers: 0

    # Maximal count of worker executions. Zero (or nothing) means no limit.
    #
    # Default: 0
    max_jobs: 64

    # Timeout for worker allocation. Zero means no limit.
    #
    # Default: 60s
    allocate_timeout: 60s

    # Timeout for worker destroying before process killing. Zero means no limit.
    #
    # Default: 60s
    destroy_timeout: 60s

    # Supervisor is used to control http workers (previous name was "limit", docs:
    # https://roadrunner.dev/docs/php-limit). "Soft" limits will not interrupt current request processing. "Hard"
    # limit on the contrary - interrupts the execution of the request.
    supervisor:
      # How often to check the state of the workers.
      #
      # Default: 1s
      watch_tick: 1s

      # Maximum time worker is allowed to live (soft limit). Zero means no limit.
      #
      # Default: 0s
      ttl: 0s

      # How long worker can spend in IDLE mode after first using (soft limit). Zero means no limit.
      #
      # Default: 0s
      idle_ttl: 10s

      # Maximal worker memory usage in megabytes (soft limit). Zero means no limit.
      #
      # Default: 0
      max_worker_memory: 128

      # Maximal job lifetime (hard limit). Zero means no limit.
      #
      # Default: 0s
      exec_ttl: 60s

  # SSL (Secure Sockets Layer) (TLS) settings (docs: https://roadrunner.dev/docs/http-https).
  ssl:
    # Host and port to listen on (eg.: `127.0.0.1:443`).
    #
    # Default: ":443"
    address: "127.0.0.1:443"

    # Use ACME certificates provider (Let's encrypt)
    acme:
      # Directory to use as a certificate/pk, account info storage
      #
      # Optional. Default: rr_cache
      certs_dir: rr_le_certs

      # User email
      #
      # Used to create LE account. Mandatory. Error on empty.
      email: you-email-here@email

      # Alternate port for the http challenge. Challenge traffic should be redirected to this port if overridden.
      #
      # Optional. Default: 80
      alt_http_port: 80


      # Alternate port for the tls-alpn-01 challenge. Challenge traffic should be redirected to this port if overridden.
      #
      # Optional. Default: 443.
      alt_tlsalpn_port: 443

      # Challenge types
      #
      # Optional. Default: http-01. Possible values: http-01, tlsalpn-01
      challenge_type: http-01

      # Use production or staging endpoint. NOTE, try to use staging endpoint to make sure, that everything works correctly.
      #
      # Optional, but for production should be set to true. Default: false
      use_production_endpoint: true

      # List of your domains to obtain certificates
      #
      # Mandatory. Error on empty.
      domains: [
          "your-cool-domain.here",
          "your-second-domain.here"
      ]

    # Automatic redirect from http:// to https:// schema.
    #
    # Default: false
    redirect: true

    # Path to the cert file. This option is required for SSL working.
    #
    # This option is required.
    cert: /ssl/server.crt

    # Path to the cert key file.
    #
    # This option is required.
    key: /ssl/server.key

    # Path to the root certificate authority file.
    #
    # This option is optional (required for the mTLS).
    root_ca: /ssl/root.crt

    # Client auth type (mTLS)
    #
    # This option is optional. Default value: no_client_certs. Possible values: request_client_cert, require_any_client_cert, verify_client_cert_if_given, require_and_verify_client_cert, no_client_certs
    client_auth_type: no_client_certs

  # FastCGI frontend support.
  fcgi:
    # FastCGI connection DSN. Supported TCP and Unix sockets. An empty value disables this.
    #
    # Default: ""
    address: tcp://0.0.0.0:7921

  # HTTP/2 settings.
  http2:
    # HTTP/2 over non-encrypted TCP connection using H2C.
    #
    # Default: false
    h2c: false

    # Maximal concurrent streams count.
    #
    # Default: 128
    max_concurrent_streams: 128

# Redis section. Should be defined to use as a broadcast driver for the websockets (with no limitation to use in other plugins)
redis:
  # UniversalClient is an abstract client which - based on the provided options -
  # can connect to either clusters, or sentinel-backed failover instances
  # or simple single-instance servers. This can be useful for testing
  # cluster-specific applications locally.
  # if the number of addrs is 1 and master_name is empty, a single-node redis Client will be returned
  # if the number of addrs is two or more, a ClusterClient will be returned
  addrs:
    - "localhost:6379"
    # if a MasterName is passed a sentinel-backed FailoverClient will be returned
  master_name: ""
  username: ""
  password: ""
  db: 0
  sentinel_password: ""
  route_by_latency: false
  route_randomly: false
  dial_timeout: 0 # accepted values [1s, 5m, 3h]
  max_retries: 1
  min_retry_backoff: 0 # accepted values [1s, 5m, 3h]
  max_retry_backoff: 0 # accepted values [1s, 5m, 3h]
  pool_size: 0
  min_idle_conns: 0
  max_conn_age: 0 # accepted values [1s, 5m, 3h]
  read_timeout: 0 # accepted values [1s, 5m, 3h]
  write_timeout: 0 # accepted values [1s, 5m, 3h]
  pool_timeout: 0 # accepted values [1s, 5m, 3h]
  idle_timeout: 0 # accepted values [1s, 5m, 3h]
  idle_check_freq: 0 # accepted values [1s, 5m, 3h]
  read_only: false

# Websockets plugin
#
# Should be attached as a middleware to the http plugin middlewares
websockets:
  # Broker to use. Brokers can be set in the broadcast plugin. For example, if you use broker: default here, broadcast plugin should have default broker in its config.
  #
  # This option is required.
  broker: default-redis

  # Allowed request origin (single value). This option is optional (allowed all by default)
  #
  # Default: "*". Samples: "https://*.my.site", "http//*.com", "10.1.1.1", etc
  allowed_origin: "*"

  # http path where to handle websockets connections
  #
  # Default: /ws
  path: "/ws"

# Broadcast plugin. It main purpose is to broadcast published messages via all brokers
#
# Use it in conjunction with the websockets, memory and redis plugins.
# LIMITATION: DO NOT use the same redis connection within different sections or messages will be duplicated.
# There is no limitation to use different redis connections (ie localhost:6379, localhost:6378, etc) in different sections.
broadcast:
  # Section name.
  #
  # This option is required and should match with other plugins broker section.
  default:
    # Driver to use. Available drivers: redis, memory. In-memory driver does not require any configuration.
    #
    # This option is required. There is no config for this driver for the broadcast, thus we need to use {}
    driver: memory
    # This option is required if you want to use local configuration
    #
    # Default: empty.
    config: { }

  # Section name.
  #
  # This option is required and should match with other plugins broker section.
  default-redis:
    # Driver to use. Available drivers: redis, memory. Redis driver require configuration (if empty - localhost:6379 one-node client will be used, see redis plugin config).
    #
    # This option is required.
    driver: redis
    # Local configuration section
    #
    # This option is required to use local section, otherwise (default-redis) global configuration will be used.
    config:
      # Redis configuration. This configuration related to the default-redis section. Broadcast plugin will use this configuration first.
      # If section configuration doesn't exists, second priority - global redis configuration.
      # If there are no configurations provided, default will be used.
      #
      # Default: localhost:6379
      addrs:
        - "localhost:6379"
      # if a MasterName is passed a sentinel-backed FailoverClient will be returned
      master_name: ""
      username: ""
      password: ""
      db: 0
      sentinel_password: ""
      route_by_latency: false
      route_randomly: false
      dial_timeout: 0 # accepted values [1s, 5m, 3h]
      max_retries: 1
      min_retry_backoff: 0 # accepted values [1s, 5m, 3h]
      max_retry_backoff: 0 # accepted values [1s, 5m, 3h]
      pool_size: 0
      min_idle_conns: 0
      max_conn_age: 0 # accepted values [1s, 5m, 3h]
      read_timeout: 0 # accepted values [1s, 5m, 3h]
      write_timeout: 0 # accepted values [1s, 5m, 3h]
      pool_timeout: 0 # accepted values [1s, 5m, 3h]
      idle_timeout: 0 # accepted values [1s, 5m, 3h]
      idle_check_freq: 0 # accepted values [1s, 5m, 3h]
      read_only: false

# Application metrics in Prometheus format (docs: https://roadrunner.dev/docs/beep-beep-metrics). Drop this section
# for this feature disabling.
metrics:
  # Prometheus client address (path /metrics added automatically).
  #
  # Default: "127.0.0.1:2112"
  address: "127.0.0.1:2112"

  # Application-specific metrics (published using an RPC connection to the server).
  collect:
    app_metric:
      type: histogram
      help: "Custom application metric"
      labels: [ "type" ]
      buckets: [ 0.1, 0.2, 0.3, 1.0 ]
      # Objectives defines the quantile rank estimates with their respective absolute error (for summary only).
      objectives:
        - 1.4: 2.3
        - 2.0: 1.4

# Health check endpoint (docs: https://roadrunner.dev/docs/beep-beep-health). If response code is 200 - it means at
# least one worker ready to serve requests. 500 - there are no workers ready to service requests.
# Drop this section for this feature disabling.
status:
  # Host and port to listen on (eg.: `127.0.0.1:2114`). Use the following URL: http://127.0.0.1:2114/health?plugin=http
  # Multiple plugins must be separated using "&" - http://127.0.0.1:2114/health?plugin=http&plugin=rpc where "http" and
  # "rpc" are active (connected) plugins.
  #
  # This option is required.
  address: 127.0.0.1:2114

  # Response status code if a requested plugin not ready to handle requests
  # Valid for both /health and /ready endpoints
  #
  # Default: 503
  unavailable_status_code: 503

# Automatically detect PHP file changes and reload connected services (docs:
# https://roadrunner.dev/docs/beep-beep-reload). Drop this section for this feature disabling.
reload:
  # Sync interval.
  #
  # Default: "1s"
  interval: 1s

  # Global patterns to sync.
  #
  # Default: [".php"]
  patterns: [ ".php" ]

  # List of included for sync services (this is a map, where key name is a plugin name).
  #
  # Default: <empty map>
  services:
    http:
      # Directories to sync. If recursive is set to true, recursive sync will be applied only to the directories in
      # "dirs" section. Dot (.) means "current working directory".
      #
      # Default: []
      dirs: [ "." ]

      # Recursive search for file patterns to add.
      #
      # Default: false
      recursive: true

      # Ignored folders.
      #
      # Default: []
      ignore: [ "vendor" ]

      # Service specific file pattens to sync.
      #
      # Default: []
      patterns: [ ".php", ".go", ".md" ]


# NATS jobs driver
#
# Default: nats://127.0.0.1:4222
nats:
  addr: "demo.nats.io"

# Boltdb jobs driver
#
# This option is required to use boltdb driver
boltdb:
  # Permissions for hte boltdb database file
  #
  # This option is optional. Default: 0777
  permissions: 0777

# AMQP jobs driver
#
# This option is required to use AMQP driver
amqp:
  # AMQP Uri to connect to the rabbitmq server https://www.rabbitmq.com/uri-spec.html
  #
  # This option is required for the production. Default: amqp://guest:guest@127.0.0.1:5672
  addr: amqp://guest:guest@127.0.0.1:5672/

# Beanstalk jobs driver
#
# This option is required to use Beanstalk driver
beanstalk:
  # Beanstalk address
  #
  # This option is required for the production. Default: tcp://127.0.0.1:11300
  addr: tcp://127.0.0.1:11300

  # Beanstalk connect timeout.
  #
  # Default: 30s
  timeout: 10s

# SQS jobs driver (https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html)
#
# This option is required to use SQS driver
sqs:
  # AccessKey ID
  #
  # This option is required for the production. Default: empty
  key: api-key

  # Secret access key
  #
  # This option is required for the production. Default: empty
  secret: api-secret

  # AWS region
  #
  # This option is required for the production. Default: empty
  region: us-west-1

  # AWS session token
  #
  # This option is required for the production. Default: empty
  session_token: test

  # AWS SQS endpoint to connect
  #
  # This option is required for the production. Default: http://127.0.0.1:9324
  endpoint: http://127.0.0.1:9324

jobs:
  # Number of threads which will try to obtain the job from the priority queue
  #
  # Default: number of the logical CPU cores
  num_pollers: 32

  # Size of the internal priority queue
  #
  # Default: 1_000_000
  pipeline_size: 100000

  # worker pool configuration
  pool:
    command: ""
    num_workers: 10
    max_jobs: 0
    allocate_timeout: 60s
    destroy_timeout: 60s

  # List of broker pipelines associated with the drivers.
  #
  # This option is not required since you can declare pipelines in the runtime. Pipeline driver should exist.
  pipelines:
    # Pipeline name
    #
    # This option is required when defining pipelines via configuration.
    test-local:

      # Driver associated with the pipeline
      #
      # This option is required. Possible values: amqp, memory, sqs, beanstalk, boltdb
      driver: memory

      # Driver's configuration
      #
      # Should not be empty
      config:
        # Pipeline priority
        #
        # If the job has priority set to 0, it will inherit the pipeline's priority. Default: 10.
        priority: 10

        # Number of job to prefetch from the driver.
        #
        # Default: 100_000.
        prefetch: 10000

    # Pipeline name
    #
    # This option is required when defining pipelines via configuration.
    test-local-1:
      # Driver associated with the pipeline
      #
      # This option is required. Possible values: amqp, memory, sqs, beanstalk, boltdb
      driver: boltdb

      # Driver's configuration
      #
      # Should not be empty
      config:
        # BoldDB file to create or DB to use
        #
        # Default: "rr.db"
        file: "path/to/rr.db"

        # Pipeline priority
        #
        # If the job has priority set to 0, it will inherit the pipeline's priority. Default: 10.
        priority: 10

        # Number of job to prefetch from the driver.
        #
        # Default: 100_000.
        prefetch: 10000

    test-local-2:
      # Driver name
      #
      # This option is required.
      driver: amqp

      # Driver's configuration
      #
      # Should not be empty
      config:

        # QoS - prefetch.
        #
        # Default: 10
        prefetch: 10

        # Pipeline priority
        #
        # If the job has priority set to 0, it will inherit the pipeline's priority. Default: 10.
        priority: 1

        # Consume any payload type (not only Jobs structured)
        #
        # Default: false
        consume_all: false

        # Durable queue
        #
        # Default: false
        durable: false

        # Delete queue when stopping the pipeline
        #
        # Default: false
        delete_queue_on_stop: false

        # Queue name
        #
        # Default: default
        queue: test-1-queue

        # Exchange name
        #
        # Default: amqp.default
        exchange: default

        # Exchange type
        #
        # Default: direct.
        exchange_type: direct

        # Routing key for the queue
        #
        # Default: empty.
        routing_key: test

        # Declare a queue exclusive at the exchange
        #
        # Default: false
        exclusive: false

        # When multiple is true, this delivery and all prior unacknowledged deliveries
        # on the same channel will be acknowledged.  This is useful for batch processing
        # of deliveries
        #
        # Default:false
        multiple_ack: false

        # Use rabbitmq mechanism to requeue the job on fail
        #
        # Default: false
        requeue_on_fail: false

    test-local-3:
      # Driver name
      #
      # This option is required.
      driver: beanstalk

      # Driver's configuration
      #
      # Should not be empty
      config:

        # Pipeline priority
        #
        # Default
        priority: 11

        # Beanstalk internal tube priority
        #
        # Default: 1
        tube_priority: 1

        # Consume any payload type (not only Jobs structured)
        #
        # Default: false
        consume_all: false

        # Tube name
        #
        # Default: default
        tube: default-1

        # If no job is available before this timeout has passed, Reserve returns a ConnError recording ErrTimeout.
        #
        # Default: 5s
        reserve_timeout: 10s

    test-local-4:
      # Driver name
      #
      # This option is required.
      driver: sqs

      # Driver's configuration
      #
      # Should not be empty
      config:

        # Pipeline priority
        #
        # If the job has priority set to 0, it will inherit the pipeline's priority. Default: 10.
        priority: 10

        # Number of jobs to prefetch from the SQS. mazon SQS never returns more messages than this value
        # (however, fewer messages might be returned). Valid values: 1 to 10.
        #
        # Default: 10
        prefetch: 10

        # Consume any payload type (not only Jobs structured)
        #
        # Default: false
        consume_all: false

        # Get queue URL only
        #
        # Default: false
        skip_queue_declaration: false

        # The duration (in seconds) that the received messages are hidden from subsequent
        # retrieve requests after being retrieved by a ReceiveMessage request
        #
        # Default: 0
        visibility_timeout: 0

        # The duration (in seconds) for which the call waits for a message to arrive
        # in the queue before returning. If a message is available, the call returns
        # sooner than WaitTimeSeconds. If no messages are available and the wait time
        # expires, the call returns successfully with an empty list of messages.
        #
        # Default: 0
        wait_time_seconds: 0

        # Queue name.
        #
        # Default: default
        queue: default

        # List of the AWS SQS attributes https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html.
        attributes:
          DelaySeconds: 0
          MaximumMessageSize: 262144
          MessageRetentionPeriod: 345600
          ReceiveMessageWaitTimeSeconds: 0
          VisibilityTimeout: 30
        # Tags don't have any semantic meaning. Amazon SQS interprets tags as character
        #	strings.
        tags:
          test: "tag"

    test-local-5:
      # Driver name
      #
      # This option is required
      driver: nats

      # Driver's configuration
      #
      # Should not be empty
      config:
        # Pipeline priority
        #
        # If the job has priority set to 0, it will inherit the pipeline's priority. Default: 10.
        priority: 2

        # NATS prefetch
        #
        # Messages to read into the channel
        prefetch: 100

        # Consume any payload type (not only Jobs structured)
        #
        # Default: false
        consume_all: false

        # NATS subject
        #
        # Default: default
        subject: default

        # NATS stream
        #
        # Default: default-stream
        stream: foo

        # The consumer will only start receiving messages that were created after the consumer was created
        #
        # Default: false (deliver all messages from the stream beginning)
        deliver_new: true

        # Consumer rate-limiter in bytes https://docs.nats.io/jetstream/concepts/consumers#ratelimit
        #
        # Default: 1000
        rate_limit: 100

        # Delete the stream when after pipeline was stopped
        #
        # Default: false
        delete_stream_on_stop: false

        # Delete message from the stream after successful acknowledge
        #
        # Default: false
        delete_after_ack: false

  # list of pipelines to be consumed by the server automatically at the start, keep empty if you want to start consuming manually
  consume:
    [
        "test-local",
        "test-local-1",
        "test-local-2",
        "test-local-3",
        "test-local-4",
        "test-local-5",
    ]

grpc:
  # GRPC address to listen
  #
  # This option is required
  listen: "tcp://localhost:9001"

  # Proto file to use, multiply files supported [SINCE 2.6]
  #
  # This option is required
  proto:
    - "first.proto"
    - "second.proto"

  # GRPC TLS configuration
  #
  # This section is optional
  tls:
    # Path to the key file
    #
    # This option is required
    key: ""

    # Path to the certificate
    #
    # This option is required
    cert: ""

    # Path to the CA certificate, defines the set of root certificate authorities that servers use if required to verify a client certificate. Used with the `client_auth_type` option.
    #
    # This option is optional
    root_ca: ""

    # Client auth type.
    #
    # This option is optional. Default value: no_client_certs. Possible values: request_client_cert, require_any_client_cert, verify_client_cert_if_given, require_and_verify_client_cert, no_client_certs
    client_auth_type: no_client_certs

  # Maximum send message size
  #
  # This option is optional. Default value: 50 (MB)
  max_send_msg_size: 50

  # Maximum receive message size
  #
  # This option is optional. Default value: 50 (MB)
  max_recv_msg_size: 50

  # MaxConnectionIdle is a duration for the amount of time after which an
  #	idle connection would be closed by sending a GoAway. Idleness duration is
  #	defined since the most recent time the number of outstanding RPCs became
  #	zero or the connection establishment.
  #
  # This option is optional. Default value: infinity.
  max_connection_idle: 0s

  # MaxConnectionAge is a duration for the maximum amount of time a
  #	connection may exist before it will be closed by sending a GoAway. A
  #	random jitter of +/-10% will be added to MaxConnectionAge to spread out
  #	connection storms.
  #
  # This option is optional. Default value: infinity.
  max_connection_age: 0s

  # MaxConnectionAgeGrace is an additive period after MaxConnectionAge after
  #	which the connection will be forcibly closed.
  max_connection_age_grace: 0s8h

  # MaxConnectionAgeGrace is an additive period after MaxConnectionAge after
  #	which the connection will be forcibly closed.
  #
  # This option is optional: Default value: 10
  max_concurrent_streams: 10

  # After a duration of this time if the server doesn't see any activity it
  #	pings the client to see if the transport is still alive.
  #	If set below 1s, a minimum value of 1s will be used instead.
  #
  # This option is optional. Default value: 2h
  ping_time: 1s

  # After having pinged for keepalive check, the server waits for a duration
  #	of Timeout and if no activity is seen even after that the connection is
  #	closed.
  #
  # This option is optional. Default value: 20s
  timeout: 200s

  # Usual workers pool configuration
  pool:
    num_workers: 2
    max_jobs: 0
    allocate_timeout: 60s
    destroy_timeout: 60

# [SINCE 2.6] TCP plugin
tcp:
  # The list of TCP servers to start. There are can be any number of servers you want to allocate.
  servers:

    # Server name. Can be any string. Will be sent to the worker in the context.
    #
    # Default: none.
    server1:

      # Address to listen.
      #
      # Error if empty.
      addr: 127.0.0.1:7778

      # Data packets delimiter. Every send should end either with EOF or with the delimiter.
      #
      # Default: CRLF (\r\n)
      delimiter: "\r\n"

      # Chunks that RR uses to read the data. In MB. If you expect big payloads on a TCP server, to reduce `read` syscalls, would be a good practice to use a fairly big enough buffer.
      #
      # Default: 1MB
      read_buf_size: 1
    server2:
      addr: 127.0.0.1:8811
      read_buf_size: 10
    server3:
      addr: 127.0.0.1:8812
      delimiter: "\r\n"
      read_buf_size: 1

  pool:
    command: ""
    num_workers: 5
    max_jobs: 0
    allocate_timeout: 60s
    destroy_timeout: 60s

# [SINCE 2.6] Fileserver to serve static files.
fileserver:
  # File server address
  #
  # Error on empty
  address: 127.0.0.1:10101
  # Etag calculation. Request body CRC32.
  #
  # Default: false
  calculate_etag: true

  # Weak etag calculation
  #
  # Default: false
  weak: false

  # Enable body streaming for the files more than 4KB
  #
  # Default: false
  stream_request_body: true

  serve:
    # HTTP prefix
    #
    # Error on empty
    - prefix: "/foo"

      # Directory to serve
      #
      # Default: "."
      root: "../../../tests"

      # When set to true, the server tries minimizing CPU usage by caching compressed files
      #
      # Default: false
      compress: false

      # Expiration duration for inactive file handlers. Units: seconds.
      #
      # Default: 10, use a negative value to disable it.
      cache_duration: 10

      # The value for the Cache-Control HTTP-header. Units: seconds
      #
      # Default: 10 seconds
      max_age: 10

      # Enable range requests
      # https://developer.mozilla.org/en-US/docs/Web/HTTP/Range_requests
      #
      # Default: false
      bytes_range: true

    - prefix: "/foo/bar"
      root: "../../../tests"
      compress: false
      cache_duration: 10
      max_age: 10
      bytes_range: true

## RoadRunner internal container configuration (docs: https://github.com/spiral/endure).
endure:
  # How long to wait for stopping.
  #
  # Default: 30s
  grace_period: 30s

  # Print graph in the graphviz format to the stdout (paste here to visualize https://dreampuf.github.io)
  #
  # Default: false
  print_graph: false

  # Logging level. Possible values: "debug", "info", "warn", "error", "panic", "fatal".
  #
  # Default: "error"
  log_level: error
```