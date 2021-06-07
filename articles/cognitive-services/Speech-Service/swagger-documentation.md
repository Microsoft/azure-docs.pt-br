---
title: Documentação do Swagger – Serviços de Fala
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100636261"
---
# <a name="swagger-documentation"></a>Documentação do Swagger

O serviço de Fala oferece uma especificação do Swagger para interagir com uma variedade de APIs REST usadas para importar dados, criar modelos, testar a precisão do modelo, criar pontos de extremidade personalizados, colocar transcrições de lote na fila e gerenciar assinaturas. A maioria das operações disponíveis por meio da [área de Fala Personalizada do Speech Studio](https://aka.ms/customspeech) pode ser concluída de maneira programática usando essas APIs.

> [!NOTE]
> O serviço de Fala tem várias APIs REST para [reconhecimento de fala](rest-speech-to-text.md) e [conversão de texto em fala](rest-text-to-speech.md).  
>
> No entanto, somente a [API REST de Reconhecimento de Fala v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) e v2.0 está documentada na especificação do Swagger. Confira os documentos referenciados no parágrafo anterior para obter informações sobre todas as outras APIs REST de Serviços de Fala.

## <a name="generating-code-from-the-swagger-specification"></a>Como gerar o código da especificação do Swagger

A [especificação do Swager](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) traz opções que permitem testar rapidamente vários caminhos. No entanto, às vezes, é desejável para gerar o código para todos os caminhos, criando uma única biblioteca de chamadas na qual é possível basear as soluções futuras. Vamos dar uma olhada no processo de geração de uma biblioteca do Python.

Você precisará configurar o Swagger na região do seu Recurso de Fala. Você pode confirmar a região na parte de **Visão Geral** das configurações do seu recurso de Fala no portal do Azure. A lista completa de regiões com suporte está disponível [aqui](regions.md#speech-to-text).

1. No navegador, vá para a especificação do Swagger da sua [região](regions.md#speech-to-text):  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. Nessa página, clique em **definição da API** e depois em **Swagger**. Copie a URL da página exibida.
1. Em outro navegador, acesse [https://editor.swagger.io](https://editor.swagger.io)
1. Clique em **Arquivo**, depois em **Importar URL**, cole a URL e clique em **OK**.
1. Clique em **Gerar Cliente** e selecione **Python**. A biblioteca de clientes é baixada no seu computador em um arquivo `.zip`.
1. Extraia tudo do download. Você pode usar o `tar -xf` para extrair tudo.
1. Instale o módulo extraído em seu ambiente Python:  
      `pip install path/to/package/python-client`
1. O pacote instalado é chamado `swagger_client`. Verifique se a instalação funcionou:  
       `python -c "import swagger_client"`

Você pode usar a biblioteca do Python gerada com as [amostras dos serviços de Fala no GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-documents"></a>Documentos de referência

* [Swagger: API REST de Reconhecimento de Fala v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [API REST de conversão de fala em texto](rest-speech-to-text.md)
* [API REST conversão de texto em fala](rest-text-to-speech.md)

## <a name="next-steps"></a>Próximas etapas

* [Amostras do serviço de Fala no GitHub](https://aka.ms/csspeech/samples).
* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](overview.md#try-the-speech-service-for-free)
