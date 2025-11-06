Perfect — here’s a **complete AWS S3 CLI command guide** with **clear explanations and real-world examples** you can practice directly in your terminal.

---

# 🧠 AWS S3 CLI — Complete Command Reference

✅ **Prerequisite:**
Make sure you’ve configured your CLI:

```bash
aws configure
```

---

## 🗂️ 1. Basic Bucket Operations

### 🔹 List All Buckets

```bash
aws s3 ls
```

> Lists all S3 buckets in your account.

---

### 🔹 Create a Bucket

```bash
aws s3 mb s3://my-demo-bucket --region ap-south-1
```

> `mb` = make bucket.

---

### 🔹 Remove a Bucket

```bash
aws s3 rb s3://my-demo-bucket
```

> Only works if the bucket is empty.
> To delete non-empty buckets, use `--force`.

```bash
aws s3 rb s3://my-demo-bucket --force
```

---

## 📦 2. Upload and Download Files

### 🔹 Upload a Single File

```bash
aws s3 cp myfile.txt s3://my-demo-bucket/
```

---

### 🔹 Upload a Folder (Recursive)

```bash
aws s3 cp ./myfolder s3://my-demo-bucket/ --recursive
```

---

### 🔹 Download a File

```bash
aws s3 cp s3://my-demo-bucket/myfile.txt ./downloaded-file.txt
```

---

### 🔹 Download Entire Bucket

```bash
aws s3 sync s3://my-demo-bucket ./local-folder
```

---

## 🔁 3. Synchronization

### 🔹 Sync Local → S3

```bash
aws s3 sync ./my-local-folder s3://my-demo-bucket/
```

### 🔹 Sync S3 → Local

```bash
aws s3 sync s3://my-demo-bucket/ ./my-local-folder
```

### 🔹 Sync Between Buckets

```bash
aws s3 sync s3://source-bucket/ s3://destination-bucket/
```

---

## 🧭 4. Listing and Viewing Objects

### 🔹 List Bucket Contents

```bash
aws s3 ls s3://my-demo-bucket/
```

### 🔹 List with Date and Size

```bash
aws s3 ls s3://my-demo-bucket/ --recursive --human-readable --summarize
```

---

### 🔹 View File Content (Without Downloading)

```bash
aws s3 cp s3://my-demo-bucket/myfile.txt - | cat
```

---

## 🧹 5. Delete Files and Folders

### 🔹 Delete a Single File

```bash
aws s3 rm s3://my-demo-bucket/oldfile.txt
```

### 🔹 Delete Multiple Files

```bash
aws s3 rm s3://my-demo-bucket/logs/ --recursive
```

---

## 🔒 6. Bucket ACLs and Policies

### 🔹 Make Bucket Public

```bash
aws s3api put-bucket-acl --bucket my-demo-bucket --acl public-read
```

### 🔹 Get Bucket ACL

```bash
aws s3api get-bucket-acl --bucket my-demo-bucket
```

### 🔹 Set Object ACL

```bash
aws s3api put-object-acl --bucket my-demo-bucket --key myfile.txt --acl public-read
```

---

## 🧰 7. Using `s3api` for Advanced Control

The `aws s3api` gives low-level control.

### 🔹 List All Buckets

```bash
aws s3api list-buckets
```

---

### 🔹 Create Bucket (explicit region)

```bash
aws s3api create-bucket \
  --bucket my-demo-bucket \
  --region ap-south-1 \
  --create-bucket-configuration LocationConstraint=ap-south-1
```

---

### 🔹 Upload File via `s3api`

```bash
aws s3api put-object --bucket my-demo-bucket --key myfile.txt --body ./myfile.txt
```

---

### 🔹 Get Object Metadata

```bash
aws s3api head-object --bucket my-demo-bucket --key myfile.txt
```

---

### 🔹 Get Bucket Policy

```bash
aws s3api get-bucket-policy --bucket my-demo-bucket
```

---

### 🔹 Enable Versioning

```bash
aws s3api put-bucket-versioning --bucket my-demo-bucket --versioning-configuration Status=Enabled
```

---

### 🔹 List Object Versions

```bash
aws s3api list-object-versions --bucket my-demo-bucket
```

---

### 🔹 Enable Server Access Logging

```bash
aws s3api put-bucket-logging \
  --bucket my-demo-bucket \
  --bucket-logging-status '{
    "LoggingEnabled": {
      "TargetBucket": "my-logs-bucket",
      "TargetPrefix": "logs/"
    }
  }'
```

---

### 🔹 Enable Default Encryption (SSE-S3)

```bash
aws s3api put-bucket-encryption \
  --bucket my-demo-bucket \
  --server-side-encryption-configuration '{
    "Rules": [
      {
        "ApplyServerSideEncryptionByDefault": {
          "SSEAlgorithm": "AES256"
        }
      }
    ]
  }'
```

---

## 🌍 8. Static Website Hosting

### 🔹 Enable Website Hosting

```bash
aws s3 website s3://my-demo-bucket/ --index-document index.html --error-document error.html
```

### 🔹 Get Website URL

```bash
aws s3api get-bucket-website --bucket my-demo-bucket
```

---

## 🧮 9. Storage Class Management

### 🔹 Change Storage Class to Glacier

```bash
aws s3 cp s3://my-demo-bucket/data.zip s3://my-demo-bucket/data.zip --storage-class GLACIER --metadata-directive COPY
```

---

## ⚙️ 10. Presigned URLs (Temporary Access)

### 🔹 Generate a 10-minute Signed URL

```bash
aws s3 presign s3://my-demo-bucket/myfile.txt --expires-in 600
```

---

## 🧑‍💻 11. Useful Tips

| Purpose                     | Command                                                                         |        |
| --------------------------- | ------------------------------------------------------------------------------- | ------ |
| Count total files in bucket | `aws s3 ls s3://my-demo-bucket --recursive                                      | wc -l` |
| Show total size of bucket   | `aws s3 ls s3://my-demo-bucket --recursive --human-readable --summarize`        |        |
| Copy only `.jpg` files      | `aws s3 cp ./ s3://my-demo-bucket/ --recursive --exclude "*" --include "*.jpg"` |        |
| Dry-run before sync         | `aws s3 sync ./ s3://my-demo-bucket/ --dryrun`                                  |        |

---

## ⚡ Summary

| Category           | Command                           | Example |
| ------------------ | --------------------------------- | ------- |
| **List Buckets**   | `aws s3 ls`                       | —       |
| **Create Bucket**  | `aws s3 mb s3://bucket`           | —       |
| **Upload File**    | `aws s3 cp file s3://bucket/`     | —       |
| **Sync Folders**   | `aws s3 sync ./ s3://bucket/`     | —       |
| **Delete File**    | `aws s3 rm s3://bucket/file`      | —       |
| **Versioning**     | `aws s3api put-bucket-versioning` | —       |
| **Encryption**     | `aws s3api put-bucket-encryption` | —       |
| **Static Website** | `aws s3 website s3://bucket/`     | —       |

---

Would you like me to give you a **PDF cheat sheet version** of this (with commands categorized and examples neatly formatted)? It’ll be perfect for quick revision before interviews or hands-on tasks.
