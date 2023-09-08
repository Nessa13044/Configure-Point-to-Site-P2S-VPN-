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


      
 


  

  
