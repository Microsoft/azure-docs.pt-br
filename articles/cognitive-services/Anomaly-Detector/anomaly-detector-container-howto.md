---
title: Instalar e executar contêineres do Docker para a API do detector de anomalias
titleSuffix: Azure Cognitive Services
description: Use os algoritmos da API do detector de anomalias para localizar anomalias em seus dados, localmente usando um contêiner do Docker.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbullwin
ms.custom: cog-serv-seo-aug-2020
keywords: no local, Docker, contêiner, streaming, algoritmos
ms.openlocfilehash: 70e5950f6577ce2cca2f28be070f3ba372d46a7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97862306"
---
# <a name="install-and-run-docker-containers-for-the-anomaly-detector-api"></a>Instalar e executar contêineres do Docker para a API do detector de anomalias 

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

Os contêineres permitem que você use a API do detector de anomalias em seu próprio ambiente. Contêineres são excelentes para especificar requisitos de segurança e governança de dados. Neste artigo, você aprenderá a baixar, instalar e executar um contêiner de detector de anomalias.

O detector de anomalias oferece um único contêiner do Docker para usar a API local. Use o contêiner para:
* Usar os algoritmos do detector de anomalias em seus dados
* Monitore dados de streaming e detecte anomalias à medida que ocorrem em tempo real.
* Detecte anomalias em todo o conjunto de dados como um lote. 
* Detectar pontos de alteração de tendência em seu conjunto de dados como um lote.
* Ajuste a sensibilidade do algoritmo de detecção de anomalias para se adequar melhor aos seus dados.

Para obter informações detalhadas sobre a API, consulte:
* [Saiba mais sobre o serviço de API do detector de anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você deve atender aos seguintes pré-requisitos antes de usar contêineres de detector de anomalias:

|Obrigatório|Finalidade|
|--|--|
|Mecanismo do Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.|
|Recurso de detector de anomalias |Para usar esses contêineres, será necessário ter:<br><br>Um recurso de _detector de anomalias_ do Azure para obter a chave de API e o URI de ponto de extremidade associados. Ambos os valores estão disponíveis nas páginas visão geral do **detector de anomalias** do portal do Azure e chaves e são necessários para iniciar o contêiner.<br><br>**{Api_key}**: uma das duas chaves de recurso disponíveis na página **chaves**<br><br>**{ENDPOINT_URI}**: o ponto de extremidade conforme fornecido na página **visão geral**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](../../iot-edge/index.yml). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve os núcleos e memória de CPU mínimos e recomendados para alocar para o contêiner do detector de anomalias.

| QPS (consultas por segundo) | Mínimo | Recomendadas |
|-----------|---------|-------------|
| 10 QPS | 4 núcleos, 1 GB de memória | 8 núcleos de memória de 2 GB |
| 20 QPS | 8 núcleos, 2 GB de memória | 16 núcleos de 4 GB de memória |

Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner.

| Contêiner | Repositório |
|-----------|------------|
| cognitiva-serviços-detector de anomalias | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Pull do Docker para o contêiner do detector de anomalias

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run) com as configurações de cobrança necessárias. Há outros [exemplos](anomaly-detector-container-configuration.md#example-docker-run-commands) do comando `docker run` disponíveis.
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. Consulte [coletando parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os `{ENDPOINT_URI}` `{API_KEY}` valores e.

[Exemplos](anomaly-detector-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Esse comando:

* Executa um contêiner de detector de anomalias da imagem de contêiner
* Aloca um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Executando vários contêineres no mesmo host

Se você pretende executar vários contêineres com portas expostas, execute cada contêiner com uma porta diferente. Por exemplo, execute o primeiro contêiner na porta 5000 e o segundo contêiner na porta 5001.

Substitua `<container-registry>` e `<container-name>` pelos valores dos contêineres que você usa. Eles não precisam estar no mesmo contêiner. Você pode ter o contêiner do detector de anomalias e o contêiner LUIS em execução no HOST juntos ou pode ter vários contêineres de detector de anomalias em execução.

Execute o primeiro contêiner na porta 5000.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Execute o segundo contêiner na porta 5001.


```bash
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cada contêiner seguinte deve estar em uma porta diferente.

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta baseadas em REST.

Use o host, http://localhost:5000, para as APIs do contêiner.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Solução de problemas

Se você executar o contêiner com uma [montagem](anomaly-detector-container-configuration.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem durante a inicialização ou execução do contêiner.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Cobrança

Os contêineres do detector de anomalias enviam informações de cobrança para o Azure, usando um recurso de _detector de anomalias_ em sua conta do Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](anomaly-detector-container-configuration.md).

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para baixar, instalar e executar contêineres de detector de anomalias. Em resumo:

* O detector de anomalias fornece um contêiner do Linux para o Docker, encapsulando a detecção de anomalias com o lote versus streaming, a inferência de intervalo esperada e o ajuste de sensibilidade.
* As imagens de contêiner são baixadas de um registro de contêiner do Azure privado dedicado para contêineres.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar operações em contêineres de detector de anomalias especificando o URI do host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de serviços cognitivas não enviam dados do cliente (por exemplo, os dados de série temporal que estão sendo analisados) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](anomaly-detector-container-configuration.md) para configurações
* [Implantar um contêiner de detector de anomalias nas instâncias de contêiner do Azure](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Saiba mais sobre o serviço de API do detector de anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)