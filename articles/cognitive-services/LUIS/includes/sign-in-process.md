---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 10/30/2020
ms.topic: include
ms.openlocfilehash: e006f804b8ab6411f4949424147acf567dc2ed24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97371297"
---
## <a name="sign-in-to-luis-portal"></a>Entrar no portal do LUIS

[!INCLUDE [Note about portal deprecation](luis-portal-note.md)]

Um novo usuário do LUIS precisa seguir este procedimento:

1. Entrar no [portal do LUIS](https://www.luis.ai), selecionar seu país/região e concordar com os termos de uso. Caso, em vez disso, você consulte **Meus aplicativos**, um recurso do LUIS já existirá e você deverá avançar e criar um aplicativo. Você terá duas opções de inscrição:

    * Usando um recurso do Azure (recomendado) – permite vincular sua conta do LUIS a um recurso de criação do Azure novo ou existente. Isso é equivalente a inscrever-se já migrado. Você não precisará passar pelo [processo de migração](../luis-migration-authoring.md#what-is-migration) mais tarde.

    * Usando uma chave de avaliação. Isso permite entrar no LUIS com um recurso de avaliação que você não precisa configurar. Se você escolher essa opção, em algum momento será necessário [migrar sua conta](../luis-migration-authoring.md#migration-steps) e vincular seus aplicativos a um recurso de criação.

1. Na janela **Escolher uma criação** exibida, encontre sua assinatura do Azure e o recurso de criação do LUIS. Se você não tiver um recurso, poderá criar um.

    :::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Escolher um tipo de recurso de criação de Reconhecimento vocal.":::
    
    Ao criar um recurso de criação, forneça as seguintes informações:
    * **Nome do locatário** – o locatário ao qual sua assinatura do Azure está associada.
    * **Nome da assinatura do Azure** – a assinatura que será cobrada pelo recurso.
    * **Nome do grupo de recursos do Azure** – o nome de um grupo de recursos personalizado que você escolher ou criar. Os grupos de recursos permitem agrupar os recursos do Azure para acesso e gerenciamento.
    * **Nome do recurso do Azure** – um nome personalizado que você escolher, usado como parte da URL para as consultas de ponto de extremidade de criação e previsão.
    * **Tipo de preço** – o tipo de preço determina a transação máxima por segundo e mês.


