---
title: "Criar um emparelhamento de rede virtual do Azure – Resource Manager – assinaturas diferentes | Microsoft Docs"
description: Saiba como criar um emparelhamento de rede virtual entre redes virtuais criadas por meio do Resource Manager que existem em assinaturas do Azure diferentes.
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
ms.openlocfilehash: 13e9e83dc277d8e88ab46d8d51b3f4a70ccd3b46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Criar um emparelhamento de rede virtual – Resource Manager, assinaturas diferentes 

Neste tutorial, você aprende a criar um emparelhamento de rede virtual entre redes virtuais criadas por meio do Resource Manager. As redes virtuais existem em assinaturas diferentes. O emparelhamento de duas redes virtuais permite que recursos em diferentes redes virtuais se comuniquem com a mesma largura de banda e latência, como se os recursos estivessem na mesma rede virtual. Saiba mais sobre [Emparelhamento de rede virtual](virtual-network-peering-overview.md). 

As etapas para criar um emparelhamento de rede virtual são diferentes, dependendo de as redes virtuais estarem na mesma ou em diferentes assinaturas e do [modelo de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pelo qual as redes virtuais são criadas. Saiba como criar um emparelhamento de rede virtual em outros cenários clicando no cenário na tabela a seguir:

|Modelo de implantação do Azure  | Assinatura do Azure  |
|--------- |---------|
|[Ambos Resource Manager](virtual-network-create-peering.md) |Idêntico|
|[Um Resource Manager, um clássico](create-peering-different-deployment-models.md) |Idêntico|
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

## <a name="portal"></a>Criar emparelhamento – portal do Azure

Este tutorial usa contas diferentes para cada assinatura. Se você estiver usando uma conta que tenha permissões para ambas as assinaturas, use a mesma conta para todas as etapas, pule as etapas para fazer logoff do portal e ignore as etapas para atribuir permissões de outro usuário para as redes virtuais.

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
8. Na caixa **Selecionar**, selecione um UserB ou digite o endereço de email de UserB para pesquisar por ele. A lista de usuários mostrada é do mesmo locatário do Azure Active Directory da rede virtual para a qual você está configurando o emparelhamento.
9. Clique em **Salvar**.
10. Na folha **myVnetA – Controle de acesso (IAM)**, clique em **Propriedades** na lista vertical de opções no lado esquerdo da folha. Copie a **ID DE RECURSO**, que é usada em uma etapa posterior. A ID de recurso é semelhante ao exemplo a seguir: /subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA.
11. Faça logoff do portal como UserA e faça logon como UserB.
12. Conclua as etapas 2 a 3, inserindo ou selecionando os seguintes valores na etapa 3:

    - **Nome**: *myVnetB*
    - **Espaço de endereço**: *10.1.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Assinatura:** selecione a assinatura B.
    - **Grupo de recursos**: selecione **Criar novo** e insira *myResourceGroupB*
    - **Localização**: *Leste dos EUA*

13. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myVnetB*. Clique em **myVnetB** quando ele for exibido nos resultados da pesquisa. Uma folha é exibida para a rede virtual **myVnetB**.
14. Na folha **myVnetB** exibida, clique em **Propriedades** na lista vertical de opções no lado esquerdo da folha. Copie a **ID DE RECURSO**, que é usada em uma etapa posterior. A ID de recurso é semelhante ao exemplo a seguir: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB.
15. Clique em **Controle de acesso (IAM)** na folha **myVnetB** e conclua as etapas 5 a 10 para myVnetB, inserindo **UserA** na etapa 8.
16. Faça logoff do portal como UserB e faça logon como UserA.
17. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myVnetA*. Clique em **myVnetA** quando ele for exibido nos resultados da pesquisa. Uma folha é exibida para a rede virtual **myVnet**.
18. Clique em **myVnetA**.
19. Na folha **myVnetA** exibida, clique em **Emparelhamentos** na lista vertical de opções no lado esquerdo da folha.
20. Na folha **myVnetA – Emparelhamentos** exibida, clique em **+ Adicionar**
21. Na folha **Adicionar emparelhamento** exibida, insira ou selecione as seguintes opções e clique em **OK**:
     - **Nome**: *myVnetAToMyVnetB*
     - **Modelo de implantação de rede virtual**: selecione **Resource Manager**.
     - **Sei minha ID do recurso**: marque essa caixa.
     - **ID do recurso**: insira a ID do recurso da etapa 14.
     - **Permitir acesso à rede virtual:** verifique se a opção **Habilitado** está selecionada.
    Nenhuma outra configuração é usada neste tutorial. Para saber mais sobre todas as configurações de emparelhamento, leia [Gerenciar emparelhamentos de rede virtual](virtual-network-manage-peering.md#create-a-peering).
22. Depois de clicar em **OK** na etapa anterior, a folha **Adicionar emparelhamento** será fechada e você verá a folha **myVnetA – Emparelhamentos** novamente. Depois de alguns segundos, o emparelhamento que você criou será exibido na folha. **Iniciado** é listado na coluna **STATUS DE EMPARELHAMENTO** do emparelhamento de **myVnetAToMyVnetB** criado. Você já emparelhou myVnetA a myVnetB, mas agora você deve emparelhar myVnetB a myVnetA. O emparelhamento deve ser criado em ambas as orientações para permitir que os recursos nas redes virtuais se comuniquem entre si.
23. Faça logoff do portal como UserA e faça logon como UserB.
24. Conclua as etapas 17 a 21 novamente para myVnetB. Na etapa 21, nomeie o emparelhamento *myVnetBToMyVnetA*, selecione *myVnetA* para **Rede virtual** e insira a ID da etapa 10 na caixa **ID de recurso**.
25. Alguns segundos depois de clicar em **OK** para criar o emparelhamento para a myVnetB, o emparelhamento de **myVnetBToMyVnetA** que você acabou de criar será listado com **Conectado** na coluna **STATUS DE EMPARELHAMENTO**.
26. Faça logoff do portal como UserB e faça logon como UserA.
27. Conclua as etapas 17 a 19 novamente. O **STATUS DE EMPARELHAMENTO** do emparelhamento de **myVnetAToVNetB** agora também é **Conectado**. O emparelhamento é estabelecido com êxito após a exibição de **Conectado** na coluna **STATUS DE EMPARELHAMENTO** de ambas as redes virtuais no emparelhamento. Todos os recursos do Azure criados na rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você desejar resolver nomes entre redes virtuais em um emparelhamento, deverá criar seu próprio servidor DNS. Saiba como configurar a [Resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
28. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
29. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas da seção [Excluir recursos](#delete-portal) deste artigo.

## <a name="cli"></a>Criar emparelhamento – CLI do Azure

Este tutorial usa contas diferentes para cada assinatura. Se você estiver usando uma conta que tenha permissões para ambas as assinaturas, use a mesma conta para todas as etapas, ignore as etapas para fazer logoff do Azure e remova as linhas de script que criam atribuições de função de usuário. Substitua UserA@azure.com e UserB@azure.com em todos os scripts a seguir com os nomes de usuário que você está usando para UserA e UserB.

O seguinte script:

- Exige a CLI do Azure versão 2.0.4 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisar atualizar, confira [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funciona em um shell do Bash. Para opções sobre como executar scripts da CLI do Azure no cliente Windows, veja [Execução da CLI do Azure no Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Em vez de instalar a CLI e suas dependências, você pode usar o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Clique no botão **Experimente** no script a seguir, o que invoca Cloud Shell com o qual você pode fazer logon em sua conta do Azure. 

1. Abra uma sessão da CLI e faça logon no Azure como UserA usando o comando `azure login`. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Consulte a seção [Permissões](#permissions) deste artigo para obter detalhes.
2. Copie o script a seguir para um editor de texto em seu computador, substitua `<SubscriptionA-Id>` pela ID da SubscriptionA, copie o script modificado, cole-o na sessão da CLI e pressione `Enter`. Se você não souber a ID da assinatura, insira o comando 'az account show`. O valor da **ID** na saída é sua ID da assinatura.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```
    
     A atribuição de permissão para UserB não é um requisito. O emparelhamento pode ser estabelecido mesmo que os usuários façam individualmente as solicitações de emparelhamento para suas respectivas redes virtuais, desde que as solicitações coincidam. Adicionar um usuário com privilégios de myVNetB como um colaborador de rede na rede virtual local facilita a configuração.
3. Faça logoff do Azure como UserA usando o comando `az logout`, então faça logon no Azure como UserB. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Consulte a seção [Permissões](#permissions) deste artigo para obter detalhes.
4. Crie myVnetB. Copie o conteúdo de script na etapa 2 para um editor de texto em seu computador. Substitua `<SubscriptionA-Id>` pela ID da SubscriptionB. Altere 10.0.0.0/16 para 10.1.0.0/16, altere todos os As para B e todos os Bs para A. Copie o script modificado, cole-o na sua sessão da CLI e pressione `Enter`. 
5. Faça logoff do Azure como UserB e faça logon no Azure como UserA.
6. Crie um emparelhamento de rede virtual de myVnetA para myVnetB. Copie o conteúdo de script a seguir em um editor de texto em seu computador. Substitua `<SubscriptionB-Id>` pela ID da SubscriptionB. Para executar o script, copie o script modificado, cole-o na sessão da CLI e pressione Enter.
 
    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)
    
        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Exiba o estado de emparelhamento de myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    O estado é **Iniciado**. Ele é alterado para **Conectado** depois que você cria o emparelhamento de myVnetB para myVnetA.

8. Faça logoff do Azure como UserA e faça logon no Azure como UserB.
9. Crie o emparelhamento de myVnetB para myVnetA. Copie o conteúdo de script na etapa 6 para um editor de texto em seu computador. Substitua `<SubscriptionB-Id>` pela ID de SubscriptionA e altere todos os As para B e todos os Bs para A. Depois de fazer as alterações, copie o script modificado, cole-o em sua sessão da CLI e pressione `Enter`.
10. Exiba o estado de emparelhamento de myVnetB. Copie o conteúdo de script na etapa 7 para um editor de texto em seu computador. Altere A para B para o grupo de recursos e nomes de rede virtual, copie o script, cole o script modificado em sua sessão da CLI e pressione `Enter`. O estado de emparelhamento é **Conectado**. O estado de emparelhamento de myVnetA se altera para **Conectado** depois de você criar o emparelhamento de myVnetB para myVnetA. Você pode fazer um novo logon como UserA no Azure e completar a etapa 7 novamente para verificar o estado de emparelhamento de myVnetA. 

    > [!NOTE]
    > O emparelhamento não é estabelecido até que o estado de emparelhamento para ambas as redes virtuais seja **Conectado**.

11. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
12. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas em [Excluir recursos](#delete-cli) deste artigo.

Todos os recursos do Azure criados na rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você desejar resolver nomes entre redes virtuais em um emparelhamento, deverá criar seu próprio servidor DNS. Saiba como configurar a [Resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
 
## <a name="powershell"></a>Criar emparelhamento – PowerShell

Este tutorial usa contas diferentes para cada assinatura. Se você estiver usando uma conta que tenha permissões para ambas as assinaturas, use a mesma conta para todas as etapas, ignore as etapas para fazer logoff do Azure e remova as linhas de script que criam atribuições de função de usuário. Substitua UserA@azure.com e UserB@azure.com em todos os scripts a seguir com os nomes de usuário que você está usando para UserA e UserB.

1. Instale a última versão do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se você for novo no Azure PowerShell, consulte [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell.
3. No PowerShell, faça logon no Azure como UserA inserindo o comando `login-azurermaccount`. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Consulte a seção [Permissões](#permissions) deste artigo para obter detalhes.
4. Crie um grupo de recursos e uma rede virtual A. Copie o script a seguir em um editor de texto em seu computador. Substitua `<SubscriptionA-Id>` pela ID da SubscriptionA. Se você não souber a ID da assinatura, insira o comando `Get-AzureRmSubscription` para exibi-la. O valor da **ID** na saída retornada é sua ID da assinatura. Para executar o script, copie o script modificado, cole-o no PowerShell e pressione `Enter`.

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

    A atribuição de permissão para UserB não é um requisito. O emparelhamento pode ser estabelecido mesmo que os usuários façam individualmente as solicitações de emparelhamento para suas respectivas redes virtuais, desde que as solicitações coincidam. Adicionar um usuário com privilégios de outra rede virtual como um usuário na rede virtual local facilita a configuração.
5. Faça logoff do Azure como UserA e faça logon como UserB. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Consulte a seção [Permissões](#permissions) deste artigo para obter detalhes.
6. Copie o conteúdo de script na etapa 4 para um editor de texto em seu computador. Substitua `<SubscriptionA-Id>` pela ID da assinatura B. Altere 10.0.0.0/16 para 10.1.0.0/16. Altere todos os As para B e todos os Bs para A. Para executar o script, copie o script modificado, cole-o no PowerShell e pressione `Enter`.
7. Faça logoff do Azure como UserB e faça logon como UserA.
8. Crie o emparelhamento de myVnetA para myVnetB. Copie o script a seguir em um editor de texto em seu computador. Substitua `<SubscriptionB-Id>` pela ID da assinatura B. Para executar o script, copie o script modificado, cole-o no PowerShell e pressione `Enter`.
 
    ```powershell
    # Peer myVnetA to myVnetB.
    $vNetA=Get-AzureRmVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vNetA `
      -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
    ```

9. Exiba o estado de emparelhamento de myVnetA.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    O estado é **Iniciado**. Ele é alterado para **Conectado** depois de configurar o emparelhamento de myVnetB para myVnetA.

10. Faça logoff do Azure como UserA e faça logon como UserB.
11. Crie o emparelhamento de myVnetB para myVnetA. Copie o conteúdo de script na etapa 8 para um editor de texto em seu computador. Substitua `<SubscriptionB-Id>` pela ID da assinatura A e altere todos os As para B e todos os Bs para A. Para executar o script, copie o script modificado, cole-o no PowerShell e pressione `Enter`.
12. Exiba o estado de emparelhamento de myVnetB. Copie o conteúdo de script na etapa 9 para um editor de texto em seu computador. Alterar A para B para o grupo de recursos e nomes de rede virtual. Para executar o script, cole o script modificado no PowerShell e pressione `Enter`. O estado é **Conectado**. O estado de emparelhamento de **myVnetA** se altera para **Conectado** depois de você criar o emparelhamento de **myVnetB** para **myVnetA**. Você pode fazer um novo logon como UserA no Azure e completar a etapa 9 novamente para verificar o estado de emparelhamento de myVnetA. 

    > [!NOTE]
    > O emparelhamento não é estabelecido até que o estado de emparelhamento para ambas as redes virtuais seja **Conectado**.

    Todos os recursos do Azure criados na rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você desejar resolver nomes entre redes virtuais em um emparelhamento, deverá criar seu próprio servidor DNS. Saiba como configurar a [Resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

13. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
14. **Opcional**: Para excluir os recursos criados neste tutorial, conclua as etapas em [Excluir recursos](#delete-powershell) deste artigo.

## <a name="template"></a>Criar emparelhamento – modelo do Resource Manager

1. Conclua as etapas nas seções [Portal](#portal), [CLI do Azure](#cli) ou [PowerShell](#powershell) deste artigo para criar uma rede virtual e atribuir as [ permissões](#permissions) apropriadas para a conta em cada assinatura.
2. Salve o texto que se segue em um arquivo no computador local. Substitua `<subscription ID>` pela ID de assinatura de UserA. Você pode salvar o arquivo como vnetpeeringA.json, por exemplo.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
    }
    ```

3. Faça logon no Azure como UserA e implante o modelo usando o [portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), o [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-a-template-from-your-local-machine) ou a [CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Especifique o nome do arquivo em que você salvou o exemplo de texto json na etapa 2.
4. Copie o exemplo de json da etapa 2 em um arquivo no computador e faça alterações nas linhas que começam com:
    - **nome**: altere *myVnetA/myVnetAToMyVnetB* para *myVnetB/myVnetBToMyVnetA*.
    - **id**: substitua `<subscription ID>` pela ID de assinatura de UserB e altere *myVnetB* para *myVnetA*.
5. Conclua a etapa 3 novamente, registrada em log no Azure como UserB.
6. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
7. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas da seção [Excluir recursos](#delete) deste artigo, usando o portal do Azure, o PowerShell ou a CLI do Azure.

## <a name="permissions"></a>Permissões

As contas usadas para criar um emparelhamento de rede virtual devem ter a função ou as permissões necessárias. Por exemplo, se você pretende emparelhar duas redes virtuais, chamadas **myVnetA** e **myVnetB**, sua conta deve ser atribuída à seguinte função ou permissões mínimas para cada rede virtual:
    
|Rede virtual|Função|Permissões|
|---|---|---|
|myVnetA|[Colaborador de rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|myVnetB|[Colaborador de rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

Saiba mais sobre [funções internas](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e como atribuir permissões específicas a [funções personalizadas](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (somente para o Resource Manager).

## <a name="delete"></a>Excluir recursos
Ao concluir este tutorial, talvez você deseje excluir os recursos criados no tutorial para não incorrer em encargos de uso. A exclusão de um grupo de recursos também exclui todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Faça logon no Portal do Azure como UserA.
2. Na caixa de pesquisa do portal, insira **myResourceGroupA**. Nos resultados da pesquisa, clique em **myResourceGroupA**.
3. Na folha **myResourceGroupA**, clique no ícone **Excluir**.
4. Para confirmar a exclusão, na caixa **DIGITAR O NOME DO GRUPO DE RECURSOS**, insira **myResourceGroupA** e, depois, clique em **Excluir**.
5. Faça logoff do portal como UserA e faça logon como UserB.
6. Conclua as etapas 2 a 4 para myResourceGroupB.

### <a name="delete-cli"></a>Azure CLI

1. Faça logon no Azure como UserA e execute o seguinte comando:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```
2. Faça logoff do Azure como UserA e faça logon como UserB.
3. Execute o seguinte comando:

    ```azurecli-interactive
    az group delete --name myResourceGroupB --yes
    ```

### <a name="delete-powershell"></a>PowerShell

1. Faça logon no Azure como UserA e execute o seguinte comando:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -force
    ```

2. Faça logoff do Azure como UserA e faça logon como UserB.
3. Execute o seguinte comando:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupB -force
    ```

## <a name="next-steps"></a>Próximas etapas

- Familiarize por completo com [comportamentos e restrições importantes do emparelhamento de rede virtual](virtual-network-manage-peering.md#requirements-and-constraints) antes de criar um emparelhamento de rede virtual para uso em produção.
- Saiba mais sobre todas as [configurações de emparelhamento de rede virtual](virtual-network-manage-peering.md#create-a-peering).
- Saiba como [criar uma topologia de rede hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com o emparelhamento de rede virtual.
