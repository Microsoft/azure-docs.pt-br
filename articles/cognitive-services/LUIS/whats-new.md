---
title: Novidades - reconhecimento vocal (LUIS)
description: Este artigo é atualizado regularmente com notícias sobre a API de reconhecimento vocal dos Serviços Cognitivos do Azure.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 01/21/2021
ms.openlocfilehash: 5fdde81d31eae4c50b86a5553742f9b599f32505
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693588"
---
# <a name="whats-new-in-language-understanding"></a>Novidades sobre reconhecimento vocal

Conheça o que há de novo no serviço. Esses itens incluem notas sobre a versão, vídeos, postagens no blog e outros tipos de informações. Marque esta página para manter-se atualizado quanto ao serviço.

## <a name="release-notes"></a>Notas de versão

### <a name="january-2021"></a>Janeiro de 2021

* Agora, a API de previsão V3 dá suporte à [API de Verificação Ortográfica do Bing](luis-how-to-publish-app.md#spelling-correction).
* Os portais regionais (au.luis.ai e eu.luis.ai) foram consolidados em um só portal e uma só URL. Se estiver usando um desses portais, você será automaticamente redirecionado para luis.ai.

### <a name="december-2020"></a>Dezembro de 2020

* Os usuários do LUIS deverão [migrar para um recurso de criação de LUIS](luis-migration-authoring.md)
* Novos [pontos de extremidade de avaliação](luis-how-to-batch-test.md#batch-testing-using-the-rest-api) que permitem enviar testes em lotes usando a API REST e obter resultados precisos para suas intenções e entidades. Disponível do Ponto de Extremidade do LUIS v3.0-preview em diante.

### <a name="june-2020"></a>Junho de 2020

* SDK [Preview 3.0 Authoring](luis-migration-authoring-entities.md) –
    * Versão 3.2.0-preview.3 – [.NET – NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)
    * Versão 4.0.0-preview.3 – [JS – NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)
* Aplicar práticas de DevOps com LUIS
    * Conceitos
        * [Práticas de DevOps para LUIS](luis-concept-devops-sourcecontrol.md)
        * [Fluxos de trabalho de integração contínua e entrega contínua para DevOps do LUIS](luis-concept-devops-automation.md)
        * [Teste para DevOps do LUIS](luis-concept-devops-testing.md)
    * Como fazer
        * [Aplicar DevOps ao desenvolvimento de aplicativo do LUIS usando GitHub Actions](luis-how-to-devops-with-github.md)
    * [Repositório do GitHub de código completo](https://github.com/Azure-Samples/LUIS-DevOps-Template)

### <a name="may-2020---build"></a>Maio de 2020 - //Build

* Liberado como **disponibilidade geral** (GA):
    * [Contêiner de reconhecimento vocal](luis-container-howto.md)
    * Portal de visualização promovido a [portal atual](https://www.luis.ai), portal [anterior](https://previous.luis.ai) ainda disponível
    * Nova experiência de criação e rotulação de entidade de machine learning
    * [Processo de atualização](migrate-from-composite-entity.md) de entidades compostas e simples para entidades de machine learning
    * [Configuração](how-to-application-settings-portal.md) de suporte para normalização de variantes de palavras
* Visualização de alterações da API de criação
    * Esquema de aplicativo 7.x para entidades de machine learning aninhadas
    * [Migração para recurso necessário](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* Novos recursos para desenvolvedores
    * [Ferramentas de integração contínua](developer-reference-resource.md#continuous-integration-tools)
    * Workshop: conheça as melhores práticas para [_Reconhecimento Vocal Natural_ (NLU) usando o LUIS](developer-reference-resource.md#workshops)
* [Chaves gerenciadas pelo cliente](luis-encryption-of-data-at-rest.md): criptografe todos os dados que você usa no LUIS com sua própria chave
* [AI Show](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding) (vídeo): veja os novos recursos no LUIS



### <a name="march-2020"></a>Março de 2020

* Agora o TLS 1.2 é obrigatório para todas as solicitações HTTP a este serviço. Para saber mais, confira [Segurança nos Serviços Cognitivos do Azure](../cognitive-services-security.md).

### <a name="november-4-2019---ignite"></a>4 de novembro de 2019 - Ignite

* Vídeo - [Modelos avançados de compreensão de linguagem natural (NLU) usando LUIS e Serviços Cognitivos do Azure | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Produtividade maior do desenvolvedor
    * Disponibilidade geral de nosso [ponto de extremidade de previsão v3](luis-migration-api-v3.md).
    * Capacidade de importar e exportar aplicativos com o formato `.lu` ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). Isso abre o caminho para um processo eficaz de CI/CD.
* Expansão de idioma
    * [Árabe e hindi](luis-language-support.md) na versão prévia pública.
* Modelos predefinidos
    * [Domínios predefinidos](luis-reference-prebuilt-domains.md) agora com disponibilidade geral (GA)
    * [Entidades predefinidas](luis-reference-prebuilt-entities.md#japanese-entity-support) de japonês - idade, moeda, número e porcentagem não são compatíveis com a v3.
    * [Entidades predefinidas](luis-reference-prebuilt-entities.md#italian-entity-support) de italiano - idade, moeda, dimensão, número e resolução de porcentagem alteradas na v2.
* Experiência avançada do usuário no [portal preview.luis.ai](https://preview.luis.ai) - experiência de rotulagem reformulada para permitir compilação e depuração de modelos complexos. Experimente os tutoriais do portal de visualização:
    * [Somente intenção](tutorial-intents-only.md)
    * [Entidade divisível de machine learning](tutorial-machine-learned-entity.md)
* Recursos avançados de reconhecimento vocal - [criando modelos de linguagem sofisticados](luis-concept-entity-types.md) com menos esforço.
* Defina os recursos de aprendizado de máquina no nível de modelo e habilite os modelos a serem usados como sinais para outros modelos. Por exemplo, usando entidades como recursos para intenções e para outras entidades.
* [Limites](luis-limits.md) novos e expandidos - aumento do máximo de listas de frases e do total de frases, novos limites de modelo como recurso
* Extraia informações de texto no formato de estrutura de hierarquia profunda, tornando os aplicativos de conversa mais poderosos.

    ![imagem de entidade de machine learning](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 de setembro de 2019

* Recurso de criação do Azure - [migrar agora](luis-migration-authoring.md).
    * 500 aplicativos por recurso do Azure
    * 100 versões por aplicativo
* Suporte para turco para entidades predefinidas
* Suporte para italiano para datetimeV2

### <a name="july-23-2019"></a>23 de julho de 2019

* Atualização dos [reconhecedores de texto](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) para 1.2.3
    * Reconhecedores de idade, temperatura, dimensão e moeda em italiano.
    * Melhoria no reconhecimento de feriados em inglês para calcular corretamente as datas baseadas no feriado de ação de graças.
    * Melhorias no DateTime em francês para reduzir os falsos positivos de entidades que não são de data e hora.
    * Suporte para ano calendário/escolar/fiscal e acrônimos em DateRange em inglês.
    * Reconhecimento aprimorado de PhoneNumber em chinês e japonês.
    * Suporte aprimorado para NumberRange em inglês.
    * Aprimoramentos no desempenho.

### <a name="june-24-2019"></a>24 de junho de 2019

* [Entidade predefinida OrdinalV2](luis-reference-prebuilt-ordinal-v2.md) para dar suporte para ordenação, como próximo, anterior e último. Somente cultura em inglês.

### <a name="may-6-2019---build-conference"></a>6 de maio de 2019 - //Conferência Build

Os seguintes recursos foram lançados na Conferência Build 2019:

* [Visualização do guia de migração da API V3](luis-migration-api-v3.md)
* [Painel de análise aprimorado](luis-how-to-use-dashboard.md)
* [Domínios predefinidos aprimorados](luis-reference-prebuilt-domains.md)
* [Entidades de lista dinâmica](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Entidades externas](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogs

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>vídeos

### <a name="2019-ignite-videos"></a>Vídeos do Ignite 2019

[Modelos avançados de compreensão de linguagem natural (NLU) usando LUIS e Serviços Cognitivos do Azure | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>Vídeos da Build 2019

[Como usar a IA de conversa do Azure para preparar sua empresa para a próxima geração](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Atualizações de serviço

[Comunicados de atualização do Azure para os Serviços Cognitivos](https://azure.microsoft.com/updates/?product=cognitive-services)
