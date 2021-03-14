---
title: Gerenciar alertas e estados de grupos inteligentes
description: Gerenciando os estados de alerta e as instâncias de grupo inteligentes
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: cac926c3c56c7f58e5237b820e0328999ffe2c52
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033564"
---
# <a name="manage-alert-and-smart-group-states"></a>Gerenciar alertas e estados de grupos inteligentes

Os alertas no Azure Monitor agora têm um [estado de alerta e uma condição do monitor](./alerts-overview.md) e, da mesma forma, os Grupos Inteligentes têm um [estado do grupo inteligente](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json). As alterações no estado agora são capturadas no histórico associado ao respectivo grupo inteligente ou de alerta. Este artigo orienta você pelo processo de alteração do estado, para um grupo inteligente e de alerta.

## <a name="change-the-state-of-an-alert"></a>Alterar o estado de um alerta

1. Você pode alterar o estado de um alerta das seguintes maneiras: 
    * Na página Todos os alertas, clique na caixa de seleção ao lado dos alertas que deseja alterar o estado e clique no estado de alteração.   
    ![Captura de tela mostra a página todos os alertas com o estado de alteração selecionado.](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Na página Detalhes do alerta para uma instância de alerta específica, você pode clicar no estado de alteração   
    ![Captura de tela mostra a página detalhes do alerta com estado de alerta de alteração selecionado.](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Na página Detalhes do alerta para uma instância de alerta específica, no painel de Grupo Inteligente, você pode clicar na caixa de seleção ao lado de alertas que você deseja    
    ![Captura de tela mostra a página detalhes do alerta para o alerta de pulsação com algumas instâncias com marcas de seleção.](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Na página Detalhes do Grupo Inteligente, na lista de alertas de membro, você pode clicar na caixa de seleção ao lado dos alertas que deseja alterar o estado e clicar em Alterar estado para alterar o estado e clicar em Estado de alteração.   
    ![Captura de tela mostra a página de detalhes do grupo inteligente, na qual você pode selecionar alertas para os quais alterar o estado.](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Ao clicar em Estado de alteração, um pop-up é aberto, permitindo que você selecione o estado (Novo/Confirmado/Fechado) e insira um comentário, se necessário.   
![Captura de tela mostra a caixa de diálogo alerta de alteração de detalhes.](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Depois que isso for feito, a alteração de estado será registrada no histórico do alerta respectivo. Isso pode ser exibido ao abrir a respectiva página de detalhes e, em seguida, verificando a seção de histórico.    
![Captura de tela mostra o histórico de alterações de estado.](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Alterar o estado de um grupo inteligente
1. Você pode alterar o estado de um grupo inteligente das seguintes maneiras:
    1. Na página da lista Grupo Inteligente, clique na caixa de seleção ao lado dos grupos inteligentes que deseja alterar o estado e clique em Estado de Alteração  
    ![Captura de tela mostra a página alterar estado de grupos inteligentes.](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Na página Detalhes do Grupo Inteligente, você pode clicar no estado de alteração        
    ![Captura de tela mostra a página de detalhes do grupo inteligente com alterar o estado do grupo inteligente selecionado.](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Ao clicar em Estado de alteração, um pop-up é aberto, permitindo que você selecione o estado (Novo/Confirmado/Fechado) e insira um comentário, se necessário. 
![Captura de tela mostra a caixa de diálogo Alterar Estado do grupo inteligente.](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Alterar o estado de um grupo inteligente não altera o estado dos alertas de membro individual.

1. Depois que isso for feito, a alteração de estado será registrada no histórico do grupo inteligente respectivo. Isso pode ser exibido ao abrir a respectiva página de detalhes e, em seguida, verificando a seção de histórico.     
![Captura de tela mostra o histórico de alterações para o grupo inteligente.](./media/alerts-managing-alert-states/state-sg-history.jpg)