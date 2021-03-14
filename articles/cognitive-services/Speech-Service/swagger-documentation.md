---
title: Documentação do Swagger-serviço de fala
titleSuffix: Azure Cognitive Services
description: A documentação do Swagger pode ser usada para gerar SDKs automaticamente para várias linguagens de programação. Todas as operações em nosso serviço são compatíveis com o Swagger
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 02/16/2021
ms.author: alexeyo
ms.openlocfilehash: d4369b66bacbe8cff4fc9712ffcd0cb5a370c439
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636261"
---
# <a name="swagger-documentation"></a>Documentação do Swagger

O Speech Service oferece uma especificação Swagger para interagir com algumas APIs REST usadas para importar dados, criar modelos, testar a precisão do modelo, criar pontos de extremidade personalizados, enfileirar transcrições em lote e gerenciar assinaturas. A maioria das operações disponíveis por meio [da área de fala personalizada do Speech Studio](https://aka.ms/customspeech) pode ser concluída programaticamente usando essas APIs.

> [!NOTE]
> O serviço de fala tem várias APIs REST para [conversão de fala em texto](rest-speech-to-text.md) e [texto em fala](rest-text-to-speech.md).  
>
> No entanto, somente a [API REST de fala em texto v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) e v 2.0 está documentada na especificação do Swagger. Consulte os documentos referenciados no parágrafo anterior para obter as informações sobre todas as outras APIs REST dos serviços de fala.

## <a name="generating-code-from-the-swagger-specification"></a>Como gerar o código da especificação do Swagger

A [especificação do Swager](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) traz opções que permitem testar rapidamente vários caminhos. No entanto, às vezes, é desejável para gerar o código para todos os caminhos, criando uma única biblioteca de chamadas na qual é possível basear as soluções futuras. Vamos dar uma olhada no processo de geração de uma biblioteca do Python.

Você precisará definir o Swagger para a região do seu recurso de fala. Você pode confirmar a região na parte de **visão geral** das configurações de recursos de fala no portal do Azure. A lista completa de regiões com suporte está disponível [aqui](regions.md#speech-to-text).

1. Em um navegador, vá para a especificação do Swagger para sua [região](regions.md#speech-to-text):  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. Nessa página, clique em **definição de API** e clique em **Swagger**. Copie a URL da página que aparece.
1. Em um novo navegador, vá para [https://editor.swagger.io](https://editor.swagger.io)
1. Clique em **arquivo**, clique em **importar URL**, Cole a URL e clique em **OK**.
1. Clique em **gerar cliente** e selecione **Python**. A biblioteca de cliente é baixada para o computador em um `.zip` arquivo.
1. Extraia tudo do download. Você pode usar `tar -xf` para extrair tudo.
1. Instale o módulo extraído em seu ambiente do Python:  
      `pip install path/to/package/python-client`
1. O pacote instalado é denominado `swagger_client` . Verifique se a instalação funcionou:  
       `python -c "import swagger_client"`

Você pode usar a biblioteca do Python gerada com os [exemplos de serviço de fala no GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-documents"></a>Documentos de referência

* [Swagger: API REST de fala em texto v 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [API REST de conversão de fala em texto](rest-speech-to-text.md)
* [API REST conversão de texto em fala](rest-text-to-speech.md)

## <a name="next-steps"></a>Próximas etapas

* [Exemplos de serviço de fala no GitHub](https://aka.ms/csspeech/samples).
* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](overview.md#try-the-speech-service-for-free)
