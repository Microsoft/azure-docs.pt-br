---
title: Rotear o tráfego de rede – tutorial – portal do Azure
titlesuffix: Azure Virtual Network
description: Neste tutorial, aprenda a rotear tráfego de rede com uma tabela de rotas usando o Portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: kumud
ms.openlocfilehash: 7da59e996ec37d3653dbde68c5f56caa9e8261ee
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061903"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Tutorial: Rotear tráfego com uma tabela de rotas utilizando o Portal do Azure

O Azure roteia tráfego entre todas as sub-redes contidas em uma rede virtual por padrão. É possível criar as próprias rotas para substituir o roteamento padrão do Azure. As rotas personalizadas são úteis quando, por exemplo, você deseja rotear o tráfego entre sub-redes por meio de uma NVA (solução de virtualização de rede). Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma NVA que roteia o tráfego
> * Criar uma tabela de rotas
> * Criar uma rota
> * Associar uma tabela de rotas a uma sub-rede
> * Implantar VMs (máquinas virtuais) em diferentes sub-redes
> * Rotear o tráfego de uma sub-rede para outra por meio de uma NVA

Este tutorial usa o [portal do Azure](https://portal.azure.com). Você também pode usar a [CLI do Azure](tutorial-create-route-table-cli.md) ou o [Azure PowerShell](tutorial-create-route-table-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisa ter uma conta do Azure com uma assinatura ativa. Caso não tenha uma, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No menu do portal do Azure, selecione **Criar um recurso**. No Azure Marketplace, selecione **Rede** > **Rede virtual** ou procure **Rede virtual** na caixa de pesquisa.

2. Selecione **Criar**.

2. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Subscription | Selecione sua assinatura.|
    | Resource group | Selecione **Criar** e insira **myResourceGroup**. </br> Selecione **OK**. |
    | Nome | Insira **myVirtualNetwork**. |
    | Location | Selecione **(EUA) Leste dos EUA**.|

3. Selecione a guia **Endereços IP** ou escolha o botão **Avançar: Endereços IP** na parte inferior da página.

4. Em **Espaço de endereço IPv4**, selecione o espaço de endereço existente e altere-o para **10.0.0.0/16**.

4. Selecione **+ Adicionar sub-rede** e insira **Pública** em **Nome da sub-rede** e **10.0.0.0/24** em **Intervalo de endereços de sub-rede**.

5. Selecione **Adicionar**.

6. Selecione **+ Adicionar sub-rede** e insira **Privada** em **Nome da sub-rede** e **10.0.1.0/24** em **Intervalo de endereços de sub-rede**.

7. Selecione **Adicionar**.

8. Selecione **+ Adicionar sub-rede** e insira **DMZ** em **Nome da sub-rede** e **10.0.2.0/24** em **Intervalo de endereços de sub-rede**.

9. Selecione **Adicionar**.

10. Selecione a guia **Segurança** ou escolha o botão **Avançar: Segurança** na parte inferior da página.

11. Em **BastionHost**, selecione **Habilitar**. Insira estas informações:

    | Configuração            | Valor                      |
    |--------------------|----------------------------|
    | Nome do bastion | Insira **myBastionHost** |
    | Espaço de endereço da AzureBastionSubnet | Insira **10.0.3.0/24** |
    | Endereço IP público | Selecione **Criar novo**. </br> Em **Nome**, insira **myBastionIP**. </br> Selecione **OK**. |

12. Selecione a guia **Revisar + criar** ou o botão **Revisar + criar**.

13. Selecione **Criar**.

## <a name="create-an-nva"></a>Criar uma NVA

NVAs (soluções de virtualização de rede) são máquinas virtuais que ajudam com funções de rede, como roteamento e otimização de firewall. Este tutorial presume que você esteja usando o **Windows Server 2019 Datacenter**. Você poderá selecionar um sistema operacional diferente se desejar.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina virtual**. 
   
2. Em **Criar uma máquina virtual**, digite ou selecione os valores na guia **Informações Básicas**:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **myResourceGroup** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myVMNVA** |
    | Região | Selecione **(EUA) Leste dos EUA** |
    | Opções de disponibilidade | Selecione **Nenhuma redundância infraestrutura necessária** |
    | Imagem | Selecione **Windows Server 2019 Datacenter** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho da VM ou use a configuração padrão |
    | **Conta de administrador** |  |
    | Nome de Usuário | Insira um nome de usuário |
    | Senha | Insira uma senha |
    | Confirmar senha | Insira novamente a senha |
    | **Regras de porta de entrada** |    |
    | Porta de entrada públicas | Selecione **Nenhum**. |
    |

3. Selecione a guia **Rede** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede**.
  
4. Na guia Rede, selecione ou insira:

    | Configuração | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | Selecione **myVirtualNetwork**. |
    | Sub-rede | Selecione **DMZ** |
    | IP público | Selecione **Nenhum** |
    | Grupo de segurança de rede da NIC | Selecione **Básico**|
    | Rede das portas de entrada públicas | Selecione **Nenhum**. |
   
5. Selecione a guia **Examinar + criar** ou clique no botão azul **Examinar + criar** na parte inferior da página.
  
6. Examine as configurações e selecione **Criar**.

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

1. No menu do [portal do Azure](https://portal.azure.com) ou na **Página Inicial**, selecione **Criar um recurso**.

2. Na caixa de pesquisa, insira **Tabela de rotas**. Quando a **Tabela de rotas** é exibida nos resultados da pesquisa, selecione-a.

3. Na página **Tabela de rotas**, selecione **Criar**.

4. Em **Criar tabela de rotas**, na guia **Noções básicas**, insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** |   |
    | Subscription | Selecione sua assinatura.|
    | Resource group | Selecione **myResourceGroup**. |
    | **Detalhes da instância** |    |
    | Região | Selecione **Leste dos EUA**. |
    | Nome | Insira **myRouteTablePublic**. |
    | Propagar rotas de gateway | Selecione **Sim** na barra superior. |

    :::image type="content" source="./media/tutorial-create-route-table-portal/create-route-table.png" alt-text="Criar tabela de rotas, portal do Azure." border="true":::

5. Selecione a guia **Examinar + criar** ou clique no botão azul **Examinar + criar** na parte inferior da página.

## <a name="create-a-route"></a>Criar uma rota

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar a tabela de rotas. Pesquise por **Tabela de rotas** e selecione essa opção.

2. Selecione o nome da sua tabela de rotas (**myRouteTablePublic**).

3. Na página **myRouteTablePublic**, na seção **Configurações**, selecione **Rotas**.

4. Na página rotas, selecione o botão **+ Adicionar**.

5. Em **Adicionar rota**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome da rota | Insira **ToPrivateSubnet** |
    | Prefixo de endereço | Insira **10.0.1.0/24** (o intervalo de endereços da sub-rede **Privada** criada anteriormente) |
    | Tipo do próximo salto | Selecione **Solução de virtualização**. |
    | Endereço do próximo salto | Insira **10.0.2.4** (um endereço dentro do intervalo de endereços da sub-rede **DMZ**) |

6. Selecione **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar sua rede virtual. Pesquise por **Redes virtuais** e selecione essa opção.

2. Selecione o nome da sua rede virtual (**myVirtualNetwork**).

3. Na página **myVirtualNetwork**, na seção **Configurações**, selecione **Sub-redes**.

4. Na lista de sub-redes da rede virtual, escolha **Pública**.

5. Em **Tabela de rotas**, escolha a tabela de rotas que você criou (**myRouteTablePublic**). 

6. Selecione **Salvar** para associar a tabela de rotas à sub-rede **Pública**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/associate-route-table.png" alt-text="Associe a tabela de rotas, a lista de sub-redes, a rede virtual e o portal do Azure." border="true":::

## <a name="turn-on-ip-forwarding"></a>Ativar o encaminhamento de IP

Em seguida, ative o encaminhamento de IP para sua nova máquina virtual de NVA, **myVMNVA**. Quando o Azure enviar o tráfego de rede para **myVMNVA**, se o tráfego for destinado a um endereço IP diferente, o encaminhamento de IP enviará o tráfego para a localização correta.

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar a VM. Pesquise por **Máquinas virtuais** e selecione essa opção.

2. Selecione o nome da sua máquina virtual (**myVMNVA**).

3. Na página de visão geral de **myVMNVA**, em **Configurações**, selecione **Rede**.

4. Na página **Rede** de **myVMNVA**, selecione o adaptador de rede ao lado de **Adaptador de Rede**.  O nome do adaptador começará com **myvmnva**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/virtual-machine-networking.png" alt-text="Networking, VM (máquina virtual) de NVA (solução de virtualização de rede), portal do Azure" border="true":::

5. Na página Visão geral do adaptador de rede, em **Configurações**, selecione **Configurações de IP**.

6. Na página **Configurações de IP**, defina **Encaminhamento de IP** para **Habilitado** e selecione **Salvar**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/enable-ip-forwarding.png" alt-text="Habilitar o encaminhamento de IP" border="true":::

## <a name="create-public-and-private-virtual-machines"></a>Crie máquinas virtuais públicas e privadas

Crie uma VM pública e uma VM privada na rede virtual. Posteriormente, você vai usá-los para ver que o Azure roteia o tráfego da sub-rede **Pública** rede para a sub-rede **Privada** por meio da NVA.


### <a name="public-vm"></a>VM pública

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina virtual**. 
   
2. Em **Criar uma máquina virtual**, digite ou selecione os valores na guia **Informações Básicas**:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **myResourceGroup** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myVMPublic** |
    | Região | Selecione **(EUA) Leste dos EUA** |
    | Opções de disponibilidade | Selecione **Nenhuma redundância infraestrutura necessária** |
    | Imagem | Selecione **Windows Server 2019 Datacenter** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho da VM ou use a configuração padrão |
    | **Conta de administrador** |  |
    | Nome de Usuário | Insira um nome de usuário |
    | Senha | Insira uma senha |
    | Confirmar senha | Insira novamente a senha |
    | **Regras de porta de entrada** |    |
    | Porta de entrada públicas | Selecione **Nenhum**. |
    |

3. Selecione a guia **Rede** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede**.
  
4. Na guia Rede, selecione ou insira:

    | Configuração | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | Selecione **myVirtualNetwork**. |
    | Sub-rede | Selecione **Pública** |
    | IP público | Selecione **Nenhum** |
    | Grupo de segurança de rede da NIC | Selecione **Básico**|
    | Rede das portas de entrada públicas | Selecione **Nenhum**. |
   
5. Selecione a guia **Examinar + criar** ou clique no botão azul **Examinar + criar** na parte inferior da página.
  
6. Examine as configurações e selecione **Criar**.

### <a name="private-vm"></a>VM privada

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Máquina virtual**. 
   
2. Em **Criar uma máquina virtual**, digite ou selecione os valores na guia **Informações Básicas**:

    | Configuração | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscription | Selecionar sua assinatura do Azure |
    | Grupo de recursos | Selecione **myResourceGroup** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myVMPrivate** |
    | Região | Selecione **(EUA) Leste dos EUA** |
    | Opções de disponibilidade | Selecione **Nenhuma redundância infraestrutura necessária** |
    | Imagem | Selecione **Windows Server 2019 Datacenter** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho da VM ou use a configuração padrão |
    | **Conta de administrador** |  |
    | Nome de Usuário | Insira um nome de usuário |
    | Senha | Insira uma senha |
    | Confirmar senha | Insira novamente a senha |
    | **Regras de porta de entrada** |    |
    | Porta de entrada públicas | Selecione **Nenhum**. |
    |

3. Selecione a guia **Rede** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede**.
  
4. Na guia Rede, selecione ou insira:

    | Configuração | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | Selecione **myVirtualNetwork**. |
    | Sub-rede | Selecione **Privado** |
    | IP público | Selecione **Nenhum** |
    | Grupo de segurança de rede da NIC | Selecione **Básico**|
    | Rede das portas de entrada públicas | Selecione **Nenhum**. |
   
5. Selecione a guia **Examinar + criar** ou clique no botão azul **Examinar + criar** na parte inferior da página.
  
6. Examine as configurações e selecione **Criar**.

## <a name="route-traffic-through-an-nva"></a>Rotear o tráfego por meio de uma NVA

### <a name="sign-in-to-private-vm"></a>Entrar na VM privada

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar a VM privada. Pesquise por **Máquinas virtuais** e selecione essa opção.

2. Escolha o nome da sua máquina virtual privada (**myVmPrivate**).

3. Na barra de menus da VM, selecione **Conectar** e escolha **Bastion**.

4. Na página **Conectar**, selecione o botão azul **Usar o Bastion**.

5. Na página do **Bastion**, insira o nome de usuário e a senha que você criou para a máquina virtual anteriormente.

6. Selecione **Conectar**.

### <a name="configure-firewall"></a>Configurar o firewall

Em uma etapa posterior, você usará a ferramenta de rastreamento de rota para testar o roteamento. A rota de rastreamento usa o protocolo ICMP, que o Firewall do Windows nega por padrão. 

Habilite o ICMP por meio do Firewall do Windows.

1. Na conexão do Bastion de **myVMPrivate**, abra o PowerShell com privilégios administrativos.

2. Insira este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Você estará usando o rastreamento de rotas para testar o roteamento neste tutorial. Para ambientes de produção, não é recomendável permitir ICMP por meio do Firewall do Windows.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Ativar o encaminhamento de IP em myVMNVA

Você [ativou o encaminhamento de IP](#turn-on-ip-forwarding) para o adaptador de rede da VM usando o Azure. O sistema operacional da VM também precisa encaminhar o tráfego de rede. 

Ative o encaminhamento de IP para **myVMNVA** com estes comandos.

1. No PowerShell, na VM **myVMPrivate**, abra uma área de trabalho remota para a VM **myVMNVA**:

    ```powershell
    mstsc /v:myvmnva
    ```

2. No PowerShell, na VM **myVMNVA**, insira este comando para ativar o encaminhamento de IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

3. Reinicie **myVMNVA**.

    ```powershell
    Restart-Computer
    ```

4. Após a reinicialização de **myVMNVA**, crie uma sessão de área de trabalho remota para **myVMPublic**. 
    
    Ainda conectado à VM **myVMPrivate**, abra o PowerShell e execute este comando:

    ```powershell
    mstsc /v:myvmpublic
    ```
5. Na área de trabalho remota de **myVMPublic**, abra o PowerShell.

6. Habilite o ICMP por meio do Firewall do Windows digitando este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testar o roteamento de tráfego de rede

Primeiro, vamos testar o roteamento de tráfego de rede da VM **myVMPublic** para a VM **myVMPrivate**.

1. Do PowerShell na **myVMPublic**, insira este comando:

    ```powershell
    tracert myvmprivate
    ```

    A resposta é semelhante a este exemplo:

    ```powershell
    Tracing route to myvmprivate.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

      1     1 ms     *        2 ms  myvmnva.internal.cloudapp.net [10.0.2.4]
      2     2 ms     1 ms     1 ms  myvmprivate.internal.cloudapp.net [10.0.1.4]

    Trace complete.
    ```

    * Você pode ver que o primeiro salto é para 10.0.2.4, que é o endereço IP privado da **myVMNVA**. 

    * O segundo salto é para o endereço IP privado da **myVMPrivate**: 10.0.1.4. 

    Anteriormente, você adicionou a rota para a tabela de rotas **myRouteTablePublic** e a associo à sub-rede *Pública*. O Azure enviou o tráfego por meio da NVA, e não diretamente para a sub-rede *Privada*.

2. Feche a sessão da área de trabalho remota para **myVMPublic**, que ainda mantém você conectado à **myVMPrivate**.

3. Abra o PowerShell em **myVMPrivate** e insira este comando:

    ```powershell
    tracert myvmpublic
    ```

    Esse comando testa o roteamento de tráfego de rede da VM *myVmPrivate* para a VM *myVmPublic*. A resposta é semelhante a este exemplo:

    ```cmd
    Tracing route to myvmpublic.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

      1     1 ms     1 ms     1 ms  myvmpublic.internal.cloudapp.net [10.0.0.4]

    Trace complete.
    ```

    Você pode ver que o Azure roteia o tráfego diretamente de *myVMPrivate* para *myVMPublic*. Por padrão, o Azure roteia o tráfego diretamente entre sub-redes.

4. Feche a sessão do Bastion para **myVMPrivate**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o grupo de recursos não for mais necessário, exclua **myResourceGroup** e todos os recursos que ele contém:

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar o grupo de recursos. Procure por **Grupos de recursos** e selecione essa opção.

2. Selecione o nome do seu grupo de recursos (**myResourceGroup**).

3. Selecione **Excluir grupo de recursos**.

4. Na caixa de diálogo de confirmação, insira **myResourceGroup** em **DIGITE O NOME DO GRUPO DE RECURSOS** e, depois, selecione **Excluir**. 


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

* Criou uma tabela de rotas e a associou a uma sub-rede.
* Criou uma NVA simples que roteou o tráfego de uma sub-rede pública para uma sub-rede privada. 

Com o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking), você pode implantar diferentes NVAs pré-configuradas, que fornecem muitas funções de rede úteis. 

Para saber mais sobre roteamento, consulte [Visão geral de roteamento](virtual-networks-udr-overview.md) e [Gerenciar uma tabela de rotas](manage-route-table.md).

Para filtrar o tráfego de rede em uma rede virtual, confira:
> [!div class="nextstepaction"]
> [Tutorial: Filtrar o tráfego de rede com um grupo de segurança de rede usando o portal do Azure](tutorial-filter-network-traffic.md)
