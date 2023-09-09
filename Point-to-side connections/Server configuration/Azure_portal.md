![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/6ad6796b-1d6b-4330-835f-e671009c9273)

P2S VPN connections are useful when you want to connect to your VNet from a remote location, such as when you're telecommuting from home or a conference. You can also use P2S instead of a site-to-site (S2S) VPN when you have only a few clients that need to connect to a virtual network (VNet).
# Prerequisites
# VNET 
- VNet Name: VNet1
- Address space: 10.1.0.0/16
For this example, we use only one address space. You can have more than one address space for your VNet.
Subnet name: FrontEnd
- Subnet address range: 10.1.0.0/24
- Subscription: 
- Resource Group: TestRG1
- Location: East US
# Virtual network gateway
- Virtual network gateway name: VNet1GW
- Gateway type: VPN
- VPN type: Route-based
- SKU: VpnGw2
- Generation: Generation2
- Gateway subnet address range: 10.1.255.0/27
- Public IP address name: VNet1GWpip
# Connection type and client address pool
Connection type: Point-to-site
- Client address pool: 172.16.201.0/24
- VPN clients that connect to the VNet using this point-to-site connection receive an IP address from the client address pool.

  # 1.Create a VNET
  ![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/1d105d04-dbbb-4aea-85a0-eab58a92baf0)

  - Select IP Addresses to advance to the IP Addresses tab. On the IP Addresses tab, configure the settings.
  - Subnet address range: The address range for this subnet. 10.1.0.0 and /24.
  ![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/69bd2a55-0669-4857-9c5b-cf6a7d148891)
# 2. Create the VPN gateway
- Creating a gateway can often take 45 minutes or more, depending on the selected gateway SKU.
![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/43be7d73-c2b6-4c11-933d-18e9f4605751)
- specify the number of IP addresses that the subnet contains. The number of IP addresses needed depends on the VPN gateway configuration that you want to create. Some configurations require more IP addresses than others. It's best to specify /27 or larger (/26,/25 etc.) for your gateway subnet.
- note: If you see an error that specifies that the address space overlaps with a subnet, or that the subnet isn't contained within the address space for your virtual network, check your VNet address range. You may not have enough IP addresses available in the address range you created for your virtual network. For example, if your default subnet encompasses the entire address range, there are no IP addresses left to create additional subnets. You can either adjust your subnets within the existing address space to free up IP addresses, or specify an additional address range and create the gateway subnet there.

NEXT: Specify in the values for Public IP address. These settings specify the public IP address object that gets associated to the VPN gateway. The public IP address is assigned to this object when the VPN gateway is created. The only time the primary public IP address changes is when the gateway is deleted and re-created. It doesn't change across resizing, resetting, or other internal maintenance/upgrades of your VPN gateway.
![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/3bd53485-0ab0-441f-bb58-271ae172c714)

# Generate certificates
- Certificates are used by Azure to authenticate clients connecting to a VNet over a point-to-site VPN connection. Once you obtain a root certificate, you upload the public key information to Azure. The root certificate is then considered 'trusted' by Azure for connection over P2S to the VNet.
- generate client certificates from the trusted root certificate, and then install them on each client computer. The client certificate is used to authenticate the client when it initiates a connection to the VNet.
# Generate a root certificate
Obtain the .cer file for the root certificate. You can use either a root certificate that was generated with an enterprise solution (recommended), or generate a self-signed certificate. After you create the root certificate, export the public certificate data (not the private key) as a Base64 encoded X.509 .cer file. You upload this file later to Azure.
- Self-signed root certificate: If you aren't using an enterprise certificate solution, create a self-signed root certificate. Otherwise, the certificates you create won't be compatible with your P2S connections and clients receive a connection error when they try to connect. You can use (1)Azure PowerShell, (2)MakeCert, (3)or OpenSSL.
# (1) Generate and export certificates for point-to-site using PowerShell
- Create a self-signed root certificate
Use the New-SelfSignedCertificate cmdlet to create a self-signed root certificate. For additional parameter information, see New-SelfSignedCertificate.
1. From a computer running Windows 10 or later, or Windows Server 2016, open a Windows PowerShell console with elevated privileges.
2. Create a self-signed root certificate. The following example creates a self-signed root certificate named 'P2SRootCert' that's 
automatically installed in 'Certificates-Current User\Personal\Certificates'.
![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/51e94c12-5340-4ba4-a04c-12672a7bdae4)

# Generate client certificates
- Each client computer that you connect to a VNet with a point-to-site connection must have a client certificate installed. You generate it from the root certificate and install it on each client computer. If you don't install a valid client certificate, authentication will fail when the client tries to connect to the VNet.
- You can either generate a unique certificate for each client, or you can use the same certificate for multiple clients. The advantage to generating unique client certificates is the ability to revoke a single certificate. Otherwise, if multiple clients use the same client certificate to authenticate and you revoke it, you'll need to generate and install new certificates for every client that uses that certificate.
- When you generate a client certificate from a self-signed root certificate, it's automatically installed on the computer that you used to generate it. If you want to install a client certificate on another client computer, export it as a .pfx file, along with the entire certificate chain. Doing so will create a .pfx file that contains the root certificate information required for the client to authenticate.
# PowerShell console session still open on step (1)
- This session continues from the previous section and uses the declared '$cert' variable.
- the result is a client certificate named 'P2SChildCert'. If you want to name the child certificate something else, modify the CN value.

![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/1bcd14e9-fcd7-423a-87ab-7a7d70c7a499)
![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/b25aac49-75a8-4f67-bb85-15e79dc82921)

# Export the root certificate public key (.cer)
- After create a self-signed root certificate, export the root certificate .cer file (not the private key). You'll later upload the necessary certificate data contained in the file to Azure. The following steps help you export the .cer file for your self-signed root certificate and retrieve the necessary certificate data.
1. To get the certificate .cer file, open Manage user certificates.
   Locate the self-signed root certificate, typically in "Certificates - Current User\Personal\Certificates", and right-click. Click All Tasks -> Export. This opens the Certificate Export Wizard.
   
![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/58e5a0cd-6266-41e1-bab7-c87dbe231e1e)

2. In the wizard, click Next.
3. Select No, do not export the private key, and then click Next.

![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/d2220808-d568-489c-962a-142a534563d2)

4. On the Export File Format page, select Base-64 encoded X.509 (.CER)., and then click Next.

![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/87fc92e8-8227-4377-8c55-ef3127d169a8)

5. For File to Export, Browse to the location to which you want to export the certificate. For File name, name the certificate file. Then, click Next.
6. Go to the location where exported the certificate and open it using a text editor, such as Notepad. If you exported the certificate in the required Base-64 encoded X.509 (.CER) format, you'll see text similar to the following example. The section highlighted in blue contains the information that you copy and upload to Azure.

![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/8415096c-bf80-4730-9015-c1b80bbad5a2)

# Export the client certificate
- When you generate a client certificate, it's automatically installed on the computer that you used to generate it. If you want to install the client certificate on another client computer, you need to first export the client certificate.
1. To export a client certificate, open Manage user certificates. The client certificates that you generated are, by default, located in 'Certificates - Current User\Personal\Certificates'. Right-click the client certificate that you want to export, click all tasks, and then click Export to open the Certificate Export Wizard.

![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/45fb600e-ca2e-4e1e-8365-e082401a56a5)

2. In the Certificate Export Wizard, click Next to continue.
3. Select Yes, export the private key, and then click Next.

![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/3ba4a460-8f8c-4b33-84af-ce1523081b18)

4. On the Export File Format page, leave the defaults selected. Make sure that Include all certificates in the certification path if possible is selected. This setting additionally exports the root certificate information that is required for successful client authentication. Without it, client authentication fails because the client doesn't have the trusted root certificate. Then, click Next.

![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/34ea7845-4241-4873-93ab-b4a27b82e47c)

5. On the Security page, you must protect the private key. If you select to use a password, make sure to record or remember the password that you set for this certificate. Then, click Next.

![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/44b14d96-8b14-448e-bbb3-639fbafe3a34)

6. Click Finish to export the certificate.

# Add the address pool
1. Go to the gateway you created in the previous section.
2. In the left pane, select Point-to-site configuration.
3. Click Configure now to open the configuration page.
- The client address pool is a range of private IP addresses that you specify. The clients that connect over a point-to-site VPN dynamically receive an IP address from this range. Use a private IP address range that doesn't overlap with the on-premises location that you connect from, or the VNet that you want to connect to. If you configure multiple protocols and SSTP is one of the protocols, then the configured address pool is split between the configured protocols equally.

![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/a343b659-c8a2-40ec-b0b2-a2cab127b89d)

# Authentication type
- select Azure certificate for the authentication type.

# Upload root certificate public key information
- you upload public root certificate that you create on privious session. Once the public certificate data is uploaded, Azure can use it to authenticate clients that have installed a client certificate generated from the trusted root certificate.

![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/4f760480-7341-4fdb-b21b-ae0228e85d05)


























      
 


  

  
