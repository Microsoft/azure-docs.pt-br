---
title: Como instalar e executar contêineres - Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Como baixar, instalar e executar contêineres para Pesquisa Visual Computacional neste tutorial passo a passo.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: afccce5ca9101ed1e30f69264abae7ad85b4902b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564579"
---
# <a name="install-and-run-recognize-text-containers"></a>Instalar e executar os contêineres de Reconhecimento de Texto

A parte Reconhecimento de Texto da Pesquisa Visual Computacional também está disponível como um contêiner do Docker. Ela permite a você detectar e extrair texto impresso das imagens de vários objetos com diferentes superfícies e fundos, como recibos, cartazes e cartões de visita.  
> [!IMPORTANT]
> O contêiner de Reconhecimento de Texto atualmente funciona somente em inglês.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você deve cumprir os seguintes pré-requisitos antes de usar contêineres de Reconhecimento de Texto:

|Necessário|Finalidade|
|--|--|
|Mecanismo Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.| 
|Pesquisa Visual Computacional recurso |Para usar o contêiner, você precisará ter:<br><br>Um recurso de **Pesquisa Visual computacional** do Azure e a chave de API associada do URI do ponto de extremidade. Ambos os valores estão disponíveis nas páginas visão geral e chaves para o recurso e são necessários para iniciar o contêiner.<br><br>**{API_KEY}** : Uma das duas chaves de recurso disponíveis na página **chaves**<br><br>**{ENDPOINT_URI}** : O ponto de extremidade conforme fornecido na página **visão geral**|

## <a name="request-access-to-the-private-container-registry"></a>Solicitar acesso ao registro de contêiner privado

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve os núcleos de CPU e a memória mínimos e recomendados a serem alocados para cada contêiner do Reconhecimento de Texto.

| Contêiner | Mínimo | Recomendado |TPS<br>(Mínimo, máximo)|
|-----------|---------|-------------|--|
|Reconhecimento de Texto|1 núcleo, 8 GB de memória, 0,5 TPS|2 núcleos, 8 GB de memória, 1 TPS|0,5, 1|

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.
* TPS – transações por segundo

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

Há imagens de contêiner para Reconhecimento de Texto disponíveis. 

| Contêiner | Repositório |
|-----------|------------|
|Reconhecimento de Texto | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Use o comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner.


### <a name="docker-pull-for-the-recognize-text-container"></a>Docker pull para o contêiner de Reconhecimento de Texto

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run) com as configurações de cobrança necessárias. Há outros [exemplos](computer-vision-resource-container-config.md) do comando `docker run` disponíveis. 
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. O comando usa os seguintes parâmetros:

| Espaço reservado | Valor |
|-------------|-------|
|{API_KEY} | Essa chave é usada para iniciar o contêiner e está disponível na página chaves do `Cognitive Services` Azure.  |
|{ENDPOINT_URI} | Valor de URI do ponto de extremidade de cobrança. O exemplo é:`https://westus.api.cognitive.microsoft.com/vision/v2.0`|

Você precisa adicionar o `vision/v2.0` roteamento ao URI do ponto de extremidade, conforme mostrado no exemplo de BILLING_ENDPOINT_URI a seguir.

Substitua esses parâmetros por seus próprios valores no comando `docker run` de exemplo a seguir.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Esse comando:

* Executa um contêiner de reconhecimento da imagem de contêiner
* Aloca um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host. 

Há outros [exemplos](./computer-vision-resource-container-config.md#example-docker-run-commands) do comando `docker run` disponíveis. 

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta com base em REST. 

Use o host, `http://localhost:5000`, para APIs de contêiner.

### <a name="asynchronous-text-recognition"></a>Reconhecimento de texto assíncrono

Você pode usar as operações `POST /vision/v2.0/recognizeText` e `GET /vision/v2.0/textOperations/*{id}*` em conjunto para reconhecer assincronamente texto impresso em uma imagem, semelhante a como o serviço de Pesquisa Visual Computacional usa essas operações REST correspondentes. O contêiner de Reconhecimento de Texto reconhece apenas texto impresso, texto não manuscrito, neste momento, portanto, o parâmetro `mode` normalmente especificado para a operação de serviço de Pesquisa Visual Computacional é ignorado pelo contêiner de Reconhecimento de Texto.

### <a name="synchronous-text-recognition"></a>Reconhecimento de texto síncrono

Você pode usar a operação `POST /vision/v2.0/recognizeTextDirect` para, de forma síncrona, reconhecer texto impresso em uma imagem. Como essa operação é síncrona, o corpo da solicitação para essa operação é o mesmo que `POST /vision/v2.0/recognizeText` a operação, mas o corpo da resposta para essa operação é o mesmo que o retornado `GET /vision/v2.0/textOperations/*{id}*` pela operação.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Solução de problemas

Se você executar o contêiner com uma [montagem](./computer-vision-resource-container-config.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem durante a inicialização ou execução do contêiner. 


## <a name="billing"></a>Cobrança

Os contêineres de Reconhecimento de Texto enviam informações de cobrança para Azure, usando um recurso _Reconhecimento de Texto_ na conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para baixar, instalar e executar os contêineres de Reconhecimento de Texto. Em resumo:

* O Reconhecimento de Texto fornece um contêiner do Linux para o Docker, encapsulando o Reconhecimento de Texto.
* Imagens de contêiner são baixadas de um MCR (Registro de Contêiner da Microsoft) no Azure.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar as operações em contêineres de Reconhecimento de Texto especificando o URI do host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](computer-vision-resource-container-config.md) para configurações
* Examinar [Visão geral da Pesquisa Visual Computacional](Home.md) para saber mais sobre como reconhecer texto impresso e manuscrito  
* Veja a [API da Pesquisa Visual Computacional](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para obter detalhes sobre os métodos compatíveis com o contêiner.
* Veja as [Perguntas frequentes](FAQ.md) para resolver problemas relacionados à funcionalidade de Pesquisa Visual Computacional.
* Usar mais [Contêineres dos Serviços Cognitivos](../cognitive-services-container-support.md)
