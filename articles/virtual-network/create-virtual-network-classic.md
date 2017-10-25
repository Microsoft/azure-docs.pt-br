---
title: "Criar uma rede virtual do Azure (clássica) com várias sub-redes | Microsoft Docs"
description: "Saiba como criar uma rede virtual (clássica) com várias sub-redes no Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 95c2f4fe40590a8d809f634fb5b2c92d07421bb0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Criar uma rede virtual (clássica) com várias sub-redes

> [!IMPORTANT]
> O Azure tem dois [modelos de implantação diferentes](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criar e trabalhar com recursos: Resource Manager e clássico. Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda criar a maioria das novas redes virtuais por meio do modelo de implantação do [Resource Manager](virtual-networks-create-vnet-arm-pportal.md).

Neste tutorial, aprenda como criar uma rede virtual do Azure básica (clássica) que tenha sub-redes públicas e privadas separadas. Você pode criar recursos do Azure, como Máquinas virtuais e Serviços de nuvem em uma sub-rede. Recursos criados em redes virtuais (clássicas) podem se comunicar entre si e com os recursos em outras redes conectadas a uma rede virtual.

Saiba mais sobre todas as configurações de [rede virtual](virtual-network-manage-network.md) e [sub-rede](virtual-network-manage-subnet.md).

> [!WARNING]
> Redes virtuais (clássicas) são excluídas imediatamente pelo Azure quando uma [assinatura é desabilitada](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Redes virtuais (clássicas) são excluídas independentemente de existirem recursos na rede virtual. Se você depois voltar a habilitar a assinatura, os recursos que existiam na rede virtual deverão ser recriados.

Você pode criar uma rede virtual (clássica) usando o [portal do Azure](#portal), o [a CLI (interface de linha de comando) 1.0 do Azure](#azure-cli) ou o [PowerShell](#powershell).

## <a name="portal"></a>Portal

1. Em um navegador da Internet, vá para o [portal do Azure](https://portal.azure.com). Faça logon usando sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, você poderá assinar uma versão de [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Clique em **+Novo** no portal.
3. Digite *Rede virtual* na caixa **Pesquisar no Marketplace** na parte superior da folha **Novo** que aparece.  Clique em **Rede virtual** quando essa opção aparecer entre os resultados da pesquisa.
4. Selecione **Clássico** na caixa **Selecionar um modelo de implantação** na folha **Rede Virtual** que aparece e clique em **Criar**. 
5. Insira os seguintes valores na folha **Criar rede virtual (clássica)** e depois clique em **Criar**:

    |Configuração|Valor|
    |---|---|
    |Nome|myVnet|
    |Espaço de endereço|10.0.0.0/16|
    |Nome da sub-rede|Público|
    |Intervalo de endereços da sub-rede|10.0.0.0/24|
    |Grupo de recursos|Deixe **Criar novo** selecionado e digite **myResourceGroup**.|
    |Assinatura e localização|Selecione sua assinatura e localização.

    Se você for novo no Azure, saiba mais sobre [grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [assinaturas](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [locais](https://azure.microsoft.com/regions) (também conhecido como *regiões*).
4. No portal, você pode criar apenas uma sub-rede quando você criar uma rede virtual. Neste tutorial, você cria uma segunda sub-rede após criar a rede virtual. Posteriormente, você pode criar recursos acessíveis pela Internet na sub-rede **Pública**. Você também pode criar recursos que não são acessíveis pela Internet para na sub-rede **Privada**. Para criar a segunda sub-rede, digite **myVnet** na caixa **Pesquisar recursos** na parte superior da página. Clique em **myVnet** quando ele for exibido nos resultados da pesquisa.
5. Clique em **Sub-redes** (na seção **CONFIGURAÇÕES**) na folha **Criar rede virtual (clássica)** que aparece.
6. Clique em **+Adicionar** na folha **myVnet – Sub-redes** que aparece.
7. Digite **Privada** para **Nome** na folha **Adicionar sub-rede**. Digite **10.0.1.0/24** para **Intervalo de endereços**.  Clique em **OK**.
8. Na folha **myVnet – Sub-redes**, você pode ver as sub-redes **Pública** e **Privada** que você criou.
9. **Opcional**: quando você concluir este tutorial, talvez queira excluir os recursos criados para não incorrer em encargos de uso:
    - Clique em **Visão geral** na folha **myVnet**.
    - Clique no ícone **Excluir** na folha **myVnet**.
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

7. **Opcional**: talvez queira excluir os recursos criados quando você concluir este tutorial para não incorrer em encargos de uso:

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

8. **Opcional**: talvez você queira excluir os recursos criados ao concluir este tutorial para não incorrer em encargos de uso. Para excluir a rede virtual, conclua as etapas 4 a 6 novamente, desta vez removendo o elemento **VirtualNetworkSite** adicionado na etapa 5.
 
> [!NOTE]
> Embora você não possa especificar um grupo de recursos para criar uma rede virtual (clássica) usando a o PowerShell, o Azure cria a rede virtual em um grupo de recursos denominado *Default-Networking*.

---

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre todas as configurações de sub-rede e rede virtual, confira [Gerenciar redes virtuais](virtual-network-manage-network.md) e [Gerenciar sub-redes da rede virtual](virtual-network-manage-subnet.md). Você tem várias opções para o uso de redes virtuais e sub-redes em um ambiente de produção para atender aos requisitos diferentes.
- Para filtrar o tráfego de entrada e saída da sub-rede, crie e aplique [grupos de segurança de rede](virtual-networks-nsg.md) às sub-redes.
- Crie uma máquina virtual [Windows](../virtual-machines/windows/classic/createportal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/classic/createportal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e a conecte a uma rede virtual existente.
- Para conectar duas redes virtuais no mesmo local do Azure, crie um [emparelhamento de rede virtual](create-peering-different-deployment-models.md) entre as redes virtuais. Você pode emparelhar uma rede virtual (Resource Manager) a uma rede virtual (clássica), mas não é possível criar um emparelhamento entre duas redes virtuais (clássicas).
- Conecte a rede virtual a uma rede local usando um [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou circuito do [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
