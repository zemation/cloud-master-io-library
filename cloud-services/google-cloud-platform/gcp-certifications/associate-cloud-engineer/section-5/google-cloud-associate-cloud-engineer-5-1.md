## Managing Identity and Access Management (IAM)
Identity and Access Management (IAM) is a system in Google Cloud Platform that controls access to resources. It allows you to manage permissions for users, groups, and service accounts in a structured way. IAM allows you to assign permissions to individual users, groups, or roles, and you can grant or revoke permissions at any time. IAM also provides fine-grained access control, allowing you to grant different levels of access to different resources.

In IAM, you can create and manage custom roles, which are sets of permissions that you can apply to users or service accounts. Roles are defined by a collection of permissions, and they can be applied at the organization, folder, project, or resource level. IAM also provides auditing features that enable you to monitor who has accessed a resource, and when.

IAM provides a unified and centralized way to manage permissions across all Google Cloud services. It allows you to define and enforce your organization’s security policies, and it provides a consistent way to manage access to resources. IAM is a critical component of a secure and well-managed Google Cloud environment.

Google Cloud Platform provides a wide range of predefined IAM (Identity and Access Management) roles that you can use to manage access to Google Cloud resources. These roles are grouped into four basic categories:

- Primitive roles: These roles are the legacy roles provided by Google Cloud Platform and are assigned at the project level. There are three primitive roles: Owner, Editor, and Viewer.

- Predefined roles: These roles are curated by Google and are more fine-grained than primitive roles. Predefined roles are assigned at the project or resource level.

- Custom roles: These roles are created by users to meet their specific requirements. Custom roles can be created at the project or organization level.

- Delegated roles: These roles are granted to a user or service account for a limited time, allowing them to perform a specific task.

In Google Cloud Platform, there are three types of custom IAM roles:

- Basic: A basic custom role is the simplest type of custom role that can be created, which allows you to combine one or more permissions into a single role.

- Predefined: A predefined custom role is a role that is created by a project or organization administrator by copying an existing predefined role and editing it to fit specific requirements.

- Flexible: A flexible custom role is a role that is defined by a custom Role definition file in YAML format. Flexible roles allow you to specify fine-grained access controls that are not possible with basic or predefined roles.

### Viewing IAM policies

You can view IAM policies in Google Cloud Platform using the following steps:

1. Open the Cloud Console and select your project.
2. Click on the Navigation menu icon (≡) and navigate to the "IAM & Admin" page.
3. Click on the "IAM" tab to view the list of roles in your project.
4. Click on a specific role to view the permissions included in that role.
5. Click on the "View Members" option to view the list of members who have been assigned a specific role.
6. To view the IAM policy for a resource, select the resource in the Cloud Console and click on the "Permissions" tab. This will display the IAM policy for the resource, including the roles and members assigned to the resource.

You can also use the Cloud IAM API to retrieve IAM policies programmatically.

### Creating IAM policies 
You can create IAM policies on Google Cloud Platform in several ways, including:

#### Using the Google Cloud Console:

1. Open the IAM & Admin page on the Google Cloud Console.
2. Select the project in which you want to create the policy.
3. Click the "Add" button at the top of the page.
4. In the "Add members" field, enter the email address of the user or service account for whom you want to create the policy.
5. Select a role from the dropdown list.
6. Click the "Save" button to create the policy.

#### Using the gcloud command-line tool:

1. Open a terminal or command prompt on your local machine.
2. Authenticate to your Google Cloud account by running the "gcloud auth login" command and following the prompts.
3. Use the "gcloud projects list" command to list all of your projects and find the one in which you want to create the policy.
4. Run the "gcloud projects add-iam-policy-binding" command to add a new IAM policy to the project. For example, to add a policy that grants a user the "storage.admin" role, you would run:

<pre><code>
gcloud projects add-iam-policy-binding [PROJECT-ID] --member=user:[USER-EMAIL] --role=roles/storage.admin
</code></pre>

#### Using the IAM API:

Use your preferred programming language to write a program that makes calls to the IAM API to create a new policy. The exact steps for doing this will depend on the language you're using and the specific API client library you choose to use. Google provides API client libraries for a number of popular languages, including Java, Python, and Node.js.


### Managing the various role types and defining custom IAM roles (e.g., primitive, predefined and custom)
here's an example of a custom IAM role definition in Google Cloud Platform:

Let's say we want to create a custom role that allows users to view and manage Cloud Storage buckets but not be able to modify IAM policies or manage other resources in the project. We can define a custom role with the following permissions:

- storage.buckets.get
- storage.buckets.list
- storage.buckets.create
- storage.buckets.delete

We can create this custom role using the following gcloud command:

<pre><code>
gcloud iam roles create storage-bucket-admin \
    --project my-project \
    --title "Storage Bucket Admin" \
    --description "Can view and manage Cloud Storage buckets" \
    --permissions storage.buckets.get,storage.buckets.list,storage.buckets.create,storage.buckets.delete
</code></pre>

This will create a new custom IAM role called "Storage Bucket Admin" with the specified permissions. We can then assign this role to users or groups who need to manage Cloud Storage buckets in our project.