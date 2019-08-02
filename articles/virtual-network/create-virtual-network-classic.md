---
title: Criar uma rede virtual do Azure (clássica) com várias sub-redes | Microsoft Docs
description: Saiba como criar uma rede virtual (clássica) com várias sub-redes no Azure.
services: virtual-network
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: e40648ef47b108050486d43eefdb1564786c053e
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202870"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Criar uma rede virtual (clássica) com várias sub-redes

> [!IMPORTANT]
> O Azure tem dois [diferentes modelos de implantação](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criar e trabalhar com recursos: Resource Manager e clássico. Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda criar a maioria das novas redes virtuais por meio do modelo de implantação do [Resource Manager](quick-create-portal.md).

Neste tutorial, aprenda como criar uma rede virtual do Azure básica (clássica) que tenha sub-redes públicas e privadas separadas. Você pode criar recursos do Azure, como Máquinas virtuais e Serviços de nuvem em uma sub-rede. Recursos criados em redes virtuais (clássicas) podem se comunicar entre si e com os recursos em outras redes conectadas a uma rede virtual.

Saiba mais sobre todas as configurações de [rede virtual](manage-virtual-network.md) e [sub-rede](virtual-network-manage-subnet.md).

> [!WARNING]
> Redes virtuais (clássicas) são excluídas imediatamente pelo Azure quando uma [assinatura é desabilitada](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Redes virtuais (clássicas) são excluídas independentemente de existirem recursos na rede virtual. Se você depois voltar a habilitar a assinatura, os recursos que existiam na rede virtual deverão ser recriados.

Você pode criar uma rede virtual (clássica) usando o [portal do Azure](#portal), o [a CLI (interface de linha de comando) 1.0 do Azure](#azure-cli) ou o [PowerShell](#powershell).

## <a name="portal"></a>Portal

1. Em um navegador da Internet, vá para o [portal do Azure](https://portal.azure.com). Faça logon usando sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, você poderá assinar uma versão de [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Clique em **Criar um recurso** no portal.
3. Insira a *Rede virtual* na caixa **Pesquisar no Marketplace** na parte superior do painel **Novo** que é exibido. Clique em **Rede virtual** quando essa opção aparecer entre os resultados da pesquisa.
4. Selecione **Clássico** na caixa **Selecionar um modelo de implantação** no painel **Rede Virtual** exibido e clique em **Criar**. 
5. Insira os valores a seguir no painel **Criar rede virtual (clássica)** e, em seguida, clique em **Criar**:

    |Configuração|Valor|
    |---|---|
    |NOME|myVnet|
    |Espaço de endereço|10.0.0.0/16|
    |Nome da sub-rede|Público|
    |Intervalo de endereços da sub-rede|10.0.0.0/24|
    |Grupo de recursos|Deixe **Criar novo** selecionado e digite **myResourceGroup**.|
    |Assinatura e localização|Selecione sua assinatura e localização.

    Se você for novo no Azure, saiba mais sobre [grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [assinaturas](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [locais](https://azure.microsoft.com/regions) (também conhecido como *regiões*).
4. No portal, você pode criar apenas uma sub-rede quando você criar uma rede virtual. Neste tutorial, você cria uma segunda sub-rede após criar a rede virtual. Posteriormente, você pode criar recursos acessíveis pela Internet na sub-rede **Pública**. Você também pode criar recursos que não são acessíveis pela Internet para na sub-rede **Privada**. Para criar a segunda sub-rede, digite **myVnet** na caixa **Pesquisar recursos** na parte superior da página. Clique em **myVnet** quando ele for exibido nos resultados da pesquisa.
5. Clique em **Sub-redes** (na seção **CONFIGURAÇÕES**) no painel **Criar rede virtual (clássica)** exibido.
6. Clique em **+Adicionar** no painel **myVnet – Sub-redes** exibido.
7. Insira **Privada** para **Nome** no painel **Adicionar sub-rede**. Digite **10.0.1.0/24** para **Intervalo de endereços**.  Clique em **OK**.
8. No painel **myVnet – Sub-redes**, é possível visualizar as sub-redes **Pública** e **Privada** que você criou.
9. **Opcional**: Quando você concluir este tutorial, você talvez queira excluir os recursos que você criou, para que você não incorra em encargos de uso:
    - Clique em **Visão geral** no painel **myVnet**.
    - Clique no ícone **Excluir** no painel **myVnet**.
    - Para confirmar a exclusão, clique em **Sim** na caixa **Excluir rede virtual**.

## <a name="azure-cli"></a>CLI do Azure

1. Você pode [instalar e configurar a CLI do Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou usar a CLI no Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Para obter ajuda sobre os comandos da CLI, digite `azure <command> --help`. 
2. Em uma sessão da CLI, faça logon no Azure com o comando a seguir. Se você clicar em **Experimente** na caixa abaixo, um Cloud Shell será aberto. Você pode fazer logon sua assinatura do Azure sem digitar o seguinte comando:

    ```azurecli-interactive
    azure login
    ```

3. Para garantir que a CLI esteja no modo de Gerenciamento de Serviço, digite o seguinte comando:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Crie uma rede virtual com uma sub-rede privada:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Crie uma sub-rede pública dentro da rede virtual:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Examine a rede virtual e as sub-redes:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Opcional**: Talvez você queira excluir os recursos que você criou quando você concluir este tutorial, para que você não incorra em encargos de uso:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Embora você não possa especificar um grupo de recursos para criar uma rede virtual (clássica) usando a CLI, o Azure cria a rede virtual em um grupo de recursos denominado *Rede Padrão*.

## <a name="powershell"></a>PowerShell

1. Instale a última versão do módulo [Azure](https://www.powershellgallery.com/packages/Azure) do PowerShell. Se você for novo no Azure PowerShell, consulte [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell.
3. No PowerShell, faça logon no Azure inserindo o comando `Add-AzureAccount`.
4. Altere o seguinte caminho e nome de arquivo, conforme apropriado, então exporte o arquivo de configuração de rede existente:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Para criar uma rede virtual com sub-redes públicas e privadas, use qualquer editor de texto para adicionar elemento **VirtualNetworkSite** que segue o arquivo de configuração de rede.

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    Examine o [esquema de arquivo de configuração de rede](https://msdn.microsoft.com/library/azure/jj157100.aspx) completo.

6. Importe o arquivo de configuração de rede:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Importar um arquivo de configuração de rede alterado pode causar alterações em redes virtuais existentes (clássico) na sua assinatura. Verifique se você adicionou apenas a rede virtual anterior e se você não alterou nem removeu nenhuma rede virtual existente da sua assinatura. 

7. Examine a rede virtual e as sub-redes:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Opcional**: Talvez você queira excluir os recursos que você criou quando você concluir este tutorial, para que você não incorra em encargos de uso. Para excluir a rede virtual, conclua as etapas 4 a 6 novamente, desta vez removendo o elemento **VirtualNetworkSite** adicionado na etapa 5.
 
> [!NOTE]
> Embora você não possa especificar um grupo de recursos para criar uma rede virtual (clássica) usando a o PowerShell, o Azure cria a rede virtual em um grupo de recursos denominado *Default-Networking*.

---

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre todas as configurações de sub-rede e rede virtual, confira [Gerenciar redes virtuais](manage-virtual-network.md) e [Gerenciar sub-redes da rede virtual](virtual-network-manage-subnet.md). Você tem várias opções para o uso de redes virtuais e sub-redes em um ambiente de produção para atender aos requisitos diferentes.
- Crie uma máquina virtual [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e a conecte a uma rede virtual existente.
- Para conectar duas redes virtuais no mesmo local do Azure, crie um [emparelhamento de rede virtual](create-peering-different-deployment-models.md) entre as redes virtuais. Você pode emparelhar uma rede virtual (Resource Manager) a uma rede virtual (clássica), mas não é possível criar um emparelhamento entre duas redes virtuais (clássicas).
- Conecte a rede virtual a uma rede local usando um [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou circuito do [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
