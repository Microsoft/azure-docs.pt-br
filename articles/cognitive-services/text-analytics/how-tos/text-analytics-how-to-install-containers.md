---
title: Instalar e executar contêineres do Docker para o API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Use os contêineres do Docker para a API de Análise de Texto para executar o processamento de idioma natural, como análise de sentimento, no local.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: aahi
keywords: local, Docker, contêiner, análise de sentimento, processamento de linguagem natural
ms.openlocfilehash: 012e725e31097af5af634a1aba7693048c4c6b3e
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277463"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalar e executar contêineres da Análise de Texto

> [!NOTE]
> * O contêiner para Análise de Sentimento e detecção de idioma agora está em disponibilidade geral. O contêiner de extração de frase principal está disponível como versão prévia pública livre.
> * A vinculação de entidade e o NER (Reconhecimento de Entidade Nomeada) não estão disponíveis como contêiner no momento.
> * Para acessar a Análise de Texto para o contêiner de integridade, é preciso um [formulário de solicitação](https://aka.ms/csgate). Atualmente o uso não é cobrado.
> * Os locais da imagem de contêiner podem ter sido alterados recentemente. Leia este artigo para ver a localização atualizada do contêiner.

Contêineres permitem executar APIs de Análise de Texto no seu ambiente e são excelentes para seus requisitos específicos de segurança e governança de dados. Os contêineres da Análise de Texto realizam processamento avançado em idioma natural sobre texto bruto, com três funções principais: análise de sentimento, extração de frases-chave e detecção de idioma. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

> [!IMPORTANT]
> A conta gratuita é limitada a cinco mil transações por mês, e apenas os <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">tipos de preço</a> **Gratuito** e **Standard** são válidos para contêineres. Veja mais informações sobre as taxas de solicitação de transação em [limites de dados](../concepts/data-limits.md).

## <a name="prerequisites"></a>Pré-requisitos

Para executar qualquer um dos contêineres de Análise de Texto, você deve ter o computador host e os ambientes de contêiner.

## <a name="preparation"></a>Preparação

Você deve cumprir os seguintes pré-requisitos antes de usar os contêineres de Análise de Texto:

|Obrigatório|Finalidade|
|--|--|
|Mecanismo do Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.| 
|Recurso de Análise de Texto |Para usar o contêiner, você precisará ter:<br><br>Um [recurso de Análise de Texto](../../cognitive-services-apis-create-account.md) do Azure com o [tipo de preço Gratuito (F0) ou Standard (S)](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Você precisará obter a chave de API e o URI de ponto de extremidade associado, que aparecem na página **Chave e ponto de extremidade** do recurso no portal do Azure. <br><br>**{API_KEY}** : uma das duas chaves de recursos disponíveis. <br><br>**{ENDPOINT_URI}** : o ponto de extremidade do recurso. |

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

Se você usar a Análise de Texto para o contêiner de integridade, a confirmação de [IA responsável](https://docs.microsoft.com/legal/cognitive-services/text-analytics/transparency-note-health) (RAI) também deverá estar presente com o valor de `accept`.

## <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve as especificações mínimas e recomendadas para os contêineres de Análise de Texto. Pelo menos 2 GB de memória são necessários, e cada núcleo da CPU deve ter pelo menos 2,6 GHz ou mais rápido. A lista também mostra as transações permitidas por seção (TPS).

|  | Especificações mínimas do host | Especificações recomendadas do host | TPS mínimo | TPS máximo|
|---|---------|-------------|--|--|
| **Detecção de idioma, extração de frases-chave**   | 1 núcleo, 2 GB de memória | 1 núcleo, 4 GB de memória |15 | 30|
| **Análise de Sentimento**   | 1 núcleo, 2 GB de memória | 4 núcleos, 8 GB de memória |15 | 30|
| **Análise de Texto para integridade – 1 documento/solicitação**   |  4 núcleos, 10 GB de memória | 6 núcleos, 12 GB de memória |15 | 30|
| **Análise de Texto para integridade – 10 documentos/solicitação**   |  6 núcleos, 16 GB de memória | 8 núcleos, 20 GB de memória |15 | 30|

Os núcleos de CPU e a memória correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

Imagens de contêiner para Análise de Texto estão disponíveis no Microsoft Container Registry.

# <a name="sentiment-analysis"></a>[Análise de Sentimento](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extração de frases-chave (versão prévia)](#tab/keyphrase)

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

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar os contêineres. O contêiner continua a ser executado até que você o pare.

> [!IMPORTANT]
> * os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host. 
> * As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).
> * Os contêineres de análise de sentimento e de detecção de idioma estão em disponibilidade geral. O contêiner de extração de frases-chave usa a v2 da API e está em versão prévia.

# <a name="sentiment-analysis"></a>[Análise de Sentimento](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extração de frases-chave (versão prévia)](#tab/keyphrase)

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

* A Análise de Texto oferece três contêineres do Linux para o Docker, encapsulando vários recursos:
   * *Análise de Sentimento*
   * *Extração de frases-chave (versão prévia)* 
   * *Detecção de Idioma*
   * *Análise de Texto para integridade (versão prévia)*
* As imagens de contêiner são baixadas no MCR (Microsoft Container Registry) ou na versão prévia do repositório de contêineres.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar as operações em contêineres de análise de texto especificando o URI do host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](../text-analytics-resource-container-config.md) para configurações
* Consulte [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md) para resolver problemas relacionados à funcionalidade.
