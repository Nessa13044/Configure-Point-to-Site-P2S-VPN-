# What protocol does P2S use?
Point-to-site VPN can use one of the following protocols:

- OpenVPN Protocol, an SSL/TLS based VPN protocol. A TLS VPN solution can penetrate firewalls, since most firewalls open TCP port 443 outbound, which TLS uses. OpenVPN can be used to connect from Android, iOS (versions 11.0 and above), Windows, Linux, and Mac devices (macOS versions 10.13 and above).

- Secure Socket Tunneling Protocol (SSTP), a proprietary TLS-based VPN protocol. A TLS VPN solution can penetrate firewalls, since most firewalls open TCP port 443 outbound, which TLS uses. SSTP is only supported on Windows devices. Azure supports all versions of Windows that have SSTP and support TLS 1.2 (Windows 8.1 and later).

- IKEv2 VPN, a standards-based IPsec VPN solution. IKEv2 VPN can be used to connect from Mac devices (macOS versions 10.11 and above)

# How are P2S VPN clients authenticated?
- Before Azure accepts a P2S VPN connection, the user has to be authenticated first. There are two mechanisms that Azure offers to authenticate a connecting user.
# Certificate authentication
- When using the native Azure certificate authentication, a client certificate that is present on the device is used to authenticate the connecting user. Client certificates are generated from a trusted root certificate and then installed on each client computer. we can use a root certificate that was generated using an Enterprise solution, or you can generate a self-signed certificate.
- The validation of the client certificate is performed by the VPN gateway and happens during establishment of the P2S VPN connection. The root certificate is required for the validation and must be uploaded to Azure.
- P2S Azure certificate authentication connections use the following items, which you'll configure in this exercise:
+ A RouteBased VPN gateway.
The public key (.cer file) for a root certificate, which is uploaded to Azure. Once the certificate is uploaded, it's considered a trusted certificate and is used for authentication.
+ A client certificate that is generated from the root certificate. The client certificate installed on each client computer that will connect to the VNet. This certificate is used for client authentication.
+ VPN client configuration files. The VPN client is configured using VPN client configuration files. These files contain the necessary information for the client to connect to the VNet. Each client that connects must be configured using the settings in the configuration files.

# Azure Active Directory authentication
- Azure AD authentication allows users to connect to Azure using their Azure Active Directory credentials. Native Azure AD authentication is only supported for OpenVPN protocol and also requires the use of the Azure VPN Client. The supported client operation systems are Windows 10 or later and macOS. With native Azure AD authentication, we can use Azure AD's conditional access and Multi-Factor Authentication (MFA) features for VPN.
# What are the client configuration requirements?
- The client configuration requirements vary, based on the VPN client that you use, the authentication type, and the protocol.
![image](https://github.com/Nessa13044/Configure-Point-to-Site-P2S-VPN-/assets/114730329/8f06e9e4-1d17-4253-959a-243699f682d0)





