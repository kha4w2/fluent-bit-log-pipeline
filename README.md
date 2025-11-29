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
