# zookeeper

## Description

Get a Kafka cluster automated and manage its lifecycle with Juju and charms.

## Usage

See ```config.yaml``` for the full list of options and descriptions.

### Adding extra configurations

Kafka broker charms allows to append configurations to config files such as ```zookeeper.properties``` or service ```override.conf```.

It is yaml-formatted list of ```key: value``` field. Can be added in a bundle with:

```
    zookeeper:
        charm: cs:zookeeper
        options:
            server-properties: |
                maxClientCnxns: 0
                initLimit: 5
                syncLimit: 2
```

Or using CLI, as for example:

```
    $ juju config kafka-broker server-properties="maxClientCnxns: 0
      initLimit: 5
      syncLimit: 2"
```

IMPORTANT: in case of a conflict between option OR charm's decision as for a relation, for example; the relation setup (i.e. the value defined by the charm for that given configuration) will be used instead of the value set in the option.

### Certificate management

Zookeeper uses a keystore to contain the certificate and keys for TLS. Besides, it uses a truststore with all the trusted certificates for each unit. Each listener gets its own keystore / truststore.

To disable certificates, do the following steps:
1) Remove any ssl_* configs or certificates relation
2) if keystore is set to empty, certificates will not be used for that relation
3) case disabling certificates for peer relation, set ```sslQuorum: false```

### JMX Exporter support

Kafka's JVM can export information to Prometheus. Setup the integration
with the following options:

```
    jmx_exporter_version: v0.12
    jmx_exporter_url: Maven's URL from where JMX jar can be downloaded.
        Format it to replace the versions.
    jmx-exporter-port: Port to be exposed by the exporter for prometheus.
    jmx_exporter_labels: comma-separated list of key=value tags.
    jmx_exporter_use_internal: use internal endpoint of Prometheus relation
```

The setup above will render the option in override.conf service:

```
    -javaagent:/opt/prometheus/jmx_prometheus_javaagent.jar=9409:/opt/prometheus/prometheus.yml
```

### Authentication with Kerberos

To set Kerberos, there are two steps that needs to be taken into consideration. First step, set the correct configuration on:

```
    kerberos-protocol
    kerberos-realm
    kerberos-domain
    kerberos-kdc-hostname
    kerberos-admin-hostname
```

Once the units are deployed, they will be blocked, waiting for the keytab file. That should be added per-unit, according to its hostname, using actions. Check the actions documentation for more details.

## Developing

Create and activate a virtualenv with the development requirements:

    virtualenv -p python3 venv
    source venv/bin/activate
    pip install -r requirements-dev.txt
