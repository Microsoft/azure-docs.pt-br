---
title: Conectar redes virtuais com o Emparelhamento VNET – tutorial – portal do Azure
description: Neste tutorial, você aprende a conectar redes virtuais com o emparelhamento de rede virtual usando o Portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: b7fcf7f60b18d0d44ded67cb5b22bcdcdcd56a77
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059319"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Tutorial: conectar redes virtuais ao emparelhamento de rede virtual usando o Portal do Azure

Você pode conectar redes virtuais entre si ao emparelhamento de rede virtual. Essas redes virtuais podem estar na mesma região ou em regiões diferentes (também conhecido como emparelhamento VNet Global). Depois que as redes virtuais são emparelhadas, os recursos de ambas as redes virtuais podem se comunicar entre si, com a mesma latência e largura de banda como se os recursos estivessem na mesma rede virtual. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar duas redes virtuais
> * Conectar duas redes virtuais a um emparelhamento de rede virtual
> * Implementar uma VM (máquina virtual) em cada rede virtual
> * Comunicação entre VMs

Se preferir, você pode concluir este tutorial usando a [CLI do Azure](tutorial-connect-virtual-networks-cli.md) ou o [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisa ter uma conta do Azure com uma assinatura ativa. Caso não tenha uma, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em https://portal.azure.com.

## <a name="create-virtual-networks"></a>Criar redes virtuais

1. No portal do Azure, selecione **Criar um recurso**.
2. Selecione **Rede** e, sem seguida, selecione **Rede Virtual**.
3. Na guia **Noções básicas**, insira ou selecione as seguintes informações e aceite os padrões para as configurações restantes:

    |Configuração|Valor|
    |---|---|
    |Subscription| Selecione sua assinatura.|
    |Resource group| Selecione **Criar novo** e insira *myResourceGroup*.|
    |Região| Selecione **Leste dos EUA**.|
    |Nome|myVirtualNetwork1|

4. Na guia **Endereços IP**, insira 10.0.0.0/16 para o campo **Espaço de Endereço**. Clique no botão **Adicionar sub-rede** abaixo e insira *Subnet1* para **Nome da sub-rede** e 10.0.0.0/24 para **Intervalo de endereços da sub-rede**.
5. Selecione **Examinar + Criar** e **Criar**.
   
5. Conclua as etapas 1 a 5 novamente, com as seguintes alterações:

    |Configuração|Valor|
    |---|---|
    |Nome|myVirtualNetwork2|
    |Espaço de endereço|10.1.0.0/16|
    |Resource group| Clique em **Usar existente** e selecione **myResourceGroup**.|
    |Nome da sub-rede | Subnet2|
    |Intervalo de endereços da sub-rede|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Emparelhar redes virtuais

1. Nas caixa Pesquisar na parte superior do portal, comece digitando *MyVirtualNetwork1*. Quando **myVirtualNetwork1** aparecer nos resultados da pesquisa, selecione-o.
2. Em **Emparelhamentos**, selecione **Configurações** e, em seguida, selecione **Adicionar**, como mostrado na imagem a seguir:

    ![Criar emparelhamento](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Insira, ou selecione, as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **OK**.

    |Configuração|Valor|
    |---|---|
    |Nome do emparelhamento de myVirtualNetwork1 para a rede virtual remota|myVirtualNetwork1-myVirtualNetwork2 – quando a página for carregada pela primeira vez, você verá a frase "rede virtual remota" aqui. Depois que você escolher a rede virtual remota, a frase "rede virtual remota" será substituída pelo nome da rede virtual remota.|
    |Subscription| Selecione sua assinatura.|
    |Rede virtual|myVirtualNetwork2 – Para selecionar a rede virtual *myVirtualNetwork2*, selecione **Rede virtual** e, em seguida, selecione **myVirtualNetwork2 (myResourceGroup)**. Você pode selecione uma rede virtual na mesma região ou em uma região diferente.|
    |Nome do emparelhamento de myVirtualNetwork2 para myVirtualNetwork1|myVirtualNetwork2-myVirtualNetwork1|

    ![Configurações de emparelhamento](./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png)

    O **STATUS DO EMPARELHAMENTO** é *Conectado*, conforme mostrado na seguinte imagem:

    ![Status de emparelhamento](./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png)

    Se você não vir o status, atualize seu navegador.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma VM em cada rede virtual para que seja possível comunicar-se entre elas em uma etapa posterior.

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. No portal do Azure, selecione **Criar um recurso**.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. É possível selecionar um sistema operacional diferente, mas as etapas restantes assumirão que está selecionado o **Windows Server 2016 Datacenter**. 
3. Insira, ou selecione, as informações a seguir para **Princípios básicos**, aceite os padrões para as configurações restantes e, em seguida, selecione **Criar**:

    |Configuração|Valor|
    |---|---|
    |Resource group| Clique em **Usar existente** e selecione **myResourceGroup**.|
    |Nome|myVm1|
    |Location| Selecione **Leste dos EUA**.|
    |Nome de usuário| Insira um nome de usuário de sua escolha.|
    |Senha| Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   
4. Selecione um tamanho de VM para a opção **Tamanho**.
5. Selecione os valores a seguir em **Rede**:

    |Configuração|Valor|
    |---|---|
    |Rede virtual| myVirtualNetwork1 — Se ainda não estiver selecionada, selecione a **Rede Virtual** e, em seguida, selecione **myVirtualNetwork1**.|
    |Sub-rede| Subnet1 — Se ainda não estiver selecionada, selecione **Sub-rede** e, em seguida, selecione **Subnet1**.|
   
6. Selecione **Rede**. Escolha **Permitir portas selecionadas** para a opção **Portas de entrada públicas**. Escolha **RDP** para a opção **Selecionar portas de entrada** abaixo. 

7. Selecione o botão **Examinar + Criar** no canto inferior esquerdo para iniciar a implantação da VM.

### <a name="create-the-second-vm"></a>Criar a segunda VM

Conclua as etapas 1 a 6 novamente, com as seguintes alterações:

|Configuração|Valor|
|---|---|
|Nome | myVm2|
|Rede virtual | myVirtualNetwork2|

As VMs podem levar alguns minutos para serem criadas. Não continue com as etapas restantes até que ambas as VMs sejam criadas.

## <a name="communicate-between-vms"></a>Comunicação entre VMs

1. Na caixa *Pesquisar* na parte superior do portal, comece digitando *myVm1*. Selecione **myVm1** quando aparecer nos resultados da pesquisa.
2. Crie uma conexão de área de trabalho remota para a VM *myVm1*, selecionando **Conectar**, conforme mostrado na imagem a seguir:

    ![Conectar-se à máquina virtual](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Para conectar-se à VM, abra o arquivo RDP baixado. Se solicitado, selecione **Conectar**.
4. Insira o nome de usuário e senha especificados ao criar a VM (talvez seja necessário selecionar **Mais escolhas**, em seguida, **Usar uma conta diferente**, para especificar as credenciais inseridas ao criar a VM) e selecione **OK**.
5. Você pode receber um aviso do certificado durante o processo de logon. Selecione **Sim** para prosseguir com a conexão.
6. Em uma etapa posterior, o ping será usado para comunicar-se com a VM *myVm2* da VM *myVm1*. O ping usa o protocolo ICMP que, por padrão, não é permitido através do Firewall do Windows. Sobre a VM *myVm1*, habilite o ICMP por meio do Firewall do Windows, para que você possa executar ping nessa VM pela *myVm2* em uma etapa posterior, usando o PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```
    
    Embora o ping seja usado para realizar a comunicação entre VMs neste tutorial, não é recomendável a permissão de ICMP através do Firewall do Windows para implantações de produção.

7. Para conectar a VM *myVm2*, insira o seguinte comando a partir de um prompt de comando na VM *myVm1*:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Desde que você tenha habilitado o ping em *myVm1*, você pode agora executar o ping-lo pelo endereço IP:

    ```
    ping 10.0.0.4
    ```
    
9. Desconecte as sessões RDP para ambas *myVm1* e *myVm2*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele contém: 

1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal. Quando aparecer **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o emparelhamento de rede virtual](virtual-network-peering-overview.md)


