---
title: Etapas de importação de aplicativo
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 10/16/2020
ms.openlocfilehash: b70a22e502de5441b5c91a8f33357375834f54e1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130708"
---
1. No [portal do LUIS](https://www.luis.ai), na página **Meus aplicativos** , selecione **+ Novo aplicativo para conversa** , em seguida, **Importar como JSON**. Localize o arquivo JSON salvo na etapa anterior. Você não precisa alterar o nome do aplicativo. Selecione **Concluído**

1. Na seção **Gerenciar** , na guia **Versões** , escolha a versão `0.1`, em seguida, selecione **Clonar** para clonar a versão e dê um nome de `ml-entity`. Depois, escolha **Concluído** para terminar o processo de clonagem. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL.

    > [!TIP]
    > É uma prática recomendada clonar para uma nova versão antes de modificar o aplicativo. Quando concluir a alteração para uma versão, exporte a versão (como um arquivo .json ou .lu) e faça check-in do arquivo no controle do código-fonte.

1. Selecione **Build** na parte superior da tela e clique em **Intenções** no menu de navegação à esquerda. Você verá as intenções do aplicativo, que são os principais blocos de construção de um aplicativo LUIS.
