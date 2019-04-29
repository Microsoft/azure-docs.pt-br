---
title: Instalar e executar contêineres
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Como baixar, instalar e executar contêineres para Análise de Texto neste tutorial passo a passo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: e0e8b9f767376db8028a3ac4a2d8659bab69268b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829949"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalar e executar contêineres da Análise de Texto

Os contêineres da Análise de Texto fornecem processamento avançado em idioma natural sobre texto bruto e incluem três funções principais: análise de sentimento, extração de frases-chave e detecção de idioma. Atualmente, não há suporte para vinculação de entidade em um contêiner. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para executar qualquer um dos contêineres de análise de texto, você deve ter os ambientes de computador e o contêiner de host.

## <a name="preparation"></a>Preparação

Você deve cumprir os seguintes pré-requisitos antes de usar os contêineres de Análise de Texto:

|Obrigatório|Finalidade|
|--|--|
|Mecanismo Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.| 
|`Cognitive Services` recurso |Para usar o contêiner, você precisará ter:<br><br>Um [ _os serviços Cognitivos_ ](text-analytics-how-to-access-key.md) recursos do Azure para obter a chave de cobrança associada e o URI de ponto de extremidade cobrança. Ambos os valores estão disponíveis nas páginas de visão geral de serviços Cognitivos e chaves do portal do Azure e são necessários para iniciar o contêiner. Você precisará adicionar o `text/analytics/v2.0` roteamento para o URI do ponto de extremidade, conforme mostrado no exemplo a seguir de BILLING_ENDPOINT_URI.<br><br>**{BILLING_KEY}**: chave do recurso<br><br>**{BILLING_ENDPOINT_URI}**: exemplo de URI de terminal é: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve os núcleos de CPU mínimos e recomendados, pelo menos 2,6 gigahertz (GHz) ou mais rápidos, bem como a memória, em gigabytes (GB), para alocar para cada contêiner de Análise de Texto.

| Contêiner | Mínimo | Recomendadas | TPS<br>(No mínimo, máximo)|
|-----------|---------|-------------|--|
|Extração de Frases-Chave | 1 núcleo, 2 GB de memória | 1 núcleo, 4 GB de memória |15, 30|
|Detecção de Idioma | 1 núcleo, 2 GB de memória | 1 núcleo, 4 GB de memória |15, 30|
|Análise de Sentimento | 1 núcleo, 2 GB de memória | 1 núcleo, 4 GB de memória |15, 30|

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.
* TPS - transações por segundo

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

Imagens de contêiner para Análise de Texto estão disponíveis no Registro de Contêiner da Microsoft. 

| Contêiner | Repositório |
|-----------|------------|
|Extração de Frases-Chave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Detecção de Idioma | `mcr.microsoft.com/azure-cognitive-services/language` |
|Análise de Sentimento | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Use o [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner do registro de contêiner do Microsoft.

Para obter uma descrição completa de marcas disponíveis para os contêineres de Análise de Texto, confira o seguintes contêineres no Hub do Docker:

* [Extração de Frases-chave](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Detecção de Idioma](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Análise de Sentimento](https://go.microsoft.com/fwlink/?linkid=2018654)

Use o comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner.


### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker pull para o contêiner de extração de frases-chave

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>Docker pull para o contêiner de detecção de idioma

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>Docker pull para contêiner de detecção de sentimento

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run) com as configurações de cobrança necessárias. Há outros [exemplos](../text-analytics-resource-container-config.md#example-docker-run-commands) do comando `docker run` disponíveis. 
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar qualquer um dos três contêineres. O comando usa os seguintes parâmetros:

| Placeholder | Value |
|-------------|-------|
|{BILLING_KEY} | Essa chave é usada para iniciar o contêiner e está disponível no portal do Azure `Cognitive Services` página chaves.  |
|{BILLING_ENDPOINT_URI} | O valor do URI de ponto de extremidade cobrança está disponível no Azure `Cognitive Services` página de visão geral. <br><br>Exemplo:<br>`Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

Você precisará adicionar o `text/analytics/v2.0` roteamento para o URI do ponto de extremidade, conforme mostrado no exemplo anterior BILLING_ENDPOINT_URI.

Substitua esses parâmetros por seus próprios valores no comando `docker run` de exemplo a seguir.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Esse comando:

* Executa um contêiner de frases-chave da imagem de contêiner
* Aloca um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host. 

Há outros [exemplos](../text-analytics-resource-container-config.md#example-docker-run-commands) do comando `docker run` disponíveis. 

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta com base em REST. 

Use o host, `https://localhost:5000`, para APIs de contêiner.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>solução de problemas

Se você executar o contêiner com uma [montagem](../text-analytics-resource-container-config.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem durante a inicialização ou execução do contêiner. 

## <a name="billing"></a>Cobrança

O envio de contêineres de análise de texto cobrança informações do Azure, usando um _dos serviços Cognitivos_ recurso em sua conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para baixar, instalar e executar os contêineres de Análise de Texto. Em resumo:

* A Análise de Texto fornece três contêineres do Linux para o Docker, encapsulando a Extração de Frases-chave, Detecção de Idioma e Análise de Sentimento.
* Imagens de contêiner são baixadas de um MCR (Registro de Contêiner da Microsoft) no Azure.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar as operações em contêineres de análise de texto especificando o URI do host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](../text-analytics-resource-container-config.md) para configurações
* Consulte [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md) para resolver problemas relacionados à funcionalidade.

