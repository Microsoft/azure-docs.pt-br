---
title: Criar uma rede virtual – início rápido – portal do Azure
titleSuffix: Azure Virtual Network
description: 'Início Rápido: Crie uma rede virtual no portal do Azure. Essas redes permitem que os recursos do Azure, como VMs, comuniquem-se com segurança umas com as outras e com a Internet.'
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: cc3ba3a0519400368e0cbfec7abe2d9bd1731b34
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217627"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Início Rápido: Criar uma Rede Virtual usando o portal do Azure

Neste início rápido, você aprenderá a criar uma rede virtual usando o portal do Azure. Você implantará duas VMs (máquinas virtuais). Em seguida, você se comunicará com segurança entre as VMs e se conectará com VMs da Internet. Uma rede virtual é o bloco de construção fundamental de sua rede privada no Azure. Ela permite que os recursos do Azure, como VMs, comuniquem-se de maneira segura uns com os outros e usando a Internet.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No menu do portal do Azure, selecione **Criar um recurso**. No Azure Marketplace, selecione **rede** > **rede virtual**.

1. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Subscription | Selecione sua assinatura.|
    | Resource group | Selecione **Criar novo** e insira *myResourceGroup*, depois selecione **OK**. |
    | Nome | Insira *myVirtualNetwork*. |
    | Location | Selecione **Leste dos EUA**.|

1. Selecione **Avançar: endereços IP** e, para **Espaço de endereços IPv4**, insira *10.1.0.0/16*.

1. Selecione **Adicionar sub-rede**; em seguida, insira *myVirtualSubnet* para **Nome da sub-rede** e *10.1.0.0/24* para **Intervalo de endereços de sub-rede**.

1. Selecione **Adicionar** e, em seguida, **Examinar + criar**. Deixe o restante com os valores padrão e selecione **Criar**.

1. Em **Criar rede virtual**, selecione **Criar**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual:

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. No menu do portal do Azure, selecione **Criar um recurso**.

1. No Azure Marketplace, selecione **computação** > **Windows Server 2019 datacenter**. Selecione **Criar**.

1. Em **Criar uma máquina virtual – Noções básicas**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Selecione **myResourceGroup**. Você criou esse grupo de recursos na seção anterior. |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira *myVm1*. |
    | Região | Selecione **Leste dos EUA**. |
    | Opções de disponibilidade | O padrão é **Nenhuma redundância infraestrutura necessária**. |
    | Imagem | O padrão é **Windows Server 2019 Datacenter**. |
    | Tamanho | O padrão é **Standard DS1 v2**. |
    | **Conta de administrador** |  |
    | Nome de Usuário | Insira um nome de usuário de sua escolha. |
    | Senha | Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Senha | Digite a senha novamente. |
    | **Regras de porta de entrada** |  |
    | Porta de entrada públicas | Selecione **Permitir portas selecionadas**. |
    | Selecione as portas de entrada | Insira *HTTP (80)* e *RDP (3389)* . |
    | **Economizar dinheiro** |  |
    | Já tem uma licença do Windows? | O padrão é **Não**. |

1. Selecione **Avançar: Discos**.

1. Em **Criar uma máquina virtual – Discos**, mantenha os padrões e selecione **Avançar: Rede**.

1. Em **Criar uma máquina virtual – Rede**, selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Rede virtual | O padrão é **myVirtualNetwork**. |
    | Sub-rede | O padrão é **myVirtualSubnet (10.1.0.0/24)** . |
    | IP público | O padrão é **(Novo) myVm-IP**. |
    | Grupo de segurança de rede da NIC | O padrão é **Básico**. |
    | Porta de entrada públicas | O padrão é **Permitir portas selecionadas**. |
    | Selecione as portas de entrada | O padrão é **HTTP** e **RDP**.

1. Selecione **Avançar: Gerenciamento**.

1. Em **Criar uma máquina virtual – Gerenciamento**, para **Conta de armazenamento de diagnóstico**, selecione **Criar novo**.

1. Em **Criar conta de armazenamento**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira *myvmstorageaccount*. Se esse nome já estiver sendo usado, crie um nome exclusivo.|
    | Tipo de conta | O padrão é **Armazenamento (uso geral v1)** . |
    | Desempenho | O padrão é **Standard**. |
    | Replicação | O padrão é **LRS (Armazenamento com Redundância Local)** . |

1. Selecione **OK** e, em seguida, **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração.

1. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

### <a name="create-the-second-vm"></a>Criar a segunda VM

Repita o procedimento na seção anterior para criar outra máquina virtual.

> [!IMPORTANT]
> Para o **Nome da máquina virtual**, insira *myVm2*.
>
> Para **Conta de armazenamento de diagnóstico**, selecione **myvmstorageaccount** em vez de criar um.

## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Depois de criar *myVm1*, conecte-se à Internet.

1. No portal do Azure, pesquise e selecione *myVm1*.

1. Selecione **Conectar** e, em seguida, **RDP**.

    ![Conectar-se a uma máquina virtual](./media/quick-create-portal/connect-to-virtual-machine.png)

    A página **Conectar** é aberta.

1. Selecione **Baixar Arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

1. Abra o arquivo RDP. Se solicitado, selecione **Conectar**.

1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

    > [!NOTE]
    > Talvez seja necessário selecionar **Mais escolhas** > **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.

1. Selecione **OK**.

1. Você poderá receber um aviso de certificado quando entrar. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.

## <a name="communicate-between-vms"></a>Comunicação entre VMs

1. Na Área de Trabalho Remota do *myVm1*, abra o PowerShell.

1. Digite `ping myVm2`.

    Você receberá uma mensagem semelhante a esta saída:

    ```output
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    O `ping` falha, pois `ping` usa o ICMP (Internet Control Message Protocol). Por padrão, o ICMP não é permitido pelo Firewall do Windows.

1. Para permitir que *myVm2* execute o ping *myVm1* em uma etapa posterior, digite este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Esse comando permite a entrada ICMP pelo firewall do Windows:

1. Feche a conexão da área de trabalho remota para *myVm1*.

1. Complete as etapas em [Conecte uma VM a partir da Internet](#connect-to-a-vm-from-the-internet) novamente, mas conecte para *myVm2*.

1. Em um prompt de comando, insira `ping myvm1`.

    Você obterá algo parecido com esta mensagem:

    ```output
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    Você recebe respostas de *myVm1*, porque permitiu ICMP através do firewall do Windows na VM *myVm1* na etapa 3.

1. Feche a conexão da área de trabalho remota para *myVm2*.

## <a name="clean-up-resources"></a>Limpar os recursos

Neste início rápido, você criou uma rede virtual padrão e duas VMs. Você se conectou a uma VM pela Internet e executou uma comunicação entre duas VMs de maneira segura.

Quando terminar de usar a rede virtual e as VMs, exclua o grupo de recursos e todos os recursos que ele contém:

1. Pesquise e selecione *myResourceGroup*.

1. Selecione **Excluir grupo de recursos**.

1. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre configurações de rede virtual, confira [Criar, alterar ou excluir uma rede virtual](manage-virtual-network.md).

Por padrão, o Azure permite comunicação segura entre VMs. O Azure somente permite conexões de área de trabalho remota de entrada para VMs do Windows da Internet. Para saber mais sobre os tipos de comunicações de rede de VMs, confira [Filtrar o tráfego de rede](tutorial-filter-network-traffic.md).

> [!NOTE] 
> Os serviços do Azure custam dinheiro. O Gerenciamento de Custos do Azure ajuda você a definir orçamentos e configurar alertas para manter os gastos sob controle. Analise, gerencie e otimize seus custos do Azure com o Gerenciamento de Custos. Para saber mais, confira o [guia de início rápido sobre como analisar seus custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).