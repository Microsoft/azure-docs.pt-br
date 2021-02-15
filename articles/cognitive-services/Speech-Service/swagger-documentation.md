---
title: Documentação do Swagger-serviço de fala
titleSuffix: Azure Cognitive Services
description: A documentação do Swagger pode ser usada para gerar SDKs automaticamente para várias linguagens de programação. Todas as operações em nosso serviço são compatíveis com o Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 6bb50e427fa85a170c5ad23a63d67c01e898a17d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665677"
---
# <a name="swagger-documentation"></a>Documentação do Swagger

O serviço de fala oferece uma especificação Swagger para interagir com algumas APIs REST usadas para importar dados, criar modelos, testar a precisão do modelo, criar pontos de extremidade personalizados, enfileirar transcrições em lote e gerenciar assinaturas. A maioria das operações disponíveis por meio do portal de Fala Personalizada pode ser concluída de forma programática usando essas APIs.

> [!NOTE]
> Há suporte para operações de conversão de fala em texto e texto em fala disponíveis como APIs REST, que, por vez, estão documentadas na especificação do Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Como gerar o código da especificação do Swagger

A [especificação do Swager](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) traz opções que permitem testar rapidamente vários caminhos. No entanto, às vezes, é desejável para gerar o código para todos os caminhos, criando uma única biblioteca de chamadas na qual é possível basear as soluções futuras. Vamos dar uma olhada no processo de geração de uma biblioteca do Python.

Você precisará definir o Swagger para a mesma região que sua assinatura do serviço de fala. Você pode confirmar sua região no portal do Azure em seu recurso de serviço de fala. Para obter uma lista completa das regiões com suporte, consulte [regiões](regions.md).

1. Em um navegador, vá para a especificação do Swagger para sua região:  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. Nessa página, clique em **definição de API**e clique em **Swagger**. Copie a URL da página que aparece.
1. Em um novo navegador, vá para https://editor.swagger.io
1. Clique em **arquivo**, clique em **importar URL**, Cole a URL e clique em **OK**.
1. Clique em **gerar cliente** e selecione **Python**. A biblioteca de cliente é baixada para o computador em um `.zip` arquivo.
1. Extraia tudo do download. Você pode usar `tar -xf` para extrair tudo.
1. Instale o módulo extraído em seu ambiente do Python:  
       `pip install path/to/package/python-client`
1. O pacote instalado é denominado `swagger_client` . Verifique se a instalação funcionou:  
       `python -c "import swagger_client"`

Você pode usar a biblioteca do Python gerada com os [exemplos de serviço de fala no GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Documentos de Referência

* [REST (Swagger): transcrição e personalização do lote](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [API REST: conversão de fala em texto](rest-speech-to-text.md)
* [API REST: conversão de texto em fala](rest-text-to-speech.md)

## <a name="next-steps"></a>Próximas etapas

* [Exemplos de serviço de fala no GitHub](https://aka.ms/csspeech/samples).
* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](overview.md#try-the-speech-service-for-free)
