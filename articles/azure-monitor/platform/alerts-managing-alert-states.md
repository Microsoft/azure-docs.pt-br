---
title: Gerenciar alertas e estados de grupos inteligentes
description: Gerenciando os estados de alerta e as instâncias de grupo inteligentes
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 2aa521b0552b60e5a875a5f46ab9887c6e5b6e3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60919553"
---
# <a name="manage-alert-and-smart-group-states"></a>Gerenciar alertas e estados de grupos inteligentes
Os alertas no Azure Monitor agora têm um [estado de alerta e uma condição do monitor](https://aka.ms/azure-alerts-overview) e, da mesma forma, os Grupos Inteligentes têm um [estado do grupo inteligente](https://aka.ms/smart-groups). As alterações no estado agora são capturadas no histórico associado ao respectivo grupo inteligente ou de alerta. Este artigo orienta você pelo processo de alteração do estado, para um grupo inteligente e de alerta.

## <a name="change-the-state-of-an-alert"></a>Alterar o estado de um alerta
1. Você pode alterar o estado de um alerta das seguintes maneiras: 
    * Na página Todos os alertas, clique na caixa de seleção ao lado dos alertas que deseja alterar o estado e clique no estado de alteração.   
    ![Monitoramento](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Na página Detalhes do alerta para uma instância de alerta específica, você pode clicar no estado de alteração   
    ![Monitoramento](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Na página Detalhes do alerta para uma instância de alerta específica, no painel de Grupo Inteligente, você pode clicar na caixa de seleção ao lado de alertas que você deseja    
    ![Monitoramento](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Na página Detalhes do Grupo Inteligente, na lista de alertas de membro, você pode clicar na caixa de seleção ao lado dos alertas que deseja alterar o estado e clicar em Alterar estado para alterar o estado e clicar em Estado de alteração.   
    ![Monitoramento](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Ao clicar em Estado de alteração, um pop-up é aberto, permitindo que você selecione o estado (Novo/Confirmado/Fechado) e insira um comentário, se necessário.   
![Monitoramento](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Depois que isso for feito, a alteração de estado será registrada no histórico do alerta respectivo. Isso pode ser exibido ao abrir a respectiva página de detalhes e, em seguida, verificando a seção de histórico.    
![Monitoramento](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Alterar o estado de um grupo inteligente
1. Você pode alterar o estado de um grupo inteligente das seguintes maneiras:
    1. Na página da lista Grupo Inteligente, clique na caixa de seleção ao lado dos grupos inteligentes que deseja alterar o estado e clique em Estado de Alteração  
    ![Monitoramento](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Na página Detalhes do Grupo Inteligente, você pode clicar no estado de alteração        
    ![Monitoramento](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Ao clicar em Estado de alteração, um pop-up é aberto, permitindo que você selecione o estado (Novo/Confirmado/Fechado) e insira um comentário, se necessário. 
![Monitoramento](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Alterar o estado de um grupo inteligente não altera o estado dos alertas de membro individual.

1. Depois que isso for feito, a alteração de estado será registrada no histórico do grupo inteligente respectivo. Isso pode ser exibido ao abrir a respectiva página de detalhes e, em seguida, verificando a seção de histórico.     
![Monitoramento](./media/alerts-managing-alert-states/state-sg-history.jpg)
