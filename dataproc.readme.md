# Dataproc Qwik Start: Command Line Lab

This repository documents a hands-on Google Cloud Dataproc lab completed using the `gcloud` command-line interface. The lab demonstrates how to create and manage a Dataproc cluster, submit an Apache Spark job, and dynamically scale the cluster.

---

## Lab Objectives

In this lab, you will learn how to:

- Create a Dataproc cluster using the command line  
- Run a simple Apache Spark job  
- Modify the number of worker nodes in a Dataproc cluster  

---

## Prerequisites

- A modern web browser (Chrome recommended)
- Temporary Google Cloud student credentials (Qwiklabs)
- Basic familiarity with the Linux command line
- No prior Dataproc or Spark experience required

> Important: Always use the provided student account. Using a personal Google Cloud account may result in unexpected charges.

---

## Environment Setup

### Activate Cloud Shell

Cloud Shell provides a preconfigured environment with the Google Cloud CLI installed.

1. Open the Google Cloud Console using the lab-provided credentials
2. Click **Activate Cloud Shell**
3. Authorize Cloud Shell when prompted

Verify authentication and project configuration:

```bash
gcloud auth list
gcloud config list project
```

---

## Task 1: Create a Dataproc Cluster

### Set the Dataproc Region

```bash
gcloud config set dataproc/region us-east1
```

### Reset the Dataproc API

```bash
gcloud services disable dataproc.googleapis.com --force
gcloud services enable dataproc.googleapis.com
```

### Configure IAM Permissions

```bash
PROJECT_ID=$(gcloud config get-value project)
PROJECT_NUMBER=$(gcloud projects describe $PROJECT_ID   --format='value(projectNumber)')
```

Grant required roles:

```bash
gcloud projects add-iam-policy-binding $PROJECT_ID   --member=serviceAccount:$PROJECT_NUMBER-compute@developer.gserviceaccount.com   --role=roles/storage.admin
```

```bash
gcloud projects add-iam-policy-binding $PROJECT_ID   --member=serviceAccount:$PROJECT_NUMBER-compute@developer.gserviceaccount.com   --role=roles/dataproc.worker
```

### Enable Private Google Access

```bash
gcloud compute networks subnets update default   --region=us-east1   --enable-private-ip-google-access
```

### Create the Cluster

```bash
gcloud dataproc clusters create example-cluster   --worker-boot-disk-size 500   --worker-machine-type=e2-standard-4   --master-machine-type=e2-standard-4
```

Wait until the cluster status shows **Created**.

---

## Task 2: Submit a Spark Job

```bash
gcloud dataproc jobs submit spark   --cluster example-cluster   --class org.apache.spark.examples.SparkPi   --jars file:///usr/lib/spark/examples/jars/spark-examples.jar   -- 1000
```

Expected output:

```text
Pi is roughly 3.14118528
state: FINISHED
```

---

## Task 3: Scale the Cluster

```bash
gcloud dataproc clusters update example-cluster --num-workers 4
gcloud dataproc clusters update example-cluster --num-workers 2
```


---

Reference: Google Skills, www.skills.google/paths/17/course_templates/631/labs/594532. Accessed 02 Jan. 2026.

