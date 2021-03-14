---
title: Instalar e executar contêineres do Docker para o API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Use os contêineres do Docker para o API de Análise de Texto executar o processamento de idioma natural, como análise de sentimentos, local.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
keywords: local, Docker, contêiner, análise de sentimentos, processamento de idioma natural
ms.openlocfilehash: ac82781ed4a05fbbca7f5b16edb0b5349dfc400a
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432053"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalar e executar contêineres da Análise de Texto

> [!NOTE]
> * O contêiner para detecção de Análise de Sentimento e idioma agora está disponível para o público geral. O contêiner de extração de frases-chave está disponível como uma visualização pública não restrita.
> * A vinculação de entidade e NER não estão disponíveis no momento como um contêiner.
> * Acessar o Análise de Texto para o contêiner de integridade requer um [formulário de solicitação](https://aka.ms/csgate). No momento, você não será cobrado pelo seu uso.
> * Os locais da imagem de contêiner podem ter sido alterados recentemente. Leia este artigo para ver o local atualizado para esse contêiner.

Contêineres permitem executar APIs de Análise de Texto no seu ambiente e são excelentes para seus requisitos específicos de segurança e governança de dados. Os contêineres de Análise de Texto fornecem processamento de idioma natural avançado sobre texto bruto e incluem três funções principais: análise de sentimentos, extração de frases-chave e detecção de idioma. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

> [!IMPORTANT]
> A conta gratuita é limitada a 5.000 transações por mês e apenas os tipos de  <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">preço</a> **gratuito** e padrão são válidos para contêineres. Para obter mais informações sobre taxas de solicitação de transação, consulte [limites de dados](../overview.md#data-limits).

## <a name="prerequisites"></a>Pré-requisitos

Para executar qualquer um dos contêineres de Análise de Texto, você deve ter o computador host e os ambientes de contêiner.

## <a name="preparation"></a>Preparação

Você deve cumprir os seguintes pré-requisitos antes de usar os contêineres de Análise de Texto:

|Obrigatório|Finalidade|
|--|--|
|Mecanismo do Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.| 
|Recurso de Análise de Texto |Para usar o contêiner, você precisará ter:<br><br>Um [recurso de análise de texto](../../cognitive-services-apis-create-account.md) do Azure com o [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)gratuito (F0) ou Standard (S). Você precisará obter a chave de API e o URI de ponto de extremidade associados navegando até a página de **ponto de extremidade e a chave** do recurso no portal do Azure. <br><br>**{Api_key}**: uma das duas chaves de recurso disponíveis. <br><br>**{ENDPOINT_URI}**: o ponto de extremidade para seu recurso. |

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve as especificações mínimas e recomendadas para os contêineres de Análise de Texto. Pelo menos 2 gigabytes (GB) de memória são necessários e cada núcleo da CPU deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido. As transações permitidas por seção (TPS) também são listadas.

|  | Especificações mínimas do host | Especificações de host recomendadas | TPS mínimo | TPS máximo|
|---|---------|-------------|--|--|
| **Detecção de idioma, extração de frases-chave**   | 1 núcleo, 2 GB de memória | 1 núcleo, 4 GB de memória |15 | 30|
| **Análise de Sentimento**   | 1 núcleo, 2 GB de memória | 4 núcleos, 8 GB de memória |15 | 30|
| **Análise de Texto para o documento/solicitação de integridade 1**   |  4 núcleos, 10 GB de memória | 6 núcleos, memória de 12 GB |15 | 30|
| **Análise de Texto para a integridade-10 documentos/solicitação**   |  6 núcleos, memória 16 GB | 8 núcleos, 20 GB de memória |15 | 30|

O núcleo da CPU e a memória correspondem às `--cpus` `--memory` configurações e, que são usadas como parte do `docker run` comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

As imagens de contêiner para Análise de Texto estão disponíveis no registro de contêiner da Microsoft.

# <a name="sentiment-analysis"></a>[Análise de Sentimento ](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extração de Frases-chave (versão prévia)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Detecção de Idioma](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Análise de Texto para integridade (versão prévia)](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run) com as configurações de cobrança necessárias.
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [Docker execute](https://docs.docker.com/engine/reference/commandline/run/) para executar os contêineres. O contêiner continuará a ser executado até que você o interrompa.

> [!IMPORTANT]
> * os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host. 
> * As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).
> * Os contêineres análise de sentimentos e detecção de idioma estão geralmente disponíveis. O contêiner de extração de frases-chave usa V2 da API e está em versão prévia.

# <a name="sentiment-analysis"></a>[Análise de Sentimento](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extração de Frases-chave (versão prévia)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Detecção de Idioma](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Análise de Texto para integridade (versão prévia)](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta baseadas em REST.

Use o host, `http://localhost:5000`, para as APIs do contêiner.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Solução de problemas

Se você executar o contêiner com uma [montagem](../text-analytics-resource-container-config.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem durante a inicialização ou execução do contêiner.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Cobrança

Os contêineres de Análise de Texto enviam informações de cobrança para Azure, usando um recurso _Análise de Texto_ na conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para baixar, instalar e executar os contêineres de Análise de Texto. Em resumo:

* O Análise de Texto fornece três contêineres do Linux para o Docker, encapsulando vários recursos:
   * *Análise de Sentimento*
   * *Extração de Frases-chave (versão prévia)* 
   * *Detecção de Idioma*
   * *Análise de Texto para integridade (versão prévia)*
* As imagens de contêiner são baixadas do registro de contêiner da Microsoft (MCR) ou do repositório de contêiner de visualização.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar as operações em contêineres de análise de texto especificando o URI do host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de serviços cognitivas não enviam dados do cliente (por exemplo, o texto que está sendo analisado) à Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](../text-analytics-resource-container-config.md) para configurações
* Consulte [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md) para resolver problemas relacionados à funcionalidade.
