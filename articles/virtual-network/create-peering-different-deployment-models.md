---
title: "Criar um emparelhamento de rede virtual do Azure – modelos de implantação diferentes – mesma assinatura | Microsoft Docs"
description: "Saiba como criar um emparelhamento de rede virtual entre redes virtuais criadas com modelos de implantação diferentes que existem na mesma assinatura do Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: 1adf219dc4ca9ba91dc1ffc1ae98b764c9ef61b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Criar um emparelhamento de rede virtual – modelos de implantação diferentes e na mesma assinatura 

Neste tutorial, você aprende a criar um emparelhamento de rede virtual entre redes virtuais criadas por modelos de implantação diferentes. Ambas as redes virtuais existem na mesma assinatura. O emparelhamento de duas redes virtuais permite que recursos em diferentes redes virtuais se comuniquem com a mesma largura de banda e latência, como se os recursos estivessem na mesma rede virtual. Saiba mais sobre [Emparelhamento de rede virtual](virtual-network-peering-overview.md). 

As etapas para criar um emparelhamento de rede virtual são diferentes, dependendo de as redes virtuais estarem na mesma ou em diferentes assinaturas e do [modelo de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pelo qual as redes virtuais são criadas. Saiba como criar um emparelhamento de rede virtual em outros cenários clicando no cenário na tabela a seguir:

|Modelo de implantação do Azure  | Assinatura do Azure  |
|--------- |---------|
|[Ambos Resource Manager](virtual-network-create-peering.md) |Idêntico|
|[Ambos Resource Manager](create-peering-different-subscriptions.md) |Diferente|
|[Um Resource Manager, um clássico](create-peering-different-deployment-models-subscriptions.md) |Diferente|

Um emparelhamento de rede virtual só pode ser criado entre duas redes virtuais que existem na mesma região do Azure.

  > [!WARNING]
  > A criação de emparelhamento de rede virtual entre redes virtuais em diferentes regiões está atualmente em versão prévia. Você pode registrar sua assinatura para a versão prévia abaixo. Emparelhamentos de rede virtual criados nesse cenário podem não ter o mesmo nível de disponibilidade e confiabilidade encontrado ao criar um emparelhamento de rede virtual em cenários na versão de disponibilidade geral. Emparelhamentos de rede virtual criados nesse cenário não têm suporte, podem ter recursos restringidos e podem não estar disponíveis em todas as regiões do Azure. Para ver as notificações mais recentes sobre disponibilidade e o status desse recurso, verifique a página [Atualizações da Rede Virtual](https://azure.microsoft.com/updates/?product=virtual-network) .

Um emparelhamento de redes virtuais não pode ser criado entre duas redes virtuais implantadas por meio do modelo de implantação clássico. Se você precisar conectar redes virtuais que foram ambas criadas por meio do modelo de implantação clássico ou que existem em diferentes regiões do Azure, você poderá usar um [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure para conectar as redes virtuais. 

Use o [portal do Azure](#portal), a [CLI](#cli) (interface de linha de comando) do Azure, o Azure [PowerShell](#powershell) ou um [modelo do Azure Resource Manager](#template) para criar um emparelhamento de rede virtual. Clique em um dos links de ferramentas anteriores para ir diretamente para as etapas para a criação de um emparelhamento de rede virtual usando a ferramenta de sua escolha.

## <a name="register"></a>Registrar-se para a versão prévia do Emparelhamento VNet Global

Para emparelhar redes virtuais entre regiões, registre-se para a versão prévia, conclua as etapas a seguir para ambas as assinaturas que contêm as redes virtuais que você deseja emparelhar. A única ferramenta que você pode usar para se registrar para a versão prévia é o PowerShell.

1. Instale a última versão do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se você for novo no Azure PowerShell, consulte [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Iniciar uma sessão do PowerShell e faça logon no Azure usando o comando `Login-AzureRmAccount`.
3. Registre sua assinatura para a versão prévia digitando os seguintes comandos:

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
    Não conclua as etapas nas seções PowerShell, CLI do Azure ou Portal deste artigo até que a saída de **RegistrationState** que você recebe depois de inserir o comando a seguir seja **Registrado** para ambas as assinaturas:

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```
  > [!WARNING]
  > A criação de emparelhamento de rede virtual entre redes virtuais em diferentes regiões está atualmente em versão prévia. Emparelhamentos de rede virtual criados nesse cenário podem ter recursos restringidos e podem não estar disponíveis em todas as regiões do Azure. Para ver as notificações mais recentes sobre disponibilidade e o status desse recurso, verifique a página [Atualizações da Rede Virtual](https://azure.microsoft.com/updates/?product=virtual-network) .

## <a name="cli"></a>Criar emparelhamento – Portal

1. Faça logon no [Portal do Azure](https://portal.azure.com). A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Consulte a seção [Permissões](#permissions) deste artigo para obter detalhes.
2. Clique em **+ Novo**, em **Rede** e, em seguida, em **Rede virtual**.
3. Na folha **Criar rede virtual**, insira ou selecione valores para as seguintes configurações e, depois, clique em **Criar**:
    - **Nome**: *myVnet1*
    - **Espaço de endereço**: *10.0.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.0.0.0/24*
    - **Assinatura**: selecione sua assinatura
    - **Grupo de recursos**: selecione **Criar novo** e insira *myResourceGroup*
    - **Localização**: *Leste dos EUA*
4. Clique em **+ Novo**. No campo **Pesquisar no Marketplace**, digite *Rede virtual*. Clique em **Rede virtual** quando essa opção aparecer entre os resultados da pesquisa. 
5. Na folha **Rede virtual**, selecione **Clássico** na caixa **Selecionar um modelo de implantação** e clique em **Criar**.
6. Na folha **Criar rede virtual**, insira ou selecione valores para as seguintes configurações e, depois, clique em **Criar**:
    - **Nome**: *myVnet2*
    - **Espaço de endereço**: *10.1.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Assinatura**: selecione sua assinatura
    - **Grupo de recursos**: selecione **Usar existente** e selecione *myResourceGroup*
    - **Localização**: *Leste dos EUA*
7. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myResourceGroup*. Clique em **myResourceGroup** quando ele for exibido nos resultados da pesquisa. Uma folha é exibida para o grupo de recursos **myresourcegroup**. O grupo de recursos contém as duas redes virtuais criadas nas etapas anteriores.
8. Clique em **myVNet1**.
9. Na folha **myVnet1** exibida, clique em **Emparelhamentos** na lista vertical de opções no lado esquerdo da folha.
10. Na folha **myVnet1 – Emparelhamentos** exibida, clique em **+ Adicionar**
11. Na folha **Adicionar emparelhamento** exibida, insira ou selecione as seguintes opções e clique em **OK**:
     - **Nome**: *myVnet1ToMyVnet2*
     - **Modelo de implantação de rede virtual**: selecione **Clássico**. 
     - **Assinatura**: selecione sua assinatura
     - **Rede virtual**: clique em **Escolher uma rede virtual** e, em seguida, clique em **myVnet2**.
     - **Permitir acesso à rede virtual:** verifique se a opção **Habilitado** está selecionada.
    Nenhuma outra configuração é usada neste tutorial. Para saber mais sobre todas as configurações de emparelhamento, leia [Gerenciar emparelhamentos de rede virtual](virtual-network-manage-peering.md#create-a-peering).
12. Depois de clicar em **OK** na etapa anterior, a folha **Adicionar emparelhamento** será fechada e você verá a folha **myVnet1 – Emparelhamentos** novamente. Depois de alguns segundos, o emparelhamento que você criou será exibido na folha. **Conectado** é listado na coluna **STATUS DE EMPARELHAMENTO** do emparelhamento de **myVnet1ToMyVnet2** criado.

    O emparelhamento agora está estabelecido. Todos os recursos do Azure criados na rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você desejar resolver nomes entre redes virtuais em um emparelhamento, deverá criar seu próprio servidor DNS. Saiba como configurar a [Resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
13. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
14. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas da seção [Excluir recursos](#delete-portal) deste artigo.

## <a name="cli"></a>Criar emparelhamento – CLI do Azure

1. [Instale](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a CLI do Azure 1.0 para criar a rede virtual (clássico).
2. Abra uma sessão de comando e faça logon no Azure usando o comando `azure login`.
3. Execute a CLI no modo de Gerenciamento de Serviços digitando o comando `azure config mode asm`.
4. Para criar um grupo de recursos para a rede virtual (clássico), digite o comando a seguir:
 
    ```azurecli
    azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

5. Crie um grupo de recursos e uma rede virtual (Resource Manager). Você pode usar a CLI 1.0 ou 2.0 ([instalar](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)). Neste tutorial, a CLI 2.0 é usada para criar a rede virtual (Resource Manager), já que a 2.0 deve ser usada para criar o emparelhamento. Execute o script de CLI de Bash a seguir do computador local com a CLI 2.0.4 ou posterior instalada. Para opções de como executar scripts da CLI de busca em um cliente Windows, veja [Execução da CLI do Azure no Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Você também pode executar o script usando o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Clique no botão **Experimente** no script a seguir, o que invoca um Cloud Shell com o qual você pode fazer logon em sua conta do Azure. Para executar o script, clique no botão **Copiar** e cole o conteúdo para o Cloud Shell, em seguida, pressione `Enter`.

    ```azurecli-interactive
    #!/bin/bash

    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus

    # Create the virtual network (Resource Manager).
    az network vnet create \
      --name myVnet1 \
      --resource-group myResourceGroup \
      --location eastus \
      --address-prefix 10.0.0.0/16
    ```

6. Crie um emparelhamento de rede virtual entre as duas redes virtuais criadas por meio dos modelos de implantação diferentes. Copie o script a seguir em um editor de texto em seu computador. Substitua `<subscription id>` por sua ID da assinatura. Se você não souber a ID da assinatura, insira o comando `az account show`. O valor da **ID** na saída é sua ID da assinatura. Cole o script modificado em sua sessão da CLI e, em seguida, pressione `Enter`.

    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group myResourceGroup \
      --name myVnet1 \
      --query id --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
      --allow-vnet-access
    ```
7. Após a execução do script, examine o emparelhamento da rede virtual (Resource Manager). Copie o comando a seguir, cole-o na sua sessão da CLI e, em seguida, pressione `Enter`:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    A saída mostra **Conectado** na coluna **PeeringState**. 

    Todos os recursos do Azure criados na rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você desejar resolver nomes entre redes virtuais em um emparelhamento, deverá criar seu próprio servidor DNS. Saiba como configurar a [Resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
8. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
9. **Opcional**: Para excluir os recursos criados neste tutorial, conclua as etapas em [Excluir recursos](#delete-cli) deste artigo.

## <a name="powershell"></a>Criar emparelhamento – PowerShell

1. Instale a última versão dos módulos [Azure](https://www.powershellgallery.com/packages/Azure) e [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se você for novo no Azure PowerShell, consulte [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell.
3. No PowerShell, faça logon no Azure inserindo o comando `Add-AzureAccount`.
4. Para criar uma rede virtual (clássico) com o PowerShell, você deve criar um arquivo de configuração de rede novo ou modificar um existente. Saiba como [exportar, atualizar e importar arquivos de configuração de rede](virtual-networks-using-network-configuration-file.md). O arquivo deve incluir o elemento **VirtualNetworkSite** a seguir para a rede virtual usada neste tutorial:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Importar um arquivo de configuração de rede alterado pode causar alterações em redes virtuais existentes (clássico) na sua assinatura. Verifique se você adicionou apenas a rede virtual anterior e se você não alterou nem removeu nenhuma rede virtual existente da sua assinatura. 
5. Faça logon no Azure para criar a rede virtual (Resource Manager), inserindo o comando `login-azurermaccount`. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Consulte a seção [Permissões](#permissions) deste artigo para obter detalhes.
6. Crie um grupo de recursos e uma rede virtual (Resource Manager). Copie o script, cole-o no PowerShell e pressione `Enter`.

    ```powershell
    # Create a resource group.
      New-AzureRmResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Crie um emparelhamento de rede virtual entre as duas redes virtuais criadas por meio dos modelos de implantação diferentes. Copie o script a seguir em um editor de texto em seu computador. Substitua `<subscription id>` por sua ID da assinatura. Se você não souber a ID da assinatura, insira o comando `Get-AzureRmSubscription` para exibi-la. O valor da **ID** na saída retornada é sua ID da assinatura. Para executar o script, copie o script modificado do seu editor de texto e, em seguida, clique com o botão direito do mouse em sua sessão do PowerShell e pressione `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Após a execução do script, examine o emparelhamento da rede virtual (Resource Manager). Copie o comando a seguir, cole-o na sua sessão do PowerShell e, em seguida, pressione `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    A saída mostra **Conectado** na coluna **PeeringState**.

    Todos os recursos do Azure criados na rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você desejar resolver nomes entre redes virtuais em um emparelhamento, deverá criar seu próprio servidor DNS. Saiba como configurar a [Resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

9. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
10. **Opcional**: Para excluir os recursos criados neste tutorial, conclua as etapas em [Excluir recursos](#delete-powershell) deste artigo.
 
## <a name="permissions"></a>Permissões

As contas usadas para criar um emparelhamento de rede virtual devem ter a função ou as permissões necessárias. Por exemplo, se você pretende emparelhar duas redes virtuais, chamadas myVnet1 e myVnet2, será necessário atribuir à sua conta a seguinte função mínima ou permissões mínimas para cada rede virtual:
    
|Rede virtual|Modelo de implantação|Função|Permissões|
|---|---|---|---|
|myVnet1|Gerenciador de Recursos|[Colaborador de rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clássico|[Colaborador de rede clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|myVnet2|Gerenciador de Recursos|[Colaborador de rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clássico|[Colaborador de rede clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Saiba mais sobre [funções internas](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e como atribuir permissões específicas a [funções personalizadas](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (somente para o Resource Manager).

## <a name="delete"></a>Excluir recursos
Ao concluir este tutorial, talvez você deseje excluir os recursos criados no tutorial para não incorrer em encargos de uso. A exclusão de um grupo de recursos também exclui todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa do portal, insira **myResourceGroup**. Nos resultados da pesquisa, clique em **myResourceGroup**.
2. Na folha **myResourceGroup**, clique no ícone **Excluir**.
3. Para confirmar a exclusão, na caixa **DIGITAR O NOME DO GRUPO DE RECURSOS**, insira **myResourceGroup** e, depois, clique em **Excluir**.

### <a name="delete-cli"></a>Azure CLI

1. Use a CLI do Azure 2.0 para excluir a rede virtual (Resource Manager) com o seguinte comando:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Use a CLI do Azure 1.0 para excluir a rede virtual (clássico) com os seguintes comandos:

    ```azurecli
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Digite o seguinte comando para excluir a rede virtual (Resource Manager):

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroup -Force
    ```

2. Para excluir a rede virtual (clássico) com o PowerShell, você deve modificar um arquivo de configuração de rede existente. Saiba como [exportar, atualizar e importar arquivos de configuração de rede](virtual-networks-using-network-configuration-file.md). Remova o elemento VirtualNetworkSite a seguir para a rede virtual usada neste tutorial:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Importar um arquivo de configuração de rede alterado pode causar alterações em redes virtuais existentes (clássico) na sua assinatura. Verifique se você removeu apenas a rede virtual anterior e se você não alterou nem removeu nenhuma outra rede virtual existente da sua assinatura. 

## <a name="next-steps"></a>Próximas etapas

- Familiarize por completo com [comportamentos e restrições importantes do emparelhamento de rede virtual](virtual-network-manage-peering.md#requirements-and-constraints) antes de criar um emparelhamento de rede virtual para uso em produção.
- Saiba mais sobre todas as [configurações de emparelhamento de rede virtual](virtual-network-manage-peering.md#create-a-peering).
- Saiba como [criar uma topologia de rede hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com o emparelhamento de rede virtual.
