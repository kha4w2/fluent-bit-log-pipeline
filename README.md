# Fluent Bit Log Parsing, Ingestion, and Visualization with Elasticsearch and Kibana

📌 **Overview**  
Streamlined log processing pipeline using Fluent Bit, Elasticsearch, and Kibana for structured log ingestion and visualization.

🔥 **Key Features**  
- 🛠️ Windows Fluent Bit installation and configuration  
- 📝 Custom regex log parser  
- 📂 Manual raw log management  
- 🌐 Elasticsearch integration  
- 🖥️ Kibana visualization  

🎯 **Purpose**  
Enable efficient log parsing, ingestion, and visualization for network and system events in a structured and real-time manner.

📦 **Repository Contents**  
- `fluent-bit.conf` – Fluent Bit main configuration file  
- `RawLog.conf` – Sample raw logs for testing  
- `parsers.conf` – Custom regex parser definitions  
- `README.md` – Project documentation  

✔️ **Result**  
- Fluent Bit running on Windows  
- Logs parsed, ingested into Elasticsearch  
- Logs accessible on Kali Linux and visualized in Kibana Discover  

📘 **The Steps**  

1. 🖥️ **Install Fluent Bit on Windows**

   Extracted and moved Fluent Bit binaries to the target installation directory.
   
   <img width="961" height="187" alt="1" src="https://github.com/user-attachments/assets/3a03eabf-b519-41e7-a534-e06c82bc7ad1" />

   ```powershell
   $zipPath ="C:\Users\KHALED\Downloads\fluent-bit-4.2.0-win64.zip"
   $extractPath = "C:\Users\KHALED\Downloads\fluent-bit-temp"
   Expand-Archive -Path $zipPath -DestinationPath $extractPath -Force
   $targetPath = "C:\Program Files\fluant-bit"
   Move-Item -Path "$extractPath\fluent-bit-4.2.0-win64" -Destination $targetPath

2. ✅ Test Fluent Bit Installation

After installing Fluent Bit, verify that it works by running a test input and output to **stdout**: 

<img width="975" height="643" alt="image" src="https://github.com/user-attachments/assets/db837a2c-e1e8-40e8-a3c8-f68a0ec71f1a" />

```powershell
cd "C:\Program Files\fluant-bit\bin"
.\fluent-bit.exe -i dummy -o stdout
```
3. 🛠️Configure Fluent Bit Parser

Define a **custom regex parser** to extract structured fields from your log entries.  
Add the following configuration to your Fluent Bit parser file:

<img width="975" height="450" alt="image" src="https://github.com/user-attachments/assets/a2d951a9-4d3a-451d-a303-563e42ffec39" />

<img width="975" height="134" alt="image" src="https://github.com/user-attachments/assets/b15a4517-c8f9-412b-a83f-aadbea96ad3f" />


```ini
[PARSER]
    Name    PARSER-Logs
    Format  regex
    Regex   (?<time>\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}) (?<action>ACCEPT|DROP) IN=(?<Interface>\S+).*?MAC=(?<MAC>(?:[0-9a-fA-F]{2}:){5}[0-9a-fA-F]{2}) .*?SRC=(?<SRC_IP>\d{1,3}(?:\.\d{1,3}){3}) DST=(?<DST_IP>\d{1,3}(?:\.\d{1,3}){3}) PROTO=(?<PROTO>\S+) SPT=(?<SPT>\d+)
```
4. ## 📝 Step 4: Create Raw Log File

Create a `RawLog.conf` file to **manually store log entries** that Fluent Bit will process during runtime.  

<img width="975" height="258" alt="image" src="https://github.com/user-attachments/assets/951a9aa1-0258-467a-871c-e0feb17466ef" />

Example:

```text
2025-11-29 12:00:01 ACCEPT IN=eth0 MAC=aa:bb:cc:dd:ee:ff SRC=192.168.1.10 DST=192.168.1.20 PROTO=TCP SPT=443
2025-11-29 12:01:15 DROP IN=eth1 MAC=11:22:33:44:55:66 SRC=192.168.1.15 DST=192.168.1.25 PROTO=UDP SPT=53
```
5. ## ⚙️ Step 5: Configure Fluent Bit with Elasticsearch

Create a `fluent-bit.conf` file to:

- **Read logs** from `RawLog.conf`  
- **Forward logs** to **Elasticsearch**  
- **Output logs** to **stdout** for real-time monitoring
  
<img width="975" height="468" alt="image" src="https://github.com/user-attachments/assets/8f3a42b9-056b-402b-b7f1-9b78dc4fdf6a" />

configurations:

```ini
[SERVICE]
    Flush        1
    Daemon       Off
    Log_Level    info
    Parsers_File parsers.conf
    HTTP_Server  On
    Storage.metrics On

[INPUT]
    Name           tail
    Path           C:\Program Files\fluant-bit\conf\RawLog.conf
    Parser         PARSER-Logs
    Tag            rawlog.tag
    Refresh_Interval 1

[OUTPUT]
    Name           es
    Match          *
    Host           192.168.6.130
    Port           9200
    HTTP_User      elastic
    HTTP_Passwd    FYQEm=bUNr-6yUeyfdic
    tls            On
    tls.verify     Off
    Trace_Output   On
    Suppress_Type_Name On

[OUTPUT]
    Name           stdout
    Match          *
    Format         json_lines
```

6. ▶️ Run Fluent Bit with Manual Log Input

Start Fluent Bit to process logs.  
Logs will be forwarded to **stdout** and **Elasticsearch** **only when new entries are added** to `RawLog.conf`.

<img width="624" height="276" alt="image" src="https://github.com/user-attachments/assets/c1bd9399-c13c-42c8-9ff7-6e24e65f4697" />

<img width="624" height="330" alt="image" src="https://github.com/user-attachments/assets/ea20f451-03d1-4ca9-bf71-1c3d1136b30c" />


```powershell
.\fluent-bit.exe -c "C:\Program Files\fluant-bit\conf\fluent-bit.conf"
```

7. 💻 View Logs on Kali Linux

Access Fluent Bit processed logs from **Elasticsearch** using `curl`.  
Logs will appear **only after being manually added** to `RawLog.conf`.

<img width="975" height="518" alt="image" src="https://github.com/user-attachments/assets/e7fdd833-2897-4e6c-a172-af3a8ebac1d1" />


```bash
curl -X GET http://192.168.6.130:9200/fluent-bit/_search?pretty
```

8. ## 📊 Step 8: View Logs in Kibana Discover

Open **Kibana Discover** on `localhost` to visually explore Fluent Bit logs indexed in Elasticsearch.  

You can **filter and analyze fields** such as:

- `SRC_IP` 🌐  
- `DST_IP` 🌐  
- `MAC` 🖧  
- `SPT` 🔢

<img width="1382" height="816" alt="9" src="https://github.com/user-attachments/assets/2456c5a4-063e-42b6-a87a-3c8694ca0ad9" />

> Kibana provides a powerful interface to **search, filter, and visualize log data** in real-time.

