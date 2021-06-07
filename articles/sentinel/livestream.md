---
title: Use transmissão ao vivo de busca no Azure Sentinel para detectar ameaças | Microsoft Docs
description: Este artigo descreve como usar o transmissão ao vivo de busca no Azure Sentinel para manter o controle dos dados.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2020
ms.author: yelevin
ms.openlocfilehash: 14928d3c94ced8d1cd0c12e76428be73b68b91d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84783156"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Usar a transmissão ao vivo de busca no Azure Sentinel para detectar ameaças

Use a busca de transmissão ao vivo para criar sessões interativas que permitem testar consultas recém-criadas à medida que ocorrem eventos, obter notificações das sessões quando uma correspondência for encontrada e iniciar investigações, se necessário. Você pode criar rapidamente uma sessão transmissão ao vivo usando qualquer consulta de Log Analytics.

- **Testar consultas recém-criadas conforme ocorrem eventos**
    
    Você pode testar e ajustar as consultas sem conflitos com as regras atuais que estão sendo aplicadas ativamente aos eventos. Depois de confirmar que essas novas consultas funcionam conforme o esperado, é fácil promovê-las para regras de alerta personalizadas, selecionando uma opção que eleva a sessão a um alerta.

- **Seja notificado quando as ameaças ocorrerem**
    
    Você pode comparar feeds de dados de ameaça a dados de log agregados e ser notificado quando ocorrer uma correspondência. Os feeds de dados de ameaça são fluxos contínuos de dados relacionados a ameaças potenciais ou atuais, portanto, a notificação pode indicar uma ameaça potencial à sua organização. Crie uma sessão transmissão ao vivo em vez de uma regra de alerta personalizada quando desejar ser notificado de um problema potencial sem as sobrecargas de manter uma regra de alerta personalizada.

- **Investigações de lançamento**
    
    Se houver uma investigação ativa que envolva um ativo, como um host ou usuário, você poderá exibir a atividade específica (ou qualquer) nos dados de log conforme ele ocorrer nesse ativo. Você pode ser notificado quando essa atividade ocorrer.


## <a name="create-a-livestream-session"></a>Criar uma sessão transmissão ao vivo

Você pode criar uma sessão transmissão ao vivo de uma consulta de busca existente ou criar sua sessão do zero.

1. No portal do Azure, navegue até **sentinela**  >  **Threat Management**  >  **busca**.

1. Para criar uma sessão transmissão ao vivo de uma consulta de busca:
    
    1. Na guia **consultas** , localize a consulta de busca a ser usada.
    1. Clique com o botão direito do mouse na consulta e selecione **Adicionar a transmissão ao vivo**. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![criar sessão transmissão ao vivo da consulta de busca do Azure Sentinel](./media/livestream/livestream-from-query.png)

1. Para criar uma sessão transmissão ao vivo do zero: 
    
    1. Selecione a guia **transmissão ao vivo**
    1. Clique em **+ novo transmissão ao vivo**.
    
1. No painel **transmissão ao vivo** :
    
    - Se você iniciou o transmissão ao vivo de uma consulta, examine a consulta e faça as alterações que deseja fazer.
    - Se você iniciou o transmissão ao vivo do zero, crie sua consulta. 

1. Selecione **reproduzir** na barra de comandos.
    
    A barra de status na barra de comandos indica se a sessão transmissão ao vivo está em execução ou em pausa. No exemplo a seguir, a sessão está em execução:
    
    > [!div class="mx-imgBorder"]
    > ![criar sessão transmissão ao vivo da busca do Azure Sentinel](./media/livestream/livestream-session.png)

1. Selecione **salvar** na barra de comandos.
    
    A menos que você selecione **Pausar**, a sessão continuará a ser executada até que você saia da portal do Azure.

## <a name="view-your-livestream-sessions"></a>Exibir suas sessões do transmissão ao vivo

1. Na portal do Azure, navegue até **sentinela**  >  **Threat Management**  >  **caça**  >  **transmissão ao vivo** guia.

1. Selecione a sessão transmissão ao vivo que você deseja exibir ou editar. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![criar sessão transmissão ao vivo da consulta de busca do Azure Sentinel](./media/livestream/livestream-tab.png)
    
    A sessão transmissão ao vivo selecionada é aberta para reprodução, pausa, edição e assim por diante.

## <a name="receive-notifications-when-new-events-occur"></a>Receber notificações quando novos eventos ocorrerem

Como as notificações do transmissão ao vivo para novos eventos usam notificações de portal do Azure, você vê essas notificações sempre que usa o portal do Azure. Por exemplo:

![Notificação de portal do Azure para transmissão ao vivo](./media/livestream/notification.png)

Selecione a notificação para abrir o painel **transmissão ao vivo** .
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Elevar uma sessão transmissão ao vivo a um alerta

Você pode promover uma sessão transmissão ao vivo para um novo alerta selecionando **elevar para alerta** na barra de comandos na sessão transmissão ao vivo relevante:

> [!div class="mx-imgBorder"]
> ![Elevar a sessão transmissão ao vivo a um alerta](./media/livestream/elevate-to-alert.png)

Essa ação abre o assistente de criação de regras, que é preenchido previamente com a consulta associada à sessão transmissão ao vivo.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o transmissão ao vivo de busca no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- [Buscar ameaças proativamente](hunting.md)
- [Use blocos de anotações para executar campanhas de busca automatizadas](notebooks.md)
