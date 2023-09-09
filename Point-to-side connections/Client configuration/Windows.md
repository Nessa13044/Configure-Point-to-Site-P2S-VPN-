# 1. Generate VPN client configuration files
- All of the necessary configuration settings for the VPN clients are contained in a VPN client profile configuration zip file. You can generate client profile configuration files using PowerShell, or by using the Azure portal. Either method returns the same zip file.

# View configuration files
Unzip the VPN client profile configuration file to view the following folders:

- WindowsAmd64 and WindowsX86, which contain the Windows 64-bit and 32-bit installer packages, respectively. The WindowsAmd64 installer package is for all supported 64-bit Windows clients, not just Amd.
- Generic, which contains general information used to create your own VPN client configuration. The Generic folder is provided if IKEv2 or SSTP+IKEv2 was configured on the gateway. If only SSTP is configured, then the Generic folder isn’t present.

# Configure VPN client profile
1. Select the VPN client configuration files that correspond to the architecture of the Windows computer. For a 64-bit processor architecture, choose the 'VpnClientSetupAmd64' installer package. For a 32-bit processor architecture, choose the 'VpnClientSetupX86' installer package.

2.Double-click the package to install it. If you see a SmartScreen popup, click More info, then Run anyway.

3. Install the client certificate. Typically, you can do this by double-clicking the certificate file and providing a password if required. For more information, see Install client certificates.

4. Connect to your VPN. Go to the VPN settings and locate the VPN connection that you created. It's the same name as your virtual network. Select Connect. A pop-up message may appear. Select Continue to use elevated privileges.

5. On the Connection status page, select Connect to start the connection. If you see a Select Certificate screen, verify that the client certificate showing is the one that you want to use to connect. If it isn't, use the drop-down arrow to select the correct certificate, and then select OK.
