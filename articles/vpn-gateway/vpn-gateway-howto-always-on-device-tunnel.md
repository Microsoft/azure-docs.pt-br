---
title: Configurar um túnel de VPN Always On para o gateway de VPN
description: Etapas para configurar o túnel de VPN Always On para o gateway de VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: cherylmc
ms.openlocfilehash: 98d8c2f6870be16f3eb92219fc3d02f988390a41
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295461"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Configurar um túnel de dispositivo VPN Always On

Um dos novos recursos do cliente de VPN (rede virtual privada) do Windows 10 é a capacidade de manter uma conexão VPN. Always On é um recurso do Windows 10 que permite que o perfil VPN ativo se conecte automaticamente e permaneça conectado com base em gatilhos — ou seja, entrada do usuário, alteração de estado da rede ou tela do dispositivo ativa.

Os gateways de rede virtual do Azure podem ser usados com o Windows 10 Always On para estabelecer túneis persistentes de usuário, bem como túneis de dispositivo para o Azure. Este artigo o ajudará a configurar um túnel de dispositivo VPN AlwaysOn.

Always On conexões VPN incluem dois tipos de túneis:

* O **túnel de dispositivo** se conecta a servidores VPN especificados antes que os usuários entrem no dispositivo. Os cenários de conectividade de pré-logon e o gerenciamento de dispositivos usam o túnel de dispositivo.

* O **túnel do usuário** se conecta somente depois que um usuário entra no dispositivo. O túnel do usuário permite que os usuários acessem recursos da organização por meio de servidores VPN.

O túnel de dispositivo e o túnel de usuário operam de forma independente com seus perfis de VPN. Eles podem ser conectados ao mesmo tempo e podem usar diferentes métodos de autenticação e outras definições de configuração de VPN, conforme apropriado.

## <a name="1-configure-the-gateway"></a>1. Configurar o gateway

Configure o gateway de VPN para usar a autenticação IKEv2 e com base em certificado usando este [artigo de ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-user-tunnel"></a>2. Configurar o túnel do usuário

1. Instale certificados de cliente no cliente do Windows 10, conforme mostrado neste [artigo de cliente VPN ponto a site](point-to-site-how-to-vpn-client-install-azure-cert.md). O certificado precisa estar no repositório do usuário atual
2. Configure o cliente VPN Always On por meio do PowerShell, SCCM ou Intune usando [estas instruções](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

## <a name="3-configure-the-device-tunnel"></a>3. Configurar o túnel do dispositivo

Os requisitos a seguir devem ser atendidos para estabelecer com êxito um túnel de dispositivo:

* O dispositivo deve ser um computador ingressado no domínio que executa o Windows 10 Enterprise ou o Education versão 1709 ou posterior.
* O túnel só é configurável para a solução de VPN interna do Windows e é estabelecido usando IKEv2 com autenticação de certificado do computador. 
* Somente um túnel de dispositivo pode ser configurado por dispositivo.

1. Instale certificados de cliente no cliente do Windows 10, conforme mostrado neste [artigo de cliente VPN ponto a site](point-to-site-how-to-vpn-client-install-azure-cert.md). O certificado precisa estar no repositório do computador local.
1. Use [estas instruções](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) para criar um perfil VPN e configurar o túnel de dispositivo no contexto da conta sistema local.

### <a name="configuration-example-for-device-tunnel"></a>Exemplo de configuração para o túnel de dispositivo

Depois de configurar o gateway de rede virtual e instalar o certificado do cliente no repositório do computador local no cliente do Windows 10, use os exemplos a seguir para configurar um túnel de dispositivo do cliente.

1. Copie o texto a seguir e salve-o como ***devicecert. ps1***.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Copie o texto a seguir e salve-o como ***VPNProfile. xml*** na mesma pasta que **devicecert. ps1**. Edite o texto a seguir para corresponder ao seu ambiente.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Baixe o **PsExec** da [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) e extraia os arquivos para **C:\PSTools**.
1. Em um prompt de comando de administrador, inicie o PowerShell executando:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![PowerShell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. No PowerShell, alterne para a pasta em que **devicecert. ps1** e **VPNProfile. xml** estão localizados e execute o seguinte comando:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Execute o **Rasphone**.

   ![Rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Procure a entrada **MachineCertTest** e clique em **conectar**.

   ![Connect](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Se a conexão for realizada com sucesso, reinicialize o computador. O túnel será conectado automaticamente.

## <a name="cleanup"></a>Limpeza

Para remover o perfil, execute o seguinte comando:

![Limpeza](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Próximas etapas

Para solucionar problemas, consulte [problemas de conexão de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
