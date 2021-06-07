---
title: Notificações antecipadas (versão prévia) para eventos de manutenção planejada
description: Obtenha notificações antes da manutenção planejada para o banco de dados SQL do Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/02/2021
ms.openlocfilehash: 895b9081ba7eb6d7e8b5d3304d37168e4064ed39
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560022"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a>Notificações antecipadas para eventos de manutenção planejada (versão prévia)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

As notificações antecipadas (versão prévia) estão disponíveis para os bancos de dados configurados para a [janela de manutenção (versão prévia)](maintenance-window.md). As notificações antecipadas permitem que os clientes configurem notificações para serem enviadas até 24 horas antes de qualquer evento planejado.

As notificações podem ser configuradas para que você possa obter textos, emails, notificações por push do Azure e correio de voz quando a manutenção planejada é devido ao início das próximas 24 horas. Notificações adicionais são enviadas quando a manutenção é iniciada e quando a manutenção termina.

> [!Note]
> Embora a capacidade de escolher uma janela de manutenção esteja disponível para instâncias gerenciadas do Azure SQL, as notificações antecipadas não estão disponíveis no momento para instâncias gerenciadas do Azure SQL.

## <a name="create-an-advance-notification"></a>Criar uma notificação antecipada

Notificações avançadas estão disponíveis para bancos de dados SQL do Azure que têm sua janela de manutenção configurada. 

Conclua as etapas a seguir para habilitar uma notificação.  

1. Vá para a página [manutenção planejada](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance) , selecione **alertas de integridade** e, em seguida, **adicionar alerta de integridade do serviço**.

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="opção de menu criar um novo alerta de integridade":::

2. Na seção **ações** , selecione **Adicionar grupos de ação**. 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="opção de menu Adicionar um grupo de ações":::

3. Preencha o formulário **Criar grupo de ações** e, em seguida, selecione **Avançar: notificações**.  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="formulário Criar grupo de ações":::

1. Na guia **notificações** , selecione o **tipo de notificação**. A opção **email/SMS/Push/Voice** oferece mais flexibilidade e é a opção recomendada. Selecione a caneta para configurar a notificação.  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="configurar notificações":::



   1. Preencha o formulário *Adicionar ou editar notificação* que é aberto e selecione **OK**: 

   2. As ações e as marcas são opcionais. Aqui você pode configurar ações adicionais a serem disparadas ou usar marcas para categorizar e organizar os recursos do Azure. 

   4. Verifique os detalhes na guia **revisar + criar** e selecione **criar**. 

7. Depois de selecionar criar, a tela Configuração da regra de alerta será aberta e o grupo de ações será selecionado. Dê um nome para sua nova regra de alerta, escolha o grupo de recursos para ele e selecione **criar regra de alerta**. 

8. Clique no item de menu **alertas de integridade** novamente e a lista de alertas agora contém o novo alerta. 


Você está pronto. Na próxima vez que houver um evento de manutenção do Azure SQL planejado, você receberá uma notificação antecipada.

## <a name="receiving-notifications"></a>Recebendo notificações

A tabela a seguir mostra as notificações de informações gerais que você pode receber: 

|Status|Descrição|
|:---|:---|
|**Implantação planejada**| Recebido 24 horas antes do evento de manutenção. A manutenção é planejada na data entre 17:00 – 8:00 (hora local) para o banco de dados XYZ.|
|**Em andamento** | A manutenção do banco de dados *XYZ*   está sendo iniciada.| 
|**Concluir** | A manutenção do banco de dados *XYZ* foi concluída. |

A tabela a seguir mostra notificações adicionais que podem ser enviadas enquanto a manutenção está em andamento: 

|Status|Descrição|
|:---|:---|
|**Estendido** | A manutenção está em andamento, mas não foi concluída para o banco de dados *XYZ*. A manutenção continuará na próxima janela de manutenção.| 
|**Cancelado**| A manutenção do banco de dados *XYZ* foi cancelada e será reagendada mais tarde. |
|**Bloqueado**|Houve um problema durante a manutenção do banco de dados *XYZ*. Você será notificado quando continuarmos.| 
|**Retomada**|O problema foi resolvido e a manutenção continuará na próxima janela de manutenção.|


## <a name="next-steps"></a>Próximas etapas

- [Janela de manutenção](maintenance-window.md)
- [Perguntas frequentes sobre a janela de manutenção](maintenance-window-faq.yml)
- [Visão geral dos alertas no Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md)
- [Enviar email para a Função do Azure Resource Manager](../../azure-monitor/alerts/action-groups.md#email-azure-resource-manager-role)