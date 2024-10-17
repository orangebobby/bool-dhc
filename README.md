## **DHC Node Setup with SGX on Local LAN & Running a Chain via Snapshot**

### **1. Prerequisites**
Before you begin, ensure that you have the following:

- A machine with **Intel SGX** support.
- A **Bool Network Beta Testnet** account.
- Docker and Docker Compose installed on the machine.
- Local LAN connectivity to connect multiple nodes in the local network.
- Sufficient storage to run a chain from a snapshot.

### **2. Local LAN Configuration for SGX**
If you plan to run multiple nodes in a Local Area Network (LAN) for testing, follow these steps.

#### **Step 2.1: Preparing the Environment**

1. **Update and Install Required Dependencies**:

   Ensure your system packages are up-to-date and install dependencies for working with SGX:

   ```bash
   sudo apt-get update && sudo apt-get upgrade
   sudo apt-get install libssl-dev libcurl4-openssl-dev
   ```

2. **Install Intel SGX SDK and PSW**:

   Install the **Intel SGX Software Development Kit (SDK)** and **Platform Software (PSW)**. You can download these from the [Intel SGX website](https://www.intel.com/content/www/us/en/developer/articles/tool/intel-software-guard-extensions-sdk.html).

3. **Check SGX Support**:

   Verify that Intel SGX is available and enabled on your machine:

   ```bash
   dmesg | grep -i sgx
   ```

   If SGX is not enabled, you may need to enable it in your BIOS settings.

#### **Step 2.2: Configure SGX in Docker**

1. **Install Intel SGX Driver in Docker**:

   If you’re using Docker to deploy the nodes, you need to set up the Intel SGX driver within the container. Run:

   ```bash
   sudo docker run --device /dev/isgx --rm -it boolnetwork/sgx
   ```

2. **Network Configuration**:

   Ensure that your LAN setup is properly configured. Each node should be able to communicate with the others. Set up static IPs or dynamic IP assignment using a DHCP server.

#### **Step 2.3: Configure and Start the DHC Node**

1. **Clone the Bool Network Repository**:

   ```bash
   git clone https://github.com/bool-network/dhc-node.git
   cd dhc-node
   ```

2. **Edit the Configuration File**:

   Modify the `config.toml` file to suit your local LAN environment:

   ```bash
   nano config/config.toml
   ```

   - Set the node’s **private IP address** in the `p2p` section.
   - Configure the **SGX settings** under the `sgx` section of the config.

3. **Start the DHC Node**:

   Once your configuration is set, you can start the node:

   ```bash
   sudo docker-compose up
   ```

   The node will now attempt to join the **Bool Network Beta Testnet**.

---

### **3. Running a Chain via Snapshot**

To expedite node synchronization, you can use a blockchain snapshot. Follow these steps to run the node using a snapshot of the chain state.

#### **Step 3.1: Download the Snapshot**

1. **Find the Latest Snapshot**:

   Bool Network regularly provides snapshots of the blockchain state. Navigate to the Bool Network’s snapshot directory and download the latest snapshot.

   Example:

   ```bash
   wget https://snapshots.bool.network/latest.tar.gz
   ```

2. **Extract the Snapshot**:

   Extract the downloaded snapshot into your node’s data directory:

   ```bash
   tar -xzvf latest.tar.gz -C ~/.dhc/data/
   ```

#### **Step 3.2: Configure the Node to Use the Snapshot**

1. **Edit the Configuration**:

   Open the node’s configuration file and ensure that it is set to start from the snapshot. For example, modify the `config.toml` to reflect the correct data directory:

   ```bash
   nano config/config.toml
   ```

2. **Start the Node**:

   Start the node using Docker Compose:

   ```bash
   sudo docker-compose up
   ```

   The node will now initialize from the snapshot, significantly speeding up the synchronization process.

#### **Step 3.3: Verify Synchronization**

1. **Check Node Logs**:

   Use the following command to check the logs and verify if the node is synchronized with the latest block height:

   ```bash
   sudo docker logs -f dhc-node
   ```

2. **Check Sync Status**:

   You can also query the node directly to check its status:

   ```bash
   curl http://localhost:26657/status
   ```

   Ensure that the `block_height` is increasing and matches the latest block height on the network.
