# Steps to Deploy Sentry in GKE (BoldSign):

This document describes the step-by-step process for deploying Sentry in your GKE cluster.

## Connect to Google Kubernetes (GKE):

* Get the GKE command line access command from the GKE portal.
* Click on the connect button and get the command.
* Open the Google Cloud Shell and run the command

    ![GCP connect](screen-shots/gcp-connect.png)

## Install Sentry using Helm Chart:

* Create a new namespace named `dev-sentry` for Sentry workloads.

   ![create-namespace](screen-shots/namespace-create.png)
   
* Run the following commands in Google Cloud Shell to update and add the Sentry charts ClickHouse repository:

    ``` cmd
    helm repo update
    helm repo add sentry https://sentry-kubernetes.github.io/charts
    ```
    ![Repo Update](screen-shots/Repo-Update.png)
* Clone the repository below to your local machine.
    https://github.com/sentry-kubernetes/charts

    ![Clone Repo](screen-shots/Clone-sentry-repo.png)
* Navigate to the folder below, where you can find the `Values.yaml` file.
    ```
    cd .\charts\charts\sentry\
    notepad .\values.yaml

    ```
    ![values-yaml](screen-shots/values-yaml.png)
    ![Open values.yaml](screen-shots/open-values-file.png)

* In the file, provide a `Valid username and password` for the Sentry account.

    ![email-user](screen-shots/email-user.png)

* Change the `hook` and `cleanup` time to `1000` seconds, as the deployment may take longer.

    ![hook-deadline](screen-shots/hook-deadline.png)
    ![cleaup-deadline](screen-shots/cleaup-deadline.png)

* Ensure proper CPU and memory allocation for all services to avoid memory-related issues.

    ![cpu-mem-resource](screen-shots/cpu-mem-resource.png)

* Refer to the memory details below to adjust CPU and memory settings in the Values.yaml file.
* Change the system_URL for Sentry with the domain we are using.
* Set system_public to true to create the Sentry site accessible via the public network.

    ![sytem-url](screen-shots/sytem-url.png)

* Enable autoscaler if necessary for all services.

    ![autoscaling](screen-shots/autoscaling.png)

* Set the `retention` period to `30` days, to delete the events after 30 days.

    ![retention-default](screen-shots/retention-default.png)

* Change the service to a `NodePort` service type.

    ![service-nodeport](screen-shots/service-nodeport.png)

* Disable NGINX Ingress as it is not supported.

    ![gke-ingress](screen-shots/gke-ingress.png)

* Enable Ingress and set the values to `gke` for Google Ingress controller. Include the following `annotation: kubernetes.io/ingress.allow-http: false`.
* Provide the name of the secret created for TLS.

   ![Secret-tls](screen-shots/secret-tls.png)

* After making all necessary changes in the Values.yaml file, run the command from the specified location.
* Make sure to set a timeout, or you may encounter issues due to time constraints.

    ``` cmd
    helm install sentry sentry/sentry -n dev-sentry -f values.yaml --wait --timeout 45m
    ```

    ![helm-install-sentry](screen-shots/helm-install-sentry.png)

* Once deployed some post install services are created for sentry and all the pod will be changed to running status.
* Create a new domain for sentry and map with the ingress IP.

    ![sentry-deploy1](screen-shots/sentry-deploy1.png)
    ![sentry-deploy2](screen-shots/sentry-deploy2.png)
    ![sentry-deploy3](screen-shots/sentry-deploy3.png)

## Set Proper Memory Details (Values.yaml):

* Set the proper memory in the Values.yaml file. Refer to the table below for details.

    | Service | CPU Request  | CPU Limit | Memory Request  | Memory Limit |
    | ------------- | ------------- | ------------- | ------------- | ------------- |
    | vroom | 100m  |  - | 500Mi | 500Mi|
    | relay | 100m  | -  | 2Gi | 2Gi |
    | web | 500m  |  - | 2Gi | 2Gi |
    | worker | 500m | - | 2Gi | 2Gi |
    | ingestReplayRecordings | 10m  | -  | 200Mi | 200Mi |
    | ingestProfiles |  100m | -  | 500Mi | 500Mi |
    | ingestOccurrences | 100m  |  - | 500Mi | 500Mi |
    | ingestMonitors | 10m  | -  | 200Mi | 200Mi |
    | billingMetricsConsumer | 10m  | -  | 200Mi | 200Mi |
    | genericMetricsConsumer | 10m  | -  | 500Mi | 500Mi |
    | metricsConsumer | 10m  | -  | 500Mi | 500Mi |
    | cron | 100m  | -  | 500Mi | 500Mi |
    | subscriptionConsumerEvents | 50m  | -  | 500Mi | 500Mi |
    | subscriptionConsumerSessions | 50m  | -  | 500Mi | 500Mi |
    | subscriptionConsumerTransactions | 50m  | -  | 500Mi | 500Mi |
    | postProcessForwardErrors | 10m  | -  | 500Mi | 500Mi |
    | postProcessForwardTransactions | 10m  | -  | 500Mi | 500Mi |
    | postProcessForwardIssuePlatform | 10m  | -  | 500Mi | 500Mi |
    | subscriptionConsumerGenericMetrics | 50m  | -  | 500Mi | 500Mi |
    | subscriptionConsumerMetrics | 50m  | -  | 500Mi | 500Mi |
    | snuba-api | 50m  | -  | 200Mi | 200Mi |
    | snuba-consumer | 50m  | -  | 200Mi | 200Mi |
    | snuba-outcomesConsumer | 50m  | -  | 200Mi | 200Mi |
    | snuba-replacer | 50m  | -  | 200Mi | 200Mi |
    | snuba-metricsConsumer | 50m  | -  | 200Mi | 200Mi |
    | snuba-subscriptionConsumerEvents | 50m  | -  | 200Mi | 200Mi |
    | snuba-genericMetricsCountersConsumer | 50m  | -  | 200Mi | 200Mi |
    | snuba-genericMetricsDistributionConsumer | 50m  | -  | 200Mi | 200Mi |
    | snuba-genericMetricsSetsConsumer | 50m  | -  | 200Mi | 200Mi |
    | snuba-subscriptionConsumerMetrics | 50m  | -  | 200Mi | 200Mi |
    | snuba-subscriptionConsumerTransactions | 50m  | -  | 200Mi | 200Mi |
    | snuba-subscriptionConsumerSessions | 50m  | -  | 200Mi | 200Mi |
    | snuba-replaysConsumer | 50m  | -  | 200Mi | 200Mi |
    | snuba-sessionsConsumer | 50m  | -  | 200Mi | 200Mi |
    | snuba-transactionsConsumer | 50m  | -  | 200Mi | 200Mi |
    | snuba-profilingProfilesConsumer | 50m  | -  | 200Mi | 200Mi |
    | snuba-profilingFunctionsConsumer | 50m  | -  | 200Mi | 200Mi |
    | snuba-issueOccurrenceConsumer | 50m  | -  | 200Mi | 200Mi |
    | snubaInit | 500m  | -  | 2Gi | 2Gi |
    | rabbitmq | 200m  | -  | 500Mi | 500Mi |
    | snuba-subscriptionConsumerTransactions | 50m  | -  | 200Mi | 200Mi |

## Solution to Fix the Issue in Snuba Consumer Metrics:

* After deploying Sentry, you may encounter an issue with the consumer. To resolve the issue, follow the steps below.
* Connect to the Sentry ClickHouse pod using the command below.

    ``` cmd
    kubectl exec -it sentry-clickhouse-0 -n dev-sentry -- bash
    ```
* Access the ClickHouse table and display the number of tables using the command show tables.
* Delete the metrics table in ClickHouse and create a new table using the command below.

    ``` cmd
    clickhouse-client -h sentry-clickhouse
    SHOW TABLES;
    DESCRIBE TABLE default.metrics_raw_v2_dist;
    SELECT COUNT(*) FROM default.metrics_raw_v2_dist;
    DROP table default.metrics_raw_v2_dist ON CLUSTER 'sentry-clickhouse' SYNC;

    CREATE TABLE default.metrics_raw_v2_dist ON CLUSTER 'sentry-clickhouse' 
    (
    `use_case_id` LowCardinality(String),
    `org_id` UInt64,
    `project_id` UInt64,
    `metric_id` UInt64,
    `timestamp` DateTime,
    `tags.key` Array(UInt64),
    `tags.value` Array(UInt64),
    `metric_type` LowCardinality(String),
    `set_values` Array(UInt64),
    `count_value` Float64,
    `distribution_values` Array(Float64),
    `materialization_version` UInt8,
    `retention_days` UInt16,
    `partition` UInt16,
    `offset` UInt64,
    `timeseries_id` UInt32
    )
    ENGINE = Distributed('sentry-clickhouse', 'default', 'metrics_raw_v2_local', sipHash64('timeseries_id'));
    ```






## Change Below Configuration for Performance:

* Due to heavy traffic in event capture, sometimes Sentry stops listing recent errors. This issue can be resolved by configuring the following performance settings.

    ![sentry-relay-config](screen-shots/sentry-relay-config.png)
    ![sentry-relay-config2](screen-shots/sentry-relay-config2.png)

* Navigate to the Sentry Relay configuration, edit it, and include the following changes. Then, refresh the Sentry Relay workload.

    `cache.project_grace_period` - Increasing this value may help when the upstream is unreachable, for example, due to network issues.

    `cache.project_expiry` - Increasing this value can reduce the frequency of project expiry due to network issues.

    `cache.event_buffer_size` - This setting determines how many events Relay can buffer in its local queue before it starts rejecting new 
    events. Increasing this value will prevent Relay from forwarding received messages to Sentry during network issues.

    `limits.max_concurrent_requests` -  This sets the number of concurrent requests your Relay instance can send to the upstream (Sentry). 
    If your event volume or connection latency to Sentry is high, you can increase this value to gain additional throughput.

## SMTP configuration
### Enabling SMTP in Sentry

#### Overview
SMTP (Simple Mail Transfer Protocol) is used in Sentry to send email notifications, including alerts and system messages. To enable SMTP in Sentry, you must configure the email settings in the `values.yaml` file. This document provides a step-by-step guide to setting up SMTP for Sentry.

#### 1. Configuring SMTP in `values.yaml`
To enable SMTP, modify the `values.yaml` file with the following configurations.

##### Basic SMTP Configuration
```yaml
mail:
  backend: smtp  # Set SMTP as the backend for sending emails.
  useTls: true    # Enable TLS encryption for SMTP connections.
  useSsl: false   # Disable SSL encryption for SMTP.
  username: "your-smtp-username"  # Provide the SMTP username.
  password: "your-smtp-password"  # Provide the SMTP password.
  host: "smtp.your-email-provider.com"  # Set the SMTP host.
  port: 587      # Default SMTP port for TLS.
  from: "your-email@example.com"  # Set the email address used to send the emails.
```

##### Example: Configuring SMTP with Gmail
```yaml
mail:
  backend: smtp
  useTls: true
  useSsl: false
  username: "priya.sf4360@gmail.com"
  password: ""  
  existingSecret: "smtp-secret"  # Use the secret name created
  existingSecretKey: "password"  # Key inside the secret
  port: 587
  host: "smtp.gmail.com"
  from: "BDS Sentry Logs"
```

##### Example: Configuring SMTP with Amazon SES
```yaml
mail:
  backend: smtp
  useTls: true
  useSsl: false
  username: "AKIARYUHIOTLS7B3CQ4C" 
  password: ""  
  existingSecret: "smtp-secret"  # Use the secret name created
  existingSecretKey: "password"  # Key inside the secret
  port: 587
  host: "MAIL-smtp.us-west-1.amazonaws.com" 
  from: "sentry@boldbi.com"
```

#### 2. Applying the Configuration
After modifying `values.yaml`, apply the changes by running the following command:
```sh
helm upgrade --install sentry sentry-chart -f values.yaml -n sentry-namespace
```
Ensure that Sentry is restarted to apply the new SMTP settings.

#### 3. Verifying SMTP Configuration
To check if the SMTP settings are working correctly, perform the following:
- Log in to your Sentry instance.
- Navigate to **Admin Settings** > **Mail**.

    ![SMTP configuration](screen-shots/smtp-configuration.png)

- Click **Test Mail** to verify that emails are being sent successfully.

    ![Sample Mail](screen-shots/Sample-mail.png)
    
- Check the logs using:
  ```sh
  kubectl logs -n sentry sentry-web-<pod_name>
  ```

#### Conclusion
Enabling SMTP in Sentry ensures that email notifications are delivered properly. By configuring the `values.yaml` file and applying the changes using Helm, you can set up and test the SMTP functionality for your Sentry instance. If any issues arise, check the SMTP logs and ensure that the credentials and SMTP settings are correct.


# References:

For more details, please refer to the link below.

* https://github.com/sentry-kubernetes/charts/issues/1172

* https://develop.sentry.dev/self-hosted/troubleshooting/#kafka

* https://develop.sentry.dev/architecture/

* https://github.com/sentry-kubernetes/charts/issues/1152

* https://github.com/getsentry/snuba/issues/4897

* https://github.com/getsentry/self-hosted/issues/478#issuecomment-666254392

* https://github.com/sentry-kubernetes/charts/tree/develop

* https://github.com/sentry-kubernetes/charts/tree/develop/sentry

* https://stackoverflow.com/questions/74628888/can-not-install-sentry-on-premise-using-helm-chart

* https://github.com/helm/charts/issues/11904

* https://stackoverflow.com/questions/51975069/helm-test-failure-timed-out-waiting-for-the-condition

* https://docs.sentry.io/product/relay/operating-guidelines/
