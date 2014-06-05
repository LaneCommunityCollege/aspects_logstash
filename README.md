#aspects_logstash

Install and configure Logstash log manager. Note that the logstash-web agent is unsupported.

##Requirements

Set ```hash_behaviour=merge``` in your ansible.cfg file.

You need to download the logstash tar to files/downloads https://download.elasticsearch.org/logstash/logstash/logstash-1.4.1.tar.gz

##Role Variables

###aspects_logstash_install
Whether or not to run install tasks.
Values: true|false
Default: true

You should override this to ```false``` after your initial install so that you don't keep uploading and uncompressing the logstash tar file.

###aspects_logstash_user
Dictionary with subvalues:
####username: root
Which user to run Logstash with.
Values: any system user
Default: root
####group: root
Which group to run Logstash with.
Values: any system group
Default: root

Defaults to root because of issues getting Logstash to read log files when using a different user. There are commented out sections in the role code that should help set up a logstash user. If you can make it work, please submit a pull request.

###aspects_logstash_use_daemon
Include the daemon tasks and handlers.
Values: true|false
Default: true

###aspects_logstash_bin_filename
The filename of the logstash download. Used in the unarchive task.
Values: Any file name that points to the tar file downloaded from logstash.
Default: "logstash-1.4.1.tar.gz"

###aspects_logstash_bin_dirname
The directory name that the unarchive task creates.
Values: Whatever the logstash unarchive task creates.
Default: logstash-1.4.1

###aspects_logstash_filesystem:
Dictionary that helps create and set permissions for the directories and files Logstash uses.

####binary
The symlink to the unarcived logstash directory
Values: where you want logstash to live
Default: "/opt/logstash"
You should not change this, it is embedded in several files. This should likely be removed eventually, or else the init scripts need to be templated. 
####logs: 
The directory where the logstash log files live.
Values: any directory.
Default: "/var/log/logstash"
This is unlikely to change. Set as a variable to support potential edge cases.

####conf
Where the logstash configuration lives
Values: Absolute path to the conf.d directory or .conf file logstash should use.
Default: "/etc/logstash/conf.d"
####defaults
RedHat and Debian have different folders containing the defaults files used in the init scripts.

```RedHat: "/etc/sysconfig/"```

```Debian: "/etc/default/"```

###aspects_logstash_options:
The variables to use in the defaults files for overriding what is in teh init scripts.

I'm not sure how well this works.

Current default settings:

      daemon:
        enabled: "yes"
        JAVACMD: false
        NAME: "logstash"
        DESC: "Logstash Daemon"
        PATH: "/bin:/usr/bin:/sbin:/usr/sbin"
        LS_USER: "root"
        LS_GROUP: "root"
        LS_HOME: "/opt/logstash"
        LS_HEAP_SIZE: "256m"
        LS_JAVA_OPTS: "-Djava.io.tmpdir=${LS_HOME}"
        LS_LOG_FILE: "/var/log/logstash/logstash.log"
        LS_CONF_DIR: "/etc/logstash/conf.d"
        LS_OPEN_FILES: "16384"
        LS_NICE: "19"
        LS_OPTS: ""
        LS_PIDFILE: "/var/run/logstash.pid"

###apspects_logstash_rules
Dictionary containing logstash configuration.
####patterns
Dictionary of grok patterns. See: http://logstash.net/docs/1.4.1/filters/grok
Has a default pattern for postfix logs.

####inputs, outputs, filters
Dictionary of logstash configuration. See http://logstash.net/docs/1.4.1 for details. The defaults/main.yml file has some commented out examples.

##Dependencies
* aspects_common_packages

##License

MIT
