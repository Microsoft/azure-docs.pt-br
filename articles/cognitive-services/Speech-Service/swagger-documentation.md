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
ms.openlocfilehash: 232435a424d2461bce4598356a986473cb1d3644
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552569"
---
# <a name="swagger-documentation"></a>Documentação do Swagger

Os Serviços de Fala oferecem uma especificação do Swagger para interagir com uma variedade de APIs REST usadas para importar dados, criar modelos, testar a precisão do modelo, criar pontos de extremidade personalizados, colocar transcrições de lote na fila e gerenciar assinaturas. A maioria das operações disponíveis por meio do portal de Fala Personalizada pode ser concluída de forma programática usando essas APIs.

> [!NOTE]
> Há suporte para operações de conversão de fala em texto e texto em fala disponíveis como APIs REST, que, por vez, estão documentadas na especificação do Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Como gerar o código da especificação do Swagger

A [especificação do Swager](https://cris.ai/swagger/ui/index) traz opções que permitem testar rapidamente vários caminhos. No entanto, às vezes, é desejável para gerar o código para todos os caminhos, criando uma única biblioteca de chamadas na qual é possível basear as soluções futuras. Vamos dar uma olhada no processo de geração de uma biblioteca do Python.

Você precisará definir o Swagger com a mesma região da assinatura do Serviço de Fala. Confirme a região no portal do Azure no recurso Serviços de Fala. Para obter uma lista completa das regiões com suporte, confira [Regiões](regions.md).

1. Acesse https://editor.swagger.io
2. Clique em **Arquivo** e, em seguida, em **Importar**
3. Insira a URL do Swagger, incluindo a região de sua assinatura dos Serviços de Fala `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Clique em **Gerar Cliente** e selecione o Python
5. Salvar a biblioteca de clientes

Use a biblioteca do Python gerada com as [amostras dos Serviços de Fala no GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Documentos de Referência

* [REST (Swagger): transcrição e personalização em lote](https://westus.cris.ai/swagger/ui/index)
* [API REST: conversão de fala em texto](rest-speech-to-text.md)
* [API REST: conversão de texto em fala](rest-text-to-speech.md)

## <a name="next-steps"></a>Próximas etapas

* [Amostras dos Serviços de Fala no GitHub](https://aka.ms/csspeech/samples).
* [Obter gratuitamente uma chave de assinatura do Serviço de Fala](get-started.md)
