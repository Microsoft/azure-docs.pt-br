---
title: Tutorial – monitorar a comunicação de rede usando o portal do Azure
description: Neste tutorial, saiba como monitorar a comunicação de rede entre duas máquinas virtuais com a funcionalidade de monitor de conexão do Observador de Rede do Azure.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 4f9f3e2af9abc054d3b1d5ce2bd1f1b27f7bf958
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066034"
---
# <a name="tutorial-monitor-network-communication-between-two-virtual-machines-using-the-azure-portal"></a>Tutorial: Monitorar a comunicação de rede entre as duas máquinas virtuais usando o portal do Azure

> [!NOTE]
> Este tutorial aborda o Monitor da Conexão (clássico). Teste o novo e aprimorado [Monitor da Conexão](connection-monitor-overview.md) para experimentar um monitoramento avançado da conectividade

> [!IMPORTANT]
> A partir de 1º de julho de 2021, você não poderá adicionar monitores de conexão no Monitor da Conexão (clássico), mas poderá continuar usando os monitores de conexão existentes criados antes de 1º de julho de 2021. Para minimizar a interrupção do serviço para suas cargas de trabalho atuais, [migre do Monitor da Conexão (clássico) para o novo Monitor da Conexão](migrate-to-connection-monitor-from-connection-monitor-classic.md) no Observador de Rede do Azure antes de 29 de fevereiro de 2024.

Comunicação bem-sucedida entre uma máquina virtual (VM) e um ponto de extremidade como outra VM, pode ser importante para sua organização. Às vezes, as alterações de configuração são introduzidas o que pode interromper a comunicação. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar duas VMs
> * Monitorar a comunicação entre as máquinas virtuais com o recurso de monitor de conexão do Observador de Rede
> * Gerar alertas em métrica do Monitor de Conexão
> * Diagnosticar um problema de comunicação entre as duas VMs e saiba como você pode resolvê-lo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.



## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-vms"></a>Criar VMs

Criar duas VMs.

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. Selecione **+ Criar um recurso** localizado no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, selecione um sistema operacional. Neste tutorial, **Datacenter do Windows Server 2016** é usado.
3. Insira, ou selecione, as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **OK**:

    |Configuração|Valor|
    |---|---|
    |Nome|myVm1|
    |Nome de usuário| Insira um nome de usuário de sua escolha.|
    |Senha| Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subscription| Selecione sua assinatura.|
    |Resource group| Selecione **Criar novo** e insira **myResourceGroup**.|
    |Location| Selecione **Leste dos EUA**|

4. Selecione um tamanho para a VM e selecione **Selecionar**.
5. Em **Configurações**, selecione **Extensões**. Selecione **Adicionar extensão** e selecione **Agente Observador de Rede para Windows**, conforme mostrado na figura a seguir:

    ![Extensão do Agente do Observador de Rede](./media/connection-monitor/nw-agent-extension.png)

6. Em **Agente Observador de Rede para Windows**, selecione **Criar**, em **Instalar extensão** selecione **Ok** e, em seguida, em **Extensões**, selecione **Ok**.
7. Aceite os padrões para as **Configurações** restantes e clique em **OK**
8. Em **Criar** do **Resumo**, selecione **Criar** para iniciar a implantação da VM.

### <a name="create-the-second-vm"></a>Criar a segunda VM

Conclua as etapas em [Criar a primeira VM](#create-the-first-vm) novamente, com as seguintes alterações:

|Etapa|Setting|Valor|
|---|---|---|
| 1 | Selecionar uma versão do **Ubuntu Server** |                                                                         |
| 3 | Nome                                  | myVm2                                                                   |
| 3 | Tipo de autenticação                   | Cole sua chave pública SSH ou selecione **Senha** e digite uma senha. |
| 3 | Resource group                        | Selecione **Usar existente** e, em seguida, **myResourceGroup**.                 |
| 6 | Extensões do                            | **Agente do Observador de Rede para Linux**                                             |

A VM demora alguns minutos para implantar. Aguarde até que a VM conclua a implantação antes de continuar com as etapas restantes.

## <a name="create-a-connection-monitor"></a>Criar um monitor de conexão

Criar uma comunicação de monitor para monitor de conexão usando a porta TCP 22 de *myVm1* para *myVm2*.

1. No lado esquerdo do portal, selecione **Todos os serviços**.
2. Comece digitando *observador de rede* na caixa **Filtrar**. Quando os resultados da pesquisa exibirem **Observador de Rede**, selecione essa opção.
3. Em **MONITORAMENTO**, selecione **Monitor de conexão**.
4. Selecione **+ Adicionar**.
5. Digite ou selecione as informações para a conexão que você deseja monitorar e selecione **Adicionar**. No exemplo mostrado na figura a seguir, a conexão monitorada é da VM *myVm1* e para a VM *myVm2* pela porta 22:

    | Setting                  | Valor               |
    | ---------                | ---------           |
    | Nome                     | myVm1-myVm2(22)     |
    | Fonte                   |                     |
    | Máquina virtual          | myVm1               |
    | Destino              |                     |
    | Selecione uma máquina virtual |                     |
    | Máquina virtual          | myVm2               |
    | Porta                     | 22                  |

    ![Adicionar monitor de conexão](./media/connection-monitor/add-connection-monitor.png)

## <a name="view-a-connection-monitor"></a>Visualizar um monitor de conexão

1. Conclua as etapas 1 a 3 no [Criar um monitor de conexão](#create-a-connection-monitor) para exibir o monitoramento de conexão. Você vê uma lista de monitores de conexão existentes, conforme mostrado na figura a seguir:

    ![Monitores de conexão](./media/connection-monitor/connection-monitors.png)

2. Selecione o monitor com o nome **myVm1-myVm2(22)**, conforme mostrado na figura anterior, para ver os detalhes para o monitor, conforme mostrado na figura a seguir:

    ![Detalhes do monitor](./media/connection-monitor/vm-monitor.png)

    Observe as seguintes informações:

    | Item                     | Valor                      | Detalhes                                                     |
    | ---------                | ---------                  |--------                                                     |
    | Status                   | Acessível                  | Permite que você saiba se o ponto de extremidade é acessível ou não.|
    | MÉDIA. VIAGEM          | Permite que você saiba que o tempo de ida e volta para fazer a conexão, em milissegundos. Monitor de Conexão testa a conexão a cada 60 segundos, para que você possa monitorar a latência ao longo do tempo.                                         |
    | Hops                     | Monitor de Conexão permite que você saiba os saltos entre dois pontos de extremidade. Neste exemplo, a conexão é entre duas VMs na mesma rede virtual, para que haja um único salto, para o 10.0.0.5 endereço IP. Se qualquer sistema existente ou rotas personalizadas rotear o tráfego entre as máquinas virtuais por meio de um gateway de VPN ou de rede virtuais appliance, por exemplo, saltos adicionais são listadas.                                                                                                                         |
    | STATUS                   | As marcas de seleção verdes para cada ponto de extremidade permitem que você sabe que cada ponto de extremidade está íntegro.    ||

## <a name="generate-alerts"></a>Gerar alertas

Alertas são criados por regras de alerta no Azure Monitor e podem executar automaticamente consultas salvas ou pesquisas de logs personalizadas em intervalos regulares. Um alerta gerado pode executar automaticamente uma ou mais ações, como notificar alguém ou iniciar outro processo. Ao definir uma regra de alerta, o recurso de destino determina a lista de métrica disponível que você pode usar para gerar alertas.

1. No portal do Azure, selecione o serviço **Monitor** e selecione **Alertas** > **Nova regra de alerta**.
2. Clique em **Selecionar destino** e selecione os recursos aos quais você deseja direcionar. Selecione a **Assinatura** e defina o **Tipo de recurso** para filtrar pelo Monitor de Conexão que você deseja usar.

    ![tela de alerta com o destino selecionado](./media/connection-monitor/set-alert-rule.png)
1. Depois de selecionar um recurso para direcionamento, selecione **Adicionar critérios**. O Observador de Rede tem [métrica que pode servir de base para alertas](../azure-monitor/alerts/alerts-metric-near-real-time.md#metrics-and-dimensions-supported). Defina **Sinais disponíveis** para as métricas ProbesFailedPercent e AverageRoundtripMs:

    ![página de alerta com sinais selecionados](./media/connection-monitor/set-alert-signals.png)
1. Preencha os detalhes do alerta, como nome da regra de alerta, descrição e gravidade. Você também pode adicionar um grupo de ação ao alerta para automatizar e personalizar a resposta de alerta.

## <a name="view-a-problem"></a>Ver um problema

Por padrão, o Azure permite comunicação por todas as portas entre VMs na mesma rede virtual. Eventualmente, você ou alguém na sua organização, talvez substitua as regras padrão do Azure, inadvertidamente, causando uma falha de comunicação. Conclua as seguintes etapas para criar um problema de comunicação e, em seguida, exibir o monitor de conexão novamente:

1. Na caixa Pesquisar na parte superior do portal insira *myResourceGroup*. Selecione **myResourceGroup** quando grupo de recurso aparecer nos resultados da pesquisa.
2. Selecioneo grupo de segurança de rede **myVm2-nsg**.
3. Selecione **regras de segurança inbound** e, em seguida, selecione **Adicionar**, como mostrado na imagem a seguir:

    ![Regras de segurança de entrada](./media/connection-monitor/inbound-security-rules.png)

4. A regra padrão que permite a comunicação entre todas as VMs em uma rede virtual é a regra nomeada **AllowVnetInBound**. Criar uma regra com uma prioridade mais alta (número menor) que o **AllowVnetInBound** regra que nega a comunicação de entrada pela porta 22. Insira ou selecione as informações a seguir, aceite os padrões restantes e selecione **Adicionar**:

    | Setting                 | Valor          |
    | ---                     | ---            |
    | Intervalos de portas de destino | 22             |
    | Ação                  | Negar           |
    | Prioridade                | 100            |
    | Nome                    | DenySshInbound |

5. Desde que o monitor de conexão testes em intervalos de 60 segundos, aguarde alguns minutos e, em seguida, no lado esquerdo do portal, selecione **Observador de Rede**, em seguida, **monitor de Conexão** e, em seguida, selecione o **myVm1-myVm2(22)** monitorar novamente. Os resultados são diferentes agora, conforme mostrado na figura a seguir:

    ![Falha nos detalhes do monitor](./media/connection-monitor/vm-monitor-fault.png)

    Você pode ver que há um ícone de ponto de exclamação vermelho na coluna status para o **myvm2529** interface de rede.

6. Para saber por que o status foi alterado, selecione 10.0.0.5, na figura anterior. Monitor de Conexão informa que o motivo da falha de comunicação é: *Tráfego bloqueado devido a seguinte regra de grupo de segurança de rede: UserRule_DenySshInbound*.

    Se você não souber que alguém tinha implementado a regra de segurança que você criou na etapa 4, você deve saber de monitor de conexão que a regra está causando o problema de comunicação. Você pode alterar, substituir ou remover a regra, para restaurar a comunicação entre as máquinas virtuais.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal. Quando aparecer **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como monitorar uma conexão entre duas máquinas virtuais. Você aprendeu que uma regra de grupo de segurança de rede impediu a comunicação com uma máquina virtual. Para saber mais sobre todas as respostas diferentes pode retornar o monitor de conexão, consulte [tipos de resposta](network-watcher-connectivity-overview.md#response). Você também pode monitorar uma conexão entre uma máquina virtual, um nome de domínio totalmente qualificado, um identificador de recurso uniforme ou um endereço IP.

Em algum momento, você pode achar que recursos em uma rede virtual não conseguem se comunicar com os recursos em outras redes conectadas por um gateway de rede virtual do Azure. Avance para o próximo tutorial para aprender a diagnosticar um problema com um gateway de rede virtual.

> [!div class="nextstepaction"]
> [Diagnosticar um problema de comunicação entre redes](diagnose-communication-problem-between-networks.md)
