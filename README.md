# Azure AZ-104 Project – Shaun Barnett

This repository documents the practical Azure administration tasks I completed while preparing for the **AZ‑104 Microsoft Azure Administrator** exam.  
Each step of the project was completed inside a real **PAYG subscription** in the **West Europe** region.

The project demonstrates core AZ‑104 skills, including:
- Creating and managing Resource Groups
- Building secure virtual networks and NSGs
- Deploying and configuring a Windows Server VM
- Enabling monitoring features (Boot Diagnostics, Insights, Metrics, Alerts)
- Following governance best practices

Detailed step-by-step documents are included inside the `docs/` folder.

---

## 📁 Repository Structure

AZ104-Project/
│
├── docs/
│     ├── Step1-ResourceGroup.docx
│     ├── Step2-Networking.docx
│     ├── Step3-NSG.docx
│     ├── Step4-VM.docx
│     ├── Step5-Storage.docx
│     ├── Step6-Monitoring.docx
│     ├── Step7-Governance.docx
│
└── (no images folder required – screenshots stored inside .docx files)

## 1️⃣ Resource Groups

I created three Resource Groups to organise and isolate Azure resources:

- **rg-core-we** – Networking components  
- **rg-compute-we** – Virtual machine resources  
- **rg-storage-we** – Storage account + containers  

### ✔ CLI Commands
```bash
az group create --name rg-core-we --location westeurope
az group create --name rg-compute-we --location westeurope
az group create --name rg-storage-we --location westeurope


2️⃣ Network Security Group (NSG)

An NSG was created to secure the servers subnet.
I added an inbound rule to allow RDP (3389) and associated the NSG with the correct subnet.

✔ Components

NSG: nsg-servers
Rule: Allow RDP (TCP/3389)
Subnet: subnet-servers
Region: West Europe

✔ CLI Commands

az network nsg create ^
  --resource-group rg-core-we ^
  --name nsg-servers ^
  --location westeurope

az network nsg rule create ^
  --resource-group rg-core-we ^
  --nsg-name nsg-servers ^
  --name Allow-RDP ^
  --priority 1000 ^
  --protocol Tcp ^
  --direction Inbound ^
  --source-address-prefixes "*" ^
  --source-port-ranges "*" ^
  --destination-address-prefixes "*" ^
  --destination-port-ranges 3389 ^
  --access Allow

3️⃣ Monitoring & Alerts

I enabled full VM monitoring including:

✔ Boot Diagnostics
Linked to the project storage account.
✔ VM Insights
Displayed CPU, disk, and network metrics.
✔ Metrics
Real-time charts enabled through Azure Monitor.
✔ Alerts
Created an alert for CPU > 80%.


````markdown
```mermaid
graph TD

    RGcore[rg-core-we] --> VNET[vnet-core-we]
    VNET --> SUB1[subnet-servers]
    VNET --> SUB2[subnet-management]

    SUB1 --> VM["vm-web01 | Windows Server 2022 | Standard_D2ads_v6"]
    SUB1 --> NSG[nsg-servers]

    RGstorage[rg-storage-we] --> SA["Storage Account | stcore*"]
    SA --> C1[(boot-diagnostics)]
    SA --> C2[(vm-uploads)]

    RGcompute[rg-compute-we] --> VM
