---
title: Migrar da API de Tradução de Fala para o Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Saiba como migrar seus aplicativos da API de Tradução de Fala para o Serviço de Fala.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 7b61aef13b113d9b2502c24e3001da25fa186c76
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559564"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrar da API de Tradução de Fala para o Serviço de Fala

Use este artigo para migrar seus aplicativos da API de Tradução de Fala da Microsoft para o [Serviço de Fala](index.yml). Este guia descreve as diferenças entre a API de Tradução de Fala e o Serviço de Fala e sugere estratégias para migrar seus aplicativos.

> [!NOTE]
> Sua chave de assinatura da API de Tradução de Fala não será aceita pelo Serviço de Fala. Você precisará criar uma nova assinatura dos serviços de fala.

## <a name="comparison-of-features"></a>Comparação de recursos

| Recurso                                           | API de Tradução de Fala                                  | Serviços de Fala | Detalhes                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tradução para texto                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tradução para fala                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Ponto de extremidade global                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Os serviços de fala não oferecem um ponto de extremidade global. Um ponto de extremidade global pode direcionar automaticamente o tráfego para o ponto de extremidade regional mais próximo, diminuindo a latência no aplicativo.                                                    |
| Pontos de extremidade regionais                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limite do tempo de conexão                             | 90 minutos                                               | Ilimitado com o SDK. 10 minutos com uma conexão WebSockets.                                                                                                                                                                                                                                                                                   |
| Chave de autenticação no cabeçalho                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Traduzido para vários idiomas em uma única solicitação | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDKs disponíveis                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Consulte a [documentação dos serviços de fala](index.yml) para SDKs disponíveis.                                                                                                                                                    |
| Conexões WebSockets                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API de Idiomas                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Os serviços de fala oferecem suporte ao mesmo intervalo de idiomas descritos no artigo de [referência de idiomas da API do tradutor](../translator-speech/languages-reference.md) . |
| Marcador e filtro de conteúdo ofensivo                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM como entrada                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Outros tipos de arquivo como entrada                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Resultados parciais                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informações de tempo                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| ID de Correlação                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modelos de fala personalizados                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Os serviços de fala oferecem modelos de fala personalizados que permitem que você personalize o reconhecimento de fala para o vocabulário exclusivo de sua organização.                                                                                                                                           |
| Modelos de tradução personalizados                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | A assinatura da API de Tradução de Texto da Microsoft permite que você use o [Tradutor Personalizado](https://www.microsoft.com/translator/business/customization/) para usar seus próprios dados para traduções mais precisas.                                                 |

## <a name="migration-strategies"></a>Estratégias de migração

Se você ou sua organização tiver aplicativos em desenvolvimento ou em produção que usam a API de Tradução de Fala, atualize-os para usar o Serviço de Fala. Confira os SDKs disponíveis, os exemplos de código e tutoriais na documentação do [Serviço de Fala](index.yml). Quando você estiver migrando, considere o seguinte:

* Os serviços de fala não oferecem um ponto de extremidade global. Determine se o aplicativo funcionará de maneira eficiente usando um ponto de extremidade regional único para todo o tráfego. Caso contrário, use a geolocalização para determinar o ponto de extremidade mais eficiente.

* Se seu aplicativo usar conexões de longa duração e não for possível usar os SDKs disponíveis, você poderá usar uma conexão WebSockets. Gerencie o limite de tempo limite de 10 minutos ao fazer a reconexão nos momentos apropriados.

* Se seu aplicativo usa a API de Tradução de Texto e a API de Tradução de Fala para habilitar modelos de tradução personalizados, adicione IDs de Categoria diretamente usando o Serviço de Fala.

* Ao contrário do API de Tradução de Fala, os serviços de fala podem concluir traduções em vários idiomas em uma única solicitação.

## <a name="next-steps"></a>Próximas etapas

* [Experimente os serviços de fala gratuitamente](get-started.md)
* [Início Rápido: reconhecer a fala em um aplicativo UWP usando o SDK de Fala](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Consulte também

* [O que é o Serviço de Fala](overview.md)
* [Documentação dos serviços de fala e do SDK de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
