# Driver-Share-Home-Lab
# Home Lab Printer Driver Setup & Deployment Guide

This guide details the entire process to share printer drivers from a print server in a home lab, install drivers on clients, optionally deploy via Group Policy, and automate driver installation using a batch script.

---

## Table of Contents

- [Overview](#overview)  
- [Prerequisites](#prerequisites)  
- [Step 1: Prepare Printer Driver Folder](#step-1-prepare-printer-driver-folder)  
- [Step 2: Share the Printer Driver Folder](#step-2-share-the-printer-driver-folder)  
- [Step 3: Configure Network and Firewall](#step-3-configure-network-and-firewall)  
- [Step 4: Client Installation of Printer Drivers](#step-4-client-installation-of-printer-drivers)  
- [Step 5: Deploy via Group Policy (Optional)](#step-5-deploy-via-group-policy-optional)  
- [Step 6: Automate Driver Installation Using Batch Script](#step-6-automate-driver-installation-using-batch-script)  
- [Troubleshooting](#troubleshooting)  
- [Contact](#contact)  

---

## Overview

Sharing printer drivers on a print server ensures consistent deployment to client PCs. This is suitable for home labs or small networks.

---

## Prerequisites

- Windows print server with printer installed  
- Admin access to print server  
- Extracted printer driver files  
- Network connectivity  
- Permissions to share folders and configure firewall  

---

## Step 1: Prepare Printer Driver Folder

1. Create a folder on the print server, e.g.: c:\sharedprinterdriver

2. Copy extracted driver files here.

---

## Step 2: Share the Printer Driver Folder

1. Right-click the folder → Properties → Sharing tab  
2. Click **Advanced Sharing** → check **Share this folder**  
3. Name the share (e.g., `PrinterDrivers`)  
4. Click **Permissions** → grant **Read** to users/groups  
5. Confirm and close

---

## Step 3: Configure Network and Firewall

- Enable **File and Printer Sharing**:  
Control Panel → Network and Sharing Center → Change advanced sharing settings → Turn on file and printer sharing  

- Open firewall ports for File and Printer Sharing and SMB (TCP 445, 139)  

- Verify access from clients:
## Step 4: Client Installation of Printer Drivers

1. Open **File Explorer** on the client PC.  
2. Enter the shared folder path: `\\PRINTSERVER\PrinterDrivers`  
3. Navigate to your printer model folder.  
4. When adding the printer, if prompted for drivers, select **Have Disk** and browse to this folder to install the driver manually.  
5. Add the network printer:
   - Go to **Control Panel** → **Devices and Printers** → **Add a printer**.  
   - Select **Add a network, wireless or Bluetooth printer**.  
   - Choose the shared printer and complete the installation.

## Step 5: Deploy Printer and Drivers via Group Policy (Optional)

1. Open **Group Policy Management Console (GPMC)** on a domain controller.  
2. Create or edit a Group Policy Object linked to the target Organizational Unit (OU).  
3. Navigate to:  
Computer Configuration → Policies → Windows Settings → Deployed Printers
4. Add the shared printer from the print server.  
5. The printer and drivers will deploy automatically on client PCs after next Group Policy refresh or reboot.
Note:
# Printer Driver Installation Script

This repository contains a simple batch script to install printer drivers from a shared folder using `rundll32 printui.dll`. This script automates installing the driver INF file with a single click.

---

## install-driver.bat Template

Save the following as `install-driver.bat` inside your printer driver folder (for example: `\\pr-prn1\printer01\home printer\install-driver.bat`):

```batch
@echo off
echo Installing printer driver...
echo.

:: Change this to match your exact printer model name as listed in the INF file
set PrinterName="Lexmark Universal v2"

:: Path to the INF file relative to this batch file location
set DriverINF="%~dp0LMUD1040.INF"

:: Run the install command
rundll32 printui.dll,PrintUIEntry /ia /m %PrinterName% /f %DriverINF%

if %errorlevel% equ 0 (
    echo.
    echo Driver installation completed successfully.
) else (
    echo.
    echo ERROR: Driver installation failed.
)

Pause
How to Customize
Replace Lexmark Universal v2 with the exact printer model name as shown inside your INF file's [Manufacturer] section or as Windows expects it.

Make sure the INF file name (LMUD1040.INF) matches exactly the INF file name in the folder.

Place this batch file in the same folder as the INF and all other driver files.
Navigate to the printer driver folder on the network share, for example:
\\pr-prn1\printer01\home printer
Double-click install-driver.bat to run the script.

Follow on-screen prompts.

If prompted for permissions, right-click and select Run as administrator.

Restart the PC if necessary to complete the driver installation.
