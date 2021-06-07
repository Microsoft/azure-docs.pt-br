---
title: O que há de novo na API de Análise de Texto
titleSuffix: Text Analytics - Azure Cognitive Services
description: Este artigo fornece informações sobre novas versões e recursos para os serviços cognitivas do Azure Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: a2b001d34d265c8e7246b03875c32168f2c5c962
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598891"
---
# <a name="whats-new-in-the-text-analytics-api"></a>O que há de novo na API de Análise de Texto?

O API de Análise de Texto é atualizado em uma base contínua. Para se manter atualizado com os recentes desenvolvimentos, este artigo fornece informações sobre novas versões e recursos.

## <a name="march-2021"></a>Março de 2021

### <a name="general-api-updates"></a>Atualizações de API gerais
* Lançamento da nova API v 3.1-Preview. 4, que inclui 
   * Alterações no corpo da resposta JSON de mineração de opinião: 
      * `aspects` Agora é `targets` e `opinions` agora é `assessments` . 
   * Alterações no corpo da resposta JSON da API web hospedada do Análise de Texto para fins de integridade: 
      * O `isNegated` nome booliano de um objeto de entidade detectado para negação é preterido e substituído pela detecção de asserção.
      * Uma nova propriedade chamada `role` agora faz parte da relação extraída entre um atributo e uma entidade, bem como a relação entre entidades.  Isso adiciona a especificidade ao tipo de relação detectado.
   * A vinculação de entidade agora está disponível como uma tarefa assíncrona no `/analyze` ponto de extremidade.
   * Um novo `pii-categories` parâmetro agora está disponível no `/pii` ponto de extremidade.
      * Esse parâmetro permite que você especifique entidades PII e não suportadas por padrão para o idioma de entrada.
* Bibliotecas de cliente atualizadas, que incluem análise assíncrona e Análise de Texto para operações de integridade. Você pode encontrar exemplos no GitHub:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/javascript)
    
> [!div class="nextstepaction"]
> [Saiba mais sobre o API de Análise de Texto v 3.1-Preview. 4](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-4/operations/Languages)

### <a name="text-analytics-for-health-updates"></a>Análise de Texto para atualizações de integridade

* Uma nova versão `2021-03-01` de modelo para o `/health` ponto de extremidade e o contêiner local que fornece
    * Uma renomeação do `Gene` tipo de entidade como `GeneOrProtein` .
    * Um novo `Date` tipo de entidade.
    * Detecção de asserção que substitui a detecção de negação (disponível somente na API v 3.1-Preview. 4).
    * Uma nova `name` Propriedade preferida para entidades vinculadas que é normalizada de vários sistemas de ontologies e codificação (disponível somente na API v 3.1-Preview. 4). 
* Uma nova imagem de contêiner com marca `3.0.015370001-onprem-amd64` e a nova versão de modelo foi `2021-03-01` liberada para o repositório de visualização de contêiner. 
* O Análise de Texto para a imagem de contêiner de integridade será movido para um novo repositório no próximo mês.  Aguarde uma comunicação por email no local de sua nova casa.
> [!div class="nextstepaction"]
> [Saiba mais sobre o Análise de Texto para integridade](how-tos/text-analytics-for-health.md)
>

### <a name="text-analytics-resource-portal-update"></a>Análise de Texto atualização do portal de recursos
* Os **registros de texto processados** agora estão disponíveis como uma métrica na seção **monitoramento** para seu recurso de análise de texto no portal do Azure.  

## <a name="february-2021"></a>Fevereiro de 2021

* A `2021-01-15` versão do modelo para o ponto de extremidade PII no [reconhecimento de entidade nomeada](how-tos/text-analytics-how-to-entity-linking.md) v 3.1-Preview. x, que fornece 
  * Suporte expandido para 9 novos idiomas
  * Qualidade de ia aprimorada de categorias de entidade nomeada para idiomas com suporte.
* Os tipos de preço S0 a S4 estão sendo desativados em 8 de março de 2021. Se você tiver um recurso de Análise de Texto existente usando o tipo de preço S0 a S4, deverá atualizá-lo para usar o [tipo de preço](how-tos/text-analytics-how-to-call-api.md#change-your-pricing-tier)Standard (S).
* O [contêiner de detecção de idioma](how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment) já está disponível para o público geral.
* a v 2.1 da API está sendo desativada. 

## <a name="january-2021"></a>Janeiro de 2021

* A `2021-01-15` versão do modelo para o [reconhecimento de entidade nomeada](how-tos/text-analytics-how-to-entity-linking.md) v3. x, que fornece 
  * Suporte a idiomas expandidos para [várias categorias de entidade geral](named-entity-types.md). 
  * Qualidade de ia aprimorada de categorias gerais de entidade para todos os idiomas v3 com suporte. 

* A `2021-01-05` versão do modelo para [detecção de idioma](how-tos/text-analytics-how-to-language-detection.md), que fornece suporte a [idioma](language-support.md?tabs=language-detection)adicional.

Essas versões de modelo não estão disponíveis no momento na região leste dos EUA. 

> [!div class="nextstepaction"]
> [Saiba mais sobre o novo modelo NER](https://azure.microsoft.com/updates/text-analytics-ner-improved-ai-quality)

## <a name="december-2020"></a>Dezembro de 2020

* Detalhes de [preços atualizados](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) para o API de análise de texto.

## <a name="november-2020"></a>Novembro de 2020

* Um [novo ponto de extremidade](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Analyze) com API de análise de texto v 3.1-Preview. 3 para a nova [API de análise](how-tos/text-analytics-how-to-call-api.md?tabs=analyze)assíncrona, que dá suporte ao processamento em lotes para operações de extração de Ner, PII e chave de senha.
* Um [novo ponto de extremidade](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) com API de análise de texto v 3.1-Preview. 3 para a nova análise de texto assíncrona para API hospedada [de integridade](how-tos/text-analytics-for-health.md) com suporte para processamento em lote.
* Os dois novos recursos listados acima só estão disponíveis nas regiões a seguir `West US 2` : `East US 2` , `Central US` , `North Europe` e `West Europe` .
* `pt-BR`Agora há suporte para Português (Brasil) no [análise de sentimento](how-tos/text-analytics-how-to-sentiment-analysis.md) v3. x, começando com a versão do modelo `2020-04-01` . Ele adiciona ao suporte existente `pt-PT` para Português.
* Bibliotecas de cliente atualizadas, que incluem análise assíncrona e Análise de Texto para operações de integridade. Você pode encontrar exemplos no GitHub:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * 
> [!div class="nextstepaction"]
> [Saiba mais sobre o API de Análise de Texto v 3.1-Preview. 3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

## <a name="october-2020"></a>Outubro de 2020

* Suporte a hindi para Análise de Sentimento v3. x, começando com a versão do modelo `2020-04-01` . 
* Versão `2020-09-01` do modelo para o ponto de extremidade/Languages v3, que adiciona aprimoramentos de precisão e detecção de linguagem aumentada.
* disponibilidade V3 na Índia central e Norte dos EAU.

## <a name="september-2020"></a>Setembro de 2020

### <a name="general-api-updates"></a>Atualizações de API gerais

* Lançamento de uma nova URL para a visualização pública do Análise de Texto v 3.1 para dar suporte a atualizações nos seguintes pontos de extremidade de reconhecimento de entidade nomeado V3: 
    * `/pii` o ponto de extremidade agora inclui a nova `redactedText` propriedade no JSON de resposta onde as entidades PII detectadas no texto de entrada são substituídas por um `*` para cada caractere dessas entidades.
    * `/linking` o ponto de extremidade agora inclui a `bingID` propriedade no JSON de resposta para entidades vinculadas.
* Os seguintes pontos de extremidade da API de visualização do Análise de Texto foram desativados em 4º de setembro de 2020:
    * v 2.1-visualização
    * v3.0–versão prévia
    * v 3.0-visualização. 1
    
> [!div class="nextstepaction"]
> [Saiba mais sobre o API de Análise de Texto v 3.1-Preview. 2](quickstarts/client-libraries-rest-api.md)

### <a name="text-analytics-for-health-container-updates"></a>Análise de Texto para atualizações de contêiner de integridade

As atualizações a seguir são específicas para a versão de setembro do Análise de Texto apenas para o contêiner de integridade.
* Uma nova imagem de contêiner com `1.1.013530001-amd64-preview` a marca com a nova versão de modelo foi `2020-09-03` liberada para o repositório de visualização de contêiner. 
* Essa versão do modelo fornece melhorias no reconhecimento de entidades, detecção de abreviação e aprimoramentos de latência.

> [!div class="nextstepaction"]
> [Saiba mais sobre o Análise de Texto para integridade](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>Agosto de 2020

### <a name="general-api-updates"></a>Atualizações de API gerais

* Versão `2020-07-01` do modelo para v3 `/keyphrases` `/pii` e pontos de `/languages` extremidade, que adiciona:
    * [Categorias de entidades](named-entity-types.md?tabs=personal) específicas do governo e do país adicionais para reconhecimento de entidade nomeada.
    * Suporte a norueguês e turco no Análise de Sentimento v3.
* Um erro HTTP 400 agora será retornado para solicitações de API v3 que excedem os [limites de dados](concepts/data-limits.md)publicados. 
* Os pontos de extremidade que retornam um deslocamento agora dão suporte ao `stringIndexType` parâmetro opcional, que ajusta o retornado `offset` e os `length` valores para corresponder a um [esquema de índice de cadeia de caracteres](concepts/text-offsets.md)com suporte.

### <a name="text-analytics-for-health-container-updates"></a>Análise de Texto para atualizações de contêiner de integridade

As atualizações a seguir são específicas para a versão de agosto do Análise de Texto apenas para o contêiner de integridade.

* Nova versão de modelo para Análise de Texto para integridade: `2020-07-24`
* Nova URL para enviar Análise de Texto para solicitações de integridade: `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (Observe que uma limpeza de cache do navegador será necessária para usar o aplicativo Web de demonstração incluído nesta nova imagem de contêiner)

As seguintes propriedades na resposta JSON foram alteradas:

* `type` foi renomeado para `category` 
* `score` foi renomeado para `confidenceScore`
* As entidades no `category` campo da saída JSON agora estão no caso do Pascal. As seguintes entidades foram renomeadas:
    * `EXAMINATION_RELATION` foi renomeado para `RelationalOperator`.
    * `EXAMINATION_UNIT` foi renomeado para `MeasurementUnit`.
    * `EXAMINATION_VALUE` foi renomeado para `MeasurementValue`.
    * `ROUTE_OR_MODE` foi renomeado `MedicationRoute` .
    * A entidade relacional foi `ROUTE_OR_MODE_OF_MEDICATION` renomeada para `RouteOfMedication` .

As seguintes entidades foram adicionadas:

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* Extração de relações
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [Saiba mais sobre o Análise de Texto para o contêiner de integridade](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>Julho de 2020 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>Análise de Texto para contêiner de integridade – visualização de portão público

O Análise de Texto para o contêiner de integridade agora está em uma visualização de entrada pública, que permite extrair informações de textos não estruturados em idiomas em inglês em documentos clínicos, como: formulários de ingestão de pacientes, notas do médico, documentos de pesquisa e resumos de descarga. No momento, você não será cobrado por Análise de Texto para uso do contêiner de integridade.

O contêiner oferece os seguintes recursos:

* Reconhecimento de Entidade Nomeada
* Extração de relações
* Vinculação de entidade
* Negação

## <a name="may-2020"></a>Maio de 2020

### <a name="text-analytics-api-v3-general-availability"></a>Disponibilidade geral do API de Análise de Texto v3

A API de análise de texto v3 já está disponível para o público geral com as seguintes atualizações:

* Versão do modelo `2020-04-01`
* Novos [limites de dados](concepts/data-limits.md) para cada recurso
* [Suporte de idioma](language-support.md) atualizado para o [análise de sentimento (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md)
* Ponto de extremidade separado para vinculação de entidade 
* Nova categoria de entidade "address" no [reconhecimento de entidade nomeada (Ner) v3](how-tos/text-analytics-how-to-entity-linking.md).
* Novas subcategorias no NER V3:
   * Local-geográfico
   * Local-estrutural
   * Organização – troca de ações
   * Organização – médico
   * Esportes da organização
   * Cultura do evento
   * Evento-natural
   * Evento-esportes

As seguintes propriedades na resposta JSON foram adicionadas:
   * `SentenceText` em Análise de Sentimento
   * `Warnings` para cada documento 

Os nomes das seguintes propriedades na resposta JSON foram alterados, quando aplicável:

* `score` foi renomeado para `confidenceScore`
    * `confidenceScore` tem dois pontos decimais de precisão. 
* `type` foi renomeado para `category`
* `subtype` foi renomeado para `subcategory`

> [!div class="nextstepaction"]
> [Saiba mais sobre o API de Análise de Texto v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Visualização pública do API de Análise de Texto v 3.1
   * Nova Análise de Sentimento recurso – [mineração de opinião](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Novo `PII` filtro de domínio pessoal () para informações de integridade protegidas ( `PHI` ).

> [!div class="nextstepaction"]
> [Saiba mais sobre a versão prévia do API de Análise de Texto v 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Suporte do SDK para a visualização pública do API de Análise de Texto v3

Como parte da [versão unificada do SDK do Azure](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290), o sdk do API de análise de texto v3 agora está disponível como uma visualização pública para as seguintes linguagens de programação:
   * [C#](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-csharp&tabs=version-3)
   * [Python](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-python&tabs=version-3)
   * [JavaScript (Node.js)](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-javascript&tabs=version-3)
   * [Java](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-java&tabs=version-3)
   
> [!div class="nextstepaction"]
> [Saiba mais sobre o SDK do API de Análise de Texto v3](./quickstarts/client-libraries-rest-api.md?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Visualização pública de reconhecimento de entidade nomeada v3

Tipos de entidade adicionais agora estão disponíveis no serviço de visualização pública de reconhecimento de entidade nomeada (NER) v3 à medida que expandimos a detecção de entidades de informações gerais e pessoais encontradas no texto. Esta atualização introduz a [versão do modelo](concepts/model-versioning.md) `2020-02-01` , que inclui:

* Reconhecimento dos seguintes tipos de entidade geral (somente em inglês):
    * Persontype
    * Produto
    * Evento
    * Entidade geopolítica (GPE) como um subtipo no local
    * Habilidade

* Reconhecimento dos seguintes tipos de entidade de informações pessoais (somente em inglês):
    * Pessoa
    * Organização
    * Idade como um subtipo em quantidade
    * Data como um subtipo em DateTime
    * Email 
    * Número de telefone (somente EUA)
    * URL
    * Endereço IP

### <a name="october-2019"></a>Outubro de 2019

#### <a name="named-entity-recognition-ner"></a>NER (Reconhecimento de Entidade Nomeada)

* Um [novo ponto de extremidade](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii) para reconhecer tipos de entidade de informações pessoais (somente em inglês)

* Separe pontos de extremidade para [reconhecimento de entidade](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) e [vinculação de entidade](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking).

* [Versão do modelo](concepts/model-versioning.md) `2019-10-01` , que inclui:
    * Detecção expandida e categorização de entidades encontradas no texto. 
    * Reconhecimento dos seguintes novos tipos de entidade:
        * Número do telefone
        * Endereço IP

A vinculação de entidades dá suporte a inglês e espanhol. O suporte ao idioma NER varia de acordo com o tipo de entidade.

#### <a name="sentiment-analysis-v3-public-preview"></a>Análise de Sentimento v3 em versão prévia pública

* Um [novo ponto de extremidade](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment) para análise de sentimentos.
* [Versão do modelo](concepts/model-versioning.md) `2019-10-01` , que inclui:

    * Melhorias significativas na precisão e nos detalhes da categorização e da Pontuação de texto da API.
    * Rotulagem automática para diferentes sentimentos no texto.
    * Análise de sentimentos e saída em um nível de documento e frase. 

Ele dá suporte a Inglês ( `en` ), japonês (), `ja` chinês simplificado ( `zh-Hans` ), chinês tradicional ( `zh-Hant` ), francês ( `fr` ), italiano ( `it` ), espanhol (), holandês (), `es` `nl` Português ( `pt` ) e alemão () `de` e está disponível nas seguintes regiões: `Australia East` , `Central Canada` , `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` ,,,,,,,, e. 

> [!div class="nextstepaction"]
> [Saiba mais sobre o Análise de Sentimento v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Próximas etapas

* [O que é a API de Análise de Texto?](overview.md)  
* [Cenários de usuário de exemplo](text-analytics-user-scenarios.md)
* [Análise de sentimento](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Detecção de idioma](how-tos/text-analytics-how-to-language-detection.md)
* [Reconhecimento de entidade](how-tos/text-analytics-how-to-entity-linking.md)
* [Extração de frases-chave](how-tos/text-analytics-how-to-keyword-extraction.md)
