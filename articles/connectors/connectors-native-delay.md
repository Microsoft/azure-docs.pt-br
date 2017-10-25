---
title: "Adicionar atraso a aplicativos lógicos | Microsoft Docs"
description: "Visão geral das ações atrasar e atrasar até, e como usá-las como um aplicativo lógico do Azure."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 915f48bf-3bd8-4656-be73-91a941d0afcd
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 5f4f7052d48b4ca4ed91212d970551141e78e852
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Introdução às ações atrasar e atrasar até
Usando as ações atrasar e “atrasar até”, você pode concluir cenários de fluxo de trabalho.

Por exemplo, você pode:

* Aguardar até um dia útil para enviar uma atualização de status por email.
* Atrasar o fluxo de trabalho até que uma chamada HTTP tenha tempo para ser concluída antes da retomada e da recuperação do resultado.

Para começar a usar a ação atrasar em um aplicativo lógico, confira [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-the-delay-actions"></a>Usar as ações atrasar
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](connectors-overview.md).

Veja uma sequência de exemplos de como usar uma etapa de atraso em um aplicativo lógico:

1. Depois de adicionar um gatilho, clique em **Nova Etapa** para adicionar uma ação.
2. Procure por **atrasar** para exibir as ações atrasar. Neste exemplo, escolheremos **Atrasar**.
   
    ![Ações atrasar](./media/connectors-native-delay/using-action-1.png)
3. Preencha qualquer uma das propriedades de ação para configurar o atraso.
   
    ![Configuração de atraso](./media/connectors-native-delay/using-action-2.png)
4. Clique em **Salvar** para publicar e ativar o aplicativo lógico.

## <a name="action-details"></a>Detalhes da ação
O gatilho de recorrência tem as propriedades a seguir que podem ser configuradas.

### <a name="delay-action"></a>Ação atrasar
Essa ação atrasa a execução por um determinado intervalo de tempo.
Um * significa que é um campo obrigatório.

| Nome de exibição | Nome da propriedade | Descrição |
| --- | --- | --- |
| Contagem* |count |O número de unidades de tempo a serem atrasadas |
| Unidade* |unit |A unidade de tempo: `Second`, `Minute`, `Hour`, ou `Day` |

<br>

### <a name="delay-until-action"></a>Ação atrasar até
Essa ação atrasa a execução até uma data/hora especificada.
Um * significa que é um campo obrigatório.

| Nome de exibição | Nome da propriedade | Descrição |
| --- | --- | --- |
| Ano* |timestamp |O ano até o qual atrasar (GMT) |
| Mês* |timestamp |O mês até o qual atrasar (GMT) |
| Dia* |timestamp |O dia até o qual atrasar (GMT) |

<br>

## <a name="next-steps"></a>Próximas etapas
Agora, experimente a plataforma e [crie um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md). Você pode explorar os outros conectores disponíveis em aplicativos lógicos examinando nossa [lista de APIs](apis-list.md).

