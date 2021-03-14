---
title: Migrar implantações de atualização de logs do Azure Monitor para o portal do Azure
description: Este artigo explica como migrar implantações de atualização de logs do Azure Monitor para o portal do Azure.
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 2e94191e80d39e28d7ff0ffc9aa22b522fda68c1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576022"
---
# <a name="migrate-azure-monitor-logs-update-deployments-to-azure-portal"></a>Migrar implantações de atualização de logs do Azure Monitor para o portal do Azure

O portal do OMS (Operations Management Suite) está sendo [preterido](../azure-monitor/logs/oms-portal-transition.md). Todas as funcionalidades que estavam disponíveis no portal do OMS para Gerenciamento de Atualizações estão disponíveis no portal do Azure, por meio dos logs do Azure Monitor. Este artigo fornece as informações necessárias para migrar para o portal do Azure.

## <a name="key-information"></a>Principais informações

* As implantações existentes continuarão funcionando. Após recriar a implantação no Azure, será possível excluir a implantação antiga.
* Todos os recursos existentes que estavam disponíveis no OMS também estão no Azure. Para saber mais sobre o Gerenciamento de Atualizações, consulte [Visão geral do Gerenciamento de Atualizações](./update-management/overview.md).

## <a name="access-the-azure-portal"></a>Acesse o portal do Azure

1. No workspace, clique em **Abrir no Azure**. 

    ![Abrir no Azure - Análise de logs](media/migrate-oms-update-deployments/link-to-azure-portal.png)

2. No portal do Azure, clique em **Conta de Automação**

    ![Logs do Azure Monitor](media/migrate-oms-update-deployments/log-analytics.png)

3. Na conta da Automação, clique em **Gerenciamento de Atualizações**.

    :::image type="content" source="media/migrate-oms-update-deployments/azure-automation.png" alt-text="Captura de tela da página de gerenciamento de atualizações.":::

4. No portal do Azure, selecione **Contas da Automação** em **Todos os serviços**. 

5. Em **Ferramentas de Gerenciamento**, selecione a conta da Automação apropriada e clique em **Gerenciamento de Atualizações**.

## <a name="recreate-existing-deployments"></a>Recriar implantações existentes

Todas as implantações de atualização criadas no portal do OMS têm uma [pesquisa salva](../azure-monitor/logs/computer-groups.md) também conhecida como grupo de computadores, com o mesmo nome da implantação de atualização existente. A pesquisa salva contém a lista de computadores que foram agendados na implantação de atualizações.

:::image type="content" source="media/migrate-oms-update-deployments/oms-deployment.png" alt-text="Captura de tela da página atualizar implantações com os campos nome e servidores realçados.":::

Para usar essa pesquisa salva existente, siga estas etapas:

1. Para criar uma nova implantação de atualizações, vá para o portal do Azure, selecione a conta da Automação usada e clique em **Gerenciamento de Atualizações**. Clique em **Agendar implantação de atualizações**.

    ![Agendar implantação de atualizações](media/migrate-oms-update-deployments/schedule-update-deployment.png)

2. O painel “Nova implantação de atualizações” é aberto. Insira valores para as propriedades descritas na tabela a seguir e clique em **Criar**:

3. Para que os **computadores atualizem**, selecione a pesquisa salva usada pela implantação do OMS.

    | Propriedade | Descrição |
    | --- | --- |
    |Nome |Nome exclusivo para identificar a Implantação de Atualizações. |
    |Sistema operacional| Selecione **Linux** ou **Windows**.|
    |Computadores para atualizar |Selecione uma pesquisa salva, um grupo importado ou escolha o computador na lista suspensa e selecione computadores individuais. Se você escolher **Machines**, a prontidão da máquina é mostrada na coluna **UPDATE AGENT READINESS**.</br> Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos logs do Azure Monitor, veja [Grupos de computadores nos logs do Azure Monitor](../azure-monitor/logs/computer-groups.md) |
    |Classificações de origem|Selecione todas as classificações de atualização que você precisa. CentOS não oferece suporte para isso fora da caixa.|
    |Atualizações para excluir|Insira as atualizações a serem excluídas. Para Windows, insira o artigo KB sem o prefixo **KB**. Para o Linux, insira o nome do pacote ou use um caractere curinga.  |
    |Configurações de agendamento|Selecione o tempo para iniciar e selecione **Uma Vez** ou **Recorrente** para a recorrência. | 
    | Janela de manutenção |Número de minutos definido para atualizações. O valor não pode ser inferior a 30 minutos ou superior a 6 horas. |
    | Reinicialize o controle| Determina como as reinicializações devem ser tratadas.</br>As opções disponíveis são:</br>Reinicialização, se necessário (Padrão)</br>Sempre reinicializar</br>Nunca reinicializar</br>Somente reinicialização - não instalará as atualizações|

4. Clique em **Implantações de atualização agendadas** para exibir o status da implantação da atualização criada recentemente.

    ![nova implantação de atualizações](media/migrate-oms-update-deployments/new-update-deployment.png)

5. Como mencionamos anteriormente, quando as novas implantações forem configuradas por meio do portal do Azure, você poderá remover as implantações existentes do portal do Azure.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Gerenciamento de Atualizações na Automação do Azure, consulte [Visão geral do Gerenciamento de Atualizações](./update-management/overview.md).