---
title: "Configurar uma rede virtual do Azure (clássico) - arquivo de configuração de rede | Microsoft Docs"
description: "Saiba como criar e modificar redes virtuais (clássico) exportando, alterando e importando um arquivo de configuração de rede."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: f1e3ae26b6525f2235a6b0d53546b334dc027b94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Configurar uma rede virtual (clássico) usando um arquivo de configuração de rede
> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo de implantação do Gerenciador de Recursos.

Você pode criar e configurar uma rede virtual (clássica) com um arquivo de configuração de rede usando a interface de linha de comando (CLI) 1.0 do Azure ou o Azure PowerShell. Não é possível criar ou modificar uma rede virtual por meio do modelo de implantação do Azure Resource Manager usando um arquivo de configuração de rede. Não é possível utilizar o portal do Azure para criar ou modificar uma rede virtual (clássica) utilizando um arquivo de configuração de rede, no entanto, você pode usar o portal do Azure para criar uma rede virtual (clássica) sem utilizar um arquivo de configuração de rede.

Criar e configurar uma rede virtual (clássica) com um arquivo de configuração de rede requer exportar, alterar e importar o arquivo.

## <a name="export"></a>Exportar um arquivo de configuração de rede

É possível utilizar o PowerShell ou CLI do Azure para exportar um arquivo de configuração de rede. O PowerShell exporta um arquivo XML, enquanto a CLI do Azure exporta um arquivo json.

### <a name="powershell"></a>PowerShell
 
1. [Instalar o Azure PowerShell e entrar no Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Altere o diretório (e certifique-se de que ele existe) e o nome do arquivo no seguinte comando conforme desejado, em seguida, execute o comando para exportar o arquivo de configuração de rede:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>CLI 1.0 do Azure

1. [Instalar a CLI 1.0 do Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Conclua as etapas restantes em um prompt de comando da CLI 1.0 do Azure.
2. Faça logon no Azure digitando o comando `azure login`.
3. Certifique-se de que está no modo de asm inserindo o `azure config mode asm` comando.
4. Altere o diretório (e certifique-se de que ele existe) e o nome do arquivo no seguinte comando conforme desejado, em seguida, execute o comando para exportar o arquivo de configuração de rede:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Criar ou modificar um arquivo de configuração de rede

Um arquivo de configuração de rede é um arquivo XML (ao usar o PowerShell) ou um arquivo json (ao usar a CLI do Azure). É possível editar o arquivo em qualquer texto ou editor XML/json. O artigo [Configurações de esquema de arquivo de configuração de rede](https://msdn.microsoft.com/library/azure/jj157100.aspx) inclui detalhes de todas as configurações. Para obter explicações adicionais sobre as configurações, consulte [Exibir redes virtuais e configurações](virtual-network-manage-network.md#view-vnet). As alterações feitas no arquivo:

- Deverão cumprir o esquema ou a importação do arquivo de configuração da rede falhará.
- Substituirão todas as configurações de rede existentes para sua assinatura, portanto, tenha extremo cuidado ao realizar as modificações. Por exemplo, faça referência aos exemplos de arquivos de configuração de rede que se seguem. Digamos que o arquivo original continha duas instâncias **VirtualNetworkSite** e foram alteradas, conforme mostrado nos exemplos. Ao importar o arquivo, o Azure exclui a rede virtual da instância **VirtualNetworkSite** removida no arquivo. Esse cenário simplificado não considera recursos na rede virtual, como se existisse, a rede virtual não poderia ser excluída e, a importação falharia.

> [!IMPORTANT]
> O Azure considera uma sub-rede com algo implantado como **em uso**. Quando uma sub-rede estiver em uso, ela não poderá ser modificada. Antes de modificar as informações de sub-rede em um arquivo de configuração de rede, mova todas as implantações existentes na sub-rede para uma sub-rede diferente que não está sendo modificada. Consulte [Mover uma VM ou instância de função para uma sub-rede diferente](virtual-networks-move-vm-role-to-subnet.md) para obter mais detalhes.

### <a name="example-xml-for-use-with-powershell"></a>Exemplo de XML para uso com o PowerShell

O arquivo de configuração de rede de exemplo a seguir cria uma rede virtual nomeada *myVirtualNetwork* com um espaço de endereçamento de *10.0.0.0/16* na regão *Leste dos EUA* do Azure. A rede virtual contém uma sub-rede nomeada *mySubnet* com um prefixo de endereço *10.0.0.0/24*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns />
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myVirtualNetwork" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="mySubnet">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

Se o arquivo de configuração de rede que você exportou não contém conteúdo, você pode copiar o XML no exemplo anterior e colá-lo em um novo arquivo.

### <a name="example-json-for-use-with-the-azure-cli-10"></a>Exemplo JSON para uso com o CLI 1.0 do Azure

O arquivo de configuração de rede de exemplo a seguir cria uma rede virtual nomeada *myVirtualNetwork* com um espaço de endereçamento de *10.0.0.0/16* na regão *Leste dos EUA* do Azure. A rede virtual contém uma sub-rede nomeada *mySubnet* com um prefixo de endereço *10.0.0.0/24*.

```json
{
   "VirtualNetworkConfiguration" : {
      "Dns" : "",
      "VirtualNetworkSites" : [
         {
            "AddressSpace" : [ "10.0.0.0/16" ],
            "Location" : "East US",
            "Name" : "myVirtualNetwork",
            "Subnets" : [
               {
                  "AddressPrefix" : "10.0.0.0/24",
                  "Name" : "mySubnet"
               }
            ]
         }
      ]
   }
}
```

Se o arquivo de configuração de rede que você exportou não contém conteúdo, você pode copiar o json no exemplo anterior e colá-lo em um novo arquivo.

## <a name="import"></a>Importar um arquivo de configuração de rede

Você pode utilizar o PowerShell ou a CLI do Azure para importar um arquivo de configuração de rede. O PowerShell exporta um arquivo XML, enquanto a CLI do Azure exporta um arquivo json. Se a importação falhar, confirme se o arquivo está em conformidade com o [esquema de configuração de rede](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Instalar o Azure PowerShell e entrar no Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Altere o diretório e o nome do arquivo no seguinte comando, conforme necessário, e execute o comando para importar o arquivo de configuração de rede:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>CLI 1.0 do Azure

1. [Instalar a CLI 1.0 do Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Conclua as etapas restantes em um prompt de comando da CLI 1.0 do Azure.
2. Faça logon no Azure digitando o comando `azure login`.
3. Certifique-se de que está no modo de asm inserindo o `azure config mode asm` comando.
4. Altere o diretório e o nome do arquivo no seguinte comando, conforme necessário, e execute o comando para importar o arquivo de configuração de rede:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```
