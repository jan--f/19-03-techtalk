<!-- .slide: data-state="cover" id="prometheus-cover-page" data-timing="20" data-menu-title="Monitoring Ceph with Prometheus" -->

<div class="title">
    <h1>Monitoring Ceph with Prometheus</h1>
    <h2></h2>
</div>

<div class="row presenters">
    <div class="presenter presenter-1">
        <h3 class="name">Jan Fajerski</h3>
        <h3 class="job-title">Senior Software Engineer</h3>
        <h3 class="email"><a href="mailto:jan.fajerski@suse.com">jan.fajerski@suse.com</a></h3>
    </div>
</div>

Note:
mention potential gotometting disconntect


<!-- .slide: data-state="normal" id="agenda" data-menu-title="Agenda" -->
## Motivation

Intro to Prometheus and PromQL. Demonstrate how to customize the default
monitoring stack for a given cluster.

## Agenda

* Intro Prometheus
* Exporters
* Configuration
* Deployment considerations
* Query language


<!-- .slide: data-state="normal" id="prometheus-intro" data-timing="60s" style="z-index:1;"-->
## Prometheus

<h3>
<q>
A leading open-source monitoring solution.
</q>
</h3>

* Originally developed at SoundCloud (started 2012), inspired by Borgmon
* CNCF member project (2nd ever Incubator Project)
* Built for highly-dimensional (_numerical_) data, simple operations, scalability
* Powerful query language _PromQL_
* Widely adopted

<img class="fragment" data-src="images/prom-users.png" style="height: 600px; position: absolute;
top: 200px; left: 400px; z-index:0;"/>

<img class="fragment" data-src="images/SUSE/SUSE-logo.svg" style="width: 200px; position: absolute;
top: 500px; left: 100px; z-index:0;"/>

Note:
* _NOT_ a log analysis system!!!
* scalability often considered to be a result of the pull based model
* also users: SUSE and most SES customers


<!-- .slide: data-state="normal" id="alertmanager-intro" data-timing="60s" -->
## Alertmanager

<h3>
<q cite="https://github.com/prometheus/alertmanager">
The Alertmanager handles alerts sent by client applications such as the Prometheus server. It takes care of deduplicating, grouping, and routing them to the correct receiver integrations such as email, PagerDuty, or OpsGenie. It also takes care of silencing and inhibition of alerts.
</q>
</h3>

* Only component aware of clustering
* Typically deployed alongside Prometheus
* Part of the Prometheus project
* Can be used without Prometheus

Note:
* cli flags for clustering; list of peers to query.
* Alerts are generated by Prometheus
* Prometheus interacts with Alertmanagers http API


<!-- .slide: data-state="normal" id="exporters-intro" data-timing="60s" -->
## Exporters

* Simple plain-text interface via HTTP
* _A lot_ of [existing exporters](https://github.com/prometheus/docs/blob/master/content/docs/instrumenting/exporters.md)

<div class="slide-section fragment" style="padding-top: 40px; padding-left:130px">
<pre><code class="xquery">
# TYPE ceph_my_metric counter
# HELP ceph_my_metric metric description
ceph_my_metric{labelname="labelvalue", ...} metric_value
ceph_my_metric{labelname="another_labelvalue", ...} metric_value
</code> </pre>
</div>

<p class="fragment" style="padding-top: 200px;">More on writing exporters: https://prometheus.io/docs/instrumenting/writing_exporters/</p>

Note:
* node_exporter is the first to look up
* Ideally code is instrumented directly, client libraries exist
* Basic metrics, no processing. Give only byte count not rate for example
* Goal is to have a minimal set of metrics, leave metrics out that can be
  derived
* Metrics are meant to give a base unit (like seconds, bytes used)
* our metric can do everything, we'll assign various meanings to it during the
  presentation