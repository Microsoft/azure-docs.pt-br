---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: bda289e73b9a782cd56c0c94b8f53e8002b1ccf4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171839"
---
## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>Como criar uma rede virtual usando um arquivo de configuração de rede do PowerShell
O Azure usa um arquivo xml para definir todas as redes virtuais disponíveis para uma assinatura. Você pode baixar o arquivo, editá-lo para modificar ou excluir as redes virtuais existentes e criar novas redes virtuais. Neste tutorial, você aprenderá a baixar esse arquivo, conhecido como arquivo de configuração de rede (ou netcgf) e editá-lo para criar uma nova rede virtual. Para saber mais sobre o arquivo de configuração de rede, veja o [esquema de configuração de rede virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Para criar uma rede virtual com um arquivo netcfg usando o PowerShell, conclua as seguintes etapas:

1. Caso você nunca tenha usado o Azure PowerShell, conclua as etapas do artigo [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs), então entre no Azure e selecione sua assinatura.
2. No console do Azure PowerShell, use o cmdlet **Get-AzureVnetConfig** para baixar o arquivo de configuração de rede para um diretório em seu computador ao executar o seguinte comando: 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   Saída esperada:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. Abra o arquivo que você salvou na etapa 2 usando qualquer aplicativo de editor de texto ou XML e procure o  **\<VirtualNetworkSites >** elemento. Se você tiver redes já criadas, cada rede é exibida como seu próprio  **\<VirtualNetworkSite >** elemento.
4. Para criar a rede virtual descrita neste cenário, adicione o seguinte XML logo abaixo do  **\<VirtualNetworkSites >** elemento:

   ```xml
         <?xml version="1.0" encoding="utf-8"?>
         <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
           <VirtualNetworkConfiguration>
             <VirtualNetworkSites>
                 <VirtualNetworkSite name="TestVNet" Location="East US">
                   <AddressSpace>
                     <AddressPrefix>192.168.0.0/16</AddressPrefix>
                   </AddressSpace>
                   <Subnets>
                     <Subnet name="FrontEnd">
                       <AddressPrefix>192.168.1.0/24</AddressPrefix>
                     </Subnet>
                     <Subnet name="BackEnd">
                       <AddressPrefix>192.168.2.0/24</AddressPrefix>
                     </Subnet>
                   </Subnets>
                 </VirtualNetworkSite>
             </VirtualNetworkSites>
           </VirtualNetworkConfiguration>
         </NetworkConfiguration>
   ```
   
5. Salve o arquivo de configuração de rede.
6. No console do Azure PowerShell, use o cmdlet **Set-AzureVnetConfig** para carregar o arquivo de configuração de rede executando o comando a seguir: 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   Saída retornada:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   Se **OperationStatus** não tiver *Êxito* na saída retornada, verifique novamente o arquivo xml em busca de erros e conclua a etapa 6 novamente.

7. No console do Azure PowerShell, use o cmdlet **Get-AzureVnetSite** para verificar se a nova rede foi adicionada executando o comando a seguir: 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   A saída (abreviada) retornada inclui o seguinte texto:
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```
