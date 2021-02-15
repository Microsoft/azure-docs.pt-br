---
title: Rotear o tráfego de rede – tutorial – portal do Azure
titlesuffix: Azure Virtual Network
description: Neste tutorial, aprenda a rotear tráfego de rede com uma tabela de rotas usando o Portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: e047f46e110e1f7b1d544545c80bd1097ae65167
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221911"
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

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-nva"></a>Criar uma NVA

NVAs (soluções de virtualização de rede) são máquinas virtuais que ajudam com funções de rede, como roteamento e otimização de firewall. Este tutorial presume que você esteja usando o **Windows Server 2016 Datacenter**. Você poderá selecionar um sistema operacional diferente se desejar.

1. No menu do [portal do Azure](https://portal.azure.com) ou na **Página Inicial**, selecione **Criar um recurso**.

1. Escolha **Segurança** > **Windows Server 2016 Datacenter**.

    ![Windows Server 2016 Datacenter, Criar uma VM, portal do Azure](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. Na página **Criar uma máquina virtual**, em **Noções básicas**, insira ou selecione estas informações:

    | Seção | Configuração | Ação |
    | ------- | ------- | ----- |
    | **Detalhes do projeto** | Subscription | Escolha sua assinatura. |
    | | Resource group | Selecione **Criar novo** e insira *myResourceGroup*, então selecione **OK**. |
    | **Detalhes da instância** | Nome da máquina virtual | Insira *myVmNva*. |
    | | Região | Escolha **Leste dos EUA (EUA)** . |
    | | Opções de disponibilidade | Escolha **Nenhuma redundância infraestrutura necessária**. |
    | | Imagem | Escolha **Windows Server 2016 Datacenter**. |
    | | Tamanho | Mantenha o padrão, **Standard DS1 v2**. |
    | **Conta de administrador** | Nome de Usuário | Insira um nome de usuário de sua escolha. |
    | | Senha | Insira uma senha de sua escolha, que precisa ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). |
    | | Confirmar Senha | Digite a senha novamente. |
    | **Regras de porta de entrada** | Porta de entrada públicas | Escolha **Nenhuma**. |
    | **Economizar dinheiro** | Já tem uma licença do Windows Server? | Escolha **Não**. |

    ![Noções básicas, Criar uma máquina virtual, portal do Azure](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    Em seguida, selecione **Avançar: Discos >** .

1. Em **Discos**, selecione as configurações adequadas para suas necessidades, depois selecione **Avançar: Rede >** .

1. Em **Rede**:

    1. Para **Rede virtual**, selecione **Criar novo**.
    
    1. Na caixa de diálogo **Criar rede virtual**, em **Nome**, digite *myVirtualNetwork*.

    1. Em **Espaço de endereço**, substitua o intervalo de endereços existente por *10.0.0.0/16*.

    1. Em **Sub-redes**, selecione o ícone **Excluir** para excluir a sub-rede existente e, em seguida, insira as seguintes combinações de **Nome da sub-rede** e **Intervalo de endereços**. Quando um nome e um intervalo válidos forem inseridos, uma nova linha vazia aparecerá abaixo dessas informações.

        | Nome da sub-rede | Intervalo de endereços |
        | ----------- | ------------- |
        | *Pública* | *10.0.0.0/24* |
        | *Privada* | *10.0.1.0/24* |
        | *DMZ* | *10.0.2.0/24* |

    1. Selecione **OK** para fechar a caixa de diálogo.

    1. Em **Sub-rede**, escolha **DMZ (10.0.2.0/24)** .

    1. Em **IP público**, escolha **Nenhum**, pois essa VM não se conectará pela Internet.

    1. Selecione **Avançar: Gerenciamento >** .

1. Em **Gerenciamento**:

    1. Em **Conta de armazenamento de diagnóstico**, selecione **Criar**.
    
    1. Na caixa de diálogo **Criar conta de armazenamento**, insira ou selecione estas informações:

        | Configuração | Valor |
        | ------- | ----- |
        | Nome | *mynvastorageaccount* |
        | Tipo de conta | **Armazenamento (uso geral v1)** |
        | Desempenho | **Standard** |
        | Replicação | **Armazenamento com redundância local (LRS)** |
    
    1. Selecione **OK** para fechar a caixa de diálogo.

    1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar** e o Azure valida sua configuração.

1. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

    A VM demora alguns minutos para criar. Aguarde até que o Azure termine de criar a VM. A página **Sua implantação está em andamento** mostra os detalhes da implantação.

1. Quando sua VM estiver pronta, selecione **Ir para o recurso**.

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

1. No menu do [portal do Azure](https://portal.azure.com) ou na **Página Inicial**, selecione **Criar um recurso**.

2. Na caixa de pesquisa, insira *Tabela de rotas*. Quando a **Tabela de rotas** é exibida nos resultados da pesquisa, selecione-a.

3. Na página **Tabela de rotas**, selecione **Criar**.

4. Em **Criar tabela de rota**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | *myRouteTablePublic* |
    | Subscription | Sua assinatura |
    | Resource group | **myResourceGroup** |
    | Location | **(EUA) Leste dos EUA** |
    | Propagação de rota do gateway de rede virtual | **Enabled** |

    ![Criar tabela de rotas, portal do Azure](./media/tutorial-create-route-table-portal/create-route-table.png)

5. Selecione **Criar**.

## <a name="create-a-route"></a>Criar uma rota

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar a tabela de rotas. Pesquise por **Tabela de rotas** e selecione essa opção.

1. Escolha o nome da sua tabela de rotas (**myRouteTablePublic**).

1. Escolha **Rotas** > **Adicionar**.

    ![Adicionar rota, tabela de rotas, portal do Azure](./media/tutorial-create-route-table-portal/add-route.png)

1. Em **Adicionar rota**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome da rota | *ToPrivateSubnet* |
    | Prefixo de endereço | *10.0.1.0/24* (o intervalo de endereços da sub-rede *Privada* criada anteriormente) |
    | Tipo do próximo salto | **Solução de virtualização** |
    | Endereço do próximo salto | *10.0.2.4* (um endereço dentro do intervalo de endereços da sub-rede *DMZ*) |

1. Selecione **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar sua rede virtual. Pesquise por **Redes virtuais** e selecione essa opção.

1. Escolha o nome da sua rede virtual (**myVirtualNetwork**).

1. Na barra de menus da rede virtual, escolha **Sub-redes**.

1. Na lista de sub-rede da rede virtual, escolha **Pública**.

1. Em **Tabela de rotas**, escolha a tabela de rotas que você criou (**myRouteTablePublic**) e, em seguida, selecione **Salvar** para associar a tabela de rotas à sub-rede *Pública*.

    ![Associar tabela de rotas, lista de sub-redes, rede virtual e portal do Azure](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>Ativar o encaminhamento de IP

Em seguida, ative o encaminhamento de IP para sua nova máquina virtual de NVA, *myVmNva*. Quando o Azure enviar o tráfego de rede para *myVmNva*, se o tráfego for destinado a um endereço IP diferente, o encaminhamento de IP enviará o tráfego para a localização correta.

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar a VM. Pesquise por **Máquinas virtuais** e selecione essa opção.

1. Escolha o nome da VM (**myVmNva**).

1. Na barra de menus de sua máquina virtual NVA, selecione **Rede**.

1. Selecione **myvmnva123**. Esse é o adaptador de rede que o Azure criou para sua VM. O Azure adiciona números para garantir um nome exclusivo.

    ![Networking, VM (máquina virtual) de NVA (solução de virtualização de rede), portal do Azure](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. Na barra de menus do adaptador de rede, selecione **Configurações de IP**.

1. Na página **Configurações de IP**, defina **Encaminhamento de IP** para **Habilitado** e selecione **Salvar**.

    ![Habilitar encaminhamento de IP, configurações de IP, adaptador de rede, VM (máquina virtual de rede) da NVA (solução de virtualização de rede), portal do Azure](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Crie máquinas virtuais públicas e privadas

Crie uma VM pública e uma VM privada na rede virtual. Posteriormente, você vai usá-los para ver que o Azure roteia o tráfego da sub-rede *Pública* rede para a sub-rede *Privada* por meio da NVA.

Para criar a VM pública e a VM privada, siga as etapas de [Criar uma NVA](#create-an-nva) realizadas anteriormente. Você não precisa aguardar a conclusão da implantação nem acessar o recurso da VM. Você usará a maioria das mesmas configurações, exceto pelo descrito abaixo.

Antes de selecionar **Criar** para criar a VM pública ou privada, vá para as duas subseções a seguir ([VM pública](#public-vm) e [VM privada](#private-vm)), que mostram os valores que precisam ser diferentes. Você pode continuar para a próxima seção ([Rotear o tráfego por meio de uma NVA](#route-traffic-through-an-nva)) depois que o Azure concluir a implantação de ambas as VMs.

### <a name="public-vm"></a>VM pública

| Tab | Configuração | Valor |
| --- | ------- | ----- |
| Noções básicas | Resource group | **myResourceGroup** |
| | Nome da máquina virtual | *myVmPublic* |
| | Porta de entrada públicas | **Permitir portas selecionadas** |
| | Selecione as portas de entrada | **RDP** |
| Rede | Rede virtual | **myVirtualNetwork** |
| | Sub-rede | **Público (10.0.0.0/24)** |
| | Endereço IP público | O padrão |
| Gerenciamento | Conta de armazenamento de diagnóstico | **mynvastorageaccount** |

### <a name="private-vm"></a>VM privada

| Tab | Configuração | Valor |
| --- | ------- | ----- |
| Noções básicas | Resource group | **myResourceGroup** |
| | Nome da máquina virtual | *myVmPrivate* |
| | Porta de entrada públicas | **Permitir portas selecionadas** |
| | Selecione as portas de entrada | **RDP** |
| Rede | Rede virtual | **myVirtualNetwork** |
| | Sub-rede | **Privado (10.0.1.0/24)** |
| | Endereço IP público | O padrão |
| Gerenciamento | Conta de armazenamento de diagnóstico | **mynvastorageaccount** |

## <a name="route-traffic-through-an-nva"></a>Rotear o tráfego por meio de uma NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Entrar em myVmPrivate pela área de trabalho remota

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar a VM privada. Pesquise por **Máquinas virtuais** e selecione essa opção.

1. Escolha o nome da VM privada (**myVmPrivate**).

1. Na barra de menus da VM, selecione **Conectar** para criar uma conexão de área de trabalho remota para a VM privada.

1. Na página **Conectar com o RDP**, selecione **Baixar Arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

1. Abra o arquivo *.rdp* baixado. Se solicitado, selecione **Conectar**. Selecione **Mais opções** > **Usar uma conta diferente** e, em seguida, insira o nome de usuário e a senha que você especificou ao criar a VM privada.

1. Selecione **OK**.

1. Se você receber um aviso de certificado durante o processo de entrada, selecione **Sim** para se conectar à VM.

### <a name="enable-icmp-through-the-windows-firewall"></a>Habilite o ICMP usando o firewall do Windows

Em uma etapa posterior, você usará a ferramenta de rastreamento de rota para testar o roteamento. A rota de rastreamento usa o protocolo ICMP, que o Firewall do Windows nega por padrão. Habilite o ICMP por meio do Firewall do Windows.

1. Na Área de Trabalho Remota do *myVmPrivate*, abra o PowerShell.

1. Insira este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Você estará usando o rastreamento de rotas para testar o roteamento neste tutorial. Para ambientes de produção, não é recomendável permitir ICMP por meio do Firewall do Windows.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Ativar o encaminhamento de IP no myVmNva

Você [ativou o encaminhamento de IP](#turn-on-ip-forwarding) para o adaptador de rede da VM usando o Azure. O sistema operacional da VM também deve encaminhar o tráfego de rede. Ativar o encaminhamento de IP para o sistema operacional da VM *myVmNva* com estes comandos.

1. Em um prompt de comando na VM *myVmPrivate*, abra uma área de trabalho remota para a VM *myVmNva*:

    ```cmd
    mstsc /v:myvmnva
    ```

1. Do PowerShell na VM *myVmNva*, insira este comando para ativar o encaminhamento de IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Reinicie a VM *myVmNva*: Na barra de tarefas, selecione **Iniciar** > **Liga/Desliga**, **Outro (Planejado)**  > **Continuar**.

    Isso também desconecta a sessão de área de trabalho remota.

1. Após a VM *myVmNva* reiniciar, crie uma sessão de área de trabalho remota para a VM *myVmPublic*. Enquanto ainda conectado à VM *myVmPrivate*, abra um prompt de comando e execute este comando:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Na área de trabalho remota do *myVmPublic*, abra o PowerShell.

1. Habilite o ICMP por meio do Firewall do Windows digitando este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testar o roteamento de tráfego de rede

Primeiro, vamos testar o roteamento de tráfego de rede da VM *myVmPublic* para a VM *myVmPrivate*.

1. Do PowerShell na VM *myVmPublic*, digite este comando:

    ```powershell
    tracert myVmPrivate
    ```

    A resposta é semelhante a este exemplo:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    Você pode ver que o primeiro salto é para 10.0.2.4, que é o endereço IP privado da NVA. O segundo salto é para o endereço IP privado da VM *myVmPrivate*: 10.0.1.4. Anteriormente, você adicionou a rota para a tabela de rotas *myRouteTablePublic* e a associo à sub-rede *Pública*. Como resultado, o Azure enviou o tráfego por meio da NVA, e não diretamente para a sub-rede *Privada*.

1. Feche a sessão da área de trabalho remota para a VM *myVmPublic*, que ainda mantém você ainda conectado à VM *myVmPrivate*.

1. Em um prompt de comando na VM *myVmPrivate*, digite este comando:

    ```cmd
    tracert myVmPublic
    ```

    Esse comando testa o roteamento de tráfego de rede da VM *myVmPrivate* para a VM *myVmPublic*. A resposta é semelhante a este exemplo:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Você pode ver que o Azure roteia o tráfego diretamente da VM *myVmPrivate* para a VM *myVmPublic*. Por padrão, o Azure roteia o tráfego diretamente entre sub-redes.

1. Feche a sessão da área de trabalho remota para a VM *myVmPrivate*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o grupo de recursos não for mais necessário, exclua *myResourceGroup* e todos os recursos que ele tem:

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar o grupo de recursos. Procure por **Grupos de recursos** e selecione essa opção.

1. Escolha o nome do seu grupo de recursos (**myResourceGroup**).

1. Selecione **Excluir grupo de recursos**.

1. Na caixa de diálogo de confirmação, insira *myResourceGroup* em **DIGITE O NOME DO GRUPO DE RECURSOS** e, depois, selecione **Excluir**. O Azure exclui O *myResourceGroup* e todos os recursos vinculados a esse grupo de recursos, incluindo suas tabelas de rotas, contas de armazenamento, redes virtuais, VMs, adaptadores de rede e endereços IP públicos.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma tabela de rotas e associou-a a uma sub-rede. Você criou uma NVA simples que roteou o tráfego de uma sub-rede pública para uma sub-rede privada. Agora, usando o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking), você pode implantar diferentes NVAs pré-configuradas, que fornecem muitas funções de rede úteis. Para saber mais sobre roteamento, consulte [Visão geral de roteamento](virtual-networks-udr-overview.md) e [Gerenciar uma tabela de rotas](manage-route-table.md).

Embora seja possível implantar muitos recursos do Azure dentro de uma rede virtual, o Azure não pode implantar recursos para alguns serviços PaaS em uma rede virtual. É possível restringir acesso aos recursos de alguns serviços PaaS do Azure, embora a restrição precise ser somente para o tráfego de uma sub-rede de rede virtual. Para aprender como restringir o acesso de rede aos recursos PaaS do Azure, confira o próximo tutorial.

> [!div class="nextstepaction"]
> [Restringir o acesso à rede para recursos PaaS](tutorial-restrict-network-access-to-resources.md)

> [!NOTE] 
> Os serviços do Azure custam dinheiro. O Gerenciamento de Custos do Azure ajuda você a definir orçamentos e configurar alertas para manter os gastos sob controle. Analise, gerencie e otimize seus custos do Azure com o Gerenciamento de Custos. Para saber mais, confira o [guia de início rápido sobre como analisar seus custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).