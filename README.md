## **Step 1: Sign Up & Log In**

1. Go to [Confluent Cloud](https://confluent.cloud).
2. Log in (or create a free account – you’ll get free credits for testing).
3. Verify your email and log in.

---

## **Step 2: Install Confluent CLI (Optional but Recommended)**

The CLI is useful for scripting and automation.

1. Install the CLI:

   ```bash
   curl -sL --http1.1 https://cnfl.io/install-cli | sh
   ```
2. Log in to your account:

   ```bash
   confluent login
   ```
3. (Optional) List your organizations and environments:

   ```bash
   confluent environment list
   ```

---

## **Step 3: Create a Kafka Cluster**

### Option 1 – **Using Confluent Cloud Console (UI)**:

1. Click **"Clusters"** in the left menu → **"Create Cluster"**.
2. Choose:

   * **Cluster Type**: Dedicated, Standard, or Basic (for testing).
   * **Cloud Provider**: AWS, Azure, or GCP.
   * **Region**: Select a region near you.
   * **Cluster Name**: e.g., `my-cluster`.
3. Click **"Create Cluster"**.

### Option 2 – **Using Confluent CLI**:

1. Set your active environment (if you have multiple):

   ```bash
   confluent environment use <ENVIRONMENT_ID>
   ```
2. Create a cluster (Basic example on AWS):

   ```bash
   confluent kafka cluster create my-cluster --cloud aws --region us-east-1 --type basic
   ```
3. Get your Cluster ID:

   ```bash
   confluent kafka cluster list
   ```
4. Set it as active:

   ```bash
   confluent kafka cluster use <CLUSTER_ID>
   ```

---

## **Step 4: Create Topics**

### Option 1 – **Using Confluent Cloud Console (UI)**:

1. Go to your **Cluster → Topics → Create Topic**.
2. Enter:

   * **Topic Name** (e.g., `orders`).
   * **Partitions** (default: 1–3 for dev, 6+ for production).
   * **Retention Settings** (default 7 days or customize).
3. Click **"Create"**.

### Option 2 – **Using Confluent CLI**:

1. Create a topic:

   ```bash
   confluent kafka topic create orders --partitions 3 --config retention.ms=604800000
   ```
2. Verify the topic:

   ```bash
   confluent kafka topic list
   ```

---

## **Step 5: Create API Keys (for Clients)**

To connect producers/consumers:

```bash
confluent api-key create --resource <CLUSTER_ID>
```

Note the **API Key** and **Secret** for your client apps.

---

## **Step 6: Produce and Consume Messages (Test)**

1. Produce a message:

   ```bash
   confluent kafka topic produce orders
   ```

   Type a message and hit **Enter** (Ctrl+D to exit).

2. Consume a message:

   ```bash
   confluent kafka topic consume orders --from-beginning
   ```

---

## **Optional – Automate via REST API**

You can also create topics via Confluent Cloud’s REST API using `curl` or Python (like the script you previously used).
For example (topic creation):

```bash
curl -X POST https://api.confluent.cloud/kafka/v3/clusters/<CLUSTER_ID>/topics \
  -u "<API_KEY>:<API_SECRET>" \
  -H "Content-Type: application/json" \
  -d '{"topic_name": "orders", "partitions_count": 3}'
```
