---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 0964872d03d6e321d25d51a18edbb4a6f0be8a4f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425482"
---
Neste início rápido, você aprenderá a usar a conversão de fala em texto com o serviço de Fala e o cURL.

Para obter uma visão de alto nível dos conceitos de Conversão de Fala em Texto, confira o artigo de [visão geral](../../../speech-to-text.md).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-speech-to-text"></a>Conversão de fala em texto

No prompt de comando, execute o comando a seguir. Será necessário inserir os valores a seguir no comando.
- Sua chave de assinatura do serviço de Fala.
- Sua região do serviço de Fala.
- O caminho do arquivo de áudio de entrada. É possível gerar arquivos de áudio usando a [conversão de texto em fala](../../../get-started-text-to-speech.md).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

Você deverá receber uma resposta semelhante à mostrada a seguir.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

Para obter mais informações, confira a [Referência da API REST de conversão de fala em texto](../../../rest-speech-to-text.md).