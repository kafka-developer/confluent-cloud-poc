# How to Create a Kafka Cluster and Topics in Confluent Cloud

This guide walks you through:

1. Installing the **Confluent CLI** (Mac, Windows, Linux)  
2. Creating a **Kafka Cluster** (via Console UI or CLI)  
3. Creating **Topics**  
4. Generating **API Keys**  
5. Testing producers and consumers  
6. Optionally, automating with **REST API** and **bulk scripts**

---

## Step 1: Sign Up & Log In

- Go to [Confluent Cloud](https://confluent.cloud).  
- Log in (or sign up for a free trial with credits).  
- Verify your email and log in.

---

## Step 2: Install Confluent CLI

The **Confluent CLI** allows you to manage Kafka clusters and resources via the command line.  
Follow the instructions below for your operating system.  
Official docs: [Confluent CLI Install](https://docs.confluent.io/confluent-cli/current/install.html)

### Mac (Intel & Apple Silicon)

1. Install via Homebrew:
   ```bash
   brew install confluentinc/tap/cli
   ```
2. Verify installation:
   ```bash
   confluent --version
   ```

### Windows

1. Download the latest `.zip` from:  
   [Confluent CLI Download](https://docs.confluent.io/confluent-cli/current/install.html)

2. Extract the zip file.

3. Add the extracted folder (where `confluent.exe` is located) to your **System PATH**:
   - Go to **System Properties → Environment Variables**.  
   - Edit the `Path` variable and add the folder path.

4. Verify installation (in Command Prompt):
   ```cmd
   confluent --version
   ```

### Linux (Debian/Ubuntu)

1. Install via script:
   ```bash
   curl -sL --http1.1 https://cnfl.io/install-cli | sh
   ```
2. Verify installation:
   ```bash
   confluent --version
   ```

---

## Step 3: Authenticate the CLI

After installation, log in to Confluent Cloud:
```bash
confluent login
```

To view your available environments:
```bash
confluent environment list
```

---

## Step 4: Create a Kafka Cluster

You can create a cluster using the **Confluent Cloud Console (UI)** or the **CLI**.

### Option 1 – Using Confluent Cloud Console (UI)

1. Go to **Clusters** → **Create Cluster**.
2. Select:
   - **Cluster Type**: Basic (for dev/test), Standard, or Dedicated (for production).
   - **Cloud Provider**: AWS, Azure, or GCP.
   - **Region**: Choose a region near your services.
   - **Cluster Name**: Example: `my-cluster`.
3. Click **Create Cluster**.

### Option 2 – Using Confluent CLI

1. Set the active environment:
   ```bash
   confluent environment use <ENVIRONMENT_ID>
   ```
2. Create a cluster (example for AWS, `us-east-1`):
   ```bash
   confluent kafka cluster create my-cluster --cloud aws --region us-east-1 --type basic
   ```
3. List clusters to find your Cluster ID:
   ```bash
   confluent kafka cluster list
   ```
4. Set the cluster as active:
   ```bash
   confluent kafka cluster use <CLUSTER_ID>
   ```

---

## Step 5: Create Topics

### Option 1 – Using Confluent Cloud Console (UI)

1. Go to **Cluster → Topics → Create Topic**.
2. Enter:
   - **Topic Name** (e.g., `orders`).
   - **Partitions** (1–3 for dev, 6+ for production).
   - **Retention Settings** (default 7 days or custom).
3. Click **Create**.

### Option 2 – Using Confluent CLI

1. Create a topic:
   ```bash
   confluent kafka topic create orders --partitions 3 --config retention.ms=604800000
   ```
2. List topics to verify:
   ```bash
   confluent kafka topic list
   ```

---

## Step 6: Create API Keys for Clients

Producers and consumers need credentials.  
Generate an API key for your cluster:
```bash
confluent api-key create --resource <CLUSTER_ID>
```
Save the **API Key** and **Secret** securely.

---

## Step 7: Test Producing and Consuming Messages

### Produce Messages
```bash
confluent kafka topic produce orders
```
- Type messages and press **Enter** to send.  
- Press **Ctrl+D** to exit.

### Consume Messages
```bash
confluent kafka topic consume orders --from-beginning
```

---

## Step 8: Automate via REST API (Optional)

You can also programmatically create topics using Confluent’s REST API.

**Example – Create a Topic:**
```bash
curl -X POST https://api.confluent.cloud/kafka/v3/clusters/<CLUSTER_ID>/topics   -u "<API_KEY>:<API_SECRET>"   -H "Content-Type: application/json"   -d '{"topic_name": "orders", "partitions_count": 3}'
```

---

## Step 9: Bulk Topic Creation (Automation via CLI)

If you need to create multiple topics at once, create a `topics.txt` file:
```
orders,3,604800000
payments,6,1209600000
logs,1,86400000
```

Then use this script:
```bash
while IFS=, read -r topic partitions retention; do
  confluent kafka topic create "$topic" --partitions "$partitions" --config "retention.ms=$retention"
done < topics.txt
```
