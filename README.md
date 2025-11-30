Azure 3-Tier Secure Architecture – Portfolio Project

This project demonstrates a production-style, security-first 3-tier architecture deployed fully on Microsoft Azure.
It showcases real-world cloud engineering skills in networking, identity, private endpoints, RBAC, VM configuration, and application integration.

Project Structure:

├── app-api/
│   ├── app.py
│   ├── requirements.txt
│   └── README.md
├── infra/
│   ├── architecture-diagram.png
│   ├── network-overview.md
│   └── security-controls.md
├── scripts/
│   ├── install-azure-cli.sh
│   └── deploy-vm.sh
└── README.md

---

  Architecture Overview

This environment was designed around enterprise principles:

Zero Trust networking

Identity-based access (no secrets in VMs)

Complete network segmentation

Private endpoints for all sensitive services

Separation of frontend, API, and data tiers


  Tiers

Tier	Subnet	Purpose

Frontend	10.10.1.0/24	Public entry point (VM), curls API privately
API	10.10.2.0/24	Internal-only Flask API VM
Database	10.10.3.0/24	Azure SQL via Private Endpoint


  Virtual Networks

vnet-hub-01 – shared services

vnet-app-01 – application hosting

Peered for secure east–west traffic

---

  Security Highlights

  Network Security Groups (NSGs)

Strict segmentation:

Frontend → API allowed

API → SQL allowed

Internet → only allowed to frontend

All other flows denied


  Managed Identity Everywhere

Both VMs use system-assigned managed identity to:

Authenticate to Azure

Retrieve secrets from Key Vault

Access SQL (via private link and AAD admin)


No passwords, no connection strings stored anywhere.

  Azure Key Vault Integration

RBAC-based access

Key Vault not publicly reachable

Both VMs successfully retrieved secrets using MI


  Azure SQL Security

Public network disabled

SQL only accessible through Private Endpoint

Private DNS zone ensures resolution to 10.10.3.x

---

  Compute Layer

vm-frontend

Hosts frontend environment

Validates API via private IP

Azure CLI installed & authenticated with MI


vm-api

Runs a Python Flask API on port 5000

Only reachable inside the VNet

API endpoint: /health

Azure CLI installed & validated

---

  Application Layer

Flask app running on vm-api:

from flask import Flask, jsonify

app = Flask(_name_)

@app.route("/health")
def health():
    return jsonify(
        status="ok",
        tier="api",
        message="Hello from vm-api"
    )

if _name_ == "_main_":
    app.run(host="0.0.0.0", port=5000)

Validated Application Flow:

Frontend VM (10.10.1.4)
     ↓ private VNet traffic
API VM (10.10.2.4, port 5000)
     ↓ MI auth + private routing
Azure SQL + Key Vault

---

  Validation Tests Completed

VM identity authentication

az login --identity

Key Vault secret access

az keyvault secret list --vault-name <vault>

SQL private endpoint resolution

nslookup <sql>.database.windows.net → resolves to 10.10.3.x

Private API communication

From frontend:

curl http://10.10.2.4:5000/health

Response:

{"message":"Hello from vm-api","status":"ok","tier":"api"}

  
  Skills Demonstrated:

Azure VNets, peering, subnetting

NSGs and zero-trust segmentation

Private Endpoints & Private DNS zones

Azure SQL locking

Azure Key Vault + RBAC + Managed Identity

Linux VM configuration

Azure CLI installation & troubleshooting

Flask API deployment inside isolated cloud networks

End-to-end debugging of identity, routing, and security

---

Project Outcome

A fully functioning, identity-secured, privately-routed, production-style 3-tier architecture running entirely inside Azure — built, validated, and tested end-to-end.
