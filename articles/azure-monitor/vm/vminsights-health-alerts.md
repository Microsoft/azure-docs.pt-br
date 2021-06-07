---
title: Alertas de integridade do convidado de insights da VM (versão prévia)
description: Descreve os alertas criados pela integridade de convidado de informações de VM, incluindo como habilitá-los e configurar notificações.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: 9c4b15fa2a8be32aa7397ae425f28f1deaac6b71
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024601"
---
# <a name="vm-insights-guest-health-alerts-preview"></a>Alertas de integridade do convidado de insights da VM (versão prévia)
A integridade de convidado do insights de VM permite que você exiba a integridade de uma máquina virtual conforme definido por um conjunto de medidas de desempenho que são amostradas em intervalos regulares. Um alerta pode ser criado quando uma máquina virtual ou monitor é alterado para um estado não íntegro. Você pode exibir e gerenciar esses alertas com [aqueles criados por regras de alerta no Azure monitor](../alerts/alerts-overview.md) e optar por ser notificado proativamente quando um novo alerta for criado.

## <a name="configure-alerts"></a>Configurar alertas
Você não pode criar uma regra de alerta explícita para a integridade de convidado do insights de VM enquanto esse recurso estiver em visualização. Por padrão, os alertas serão criados para cada máquina virtual, mas não para cada monitor.  Isso significa que, se um monitor mudar para um estado que não afete o estado atual da máquina virtual, nenhum alerta será criado porque o estado da máquina virtual não foi alterado. 

Você pode Desabilitar alertas para uma máquina virtual específica ou para um monitor específico em uma máquina virtual da configuração **status do alerta** na configuração da máquina virtual na portal do Azure. Consulte [Configurar o monitoramento na integridade do convidado do insights de VM (versão prévia)](vminsights-health-configure.md) para obter detalhes sobre como configurar monitores no portal do Azure. Consulte [Configurar monitoramento na integridade do convidado do insights de VM usando regras de coleta de dados (versão prévia)](vminsights-health-configure-dcr.md) para obter detalhes sobre como configurar monitores em um conjunto de máquinas virtuais.

## <a name="alert-severity"></a>Severidade do alerta
A severidade do alerta criado pela integridade do convidado é mapeada diretamente para a severidade da máquina virtual ou do monitor que dispara o alerta.

| Estado do monitor | Severidade do alerta |
|:---|:---|
| Crítico | Sev1 |
| Aviso  | Sev2 |
| Íntegros  | Sev4 |

## <a name="alert-lifecycle"></a>Ciclo de vida do alerta
Um [alerta do Azure](../alerts/alerts-overview.md) será criado para cada máquina virtual a qualquer momento que for alterado para um estado de **aviso** ou **crítico** . Exiba o alerta de **alertas** no menu **Azure monitor** ou no menu da máquina virtual na portal do Azure.

Se um alerta já estiver no estado **acionado** quando o estado da máquina virtual for alterado, um segundo alerta não será criado, mas a severidade do mesmo alerta será alterada para corresponder ao estado da máquina virtual. Por exemplo, se a máquina virtual mudar para o estado **crítico** quando um alerta de **aviso** já estava no estado **acionado** , a severidade desse alerta será alterada para **Sev1**. Se a máquina virtual for alterada para um estado de **aviso** quando um alerta **Sev1** já estava no estado **acionado** , a severidade desse alerta será alterada para **Sev2**. Se a máquina virtual voltar a um estado **íntegro** , o alerta será resolvido com a gravidade alterada para **Sev4**.

## <a name="viewing-alerts"></a>Exibindo alertas
Exibir alertas criados pela integridade de convidado do insights de VM com outros [alertas no portal do Azure](../alerts/alerts-overview.md#alerts-experience). Você pode selecionar **alertas** no menu **Azure monitor** para exibir alertas de todos os recursos monitorados ou selecionar **alertas** no menu de uma máquina virtual para exibir alertas apenas para essa máquina virtual.

## <a name="alert-properties"></a>Propriedades do Alerta

### <a name="properties-in-the-azure-portal"></a>Propriedades no portal do Azure
As propriedades do alerta ao exibi-lo no portal do Azure são descritas na tabela a seguir.

| Propriedade | Descrição |
|:---|:---|
| Monitorar o estado antes da criação do alerta | Estado do monitor ou da máquina virtual antes que esse alerta seja acionado pela primeira vez. |
| Estado do monitor quando o alerta foi criado | Estado do monitor ou da máquina virtual quando o alerta foi acionado pela primeira vez. Esse é o estado que fez com que o alerta fosse acionado. |
| Para saber mais sobre a transição de estado quando o alerta foi criado | Link para a página de integridade da VM, em que você pode ver a transição de estado exata. Essa transição de estado representa a instância quando o monitor passou pela primeira vez do estado **íntegro** para um estado não íntegro. |

Por exemplo, um monitor vai de **íntegro** para **crítico** no tempo T0 e um novo alerta é disparado com **Sev1**. Em seguida, ele passa de **crítico** para **aviso** no tempo T1 e a severidade do alerta é atualizada para **Sev2**. Ele se torna **íntegro** no momento T2 e o alerta é resolvido.

As propriedades do alerta terão esses valores durante toda a sequência.

- Monitorar o estado antes da criação do alerta: íntegro
- Estado do monitor quando o alerta foi criado: crítico
- Para saber mais sobre a transição de estado quando o alerta foi criado: o link de navegação para a transição de estado ocorreu no T0 de tempo.


### <a name="properties-in-notifications"></a>Propriedades em notificações
As propriedades do alerta incluído nas notificações são descritas na tabela a seguir.

| Propriedade | Descrição |
|:---|:---|
| Estado anterior do monitor | Estado do monitor ou da máquina virtual antes de alterar o estado. Se a atualização de severidade do alerta disparar essa notificação, essa propriedade representará o estado que estava logo antes da atualização de gravidade. |
| Estado atual do monitor | Estado do monitor ou da máquina virtual quando ele alterou o estado. Se a atualização de severidade do alerta disparar essa notificação, essa propriedade representará o novo estado. |
| Para saber mais sobre essa transição de estado | Link para a página de integridade da VM, em que você pode ver a transição de estado exata. Essa transição de estado representa a instância quando o estado monitor alterado que disparou essa notificação. |

Usando o exemplo acima, as notificações teriam as seguintes propriedades em cada vez.

Notificação recebida no T0 de tempo
- Estado anterior do monitor: íntegro
- Estado atual do monitor: crítico
- Para saber mais sobre esta transição de Estado: o link de navegação para a transição de estado ocorreu no T0 de tempo.

Notificação recebida no tempo T1
- Estado anterior do monitor: crítico
- Estado atual do monitor: aviso
- Para saber mais sobre esta transição de Estado: o link de navegação para a transição de estado ocorreu no tempo T1.

Notificação recebida no momento T2
- Estado anterior do monitor: aviso
- Estado atual do monitor: íntegro
- Para saber mais sobre esta transição de Estado: o link de navegação para a transição de estado ocorreu no tempo T2.

## <a name="configure-notifications"></a>Configurar notificações
Para ser notificado proativamente de um alerta disparado pela integridade do convidado, crie um [grupo de ações](../alerts/action-groups.md) para definir as diferentes ações a serem executadas, como o envio de uma mensagem SMS ou a inicialização de um aplicativo lógico. Em seguida, crie uma [regra de ação](../alerts/alerts-action-rules.md) que especifique o escopo de monitores e máquinas virtuais e use esse grupo de ação.

No menu **monitorar** na portal do Azure, selecione **alertas**.  Selecione **Gerenciar ações** e **regras de ação (versão prévia)**. 

![Nova regra de ação](media/vminsights-health-alerts/action-rule-new.png)

Clique em **nova regra de ação** para criar uma nova regra. Clique em **selecionar** ao lado de escopo e selecione uma assinatura, grupo de recursos ou uma ou mais máquinas virtuais específicas. A notificação só será acionada para máquinas virtuais que se enquadram no escopo.

![Escopo da regra de ação](media/vminsights-health-alerts/action-rule-scope.png)

Clique em **Adicionar** ao lado de **Filtrar**. Criar um filtro em que o **serviço monitor é igual a VM insights-integridade**. Adicione outros filtros para especificar os alertas específicos que devem disparar a notificação. Por exemplo, você pode usar a **severidade** para fazer a correspondência de alertas de todos os monitores que correspondem a uma severidade específica.

![Filtro de regra de ação](media/vminsights-health-alerts/action-rule-filter.png)

Em **definir nesse escopo**, selecione **grupo de ações** e, em seguida, selecione o grupo de ações a ser associado ao monitor. Dê um nome à regra e selecione o grupo de recursos no qual ela deve ser salva. Clique em **criar** para criar a regra.

![Regra de ação](media/vminsights-health-alerts/action-rule.png)


## <a name="next-steps"></a>Próximas etapas

- [Habilite a integridade do convidado nos insights de VM e agentes integrados.](vminsights-health-enable.md)
- [Configure monitores usando o portal do Azure.](vminsights-health-configure.md)
- [Configure monitores usando regras de coleta de dados.](vminsights-health-configure-dcr.md)