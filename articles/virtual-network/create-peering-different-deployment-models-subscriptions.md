---
title: "Criar um emparelhamento de rede virtual do Azure – modelos de implantação diferentes – assinaturas diferentes | Microsoft Docs"
description: "Saiba como criar um emparelhamento de rede virtual entre redes virtuais criadas com modelos de implantação diferentes que existem em assinaturas do Azure diferentes."
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
ms.date: 09/15/2017
ms.author: jdial;anavin
ms.openlocfilehash: c7d2333c90f6ceca93b3eb702c3a010c37a0f70b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Criar um emparelhamento de rede virtual – modelos de implantação e assinaturas diferentes

Neste tutorial, você aprende a criar um emparelhamento de rede virtual entre redes virtuais criadas por modelos de implantação diferentes. As redes virtuais existem em assinaturas diferentes. O emparelhamento de duas redes virtuais permite que recursos em diferentes redes virtuais se comuniquem com a mesma largura de banda e latência, como se os recursos estivessem na mesma rede virtual. Saiba mais sobre [Emparelhamento de rede virtual](virtual-network-peering-overview.md).

As etapas para criar um emparelhamento de rede virtual são diferentes, dependendo de as redes virtuais estarem na mesma ou em diferentes assinaturas e do [modelo de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pelo qual as redes virtuais são criadas. Saiba como criar um emparelhamento de rede virtual em outros cenários clicando no cenário na tabela a seguir:

|Modelo de implantação do Azure  | Assinatura do Azure  |
|--------- |---------|
|[Ambos Resource Manager](virtual-network-create-peering.md) |Idêntico|
|[Ambos Resource Manager](create-peering-different-subscriptions.md) |Diferente|
|[Um Resource Manager, um clássico](create-peering-different-deployment-models.md) |Idêntico|

Não é possível criar um emparelhamento de rede virtual entre duas redes virtuais implantadas por meio do modelo de implantação clássico. Um emparelhamento de rede virtual só pode ser criado entre duas redes virtuais que existem na mesma região do Azure. 

  > [!WARNING]
  > A criação de emparelhamento de rede virtual entre redes virtuais em diferentes regiões está atualmente em versão prévia. Você pode registrar sua assinatura para a versão prévia abaixo. Emparelhamentos de rede virtual criados nesse cenário podem não ter o mesmo nível de disponibilidade e confiabilidade encontrado ao criar um emparelhamento de rede virtual em cenários na versão de disponibilidade geral. Emparelhamentos de rede virtual criados nesse cenário não têm suporte, podem ter recursos restringidos e podem não estar disponíveis em todas as regiões do Azure. Para ver as notificações mais recentes sobre disponibilidade e o status desse recurso, verifique a página [Atualizações da Rede Virtual](https://azure.microsoft.com/updates/?product=virtual-network) .

Ao criar um emparelhamento de rede virtual entre redes virtuais em assinaturas diferentes, as assinaturas devem estar ambas associadas ao mesmo locatário do Azure Active Directory. Se você ainda não tem um locatário do Azure Active Directory, você pode [criar um](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch) rapidamente. Se você precisar conectar redes virtuais que foram ambas criadas por meio do modelo de implantação clássico, ou que existem em diferentes regiões do Azure, ou ainda que existem em assinaturas associadas a locatários diferentes do Azure Active Directory, você pode usar um [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure para conectar as redes virtuais.

Você pode usar o [portal do Azure](#portal), a [CLI](#cli) (interface de linha de comando) do Azure ou o [Azure PowerShell](#powershell) para criar um emparelhamento de rede virtual. Clique em um dos links de ferramentas anteriores para ir diretamente para as etapas para a criação de um emparelhamento de rede virtual usando a ferramenta de sua escolha.

## <a name="register"></a>Registrar-se para a versão prévia do Emparelhamento VNet Global

Para emparelhar redes virtuais entre regiões, registre-se para a versão prévia, conclua as etapas a seguir para ambas as assinaturas que contêm as redes virtuais que você deseja emparelhar. A única ferramenta que você pode usar para se registrar para a versão prévia é o PowerShell.

1. Instale a última versão do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se você for novo no Azure PowerShell, consulte [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Iniciar uma sessão do PowerShell e faça logon no Azure usando o comando `Login-AzureRmAccount`.
3. Registre sua assinatura para a versão prévia digitando os seguintes comandos:

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network

    Register-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network

    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
    Não conclua as etapas nas seções PowerShell, CLI do Azure ou Portal deste artigo até que a saída de **RegistrationState** que você recebe depois de inserir o comando a seguir seja **Registrado** para ambas as assinaturas:

    ```powershell
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```
  > [!WARNING]
  > A criação de emparelhamento de rede virtual entre redes virtuais em diferentes regiões está atualmente em versão prévia. Emparelhamentos de rede virtual criados nesse cenário podem ter recursos restringidos e podem não estar disponíveis em todas as regiões do Azure. Para ver as notificações mais recentes sobre disponibilidade e o status desse recurso, verifique a página [Atualizações da Rede Virtual](https://azure.microsoft.com/updates/?product=virtual-network) .

## <a name="portal"></a>Criar emparelhamento – portal do Azure

Este tutorial usa contas diferentes para cada assinatura. Se você estiver usando uma conta que tenha permissões para ambas as assinaturas, use a mesma conta para todas as etapas, pule as etapas para fazer logoff do portal e ignore as etapas para atribuir permissões de outro usuário para as redes virtuais. Antes de completar qualquer uma das etapas a seguir, você deve se registrar para a versão prévia. Para se registrar, conclua as etapas na seção [Registrar-se para a versão prévia](#register) deste artigo. Não continue com as etapas restantes até que ambas as assinaturas estejam registradas para a versão prévia.
 
1. Faça logon no [Portal do Azure](https://portal.azure.com) como UserA. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Consulte a seção [Permissões](#permissions) deste artigo para obter detalhes.
2. Clique em **+ Novo**, em **Rede** e, em seguida, em **Rede virtual**.
3. Na folha **Criar rede virtual**, insira ou selecione valores para as seguintes configurações e, depois, clique em **Criar**:
    - **Nome**: *myVnetA*
    - **Espaço de endereço**: *10.0.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.0.0.0/24*
    - **Assinatura:** selecione a assinatura A.
    - **Grupo de recursos**: selecione **Criar novo** e insira *myResourceGroupA*
    - **Localização**: *Leste dos EUA*
4. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myVnetA*. Clique em **myVnetA** quando ele for exibido nos resultados da pesquisa. Uma folha é exibida para a rede virtual **myVnetA**.
5. Na folha **myVnetA** que é exibida, clique em **Controle de acesso (IAM)** na lista vertical de opções no lado esquerdo da folha.
6. Na folha **myVnetA – Controle de acesso (IAM)** exibida, clique em **+ Adicionar**.
7. Na folha **Adicionar permissões** exibida, selecione **Colaborador de rede** na caixa **Função**.
8. Na caixa **Selecionar**, selecione UserB ou digite o endereço de email de UserB para pesquisar por ele. A lista de usuários mostrada é do mesmo locatário do Azure Active Directory da rede virtual para a qual você está configurando o emparelhamento. Clique em UserB quando ele aparecer na lista.
9. Clique em **Salvar**.
10. Faça logoff do portal como UserA e faça logon como UserB.
11. Clique em **+ Novo**, digite *Rede virtual* na caixa **Pesquisar no Marketplace** e, em seguida, clique em **Rede Virtual** nos resultados da pesquisa.
12. Na folha **Rede Virtual** exibida, selecione **Clássico** na caixa **Selecionar um modelo de implantação** e clique em **Criar**.
13.   Na caixa Criar rede virtual (clássico) surgida, digite os seguintes valores:

    - **Nome**: *myVnetB*
    - **Espaço de endereço**: *10.1.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Assinatura:** selecione a assinatura B.
    - **Grupo de recursos**: selecione **Criar novo** e insira *myResourceGroupB*
    - **Localização**: *Leste dos EUA*

14. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myVnetB*. Clique em **myVnetB** quando ele for exibido nos resultados da pesquisa. Uma folha é exibida para a rede virtual **myVnetB**.
15. Na folha **myVnetB** exibida, clique em **Propriedades** na lista vertical de opções no lado esquerdo da folha. Copie a **ID DE RECURSO**, que é usada em uma etapa posterior. A ID de recurso é semelhante ao exemplo a seguir: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
16. Conclua as etapas 5 a 9 para myVnetB, inserindo **UserA** na etapa 8.
17. Faça logoff do portal como UserB e faça logon como UserA.
18. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myVnetA*. Clique em **myVnetA** quando ele for exibido nos resultados da pesquisa. Uma folha é exibida para a rede virtual **myVnet**.
19. Clique em **myVnetA**.
20. Na folha **myVnetA** exibida, clique em **Emparelhamentos** na lista vertical de opções no lado esquerdo da folha.
21. Na folha **myVnetA – Emparelhamentos** exibida, clique em **+ Adicionar**
22. Na folha **Adicionar emparelhamento** exibida, insira ou selecione as seguintes opções e clique em **OK**:
     - **Nome**: *myVnetAToMyVnetB*
     - **Modelo de implantação de rede virtual**: selecione **Clássico**.
     - **Sei minha ID do recurso**: marque essa caixa.
     - **ID do recurso**: insira a ID do recurso de myVnetB da etapa 15.
     - **Permitir acesso à rede virtual:** verifique se a opção **Habilitado** está selecionada.
    Nenhuma outra configuração é usada neste tutorial. Para saber mais sobre todas as configurações de emparelhamento, leia [Gerenciar emparelhamentos de rede virtual](virtual-network-manage-peering.md#create-a-peering).
23. Depois de clicar em **OK** na etapa anterior, a folha **Adicionar emparelhamento** será fechada e você verá a folha **myVnetA – Emparelhamentos** novamente. Depois de alguns segundos, o emparelhamento que você criou será exibido na folha. **Conectado** é listado na coluna **STATUS DE EMPARELHAMENTO** do emparelhamento de **myVnetAToMyVnetB** criado. O emparelhamento agora está estabelecido. Não é necessário emparelhar a rede virtual (clássico) à rede virtual (Resource Manager).

    Todos os recursos do Azure criados na rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você desejar resolver nomes entre redes virtuais em um emparelhamento, deverá criar seu próprio servidor DNS. Saiba como configurar a [Resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

24. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
25. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas da seção [Excluir recursos](#delete-portal) deste artigo.

## <a name="cli"></a>Criar emparelhamento – CLI do Azure

Este tutorial usa contas diferentes para cada assinatura. Se você estiver usando uma conta que tenha permissões para ambas as assinaturas, use a mesma conta para todas as etapas, ignore as etapas para fazer logoff do Azure e remova as linhas de script que criam atribuições de função de usuário. Substitua UserA@azure.com e UserB@azure.com em todos os scripts a seguir com os nomes de usuário que você está usando para UserA e UserB. 

Antes de completar qualquer uma das etapas a seguir, você deve se registrar para a versão prévia. Para se registrar, conclua as etapas na seção [Registrar-se para a versão prévia](#register) deste artigo. Não continue com as etapas restantes até que ambas as assinaturas estejam registradas para a versão prévia.

1. [Instale](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a CLI do Azure 1.0 para criar a rede virtual (clássico).
2. Abra uma sessão da CLI e faça logon no Azure como UserB usando o comando `azure login`.
3. Execute a CLI no modo de Gerenciamento de Serviços digitando o comando `azure config mode asm`.
4. Para criar um grupo de recursos para a rede virtual (clássico), digite o comando a seguir:
 
    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
5. As etapas restantes devem ser concluídas usando um shell de bash com a CLI do Azure 2.0.4 ou posterior [instalada](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) ou usando o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Clique no botão **Experimente** nos scripts a seguir, que abre um Cloud Shell que conecta você à sua conta do Azure. Para opções de como executar scripts da CLI de Bash em um cliente Windows, veja [Execução da CLI do Azure no Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
6. Copie o script a seguir em um editor de texto em seu computador. Substitua `<SubscriptionB-Id>` por sua ID da assinatura. Se você não souber a ID da assinatura, insira o comando `az account show`. O valor da **ID** na saída é sua ID da assinatura. Copie o script modificado, cole-o na sua sessão da CLI 2.0 e, em seguida, pressione `Enter`. 

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Quando você criou a rede virtual (clássico) na etapa 4, o Azure criou a rede virtual no grupo de recursos *Default-Networking*.
7. Faça o logoff de UserB do Azure e faça logon como UserA na CLI 2.0.
8. Crie um grupo de recursos e uma rede virtual (Resource Manager). Copie o script a seguir, cole-o na sua sessão da CLI e, em seguida, pressione `Enter`. 

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

9. Crie um emparelhamento de rede virtual entre as duas redes virtuais criadas por meio dos modelos de implantação diferentes. Copie o script a seguir em um editor de texto em seu computador. Substitua `<SubscriptionB-id>` por sua ID da assinatura. Se você não souber a ID da assinatura, insira o comando `az account show`. O valor da **ID** na saída é sua ID da assinatura. O Azure criou a rede virtual (clássico) que você criou na etapa 4 em um grupo de recursos denominado *Default-Networking*. Cole o script modificado em sua sessão da CLI e, em seguida, pressione `Enter`.

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

10. Após a execução do script, examine o emparelhamento da rede virtual (Resource Manager). Copie o script a seguir e cole-o na sua sessão da CLI:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    A saída mostra **Conectado** na coluna **PeeringState**.

    Todos os recursos do Azure criados na rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você desejar resolver nomes entre redes virtuais em um emparelhamento, deverá criar seu próprio servidor DNS. Saiba como configurar a [Resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

11. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
12. **Opcional**: Para excluir os recursos criados neste tutorial, conclua as etapas em [Excluir recursos](#delete-cli) deste artigo.

## <a name="powershell"></a>Criar emparelhamento – PowerShell

Este tutorial usa contas diferentes para cada assinatura. Se você estiver usando uma conta que tenha permissões para ambas as assinaturas, use a mesma conta para todas as etapas, ignore as etapas para fazer logoff do Azure e remova as linhas de script que criam atribuições de função de usuário. Substitua UserA@azure.com e UserB@azure.com em todos os scripts a seguir com os nomes de usuário que você está usando para UserA e UserB. 

Antes de completar qualquer uma das etapas a seguir, você deve se registrar para a versão prévia. Para se registrar, conclua as etapas na seção [Registrar-se para a versão prévia](#register) deste artigo. Não continue com as etapas restantes até que ambas as assinaturas estejam registradas para a versão prévia.

1. Instale a última versão dos módulos [Azure](https://www.powershellgallery.com/packages/Azure) e [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se você for novo no Azure PowerShell, consulte [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell.
3. No PowerShell, faça logon na assinatura do UserB como UserB inserindo o comando `Add-AzureAccount`.
4. Para criar uma rede virtual (clássico) com o PowerShell, você deve criar um arquivo de configuração de rede novo ou modificar um existente. Saiba como [exportar, atualizar e importar arquivos de configuração de rede](virtual-networks-using-network-configuration-file.md). O arquivo deve incluir o elemento **VirtualNetworkSite** a seguir para a rede virtual usada neste tutorial:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
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

5. Inserindo o comando `login-azurermaccount`, faça logon na assinatura do UserB como UserB usar comandos do Resource Manager.
6. Atribua permissões de UserA à rede virtual B. copie o script a seguir em um editor de texto no seu computador e substitua `<SubscriptionB-id>` com a ID da assinatura B. Se você não souber a ID da assinatura, insira o comando `Get-AzureRmSubscription` para exibi-la. O valor da **ID** na saída retornada é sua ID da assinatura. O Azure criou a rede virtual (clássico) que você criou na etapa 4 em um grupo de recursos denominado *Default-Networking*. Para executar o script, copie o script modificado, cole-o no PowerShell e pressione `Enter`.
    
    ```powershell 
    New-AzureRmRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Inserindo o comando `login-azurermaccount`, faça logoff do Azure como UserB e faça logon na assinatura de UserA como UserA. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Consulte a seção [Permissões](#permissions) deste artigo para obter detalhes.
8. Crie a rede virtual (Resource Manager) copiando o script a seguir, colando-o no PowerShell e, em seguida, pressionando `Enter`:

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Atribua permissões de UserB a myVnetA. Copie o script a seguir em um editor de texto no seu computador e substitua `<SubscriptionA-Id>` pela ID da assinatura B. Se você não souber a ID da assinatura, insira o comando `Get-AzureRmSubscription` para exibi-la. O valor da **ID** na saída retornada é sua ID da assinatura. Cole a versão modificada do script no PowerShell e pressione `Enter` para executá-lo.

    ```powershell
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Copie o script a seguir para um editor de texto em seu computador e substitua `<SubscriptionB-id>` pela ID da assinatura B. Para emparelhar myVnetA a myVNetB, copie o script modificado, cole-o no PowerShell e pressione `Enter`.

    ```powershell
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Exiba o estado de emparelhamento de myVnetA copiando o script a seguir, colando-o no PowerShell e pressionando `Enter`.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    O estado é **Conectado**. Ele é alterado para **Conectado** depois de configurar o emparelhamento de myVnetB para myVnetA.

    Todos os recursos do Azure criados na rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você desejar resolver nomes entre redes virtuais em um emparelhamento, deverá criar seu próprio servidor DNS. Saiba como configurar a [Resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

12. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
13. **Opcional**: Para excluir os recursos criados neste tutorial, conclua as etapas em [Excluir recursos](#delete-powershell) deste artigo.

## <a name="permissions"></a>Permissões

As contas usadas para criar um emparelhamento de rede virtual devem ter a função ou as permissões necessárias. Por exemplo, se você pretende emparelhar duas redes virtuais, chamadas myVnetA e myVnetB, sua conta deve ser atribuída à seguinte função ou permissões mínimas para cada rede virtual:
    
|Rede virtual|Modelo de implantação|Função|Permissões|
|---|---|---|---|
|myVnetA|Gerenciador de Recursos|[Colaborador de rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clássico|[Colaborador de rede clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|myVnetB|Gerenciador de Recursos|[Colaborador de rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clássico|[Colaborador de rede clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Saiba mais sobre [funções internas](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e como atribuir permissões específicas a [funções personalizadas](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (somente para o Resource Manager).

## <a name="delete"></a>Excluir recursos
Ao concluir este tutorial, talvez você deseje excluir os recursos criados no tutorial para não incorrer em encargos de uso. A exclusão de um grupo de recursos também exclui todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa do portal, insira **myResourceGroupA**. Nos resultados da pesquisa, clique em **myResourceGroupA**.
2. Na folha **myResourceGroupA**, clique no ícone **Excluir**.
3. Para confirmar a exclusão, na caixa **DIGITAR O NOME DO GRUPO DE RECURSOS**, insira **myResourceGroupA** e, depois, clique em **Excluir**.
4. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myVnetB*. Clique em **myVnetB** quando ele for exibido nos resultados da pesquisa. Uma folha é exibida para a rede virtual **myVnetB**.
5. No **myVnetB** folha, clique em **Excluir**.
6. Para confirmar a exclusão, clique em **Sim** na caixa **Excluir rede virtual**.

### <a name="delete-cli"></a>Azure CLI

1. Faça logon Azure usando a CLI 2.0 para excluir a rede virtual (Resource Manager) com o seguinte comando:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```

2. Faça logon Azure usando a CLI do Azure 1.0 para excluir a rede virtual (clássico) com os seguintes comandos:

    ```azurecli
    azure config mode asm 

    azure network vnet delete --vnet myVnetB --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. No prompt de comando do PowerShell, digite o seguinte comando para excluir a rede virtual (Resource Manager):

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -Force
    ```

2. Para excluir a rede virtual (clássico) com o PowerShell, você deve modificar um arquivo de configuração de rede existente. Saiba como [exportar, atualizar e importar arquivos de configuração de rede](virtual-networks-using-network-configuration-file.md). Remova o elemento VirtualNetworkSite a seguir para a rede virtual usada neste tutorial:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
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