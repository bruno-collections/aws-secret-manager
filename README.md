# AWS Secrets Manager — Bruno Collection

This collection demonstrates how to configure and use **AWS Secrets Manager** as a secret provider in [Bruno](https://www.usebruno.com/), allowing you to securely inject secrets (API keys, credentials, etc.) into your requests without hardcoding them.

---

## Prerequisites

- [Bruno](https://www.usebruno.com/downloads) installed (Ultimate License required for Secrets Manager support)
- An active **AWS account** with Secrets Manager configured
- An IAM user or role with `secretsmanager:GetSecretValue` permission
- Your AWS **Access Key ID**, **Secret Access Key**, and the target **Region**

---

## Configure AWS Secrets Manager in Bruno

### Step 1 — Open Bruno Preferences

Open Bruno and navigate to **Preferences** (top-left menu).

### Step 2 — Add a Secret Provider

1. Go to the **Secrets Manager** tab in the left sidebar.
2. Under **Secret Providers**, click **Add Secret Provider**.
3. In the dialog box that appears, fill in the following fields:

   | Field             | Value                              |
   |-------------------|------------------------------------|
   | Name              | Any label (e.g. `AWS`)             |
   | Secret Manager    | `AWS Secrets Manager`              |
   | Auth Method       | `Manual`                           |
   | Access Key ID     | Your AWS Access Key ID             |
   | Secret Access Key | Your AWS Secret Access Key         |
   | Session Token     | *(optional)* STS session token     |
   | Region            | e.g. `US East (N. Virginia) (us-east-1)` |

   ![Add Secret Provider dialog](../../.cursor/projects/Users-ganeshpatil-Documents-test-workspace-collections-AWS-Secret-Manager/assets/image-6b5a6b4f-a6cd-4648-94ce-9523a354d17d.png)

### Step 3 — Test the Provider

Click **Test Provider** at the bottom-left of the dialog to verify connectivity. You should see a success message confirming the provider is connected.

### Step 4 — Save

Click **Save** to store the secret provider configuration.

---

## Add Secrets to Your Collection

### Step 5 — Open the Collection Secrets Tab

1. Open your collection (e.g. **AWS Secret Manager**).
2. Navigate to the **Secrets** tab.
3. From the provider dropdown, select **AWS Secrets**.

   ![Select AWS Secrets provider](../../.cursor/projects/Users-ganeshpatil-Documents-test-workspace-collections-AWS-Secret-Manager/assets/image-8ca756de-3388-4abf-b004-47f2d75f958f.png)

### Step 6 — Configure the Secret Entry

Add a secret row with:

- **Name** — a local alias used to reference the secret (e.g. `usebruno`)
- **Secret Name/ARN** — the full ARN of your secret in AWS Secrets Manager  
  

   ![Secret configured with ARN](../../.cursor/projects/Users-ganeshpatil-Documents-test-workspace-collections-AWS-Secret-Manager/assets/image-f6e6dbad-2edf-409e-8a0f-3e6e0f52d0bb.png)

Click **Save**.

### Step 7 — Fetch Secrets from AWS

Click **Fetch Secrets**, select your provider (e.g. `AWS`), and click **Fetch** in the confirmation dialog.

   ![Fetch AWS Secrets dialog](../../.cursor/projects/Users-ganeshpatil-Documents-test-workspace-collections-AWS-Secret-Manager/assets/image-a858717f-273b-4375-950a-cae47ee93cc7.png)

Once fetched successfully, the **Secrets** column will display the available keys (e.g. `access_key`, `username`, `password`, `version`).

   ![Secrets fetched successfully](../../.cursor/projects/Users-ganeshpatil-Documents-test-workspace-collections-AWS-Secret-Manager/assets/image-531d127a-6ba9-4fb2-8930-2012d95f53f1.png)

---

## Using Secrets in Requests

Reference a secret in any request field (headers, body, auth, query params) using the pattern:

```
$secrets.<secret-name>.<key-name>
```

**Example** — using the `version` key from the `usebruno` secret in a request body:

```json
{
  "title": "{{$secrets.usebruno.version}}",
  "msg": "The API client developers love most"
}
```

The resolved response will contain the actual secret value fetched from AWS:

   ![Secret resolved in request](../../.cursor/projects/Users-ganeshpatil-Documents-test-workspace-collections-AWS-Secret-Manager/assets/image-dca3d32e-012f-4056-b052-26166500f5f8.png)

### Using Secrets in Scripts

You can also access secrets programmatically in Pre-request or Post-request scripts:

```javascript
const secretValue = bru.getSecretVar('<secret-name>.<key-name>');

// Example: inject an API key into a request header
const apiKey = bru.getSecretVar('usebruno.access_key');
req.setHeader('Authorization', 'Bearer ' + apiKey);
```

---

## Collection Structure

```
AWS Secret Manager/
├── README.md               # This file
├── opencollection.yml      # Bruno collection metadata
├── echo-bru.yml            # Sample request using $secrets
├── secrets.json            # Secret references (ARNs, no values)
├── .env                    # Local environment overrides (git-ignored)
├── .gitignore
└── environments/
```

> **Note:** `secrets.json` stores only secret names/ARNs — never actual secret values. Actual values are fetched at runtime from AWS.

---

## Further Reading

- [Using Secrets in Request Fields](https://docs.usebruno.com/secrets-management/secret-managers/aws-secrets-manager/using-secrets)
- [AWS Secrets Manager Overview in Bruno](https://docs.usebruno.com/secrets-management/secret-managers/aws-secrets-manager/overview)
