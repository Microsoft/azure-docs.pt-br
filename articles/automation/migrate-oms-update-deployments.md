---
title: Migrar as implantações de atualização do OMS para Azure
description: Este artigo descreve como migrar as implantações de atualização do OMS para Azure
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 07/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2cc05dc0970211b066d9e431cc7aba06cbd126eb
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478296"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Migrar as implantações de atualização do OMS para Azure

O portal do OMS (Operations Management Suite) está sendo [preterido](../azure-monitor/platform/oms-portal-transition.md). Todas as funcionalidades que estavam disponíveis no portal do OMS para Gerenciamento de Atualizações estão disponíveis no portal do Azure. Este artigo fornece as informações necessárias para migrar para o portal do Azure.

## <a name="key-information"></a>Principais informações

* As implantações existentes continuarão funcionando. Após recriar a implantação no Azure, será possível excluir a implantação antiga do OMS.
* Todos os recursos existentes que você tinha no OMS estão disponíveis no Azure, para saber mais sobre o Gerenciamento de Atualizações, consulte [Visão geral do Gerenciamento de Atualizações](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Acesse o portal do Azure

No workspace do OMS, clique em **Abrir no Azure**. Isso navegará para o espaço de trabalho do Log Analytics que o OMS usou.

![Abrir no Azure - portal do OMS](media/migrate-oms-update-deployments/link-to-azure-portal.png)

No portal do Azure, clique em **Conta de Automação**

![Logs do Azure Monitor](media/migrate-oms-update-deployments/log-analytics.png)

Na Conta de Automação, clique em **	Gerenciamento de Atualizações** para abrir o Gerenciamento de Atualizações.

![Gerenciamento de atualizações](media/migrate-oms-update-deployments/azure-automation.png)

Futuramente, será possível acessar diretamente o portal do Azure, em **Todos os serviços**, selecionar **Contas de Automação**  em **Ferramentas de Gerenciamento**, selecionar a Conta de Automação apropriada e clicar em **Gerenciamento de Atualizações**.

## <a name="recreate-existing-deployments"></a>Recriar implantações existentes

Todas as implantações de atualização criadas no portal do OMS têm uma [pesquisa salva](../azure-monitor/platform/computer-groups.md) também conhecida como grupo de computadores, com o mesmo nome da implantação de atualização existente. A pesquisa salva contém a lista de computadores que foram agendados na implantação de atualizações.

![Gerenciamento de atualizações](media/migrate-oms-update-deployments/oms-deployment.png)

Para usar essa pesquisa salva existente, siga estas etapas:

Para criar uma nova implantação de atualizações, vá para o portal do Azure, selecione a Conta de Automação usada e clique em **Gerenciamento de Atualizações**. Clique em **Agendar implantação de atualizações**.

![Agendar implantação de atualizações](media/migrate-oms-update-deployments/schedule-update-deployment.png)

O painel **Nova implantação de atualizações** é aberto. Insira valores para as propriedades descritas na tabela a seguir e clique em **Criar**:

Para que os Computadores atualizem, selecione a pesquisa salva usada pela implantação do OMS existente.

| Propriedade | Descrição |
| --- | --- |
|Nome |Nome exclusivo para identificar a Implantação de Atualizações. |
|Sistema operacional| Selecione **Linux** ou **Windows**.|
|Computadores para atualizar |Selecione uma pesquisa salva, um grupo importado ou selecione a máquina na lista suspensa e selecione máquinas individuais. Se você escolher **Machines**, a prontidão da máquina é mostrada na coluna **UPDATE AGENT READINESS**.</br> Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos logs do Azure Monitor, veja [Grupos de computadores nos logs do Azure Monitor](../azure-monitor/platform/computer-groups.md) |
|Classificações de origem|Selecione todas as classificações de atualização que você precisa. CentOS não oferece suporte para isso fora da caixa.|
|Atualizações para excluir|Insira as atualizações a serem excluídas. Para Windows, insira o artigo KB sem o prefixo **KB**. Para o Linux, insira o nome do pacote ou use um caractere curinga.  |
|Configurações de agendamento|Selecione o tempo para iniciar e selecione **Uma Vez** ou **Recorrente** para a recorrência. | 
| Janela de manutenção |Número de minutos definido para atualizações. O valor não pode ser inferior a 30 minutos ou superior a 6 horas. |
| Reinicialize o controle| Determina como as reinicializações devem ser tratadas.</br>As opções disponíveis são:</br>Reinicialização, se necessário (Padrão)</br>Sempre reinicializar</br>Nunca reinicializar</br>Somente reinicialização - não instalará as atualizações|

Clique em **Implantações de atualização agendadas** para exibir o status da implantação da atualização criada recentemente.

![nova implantação de atualizações](media/migrate-oms-update-deployments/new-update-deployment.png)

Conforme mencionado anteriormente, quando as novas implantações forem configuradas por meio do portal do Azure, as implantações existentes poderão ser removidas pelo portal do OMS.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Gerenciamento de Atualizações no Azure, consulte [Gerenciamento de Atualizações](automation-update-management.md)
