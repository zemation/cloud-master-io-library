## Viewing audit logs

Audit logs in Google Cloud Platform are a record of events that occur in a particular project or organization. These events include actions taken by users, service accounts, and Google Cloud Platform services. Audit logs capture information about who performed an action, when the action occurred, and what resources were affected by the action.

Audit logs are useful for monitoring and troubleshooting system activity, investigating security incidents, and meeting compliance requirements. They can also be used to create custom dashboards, generate alerts, and perform data analysis.

Google Cloud Platform provides access to audit logs through the Cloud Logging service, which allows users to search, filter, and analyze log data in real-time. Cloud Logging also supports exporting log data to external storage systems for long-term retention and analysis.

To view audit logs in Google Cloud Platform, you can use either the Cloud Console or the Cloud SDK (command-line interface).

#### Viewing audit logs using Cloud Console
1. Open the Cloud Console and select the project for which you want to view the audit logs.
2. In the navigation menu on the left, click on "Logging > Logs Viewer".
3. In the "Logs Viewer" page, you can filter the logs using different options like log name, resource type, severity level, etc.
3. Once you have selected the filter options, click on "Apply filter" to see the audit logs.

#### Viewing audit logs using Cloud SDK
1. Open the terminal and ensure that you have authenticated and have the required permissions to access the audit logs.
2. Run the following command to view the audit logs for a specific project:

<pre><code>
gcloud logging read "protoPayload.@type=type.googleapis.com/google.cloud.audit.AuditLog" --project=[PROJECT_ID]
</code></pre>
Replace [PROJECT_ID] with the ID of the project for which you want to view the audit logs.

3. You can also filter the audit logs by adding additional filter parameters to the above command. For example, to filter the audit logs for a specific time period, you can use the following command:

<pre><code>
gcloud logging read "protoPayload.@type=type.googleapis.com/google.cloud.audit.AuditLog timestamp>='2022-02-01T00:00:00Z' timestamp<='2022-02-28T23:59:59Z'" --project=[PROJECT_ID]
</code></pre>
This will show the audit logs for the month of February 2022.

Note that audit logs are retained for a limited time, so you may not be able to view older audit logs.

Here are some best practices for using audit logs in the Google Cloud Platform:

1. Enable audit logging: Ensure that audit logging is enabled for all Google Cloud services that store or process sensitive data. You can enable audit logging for your project in the Google Cloud Console or by using the Cloud SDK.
2. Configure audit logging retention: Set retention policies for audit logs based on the compliance and regulatory requirements. You can configure retention policies for audit logs in the Google Cloud Console or by using the Cloud SDK.
3. Analyze audit logs: Use Google Cloud's built-in audit log analysis features, such as the Logs Explorer, to analyze audit logs for security incidents, compliance violations, or operational issues.
4. Create alerts based on audit logs: Create alerts based on specific audit log events or patterns to notify the security and operations teams about potential security incidents or compliance violations.
5. Monitor audit log usage: Monitor the usage of audit logs and access to audit log data to ensure that only authorized users have access to sensitive data.
6. Implement a log ingestion and analysis solution: Consider using a log ingestion and analysis solution, such as Google Cloud's Stackdriver Logging, to collect, store, and analyze audit logs from multiple Google Cloud services.