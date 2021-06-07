---
title: Instalar e executar contêineres do Docker para o API de Detecção Facial
titleSuffix: Azure Cognitive Services
description: Use o contêiner do Docker para a API de Detecção Facial detectar e identificar faces humanas em imagens.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: aahi
keywords: local, Docker, contêiner, identificar
ms.openlocfilehash: 36cbd7bd24304871593b107f9b8ed9be02ce46de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706785"
---
# <a name="install-and-run-face-containers-preview"></a>Instalar e executar contêineres de face (visualização)

> [!IMPORTANT]
> O limite de usuários do contêiner de Detecção Facial foi atingido. No momento, não estamos aceitando novos aplicativos para o contêiner de Detecção Facial.

Os serviços cognitivas do Azure API de Detecção Facial fornecem um contêiner do Docker do Linux que detecta e analisa faces humanas em imagens. Ele também identifica atributos, que incluem pontos de referência de face, como narizs e olhos, sexo, idade e outros recursos faciais previstos por máquina. Além da detecção, a face pode verificar se duas faces na mesma imagem ou em imagens diferentes são as mesmas usando uma pontuação de confiança. A face também pode comparar rostos com um banco de dados para ver se já existe uma face semelhante ou idêntica. Ele também pode organizar faces semelhantes em grupos usando características visuais compartilhadas.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você deve atender aos seguintes pré-requisitos antes de usar os contêineres de serviço de face.

|Obrigatório|Finalidade|
|--|--|
|Mecanismo do Docker| O mecanismo do Docker deve ser instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> No Windows, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | Você precisa de uma compreensão básica dos conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner. Você também precisa de conhecimento dos `docker` comandos básicos.| 
|Recurso facial |Para usar o contêiner, você deve ter:<br><br>Um recurso de **face** do Azure e a chave de API associada e o URI do ponto de extremidade. Os dois valores estão disponíveis nas páginas **visão geral** e **chaves** do recurso. Eles são necessários para iniciar o contêiner.<br><br>**{Api_key}**: uma das duas chaves de recurso disponíveis na página **chaves**<br><br>**{ENDPOINT_URI}**: o ponto de extremidade conforme fornecido na página **visão geral**

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve os núcleos de CPU mínimos e recomendados e a memória a ser alocada para cada contêiner de serviço de face.

| Contêiner | Mínimo | Recomendadas | Transações por segundo<br>(Mínimo, máximo)|
|-----------|---------|-------------|--|
|Detecção Facial | 1 núcleo, 2 GB de memória | 1 núcleo, 4 GB de memória |10, 20|

* Cada núcleo deve ter pelo menos 2,6 GHz ou mais rápido.
* Transações por segundo (TPS).

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com docker pull

As imagens de contêiner para o serviço de face estão disponíveis. 

| Contêiner | Repositório |
|-----------|------------|
| Detecção Facial | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker pull para o contêiner de Detecção Facial

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run) com as configurações de cobrança necessárias. Há outros [exemplos](./face-resource-container-config.md#example-docker-run-commands) do comando `docker run` disponíveis. 
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com a execução do Docker

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. Consulte [coletando parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os `{ENDPOINT_URI}` `{API_KEY}` valores e.

[Exemplos](face-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Esse comando:

* Executa um contêiner facial a partir da imagem de contêiner.
* Aloca um núcleo de CPU e 4 GB de memória.
* Expõe a porta TCP 5000 e aloca um pseudo TTY para o contêiner.
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host. 

Há outros [exemplos](./face-resource-container-config.md#example-docker-run-commands) do comando `docker run` disponíveis. 

> [!IMPORTANT]
> As `Eula` `Billing` Opções, e `ApiKey` devem ser especificadas para executar o contêiner ou o contêiner não será iniciado. Para mais informações, consulte [Faturamento](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta baseadas em REST. 

Use o host, `http://localhost:5000`, para as APIs do contêiner.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Solução de problemas

Se você executar o contêiner com uma [montagem](./face-resource-container-config.md#mount-settings) de saída e o registro em log estiver habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem enquanto você inicia ou executa o contêiner.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Cobrança

Os contêineres de serviço de face enviam informações de cobrança para o Azure usando um recurso facial em sua conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](./face-resource-container-config.md).

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho sobre como baixar, instalar e executar os contêineres de serviço de face. Em resumo:

* As imagens de contêiner são baixadas do registro de contêiner do Azure.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar operações em contêineres de serviço de face especificando o URI do host do contêiner.
* Você deve especificar informações de cobrança ao criar uma instância de um contêiner.

> [!IMPORTANT]
> Os contêineres de serviços cognitivas não são licenciados para serem executados sem serem conectados ao Azure para medição. Os clientes devem habilitar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres dos Serviços Cognitivos não enviam dados do cliente, como imagem ou texto que está sendo analisado, para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Para definições de configuração, consulte [configurar contêineres](face-resource-container-config.md).
* Para saber mais sobre como detectar e identificar rostos, confira [visão geral da face](Overview.md).
* Para obter informações sobre os métodos com suporte pelo contêiner, consulte a [API de detecção facial](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Para usar mais contêineres de serviços cognitivas, consulte [contêineres de serviços cognitivas](../cognitive-services-container-support.md).
