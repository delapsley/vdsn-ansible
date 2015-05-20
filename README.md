# License

Copyright 2015 Cisco Systems, Inc

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

# Virtual Distributed VXLAN Service Node

To set this up on your mac:

* use brew to install ansible on your mac.
* install vagrant
* install a hypervisor on your mac (either virtualbox or vmware)
* Edit the CLUSTER_CONFIG settings in the Vagrantfile
* Run "vagrant up". This will stand up the virtual machines and also configure
 them.
* It will take a while for the playbook to complete (about 20 minutes). Once
  it is done, you can log into the VM and check that mcrouter is installed:


    dlapsley@defy:~$ vagrant ssh mcp1.dav1
    vagrant@mcp1:~$ mcrouter
    I0412 17:23:08.517470 24649 main.cpp:430] mcrouter
    E0412 17:23:08.517837 24649 mcrouter_config.cpp:52] no configuration source
    mcrouter 1.0
    usage: mcrouter [options] -p port(s) -f config
    
    libmcrouter options:
    
      Startup
            --new-ascii-parser                           Enables a new parser for ASCII protocol inside of AsyncMcClient
            --asynclog-disable                           disable async log file spooling
        -a, --async-dir                                  container directory for async storage spools [default: "/var/spool/mcrouter"]
            --use-asynclog-version2                      Enable using the asynclog version 2.0
            --num-proxies                                adjust how many proxy threads to run [default: 1]
            --disable-priorities                         don't use event base priorities
            --no-realtime                                when run as root, mcrouter is run with realtime priority to improve latency. Use this option to disable realtime-priority when run as root
            --big-value-split-threshold                  If 0, big value route handle is not part of route handle tree,else used as threshold for splitting big values internally [default: 0]
            --fibers-max-pool-size                       Maximum number of preallocated free fibers to keep around [default: 1000]
            --fibers-stack-size                          Size of stack in bytes to allocate per fiber. 0 means use fibers library default. [default: (16 * 1024)]
            --fibers-debug-record-stack-size             Record exact amount of fibers stacks used (expensive: debug only!)
            --runtime-vars-file                          Path to the runtime variables file. [default: ""]
            --file-observer-poll-period-ms               How often to check inotify for updates on the tracked files. [default: 100]
            --file-observer-sleep-before-update-ms       How long to sleep for after an update occured (a hack to avoid partial writes). [default: 1000]
    
      Network
        -K, --keepalive-count                            set TCP KEEPALIVE count, 0 to disable [default: 0]
        -i, --keepalive-interval                         set TCP KEEPALIVE interval parameter in seconds [default: 60]
        -I, --keepalive-idle                             set TCP KEEPALIVE idle parameter in seconds [default: 300]
            --reset-inactive-connection-interval         Will close open connections without any activity after at most 2 * interval ms. If value is 0, connections won't be closed. [default: 60000]
            --tcp-rto-min                                adjust the minimum TCP retransmit timeout (ms) to memcached [default: -1]
            --target-max-inflight-requests               Maximum inflight requests allowed per target per thread (0 means no throttling) [default: 0]
            --target-max-pending-requests                Only active if target-max-inflight-requests is nonzero. Hard limit on the number of requests allowed in the queue per target per thread.  Requests that would exceed this limit are dropped immediately. [default: 100000]
            --target-max-shadow-requests                 Hard limit on the number of shadow requests allowed in the queue per target per thread.  Requests that would exceed this limit are dropped immediately. [default: 1000]
            --no-network                                 Debug only. Return random generated replies, do not use network.
            --proxy-max-inflight-requests                If non-zero, sets the limit on maximum incoming requests that will be routed in parallel by each proxy thread.  Requests over limit will be queued up until the number of inflight requests drops. [default: 0]
            --proxy-max-throttled-requests               Only active if proxy-max-inflight-requests is non-zero. Hard limit on the number of requests to queue per proxy after there are already proxy-max-inflight-requests requests in flight for the proxy. Further requests will be rejected with an error immediately. 0 means disabled. [default: 0]
            --pem-cert-path                              Path of pem-style certificate for ssl [default: ""]
            --pem-key-path                               Path of pem-style key for ssl [default: ""]
            --pem-ca-path                                Path of pem-style CA cert for ssl [default: ""]
            --destination-rate-limiting                  If not enabled, ignore "rates" in pool configs.
            --enable-qos                                 If enabled, sets the spacified qos level in the ip packages.
            --default-qos-class                          Default qos class to use if qos is enabled and not specified. [default: 0]
    
      Routing configuration
            --constantly-reload-configs
            --disable-reload-configs
        -f, --config-file                                load configuration from file [default: ""]
            --config-str                                 Configuration string provided as a command line argument [default: ""]
        -R, --route-prefix                               default routing prefix (ex. /oregon/prn1c16/) [default: "/././"]
            --disable-miss-on-get-errors                 Disable reporting get errors as misses
            --group-remote-errors                        Groups all remote (i.e. non-local) errors together, returning a single result for all of them: mc_res_remote_error
            --send-invalid-route-to-default              Send request to default route if routing prefix is not present in config
            --enable-flush-cmd                           Enable flush_all command
    
      TKO probes
            --disable-tko-tracking                       Disable TKO tracker (marking a host down for fast failover after a number of failures, and sending probes to check if the server came back up).
        -r, --probe-timeout-initial                      TKO probe retry initial timeout in ms [default: 10000]
            --probe-timeout-max                          TKO probe retry max timeout in ms [default: 60000]
            --timeouts-until-tko                         Mark as TKO after this many failures [default: 3]
            --maximum-soft-tkos                          The maximum number of machines we can mark TKO if they don't have a hard failure. [default: 40]
            --latency-window-size                        The number of samples to track when computing moving average latency for a proxy destination. [default: 16]
    
      Timeouts
        -t, --server-timeout                             server timeout in ms (DEPRECATED try to use cluster-server-timeout and regional-server-timeout) [default: 1000]
            --cluster-pools-timeout                      server timeout for cluster pools in ms. Default value 0 means using deprecated server-timeout value for the flag [default: 0]
            --regional-pools-timeout                     server timeout for regional pools in ms. Default value 0 means using deprecated server-timeout value for the flag [default: 0]
            --cross-region-timeout-ms                    Timeouts for talking to cross region pool. If specified (non 0) takes precedence over every other timeout. [default: 0]
            --cross-cluster-timeout-ms                   Timeouts for talking to pools within same region but different cluster. If specified (non 0) takes precedence over every other timeout. [default: 0]
            --within-cluster-timeout-ms                  Timeouts for talking to pools within same cluster. If specified (non 0) takes precedence over every other timeout. [default: 0]
            --same-connection-any-timeout                If enabled - same connection to a destination may be used for requests with different timeouts.
    
      Logging
            --stats-root                                 Root directory for stats files [default: "/var/mcrouter/stats"]
            --stats-logging-interval                     Time in ms between stats reports, or 0 for no logging [default: 10000]
            --logging-rtt-outlier-threshold-us           surpassing this threshold rtt time means we will log it as an outlier. 0 (the default) means that we will do no logging of outliers. [default: 0]
            --stats-async-queue-length                   Asynchronous queue size for logging. [default: 50]
            --disable-failure-logging                    Disable failure logging.
    
      Standalone mcrouter options
        -L, --log-path                                   Log file path [default: ""]
        -p, --port                                       Port(s) to listen on (comma separated) [default: ]
            --ssl-port                                   SSL Port(s) to listen on (comma separated) [default: ]
            --listen-sock-fd                             Listen socket to take over [default: -1]
        -b, --background                                 Run in background
        -m, --managed-mode                               Managed mode (auto restart on crash)
        -n, --connection-limit                           Connection limit [default: 65535]
            --max-global-outstanding-reqs                Maximum requests outstanding globally (0 to disable) [default: (uint32_t)((1024 * 1024 * 200) / (3 * 1024))]
            --max-client-outstanding-reqs                Maximum requests outstanding per client (0 to disable) [default: (uint32_t)((1024 * 1024 * 100) / (3 * 1024))]
            --reqs-per-read                              Adjusts server buffer size to process this many requests per read. Smaller values may improve latency. [default: 0]
    
    Misc options:
            --proxy-threads                              Like --num-proxies, but also accepts 'auto' to start one thread per core
        -D, --debug-level                                set debug level
        -d, --debug                                      increase debug level (may repeat)
        -h, --help                                       help
        -V, --version                                    version
        -v, --verbose                                    verbose
            --validate-config                            Check config and exit immediately with good or error status
    
    RETURN VALUE
        2                                                On a problem that might be resolved by restarting later.
        3                                                On a problem that will definitely not be resolved by restarting.
        
        
       
