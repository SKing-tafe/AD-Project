## VM Configuration and Install - Windows 11 ##
### VM Configuration ###
- RAM = 4096
- Cores = 1
- Storage = 60GB

### Install ###
Use standard settings for Australia (NSW)  
- Select Windows 11 Pro when prompted
- Select "I Don't Have Product Key"
- If you get error message regarding TPM compatibility, do the following:
	- Shift + F10 to open the Command Prompt
	- Type **regedit** and press Enter to open the Registry Editor.
	- Navigate to HKEY_LOCAL_MACHINE\SYSTEM\Setup.
	- Right-click on Setup, select New > Key, and name it **LabConfig**.
	- Inside LabConfig, create the following DWORD (32-bit) Values: 
		- **BypassTPMCheck**: Set the value to 1.
		- **BypassSecureBootCheck**: Set the value to 1. 
		- **BypassRAMCheck**: Set the value to 1 if bypassing RAM requirements.
	- Close the Registry Editor and Command Prompt.	
	- Click on the "Back" button and proceed with the Windows 11 installation.

Use the following:
**UserName:** admin1
**Password:** #secRet#
**Answer to security all security questions:** bob