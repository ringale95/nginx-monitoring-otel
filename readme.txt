# Configuring otel to collect data from nginx


  - Create folder otel-contrib: `mkdir otel-contrib`
- Download the otel-contrib from :
   `sudo wget https://github.com/open-telemetry/opentelemetry-collector-releases/releases/download/v0.90.0/otelcol-contrib_0.90.0_linux_amd64.tar.gz`
- Unzip:  `sudo tar -xvzf otelcol-contrib_0.90.0_linux_amd64.tar.gz`
- Give exectuable access to binary: `chmod +x otelcol-contrib`
- Check verion: ` ./otelcol-contrib --version`
- Now create config.yaml `touch config.yaml`

Code inside config.yaml:
<pre>
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: localhost:4317
      http:
        endpoint: localhost:4318
  nginx:
    endpoint: "http://localhost:80/status"
    collection_interval: 10s
  filelog:
    include: ["/var/log/nginx/*.log"]
    start_at: end
    operators:
      - type: json_parser
        timestamp:
          parse_from: body.time
          layout: "%Y-%m-%dT%H:%M:%S%z"
        parse_from: body

processors:
  attributes:
    actions:
      - key: response_time
        action: insert
        from_attribute: body.response_time
</pre>
- Run : `./otelcol-contrib --config ./config.yaml`
