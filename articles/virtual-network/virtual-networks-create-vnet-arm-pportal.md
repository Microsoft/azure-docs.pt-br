---
title: "Criar uma rede virtual do Azure com várias sub-redes | Microsoft Docs"
description: "Saiba como criar uma rede virtual com várias sub-redes no Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: f82a95ec9543b2d53ef28bf7f15315e23cf4893a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Criar uma rede virtual com várias sub-redes

Neste tutorial, aprenda como criar uma rede virtual do Azure básica com sub-redes públicas e privadas separadas. Os recursos em redes virtuais podem se comunicar entre si e com os recursos em outras redes conectadas a uma rede virtual. Você pode criar recursos do Azure, como máquinas virtuais, ambientes de Serviço de Aplicativo, conjuntos de dimensionamento de máquina virtual, Azure HDInsight e serviços de nuvem na mesma ou em diferentes sub-redes dentro de uma rede virtual. Criar recursos em diferentes sub-redes permite filtrar o tráfego de rede de entrada e de saída de sub-redes independentemente com [grupos de segurança de rede](virtual-networks-create-nsg-arm-pportal.md), bem como [rotear o tráfego entre sub-redes](virtual-network-create-udr-arm-ps.md) por meio de soluções de virtualização de rede, tais como um firewall, se você assim escolher. 

As seções a seguir incluem etapas que você pode executar para criar uma rede virtual usando o [Portal do Azure](#portal), a interface de linha de comando do Azure ([CLI do Azure](#azure-cli)), [Azure PowerShell](#powershell)e um [modelo do Azure Resource Manager](#resource-manager-template). O resultado é o mesmo, não importa a ferramenta que você usa para criar a rede virtual. Clique no link da ferramenta para ir para essa seção do tutorial. Saiba mais sobre todas as configurações de [rede virtual](virtual-network-manage-network.md) e [sub-rede](virtual-network-manage-subnet.md).

Este artigo fornece etapas para criar uma rede virtual usando o modelo de implantação do Resource Manager, que é o modelo de implantação recomendado ao criar novas redes virtuais. Se você precisar criar uma rede virtual (clássica), confira [Criar uma rede virtual (clássica)](create-virtual-network-classic.md). Se você não estiver familiarizado com os modelos de implantação do Azure, confira [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="portal"></a>Portal do Azure

1. Em um navegador da Internet, vá para o [portal do Azure](https://portal.azure.com). Faça logon usando sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, você poderá assinar uma versão de [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. No portal, clique em **+ Novo** > **Rede** > **Rede virtual**.
3. Na folha **Criar rede virtual**, insira os valores a seguir e clique em **Criar**:

    |Configuração|Valor|
    |---|---|
    |Nome|myVnet|
    |Espaço de endereço|10.0.0.0/16|
    |Nome da sub-rede|Público|
    |Intervalo de endereços da sub-rede|10.0.0.0/24|
    |Grupo de recursos|Deixe **Criar novo** selecionado e digite **myResourceGroup**.|
    |Assinatura e localização|Selecione sua assinatura e localização.

    Se você for novo no Azure, saiba mais sobre [grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [assinaturas](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [locais](https://azure.microsoft.com/regions) (também conhecido como *regiões*).
4. No portal, você pode criar apenas uma sub-rede quando você criar uma rede virtual. Neste tutorial, você cria uma segunda sub-rede após criar a rede virtual. Posteriormente, você pode criar recursos acessíveis pela Internet na sub-rede **Pública**. Você também pode criar recursos que não são acessíveis pela Internet para na sub-rede **Privada**. Para criar a segunda sub-rede, na caixa **Pesquisar recursos** na parte superior da página, digite **myVnet**. Nos resultados da pesquisa, clique em **myVnet**. Se você tiver várias redes virtuais com o mesmo nome em sua assinatura, verifique os grupos de recursos que estão listados em cada rede virtual. Certifique-se de que você clique o resultado de pesquisa **myVnet** que tem o grupo de recursos **myResourceGroup**.
5. Na folha **myVnet**, em **CONFIGURAÇÕES**, clique em **Sub-redes**.
6. Na folha **myVnet – Sub-redes**, clique em **+Sub-rede**.
7. Na folha **Adicionar sub-rede**, para **Nome**, insira **Privada**. Para **Intervalo de endereços**, digite **10.0.1.0/24**.  Clique em **OK**.
8. Na folha **myVnet – Sub-Redes**, examine as sub-redes. Você pode ver as sub-redes **Pública** e **Privada** que você criou.
9. **Opcional:** conclua os tutoriais adicionais listados em [Próximas etapas](#next-steps) para filtrar o tráfego de rede de entrada e de saída de cada sub-rede com grupos de segurança de rede, para rotear o tráfego entre as sub-redes por meio de uma solução de virtualização de rede ou então para conectar a rede virtual a outras redes virtuais ou redes locais.
10. **Opcional**: exclua os recursos criados neste tutorial concluindo as etapas em [Excluir recursos](#delete-portal).

## <a name="azure-cli"></a>CLI do Azure

Comandos da CLI do Azure são os mesmos, se você executar os comandos do Windows, Linux ou macOS. No entanto, há diferenças de script entre shells de sistema operacional. O script nas etapas a seguir é executado em um shell Bash. 

1. [Instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente da CLI do Azure instalada. Para obter ajuda sobre os comandos da CLI, digite `az <command> --help`. Em vez de instalar a CLI e seus pré-requisitos, use o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. O Cloud Shell tem a CLI do Azure pré-instalada e configurada para uso com sua conta. Para usar o Cloud Shell, clique no botão Cloud Shell (**> _**) na parte superior do [portal](https://portal.azure.com), ou simplesmente clique no botão *Experimentar* nas etapas a seguir. 
2. Se você estiver executando a CLI localmente, faça logon no Azure com o comando `az login`. Se estiver usando o Cloud Shell, você já estará conectado.
3. Confira o script a seguir e seus comentários. No navegador, copie o script e cole-o em sua sessão da CLI:

    ```azurecli-interactive
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet named Public.
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet named Private in the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name myVnet \
      --resource-group myResourceGroup
    ```
    
4. Após a conclusão da execução do script, examine as sub-redes da rede virtual. Copie o comando a seguir, e cole-o em sua sessão da CLI:

    ```azurecli
    az network vnet subnet list --resource-group myResourceGroup --vnet-name myVnet --output table
    ```

5. **Opcional:** conclua os tutoriais adicionais listados em [Próximas etapas](#next-steps) para filtrar o tráfego de rede de entrada e de saída de cada sub-rede com grupos de segurança de rede, para rotear o tráfego entre as sub-redes por meio de uma solução de virtualização de rede ou então para conectar a rede virtual a outras redes virtuais ou redes locais.
6. **Opcional**: exclua os recursos criados neste tutorial concluindo as etapas em [Excluir recursos](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Instale a última versão do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se você for novo no Azure PowerShell, consulte [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Em uma sessão do PowerShell, faça logon no Azure com sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) usando o comando `login-azurermaccount`.

3. Confira o script a seguir e seus comentários. No navegador, copie o script e cole-o em sua sessão do PowerShell:

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location eastus
    
    # Create the public and private subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Location eastus `
      -Name myVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    ```

4. Para examinar as sub-redes para a rede virtual, copie o seguinte comando e, depois, cole-o em sua sessão do PowerShell:

    ```powershell
    $Vnet.subnets | Format-Table Name, AddressPrefix
    ```

5. **Opcional:** conclua os tutoriais adicionais listados em [Próximas etapas](#next-steps) para filtrar o tráfego de rede de entrada e de saída de cada sub-rede com grupos de segurança de rede, para rotear o tráfego entre as sub-redes por meio de uma solução de virtualização de rede ou então para conectar a rede virtual a outras redes virtuais ou redes locais.
6. **Opcional**: exclua os recursos criados neste tutorial concluindo as etapas em [Excluir recursos](#delete-powershell).

## <a name="resource-manager-template"></a>Modelo do Resource Manager

Você pode implantar uma rede virtual usando um modelo do Azure Resource Manager. Para saber mais sobre modelos, consulte [O que é o Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment). Para acessar o modelo e saber mais sobre seus parâmetros, consulte o modelo [Criar uma rede virtual com duas sub-redes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Você pode implantar o modelo usando [portal](#template-portal), [CLI do Azure](#template-cli) ou [PowerShell](#template-powershell).

Etapas opcionais depois de implantar o modelo:

1. Conclua os tutoriais adicionais listados em [Próximas etapas](#next-steps) para filtrar o tráfego de rede de entrada e de saída de cada sub-rede com grupos de segurança de rede, para rotear o tráfego entre as sub-redes por meio de uma solução de virtualização de rede ou então para conectar a rede virtual a outras redes virtuais ou redes locais.
2. Exclua os recursos criados neste tutorial concluindo as etapas em quaisquer subseções de [Excluir recursos](#delete).

### <a name="template-portal"></a>Portal do Azure

1. No seu navegador, abra a [página modelo](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets).
2. Clique no botão **Implantar no Azure**. Se você ainda não estiver conectado ao Azure, faça logon na tela de logon do Portal do Azure que é exibida.
3. Entre no portal usando sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, você poderá assinar uma versão de [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Insira os seguintes valores para os parâmetros:

    |Parâmetro|Valor|
    |---|---|
    |Assinatura|Selecione sua assinatura|
    |Grupo de recursos|myResourceGroup|
    |Local|Selecione um local|
    |Nome da VNet|myVnet|
    |Prefixo de Endereço da VNet|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Público|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Privado|

5. Concorde com os termos e condições, clique em **Comprar** para implantar uma rede virtual.

### <a name="template-cli"></a>Azure CLI

1. [Instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente da CLI do Azure instalada. Para obter ajuda sobre os comandos da CLI, digite `az <command> --help`. Em vez de instalar a CLI e seus pré-requisitos, use o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. O Cloud Shell tem a CLI do Azure pré-instalada e configurada para uso com sua conta. Para usar o Cloud Shell, clique no botão Cloud Shell **>_** na parte superior do [portal](https://portal.azure.com), ou simplesmente clique no botão **Experimentar** nas etapas a seguir. 
2. Se você estiver executando a CLI localmente, faça logon no Azure com o comando `az login`. Se estiver usando o Cloud Shell, você já estará conectado.
3. Para criar um grupo de recursos para a rede virtual, copie o seguinte comando e cole-o em sua sessão da CLI:

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    ```
    
4. Você pode implantar o modelo, usando uma das seguintes opções de parâmetros:
    - **Valores do parâmetro padrão**. Digite o seguinte comando:
    
        ```azurecli-interactive
        az group deployment create --resource-group myResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
        ```
    - **Valores do parâmetro personalizado**. Baixe e modifique o modelo antes de implantar o modelo. Você também pode implantar o modelo usando os parâmetros na linha de comando, ou implantar o modelo com um arquivo de parâmetros separados. Para baixar os arquivos de modelo e parâmetro, clique no botão **Procurar no GitHub** na página de modelo [Criar uma rede virtual com duas sub-redes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). No GitHub, clique no arquivo **azuredeploy.parameters.json** ou **azuredeploy.json**. Em seguida, clique no botão **Raw** para exibir o arquivo. No seu navegador, copie os conteúdos do arquivo. Salve os conteúdos em um arquivo no seu computador. Você pode modificar os valores de parâmetro no modelo, ou implantar o modelo com um arquivo de parâmetros separado.  

    Para saber mais sobre como implantar modelos usando esses métodos, digite `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Instale a última versão do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se você for novo no Azure PowerShell, consulte [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Na sessão do PowerShell, para entrar com sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account), insira `login-azurermaccount`.
3. Para criar um grupo de recursos para a rede virtual, digite o seguinte comando:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location eastus
    ```
    
4. Você pode implantar o modelo, usando uma das seguintes opções de parâmetros:
    - **Valores do parâmetro padrão**. Digite o seguinte comando:
    
        ```powershell
        New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName myResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json
        ```
        
    - **Valores do parâmetro personalizado**. Baixe e modifique o modelo antes de implantá-lo. Você também pode implantar o modelo usando os parâmetros na linha de comando, ou implantar o modelo com um arquivo de parâmetros separados. Para baixar os arquivos de modelo e parâmetro, clique no botão **Procurar no GitHub** na página de modelo [Criar uma rede virtual com duas sub-redes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). No GitHub, clique no arquivo **azuredeploy.parameters.json** ou **azuredeploy.json**. Em seguida, clique no botão **Raw** para exibir o arquivo. No seu navegador, copie os conteúdos do arquivo. Salve os conteúdos em um arquivo no seu computador. Você pode modificar os valores de parâmetro no modelo, ou implantar o modelo com um arquivo de parâmetros separado.  

    Para saber mais sobre como implantar modelos usando esses métodos, digite `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Excluir recursos

Após a conclusão este tutorial, convém excluir os recursos criados, para não incorrer em encargos de uso. A exclusão de um grupo de recursos também exclui todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa do portal, insira **myResourceGroup**. Nos resultados da pesquisa, clique em **myResourceGroup**.
2. Na folha **myResourceGroup**, clique no ícone **Excluir**.
3. Para confirmar a exclusão, na caixa **DIGITAR O NOME DO GRUPO DE RECURSOS**, insira **myResourceGroup** e, depois, clique em **Excluir**.

### <a name="delete-cli"></a>Azure CLI

Em uma sessão da CLI, insira o seguinte comando:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Em uma sessão do PowerShell, insira o seguinte comando:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre todas as configurações de sub-rede e rede virtual, confira [Gerenciar redes virtuais](virtual-network-manage-network.md#view-vnet) e [Gerenciar sub-redes da rede virtual](virtual-network-manage-subnet.md#create-subnet). Você tem várias opções para o uso de redes virtuais e sub-redes em um ambiente de produção para atender aos requisitos diferentes.
- Filtre o tráfego de entrada e de saída da sub-rede criando e aplicando [grupos de segurança de rede](virtual-networks-nsg.md) a sub-redes.
- Rotear o tráfego entre as sub-redes por meio de uma solução de virtualização de rede criando [rotas definidas pelo usuário](virtual-network-create-udr-arm-ps.md) e aplicar as rotas para cada sub-rede.
- Crie uma máquina virtual [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) em uma rede virtual existente.
- Conecte duas redes virtuais criando um [emparelhamento de rede virtual](virtual-network-peering-overview.md) entre as redes virtuais.
- Conecte a rede virtual a uma rede local usando um [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou circuito do [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
