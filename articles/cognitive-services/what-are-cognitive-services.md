---
title: O que são os Serviços Cognitivos do Azure?
titleSuffix: Azure Cognitive Services
description: Os Serviços Cognitivos tornam a IA acessível a todos os desenvolvedores sem a necessidade de aprendizado de máquina e experiência em ciência de dados. Você só precisa fazer uma chamada à API do aplicativo para adicionar a capacidade de ver (reconhecimento e pesquisa de imagem avançada), ouvir, falar, pesquisar e tomar decisões em seus aplicativos.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: serviços cognitivos, inteligência cognitiva, soluções cognitivas, serviços de IA, compreensão cognitiva, recursos cognitivos
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: c89131cc34d45ea94f3bb290ac11ec86f4b83be3
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587605"
---
# <a name="what-are-azure-cognitive-services"></a>O que são os Serviços Cognitivos do Azure?

Os Serviços Cognitivos do Azure são serviços baseados em nuvem com APIs REST e SDKs de biblioteca de clientes disponíveis para ajudar você a criar inteligência cognitiva em seus aplicativos. Você pode adicionar recursos cognitivos aos seus aplicativos sem precisar das habilidades de IA (inteligência artificial) ou de ciência de dados. Os Serviços Cognitivos do Azure compreendem vários serviços de IA que permitem criar soluções cognitivas que podem ver, ouvir, falar, entender e até tomar decisões.

## <a name="categories-of-cognitive-services"></a>Categorias dos Serviços Cognitivos

O catálogo de serviços cognitivos que fornecem compreensão cognitiva é categorizado em cinco pilares principais:

* Visão
* Fala
* Linguagem
* Decisão
* Search

As seções a seguir neste artigo fornecem uma lista de serviços que fazem parte desses cinco pilares.

## <a name="vision-apis"></a>APIs de visão

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Pesquisa Visual Computacional](./computer-vision/index.yml "Visual Computacional")|O serviço de Pesquisa Visual Computacional fornece acesso a algoritmos cognitivos avançados para processar imagens e retornar informações. Confira o [Guia de início rápido da Pesquisa Visual Computacional](./computer-vision/quickstarts-sdk/client-library.md) para começar a usar o serviço.|
|[Serviço de Visão Personalizada](./custom-vision-service/index.yml "Serviço de Visão Personalizada")|O Serviço de Visão Personalizada permite criar, implantar e aprimorar seus classificadores de imagens. Um classificador de imagens é um serviço de IA que aplica rótulos a imagens de acordo com as características visuais delas. |
|[Detecção Facial](./face/index.yml "Face")| O serviço de Detecção Facial fornece acesso a algoritmos avançados de detecção facial, habilitando a detecção e o reconhecimento de atributos faciais. Confira o [Guia de início rápido da Detecção Facial](./face/quickstarts/client-libraries.md) para começar a usar o serviço.|
|[Reconhecimento de Formulários](./form-recognizer/index.yml "Reconhecimento de Formulários")|O Reconhecimento de Formulários identifica e extrai pares chave-valor e dados de tabela de documentos de formulário e, em seguida, gera dados estruturados, incluindo as relações no arquivo original. Confira o [Guia de início rápido do Reconhecimento de Formulários](./form-recognizer/quickstarts/client-library.md) para começar.|
|[Video Indexer](../media-services/video-indexer/video-indexer-overview.md "Video Indexer")|O Video Indexer permite extrair insights de seus vídeos. Confira o [Guia de início rápido do Video Indexer](/azure/media-services/video-indexer/video-indexer-get-started) para começar.|

## <a name="speech-apis"></a>Speech APIs

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Serviço de Fala](./speech-service/index.yml "Serviço de Fala")|O serviço de fala adiciona recursos habilitados para fala aos aplicativos. O serviço de Fala inclui vários recursos, como conversão de texto em fala e de fala em texto, tradução de fala e muito mais.|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>APIs de Linguagem

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[LUIS (Reconhecimento vocal)](./luis/index.yml "Reconhecimento Vocal")|O LUIS (Reconhecimento Vocal) é um serviço de IA de conversação baseado em nuvem que aplica inteligência de machine learning personalizada em um texto de linguagem natural de conversação do usuário para prever o significado geral e extrair informações detalhadas relevantes. [Confira o guia de início rápido do LUIS](./luis/get-started-portal-build-app.md) para começar a usar o serviço.|
|[O QnA Maker](./qnamaker/index.yml "QnA Maker")|A QnA Maker permite que você crie um serviço de perguntas e respostas do seu conteúdo semiestruturado. [Confira o guia de início rápido do QnA Maker](./qnamaker/quickstarts/create-publish-knowledge-base.md) para começar a usar o serviço.|
|[Análise de Texto](./text-analytics/index.yml "Análise de texto")| A Análise de Texto oferece processamento de linguagem natural sobre texto bruto para análise de sentimento, extração de frases-chave e detecção de idioma. Confira o [Guia de início rápido da Análise de Texto](./text-analytics/quickstarts/client-libraries-rest-api.md) para começar a usar o serviço.|
|[Tradutor](./translator/index.yml "Tradutor")|O Tradutor fornece uma tradução de texto baseada em máquina quase em tempo real.|
| [Leitura Avançada](./immersive-reader/index.yml "Leitura Avançada") | A Leitura Avançada adiciona recursos de leitura e compreensão de tela aos seus aplicativos. Confira o [Guia de início rápido da Leitura Avançada](./immersive-reader/quickstarts/client-libraries.md) para começar a usar o serviço. |

## <a name="decision-apis"></a>APIs de decisão

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Detector de Anomalias](./anomaly-detector/index.yml "Detector de Anomalias") |O Detector de Anomalias permite que você monitore e detecte anormalidades em seus dados de série temporal. Confira o [Guia de início rápido do Detector de Anomalias](./anomaly-detector/quickstarts/client-libraries.md) para começar a usar o serviço.|
|[Content Moderator](./content-moderator/overview.md "Content Moderator")|O Content Moderator fornece monitoramento para um possível conteúdo ofensivo, indesejável e arriscado. Confira o [Guia de início rápido do Content Moderator](./content-moderator/client-libraries.md) para começar a usar o serviço.|
|[Assistente de Métricas](./metrics-advisor/index.yml) (versão prévia) | O Assistente de Métricas fornece detecção de anomalias personalizável em dados de série temporal multivariados, além de um portal da Web completo para ajudar você a usar o serviço. Confira o [Guia de início rápido do Assistente de Métricas](./metrics-advisor/quickstarts/rest-api-and-client-library.md) para começar a usar o serviço. |
|[Personalizador](./personalizer/index.yml "Personalizador")|O Personalizador permite escolher a melhor experiência a ser mostrada aos usuários, aprendendo com o comportamento deles em tempo real. Confira o [Guia de início rápido do Personalizador](./personalizer/quickstart-personalizer-sdk.md) para começar a usar o serviço.|

## <a name="search-apis"></a>APIs de pesquisa

> [!NOTE]
> Procurando a [Azure Cognitive Search](../search/index.yml)? Embora ele use os Serviços Cognitivos para algumas tarefas, é uma tecnologia de pesquisa diferente, que dá suporte a outros cenários.

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Pesquisa de Notícias do Bing](/azure/cognitive-services/bing-news-search/ "Pesquisa de Notícias do Bing")|A Pesquisa de Notícias do Bing retorna uma lista de artigos de notícias determinados como relevantes para a consulta do usuário.|
|[Pesquisa de Vídeo do Bing](/azure/cognitive-services/Bing-Video-Search/ "Pesquisa de Vídeo do Bing")|A Pesquisa de Vídeo do Bing retorna uma lista de vídeos determinados como relevantes para a consulta do usuário.|
|[Pesquisa na Web do Bing](./bing-web-search/index.yml "Pesquisa da Web do Bing")|A Pesquisa na Web do Bing retorna uma lista de resultados de pesquisa determinados como relevantes para a consulta do usuário.|
|[Sugestão Automática do Bing](/azure/cognitive-services/Bing-Autosuggest "Sugestão Automática do Bing")|A Sugestão Automática do Bing permite enviar um termo de consulta de pesquisa parcial ao Bing e recuperar uma lista de consultas sugeridas.|
|[Pesquisa Personalizada do Bing](/azure/cognitive-services/bing-custom-search "Pesquisa Personalizada do Bing")|A Pesquisa Personalizada do Bing permite que você crie experiências de pesquisa personalizada para os tópicos importantes para você.|
|[Pesquisa de Entidade do Bing](/azure/cognitive-services/bing-entities-search/ "Pesquisa de Entidade do Bing")|A Pesquisa de Entidade do Bing retorna informações sobre entidades que o Bing determina como relevantes para a consulta de um usuário.|
|[Pesquisa de Imagem do Bing](/azure/cognitive-services/bing-image-search "Pesquisa de Imagem do Bing")|A Pesquisa de Imagem do Bing retorna uma exibição de imagens determinadas como relevantes para a consulta do usuário.|
|[Pesquisa Visual do Bing](/azure/cognitive-services/bing-visual-search "Pesquisa Visual do Bing")|A Pesquisa Visual do Bing retorna insights sobre uma imagem, como imagens visualmente semelhantes, fontes de compras de produtos encontrados na imagem e pesquisas relacionadas.|
|[Pesquisa de Negócios Locais do Bing](/azure/cognitive-services/bing-local-business-search/ "Pesquisa de Negócios Locais do Bing")| A API de Pesquisa de Negócios Locais do Bing permite que seus aplicativos encontrem informações de contato e localização sobre locais e negócios com base em consultas de pesquisa.|
|[Verificação de Ortografia do Bing](/azure/cognitive-services/bing-spell-check/ "Verificação Ortográfica do Bing")|A Verificação Ortográfica do Bing permite fazer verificações de gramática contextual e ortográfica.|

## <a name="get-started-with-cognitive-services"></a>Introdução aos Serviços Cognitivos

Comece criando um recurso dos Serviços Cognitivos com inícios rápidos práticos usando os seguintes métodos:

* [Azure portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Portal do Azure")
* [CLI do Azure](cognitive-services-apis-create-account-cli.md?tabs=windows "CLI do Azure")
* [As bibliotecas de cliente do SDK do Azure](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [Modelos do Azure Resource Manager (ARM)](./create-account-resource-manager-template.md?tabs=portal "Modelos do Azure Resource Manager (ARM)")

## <a name="using-cognitive-services-in-different-development-environments"></a>Usando os Serviços Cognitivos em ambientes de desenvolvimento diferentes

Com o Azure e os Serviços Cognitivos, você tem acesso a várias opções de desenvolvimento, como:

* Ferramentas de automação e integração, como Aplicativos Lógicos e Power Automate.
* Opções de implantação, como Azure Functions e Serviço de Aplicativo. 
* Contêineres do Docker de Serviços Cognitivos para acesso seguro.
* Ferramentas como Apache Spark, Azure Databricks, Azure Synapse Analytics e Serviço de Kubernetes do Azure para cenários de Big Data. 

Para saber mais, confira [opções de desenvolvimento de Serviços Cognitivos](./cognitive-services-development-options.md).

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Usar os Serviços Cognitivos com segurança

Os Serviços Cognitivos do Azure fornecem um modelo de segurança em camadas, incluindo [autenticação](authentication.md "autenticação") por meio de credenciais do Azure Active Directory, uma chave de recurso válida e [redes virtuais do Azure](cognitive-services-virtual-networks.md "Redes Virtuais do Azure").

## <a name="containers-for-cognitive-services"></a>Contêineres para Serviços Cognitivos

 Os Serviços Cognitivos do Azure fornecem vários contêineres do Docker que permitem usar no local as mesmas APIs que estão disponíveis no Azure. O uso desses contêineres oferece a flexibilidade de aproximar os Serviços Cognitivos de seus dados para fins de conformidade, segurança ou por outros motivos operacionais. Saiba mais sobre [Contêineres de Serviços Cognitivos](cognitive-services-container-support.md "Contêineres de Serviços Cognitivos").

## <a name="regional-availability"></a>Disponibilidade regional

As APIs dos Serviços Cognitivos são hospedadas em uma rede cada vez maior de data centers gerenciados pela Microsoft. Encontre a disponibilidade regional de cada API na [lista de regiões do Azure](https://azure.microsoft.com/regions "Lista de regiões do Azure").

Procurando uma região ainda sem suporte? Informe-nos com uma solicitação de recurso em nosso [fórum do UserVoice](https://cognitive.uservoice.com/ "Fórum do UserVoice").

## <a name="supported-cultural-languages"></a>Linguagens culturais compatíveis

Os Serviços Cognitivos oferecem suporte a uma ampla variedade de linguagens culturais no nível do serviço. Você pode encontrar a disponibilidade de linguagem para cada API na [lista de linguagens compatíveis](language-support.md "lista de idiomas com suporte").

## <a name="certifications-and-compliance"></a>Certificações e conformidade

Os Serviços Cognitivas receberam certificações como a Certificação CSA STAR, FedRAMP Moderado e HIPAA BAA. Você pode [baixar](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "download") certificações para suas próprias auditorias e revisões de segurança.

Para entender a privacidade e o gerenciamento de dados, acesse a [Central de Confiabilidade](https://servicetrust.microsoft.com/ "Central de Confiabilidade").

## <a name="support"></a>Suporte

Os Serviços Cognitivos fornecem várias opções de suporte para ajudar você a avançar com a criação de aplicativos inteligentes. Os Serviços Cognitivos também têm uma grande comunidade de desenvolvedores que podem ajudar a responder às suas perguntas específicas. Para obter uma lista completa das opções disponíveis, confira [Opções de suporte e ajuda dos Serviços Cognitivos](cognitive-services-support-options.md "Opções de ajuda e suporte dos Serviços Cognitivos").

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta dos Serviços Cognitivos](cognitive-services-apis-create-account.md "Criar uma conta dos Serviços Cognitivos")
* [Novidades nos documentos dos Serviços Cognitivos](whats-new-docs.md "Novidades nos documentos dos Serviços Cognitivos")
* [Planejar e gerenciar custos dos Serviços Cognitivos](plan-manage-costs.md)
