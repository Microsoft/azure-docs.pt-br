---
title: Como instalar e executar o contêiner para o reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Saiba como usar o contêiner do Reconhecimento de Formulários para analisar dados de formulário e de tabela.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: d03abee7c7a4adb65e1d6146501ee5b7d9e1534c
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348667"
---
# <a name="install-and-run-form-recognizer-containers"></a>Instalar e executar contêineres do Reconhecimento de Formulários

O Reconhecimento de Formulários do Azure aplica tecnologia de aprendizado de máquina para identificar e extrair pares chave-valor e tabelas de formulários. Ele associa valores e entradas de tabelas a pares chave-valor e, em seguida, gera dados estruturados que incluem as relações no arquivo original. 

Para reduzir a complexidade e integrar facilmente um modelo de Reconhecimento de Formulários personalizado ao seu processo de automação de fluxo de trabalho ou a outro aplicativo, você pode chamar o modelo usando uma API REST simples. Somente cinco documentos de formulário (ou um formulário vazio e dois formulários preenchidos) são necessários, para que você possa obter resultados de forma rápida, precisa e adaptada ao seu conteúdo específico. Não é necessária intervenção manual intensa nem ampla experiência em ciência de dados. E não é necessária rotulagem nem anotação de dados.

|Função|Recursos|
|-|-|
|Reconhecimento de Formulários| <li>Processa arquivos PDF, PNG e JPG<li>Treina modelos personalizados com um mínimo de cinco formas do mesmo layout <li>Extrai pares chave-valor e informações de tabela <li>Usa o recurso de Reconhecimento de Texto da API de Pesquisa Visual Computacional dos Serviços Cognitivos do Azure para detectar e extrair texto impresso de imagens em formulários<li>Não exige anotação nem rotulagem|

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de usar contêineres de Reconhecimento de Formulários, é necessário atender aos seguintes pré-requisitos:

|Obrigatório|Finalidade|
|--|--|
|Mecanismo Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> No Windows, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como Registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.|
|A CLI do Azure| Instale a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) no host.|
|Recurso da API de Pesquisa Visual Computacional| Para processar imagens e documentos digitalizados, é necessário ter um recurso da Pesquisa Visual Computacional. Acesse o recurso de Reconhecimento de Texto como um recurso do Azure (API REST ou SDK) ou como um *contêiner* do [cognitive-services-recognize-text](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull). Os valores de cobrança normais se aplicam. <br><br>Transmita a chave de API e os pontos de extremidade para seu recurso de Pesquisa Visual Computacional (contêiner de nuvem do Azure ou serviços cognitivas). Use esta chave de API e o ponto de extremidade como **{COMPUTER_VISION_API_KEY}** e **{COMPUTER_VISION_ENDPOINT_URI}** .<br><br> Se você usar o contêiner *cognitive-services-recognize-text*, certifique-se de que:<br><br>A chave da Pesquisa Visual Computacional para o contêiner do Reconhecimento de Formulários seja a chave especificada no comando `docker run` da Pesquisa Visual Computacional para o contêiner do *cognitive-services-recognize-text*.<br>O ponto de extremidade de cobrança é o ponto de extremidade do contêiner (por exemplo, `https://localhost:5000`). Se você usar os contêineres da Pesquisa Visual Computacional e do Reconhecimento de Formulários juntos no mesmo host, eles não poderão ser iniciados com a porta padrão *5000*.  |
|Recurso do Reconhecimento de Formulários |Para usar esses contêineres, é necessário ter:<br><br>Um recurso do reconhecedor do Azure **Form** para obter a chave de API e o URI de ponto de extremidade associados. Ambos os valores estão disponíveis nas páginas visão geral e chaves do reconhecedor do portal do Azure **Form** e os dois valores são necessários para iniciar o contêiner.<br><br>**{FORM_RECOGNIZER_API_KEY}** : Uma das duas chaves de recurso disponíveis na página chaves<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}** : O ponto de extremidade conforme fornecido na página Visão geral|

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao Registro de contêiner

Primeiro, é necessário preencher e enviar o [formulário de solicitação de acesso a Contêineres do Reconhecimento de Formulários dos Serviços Cognitivos](https://aka.ms/FormRecognizerRequestAccess) para solicitar acesso ao contêiner. Fazer isso também fará sua inscrição na Pesquisa Visual Computacional. Você não precisa se inscrever no formulário de solicitação da Pesquisa Visual Computacional separadamente. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

Os núcleos de CPU e a memória mínimos e recomendados a serem alocados para cada contêiner do Reconhecimento de Formulários são descritos na tabela a seguir:

| Contêiner | Mínimo | Recomendado |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 núcleos, 4 GB de memória | 4 núcleos, 8 GB de memória |

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.
* TPS – transações por segundo
* Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

> [!Note]
> Os valores mínimos e recomendados se baseiam nos limites do Docker e *não* nos recursos do computador host.

## <a name="get-the-container-image-with-the-docker-pull-command"></a>Obtenha a imagem de contêiner com o comando docker pull

Imagens de contêiner para o Reconhecimento de Formulários estão disponíveis no seguinte repositório:

| Contêiner | Repositório |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

Se você pretende usar o [contêiner](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull) do `cognitive-services-recognize-text` em vez do serviço de Reconhecimento de Formulários, use o comando `docker pull` com o nome do contêiner correto: 

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Docker pull para o contêiner do Reconhecimento de Formulários

#### <a name="form-recognizer"></a>Reconhecimento de Formulários

Para obter o contêiner do Reconhecimento de Formulários, use o seguinte comando:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-by-using-the-docker-run-command) com as configurações de cobrança necessárias, mas não utilizadas. Há outros [exemplos](form-recognizer-container-configuration.md#example-docker-run-commands) do comando `docker run` disponíveis.
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Execute o contêiner usando o comando docker run

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar qualquer um dos três contêineres. O comando usa os seguintes parâmetros:

| Placeholder | Valor |
|-------------|-------|
|{FORM_RECOGNIZER_API_KEY} | Essa chave é usada para iniciar o contêiner. Ela está disponível na página **Chaves de Reconhecimento de Formulários** do portal do Azure.  |
|{FORM_RECOGNIZER_ENDPOINT_URI} | O valor de URI do ponto de extremidade de cobrança está disponível na página **Visão Geral do Reconhecimento de Formulários** do portal do Azure.|
|{COMPUTER_VISION_API_KEY}| A chave está disponível na página **Chaves de API de Pesquisa Visual Computacional** do portal do Azure.|
|{COMPUTER_VISION_ENDPOINT_URI}|O ponto de extremidade de cobrança. Se você estiver usando um recurso baseado em nuvem da Pesquisa Visual Computacional, o valor do URI estará disponível na página **Visão Geral da API de Pesquisa Visual Computacional** do portal do Azure. Se você estiver usando um contêiner do `cognitive-services-recognize-text`, use a URL do ponto de extremidade de cobrança passada para o contêiner no comando `docker run`.|

Substitua esses parâmetros por seus próprios valores no comando `docker run` de exemplo a seguir.

### <a name="form-recognizer"></a>Reconhecimento de Formulários

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Esse comando:

* Executa um contêiner do Reconhecimento de Formulários na imagem de contêiner.
* Aloca 2 núcleos de CPU e 8 GB (gigabytes) de memória.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.
* Monta um volume /input e /output no contêiner.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Executar contêineres separados como comandos de execução do docker separados

Para a combinação de Reconhecimento de Formulários e Reconhecimento de Texto hospedada localmente no mesmo host, use os dois comandos de exemplo da CLI do Docker a seguir:

Execute o primeiro contêiner na porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Execute o segundo contêiner na porta 5001.

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Cada contêiner seguinte deve estar em uma porta diferente. 

### <a name="run-separate-containers-with-docker-compose"></a>Executar contêineres separados com o Docker Compose

Para a combinação de Reconhecimento de Formulários e Reconhecimento de Texto hospedada localmente no mesmo host, consulte o exemplo de arquivo YAML do Docker Compose a seguir. A `{COMPUTER_VISION_API_KEY}` do Reconhecimento de Texto precisa ser a mesma para os contêineres `formrecognizer` e `ocr`. O `{COMPUTER_VISION_ENDPOINT_URI}` só é usado no contêiner `ocr`, porque o contêiner `formrecognizer` usa o nome e a porta do `ocr`. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: "{COMPUTER_VISION_API_KEY}"

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"
```

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey`, bem como `FormRecognizer:ComputerVisionApiKey` e `FormRecognizer:ComputerVisionEndpointUri`, precisam ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado. Para mais informações, consulte [Faturamento](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

|Contêiner|Ponto de extremidade|
|--|--|
|form-recognizer|http://localhost:5000

### <a name="form-recognizer"></a>Reconhecimento de Formulários

O contêiner fornece APIs do ponto de extremidade de consulta baseadas em WebSocket, que são acessadas por meio da [documentação do SDK de serviços do Reconhecimento de Formulários](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Por padrão, o SDK do Reconhecimento de Formulários usa os serviços online. Para usar o contêiner, você precisa alterar o método de inicialização. Confira os exemplos abaixo.

#### <a name="for-c"></a>Para o C#

Em vez de usar esta chamada de inicialização de nuvem do Azure:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
para esta chamada, que usa o ponto de extremidade do contêiner:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Para o Python

Em vez de usar esta chamada de inicialização de nuvem do Azure:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

para esta chamada, que usa o ponto de extremidade do contêiner:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Reconhecimento de Formulários

O contêiner fornece as APIs do ponto de extremidade REST, que podem ser encontradas na página [API de Reconhecimento de Formulários](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Solução de problemas

Quando você executa o contêiner, ele usa **stdout** e **stderr** para gerar informações úteis para solucionar problemas que ocorrem durante a inicialização ou a execução do contêiner.

## <a name="billing"></a>Cobrança

Os contêineres do Reconhecimento de Formulários enviam informações de cobrança para o Azure usando um recurso do _Reconhecimento de Formulários_ em sua conta do Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e o fluxo de trabalho para baixar, instalar e executar contêineres do Reconhecimento de Formulários. Em resumo:

* O Reconhecimento de Formulários fornece um contêiner do Linux para o Docker.
* As imagens de contêiner são baixadas do Registro de contêiner particular no Azure.
* Imagens de contêiner são executadas no Docker.
* Use a API REST ou o SDK REST para chamar operações no contêiner do Reconhecimento de Formulários especificando o URI de host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
>  Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](form-recognizer-container-configuration.md) para configurações.
* Use mais [Contêineres de Serviços Cognitivos](../cognitive-services-container-support.md).
