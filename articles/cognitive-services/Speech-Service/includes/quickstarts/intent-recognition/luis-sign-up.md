---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: ab22ad75b5b49588bbdcedf5fc995ce65fe4e690
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104154"
---
Para concluir o início rápido do reconhecimento de intenção, você precisará criar uma conta do LUIS e um projeto usando a versão prévia do portal do LUIS. Este início rápido requer apenas uma assinatura do LUIS. *Não é* necessário ter uma assinatura do serviço de Fala.

A primeira coisa que você precisará fazer é criar uma conta e um aplicativo do LUIS usando a versão prévia do portal do LUIS. O aplicativo LUIS que você cria usará um domínio predefinido para a automação doméstica, que fornece intenções, entidades e exemplos de declarações. Quando terminar, você terá um ponto de extremidade do LUIS em execução na nuvem que você poderá chamar usando o SDK de Fala. 

Siga estas instruções para criar seu aplicativo LUIS:

* <a href="/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Início Rápido: criar um aplicativo de domínio predefinido </a>

Quando terminar, você precisará de quatro itens:

* Republicar com a **Preparação da fala** ativada
* Sua **chave primária** do LUIS
* Sua **localização** do LUIS
* Sua **ID do aplicativo** LUIS

Veja em que parte da [versão prévia do portal do LUIS](https://preview.luis.ai/) é possível encontrar essas informações:

1. Na versão prévia do portal do LUIS, selecione seu aplicativo e o botão **Publicar**.

2. Selecione o slot de **Produção**, se estiver usando `en-US`, alterne a opção **Preparação da fala** para a posição **Ativada**. Em seguida, selecione o botão **Publicar**.

    > [!IMPORTANT]
    > A **Preparação da fala** é altamente recomendável pois aprimorará a precisão do reconhecimento de fala.

    > [!div class="mx-imgBorder"]
    > ![Publicar LUIS no ponto de extremidade](../../../media/luis/publish-app-popup.png)

3. Na versão prévia do portal do LUIS, selecione **Gerenciar** e, em seguida, selecione **Recursos do Azure**. Nesta página, você encontrará sua chave e local (às vezes chamado de _região_) do LUIS.

   > [!div class="mx-imgBorder"]
   > ![Chave e local do LUIS](../../../media/luis/luis-key-region.png)

4. Depois de obter a chave e o local, você precisará da ID do aplicativo. Selecione **Configurações do Aplicativo** – a ID do aplicativo está disponível nesta página.

   > [!div class="mx-imgBorder"]
   > ![ID do aplicativo LUIS](../../../media/luis/luis-app-id.png)