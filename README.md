# FuCloud 🚀

A personal mini-project developed during my university years. **FuCloud** serves as a lightweight web interface to monitor and access various services hosted in my personal homelab.

---

## 📌 Purpose

As a Vietnamese IT student passionate about Cloud Computing, I built this project to gain hands-on experience with:
* Infrastructure & Server Administration
* Networking concepts
* Hypervisors and Virtualization

---

## 💻 My Hardware Configuration (The Homelab)

* **Machine:** HP EliteDesk 800 G5 Mini PC
* **CPU:** Intel Core i5-8500T (6 Cores, 6 Threads)
* **RAM:** 8GB DDR4 2666MHz *(Planning to upgrade to 16GB soon! 🤞)*
* **Storage:** * 120GB NVMe M.2 (OS & Proxmox)
  * 256GB 2.5" SATA SSD (Fast storage for containers)
  * 500GB 2.5" HDD (Data backup & media)

---

## ⚙️ How It Works & Architecture

I use **Proxmox Virtual Environment (PVE)**, a powerful open-source Debian-based hypervisor, as the backbone of my homelab. 

Inside Proxmox, I deploy multiple **Linux Containers (LXC)** running Ubuntu to host isolated services. 

### Key Services:
* **File Browser:** A self-hosted cloud storage (similar to Google Drive) written in Go. I use it to store personal data and share interesting files with my friends.
* *(Future services will be listed here...)*

---

## 📂 Project Structure

```text
├── index.html   # Main web interface to access my homelab services
└── README.md    # Project documentation
