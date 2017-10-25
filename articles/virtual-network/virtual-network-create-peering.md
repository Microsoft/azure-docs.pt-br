---
title: "Criar um emparelhamento de rede virtual do Azure – Resource Manager – mesma assinatura | Microsoft Docs"
description: Saiba como criar um emparelhamento de rede virtual entre redes virtuais criadas por meio do Resource Manager que existem na mesma assinatura do Azure.
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anavin;jdial
ms.openlocfilehash: ebe418f03c2edf176790f654f3f9f4d7eec09165
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-peering---resource-manager-same-subscription"></a>Criar um emparelhamento de rede virtual – Resource Manager, mesma assinatura

Neste tutorial, você aprende a criar um emparelhamento de rede virtual entre redes virtuais criadas por meio do Resource Manager. Ambas as redes virtuais existem na mesma assinatura. O emparelhamento de duas redes virtuais permite que recursos em diferentes redes virtuais se comuniquem com a mesma largura de banda e latência, como se os recursos estivessem na mesma rede virtual. Saiba mais sobre [Emparelhamento de rede virtual](virtual-network-peering-overview.md). 

As etapas para criar um emparelhamento de rede virtual são diferentes, dependendo de as redes virtuais estarem na mesma ou em diferentes assinaturas e do [modelo de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pelo qual as redes virtuais são criadas. Saiba como criar um emparelhamento de rede virtual em outros cenários clicando no cenário na tabela a seguir:

|Modelo de implantação do Azure  | Assinatura do Azure  |
|--------- |---------|
|[Ambos Resource Manager](create-peering-different-subscriptions.md) |Diferente|
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
4. Conclua as etapas 2 a 3 novamente especificando os seguintes valores na etapa 3:
    - **Nome**: *myVnet2*
    - **Espaço de endereço**: *10.1.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Assinatura**: selecione sua assinatura
    - **Grupo de recursos**: selecione **Usar existente** e selecione *myResourceGroup*
    - **Localização**: *Leste dos EUA*
5. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myResourceGroup*. Clique em **myResourceGroup** quando ele for exibido nos resultados da pesquisa. Uma folha é exibida para o grupo de recursos **myresourcegroup**. O grupo de recursos contém as duas redes virtuais criadas nas etapas anteriores.
6. Clique em **myVNet1**.
7. Na folha **myVnet1** exibida, clique em **Emparelhamentos** na lista vertical de opções no lado esquerdo da folha.
8. Na folha **myVnet1 – Emparelhamentos** exibida, clique em **+ Adicionar**
9. Na folha **Adicionar emparelhamento** exibida, insira ou selecione as seguintes opções e clique em **OK**:
     - **Nome**: *myVnet1ToMyVnet2*
     - **Modelo de implantação de rede virtual**: selecione **Resource Manager**. 
     - **Assinatura**: selecione sua assinatura
     - **Rede virtual**: clique em **Escolher uma rede virtual** e, em seguida, clique em **myVnet2**.
     - **Permitir acesso à rede virtual:** verifique se a opção **Habilitado** está selecionada.
    Nenhuma outra configuração é usada neste tutorial. Para saber mais sobre todas as configurações de emparelhamento, leia [Gerenciar emparelhamentos de rede virtual](virtual-network-manage-peering.md#create-a-peering).
10. Depois de clicar em **OK** na etapa anterior, a folha **Adicionar emparelhamento** será fechada e você verá a folha **myVnet1 – Emparelhamentos** novamente. Depois de alguns segundos, o emparelhamento que você criou será exibido na folha. **Iniciado** é listado na coluna **STATUS DE EMPARELHAMENTO** do emparelhamento de **myVnet1ToMyVnet2** criado. Você emparelhou a Vnet1 com a Vnet2, mas agora deve emparelhar a myVnet2 com a myVnet1. O emparelhamento deve ser criado em ambas as orientações para permitir que os recursos nas redes virtuais se comuniquem entre si.
11. Conclua as etapas 5 a 10 novamente para a myVnet2.  Nomeie o emparelhamento *myVnet2ToMyVnet1*.
12. Alguns segundos depois de clicar em **OK** para criar o emparelhamento para a MyVnet2, o emparelhamento de **myVnet2ToMyVnet1** que você acabou de criar será listado com **Conectado** na coluna **STATUS DE EMPARELHAMENTO**.
13. Conclua as etapas 5 a 7 novamente para a MyVnet1. O **STATUS DE EMPARELHAMENTO** do emparelhamento de **myVnet1ToVNet2** agora também é **Conectado**. O emparelhamento é estabelecido com êxito após a exibição de **Conectado** na coluna **STATUS DE EMPARELHAMENTO** de ambas as redes virtuais no emparelhamento.
14. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
15. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas da seção [Excluir recursos](#delete-portal) deste artigo.

Todos os recursos do Azure criados na rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você desejar resolver nomes entre redes virtuais em um emparelhamento, deverá criar seu próprio servidor DNS. Saiba como configurar a [Resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="cli"></a>Criar emparelhamento – CLI do Azure

O seguinte script:

- Exige a CLI do Azure versão 2.0.4 ou posterior. Para localizar a versão, execute o comando `az --version`. Se você precisar atualizar, confira [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funciona em um shell do Bash. Para opções sobre como executar scripts da CLI do Azure no cliente Windows, veja [Execução da CLI do Azure no Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Em vez de instalar a CLI e suas dependências, você pode usar o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Clique no botão **Experimente** no script a seguir, o que invoca um Cloud Shell com o qual você pode fazer logon em sua conta do Azure. Para executar o script, clique no botão **Copiar** e cole o conteúdo no seu Cloud Shell.

1. Crie um grupo de recursos e duas redes virtuais.

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    ```

2. Crie um emparelhamento de rede virtual entre as duas redes virtuais.
 
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    ```

3. Depois que o script for executado, examine os emparelhamentos de cada rede virtual. 

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    Execute o comando anterior novamente, substituindo *myVnet1* por *myVnet2*. A saída dos dois comandos mostra **Conectado** na coluna **PeeringState**.

     Todos os recursos do Azure criados na rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você desejar resolver nomes entre redes virtuais em um emparelhamento, deverá criar seu próprio servidor DNS. Saiba como configurar a [Resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

4. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
5. **Opcional**: Para excluir os recursos criados neste tutorial, conclua as etapas em [Excluir recursos](#delete-cli) deste artigo.


## <a name="powershell"></a>Criar emparelhamento – PowerShell

1. Instale a última versão do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se você for novo no Azure PowerShell, consulte [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Para iniciar uma sessão do PowerShell, acesse **Iniciar**, insira **powershell** e, em seguida, clique em **PowerShell**.
3. No PowerShell, faça logon no Azure inserindo o comando `login-azurermaccount`. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Consulte a seção [Permissões](#permissions) deste artigo para obter detalhes.
4. Crie um grupo de recursos e duas redes virtuais. Para executar o script, copie o script a seguir, cole-o no PowerShell e pressione `Enter` após a última linha aparecer na tela:

    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    ```

5. Crie um emparelhamento de rede virtual entre as duas redes virtuais. Copie o script a seguir, cole no PowerShell e pressione `Enter` após a última linha aparece na tela:
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    ```
6. Para examinar as sub-redes para a rede virtual, copie o seguinte comando cole-o na janela do PowerShell e, em seguida, pressione `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Execute o comando anterior novamente, substituindo *myVnet1* por *myVnet2*. A saída dos dois comandos mostra **Conectado** na coluna **PeeringState**.
7. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
8. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas em [Excluir recursos](#delete-powershell) deste artigo.

Todos os recursos do Azure criados na rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você desejar resolver nomes entre redes virtuais em um emparelhamento, deverá criar seu próprio servidor DNS. Saiba como configurar a [Resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="template"></a>Criar emparelhamento – modelo do Resource Manager

1. Consulte [Criar um emparelhamento de rede virtual](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering) usando um modelo do Resource Manager. As instruções são fornecidas com o modelo para implantação do modelo usando o portal do Azure, o PowerShell ou a CLI do Azure. Faça logon na ferramenta de sua escolha para implantar o modelo usando uma conta que tenha as permissões necessárias para criar um emparelhamento de rede virtual. Consulte a seção [Permissões](#permissions) deste artigo para obter detalhes.
2. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
3. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas da seção [Excluir recursos](#delete) deste artigo, usando o portal do Azure, o PowerShell ou a CLI do Azure.

## <a name="permissions"></a>Permissões

As contas usadas para criar um emparelhamento de rede virtual devem ter a função ou as permissões necessárias. Por exemplo, se você pretender emparelhar duas redes virtuais, chamadas VNet1 e VNet2, sua conta deverá ser atribuído a seguinte função ou permissões mínimas para cada rede virtual:
    
|Rede virtual|Função|Permissões|
|---|---|---|
|VNet1|[Colaborador de rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|VNet2|[Colaborador de rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

Saiba mais sobre [funções internas](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e como atribuir permissões específicas a [funções personalizadas](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (somente para o Resource Manager).

## <a name="delete"></a>Excluir recursos
Ao concluir este tutorial, talvez você deseje excluir os recursos criados no tutorial para não incorrer em encargos de uso. A exclusão de um grupo de recursos também exclui todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa do portal, insira **myResourceGroup**. Nos resultados da pesquisa, clique em **myResourceGroup**.
2. Na folha **myResourceGroup**, clique no ícone **Excluir**.
3. Para confirmar a exclusão, na caixa **DIGITAR O NOME DO GRUPO DE RECURSOS**, insira **myResourceGroup** e, depois, clique em **Excluir**.

### <a name="delete-cli"></a>Azure CLI

Digite o seguinte comando:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Digite o seguinte comando:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -force
```

## <a name="next-steps"></a>Próximas etapas

- Familiarize por completo com [comportamentos e restrições importantes do emparelhamento de rede virtual](virtual-network-manage-peering.md#requirements-and-constraints) antes de criar um emparelhamento de rede virtual para uso em produção.
- Saiba mais sobre todas as [configurações de emparelhamento de rede virtual](virtual-network-manage-peering.md#create-a-peering).
- Saiba como [criar uma topologia de rede hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com o emparelhamento de rede virtual.
