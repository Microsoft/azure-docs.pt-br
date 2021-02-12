---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 34e841a5f17d589c4fbef54a4a8674a99ac6c640
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027370"
---
Os requisitos a seguir devem ser atendidos para estabelecer com êxito um túnel de dispositivo:

* O dispositivo deve ser um computador ingressado no domínio que executa o Windows 10 Enterprise ou o Education versão 1809 ou posterior.
* O túnel só é configurável para a solução de VPN interna do Windows e é estabelecido usando IKEv2 com autenticação de certificado do computador.
* Somente um túnel de dispositivo pode ser configurado por dispositivo.

1. Instale certificados de cliente no cliente do Windows 10 usando o artigo [cliente VPN ponto a site](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) . O certificado precisa estar no repositório do computador local.
1. Crie um perfil VPN e configure o túnel de dispositivo no contexto da conta sistema LOCAL usando [estas instruções](/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration).

### <a name="configuration-example-for-device-tunnel"></a>Exemplo de configuração para o túnel de dispositivo

Depois de configurar o gateway de rede virtual e instalar o certificado do cliente no repositório do computador local no cliente do Windows 10, use os exemplos a seguir para configurar um túnel de dispositivo do cliente:

1. Copie o texto a seguir e salve-o como ***devicecert.ps1** _.

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
1. Copie o texto a seguir e salve-o como _*_VPNProfile.xml_*_ na mesma pasta que _ * devicecert.ps1 * *. Edite o texto a seguir para corresponder ao seu ambiente.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

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
1. Baixe o **PsExec** da [Sysinternals](/sysinternals/downloads/psexec) e extraia os arquivos para **C:\PSTools**.
1. Em um prompt de comando de administrador, inicie o PowerShell executando:

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![Captura de tela mostra uma janela de prompt de comando com um comando para iniciar a versão de 64 bits do PowerShell.](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. No PowerShell, alterne para a pasta onde **devicecert.ps1** e **VPNProfile.xml** estão localizados e execute o seguinte comando:

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![Captura de tela mostra uma janela do PowerShell que executou o MachineCertTest usando o script devicesert.](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. Execute o **Rasphone**.

   ![Captura de tela mostra uma caixa de diálogo Executar com o Rasphone selecionado.](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. Procure a entrada **MachineCertTest** e clique em **conectar**.

   ![Captura de tela mostra uma caixa de diálogo conexões de rede com MachineCertTest selecionado e um botão conectar.](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. Se a conexão for realizada com sucesso, reinicialize o computador. O túnel será conectado automaticamente.