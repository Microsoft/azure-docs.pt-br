---
title: Como instalar e executar o contêiner para o reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Este artigo explicará como usar o contêiner do reconhecedor do formulário de serviços cognitivas do Azure para analisar dados de formulário e tabela.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 53334dfdcdb917c754c9bc4205b0918c6d207da8
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584780"
---
# <a name="install-and-run-form-recognizer-containers-retiring"></a>Instalar e executar contêineres do reconhecedor de formulário (aposentando)

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

O Reconhecimento de Formulários do Azure aplica tecnologia de aprendizado de máquina para identificar e extrair pares chave-valor e tabelas de formulários. Ele associa valores e entradas de tabelas a pares chave-valor e, em seguida, gera dados estruturados que incluem as relações no arquivo original. 

Para reduzir a complexidade e integrar facilmente um modelo de Reconhecimento de Formulários personalizado ao seu processo de automação de fluxo de trabalho ou a outro aplicativo, você pode chamar o modelo usando uma API REST simples. Somente cinco documentos de formulário são necessários, para que você possa obter resultados com rapidez, precisão e adaptados ao seu conteúdo específico. Não é necessária intervenção manual intensa nem ampla experiência em ciência de dados. E não é necessária rotulagem nem anotação de dados.

| Função | Recursos |
|----------|----------|
| Reconhecimento de Formulários | <li>Processa arquivos PDF, PNG e JPG<li>Treina modelos personalizados com um mínimo de cinco formas do mesmo layout <li>Extrai pares chave-valor e informações de tabela <li>Usa o recurso de Reconhecimento de Texto da API de Pesquisa Visual Computacional dos Serviços Cognitivos do Azure para detectar e extrair texto impresso de imagens em formulários<li>Não exige anotação nem rotulagem |

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de usar contêineres de Reconhecimento de Formulários, é necessário atender aos seguintes pré-requisitos:

| Obrigatório | Finalidade |
|----------|---------|
| Mecanismo do Docker | É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> No Windows, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br> |
| Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como Registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`. |
| A CLI do Azure | Instale a [CLI do Azure](/cli/azure/install-azure-cli) no host. |
| Recurso da API de Pesquisa Visual Computacional | Para processar imagens e documentos digitalizados, é necessário ter um recurso da Pesquisa Visual Computacional. Acesse o recurso de Reconhecimento de Texto como um recurso do Azure (API REST ou SDK) ou como um *contêiner do* [cognitive-services-recognize-text](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull). Os valores de cobrança normais se aplicam. <br><br>Transmita a chave de API e os pontos de extremidade para seu recurso de Pesquisa Visual Computacional (contêiner de nuvem do Azure ou serviços cognitivas). Use essa chave de API e o ponto de extremidade como **{COMPUTER_VISION_API_KEY}** e **{COMPUTER_VISION_ENDPOINT_URI}**.<br><br> Se você usar o contêiner *cognitive-services-recognize-text*, certifique-se de que:<br><br>A chave da Pesquisa Visual Computacional para o contêiner do Reconhecimento de Formulários seja a chave especificada no comando `docker run` da Pesquisa Visual Computacional para o contêiner do *cognitive-services-recognize-text*.<br>O ponto de extremidade de cobrança é o ponto de extremidade do contêiner (por exemplo, `http://localhost:5000`). Se você usar os contêineres da Pesquisa Visual Computacional e do Reconhecimento de Formulários juntos no mesmo host, eles não poderão ser iniciados com a porta padrão *5000*. |
| Recurso do Reconhecimento de Formulários | Para usar esses contêineres, é necessário ter:<br><br>Um recurso do **reconhecedor** do Azure Form para obter a chave de API e o URI de ponto de extremidade associados. Ambos os valores estão disponíveis nas páginas visão geral e chaves do **reconhecedor** do portal do Azure Form e os dois valores são necessários para iniciar o contêiner.<br><br>**{FORM_RECOGNIZER_API_KEY}**: uma das duas chaves de recurso disponíveis na página chaves<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}**: o ponto de extremidade conforme fornecido na página Visão geral |

> [!NOTE]
> O nome do recurso de Pesquisa Visual Computacional deve ser uma única palavra, sem um hífen `-` ou qualquer outro caractere especial. Essa restrição está em vigor para garantir a compatibilidade do reconhecedor de formulários e do contêiner de Reconhecimento de Texto.

## <a name="gathering-required-parameters"></a>Como reunir os parâmetros necessários

Há três parâmetros principais para todos os contêineres de serviços cognitivas que são necessários. O contrato de licença de usuário final (EULA) deve estar presente com um valor de `accept` . Além disso, uma URL de ponto de extremidade e uma chave de API são necessárias.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>URI do ponto `{COMPUTER_VISION_ENDPOINT_URI}` de extremidade e `{FORM_RECOGNIZER_ENDPOINT_URI}`

O valor do URI do **ponto de extremidade** está disponível na página de *visão geral* portal do Azure do recurso de serviço cognitiva correspondente. Navegue até a página *visão geral* , focalize o ponto de extremidade e um `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ícone será exibido. Copie e use onde for necessário.

![Coletar o URI do ponto de extremidade para uso posterior](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Chaves `{COMPUTER_VISION_API_KEY}` e `{FORM_RECOGNIZER_API_KEY}`

Essa chave é usada para iniciar o contêiner e está disponível na página chaves do portal do Azure do recurso de serviço cognitiva correspondente. Navegue até a página *chaves* e clique no `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ícone.

![Obter uma das duas chaves para uso posterior](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Essas chaves de assinatura são usadas para acessar sua API do Serviço Cognitivo. Não compartilhe suas chaves. Armazene-os com segurança, por exemplo, usando Azure Key Vault. Recomendamos a regeneração regular dessas chaves. Apenas uma chave é necessária para fazer uma chamada à API. Ao regenerar a primeira chave, você pode usar a segunda chave para obter acesso contínuo ao serviço.

## <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

Os núcleos de CPU e a memória mínimos e recomendados a serem alocados para cada contêiner do Reconhecimento de Formulários são descritos na tabela a seguir:

| Contêiner | Mínimo | Recomendadas |
|-----------|---------|-------------|
| Reconhecimento de Formulários | 2 núcleos, 4 GB de memória | 4 núcleos, 8 GB de memória |
| Reconhecimento de Texto | 1 núcleo, 8 GB de memória | 2 núcleos, 8 GB de memória |

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.
* Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

> [!Note]
> Os valores mínimos e recomendados se baseiam nos limites do Docker e *não* nos recursos do computador host.

Você precisará dos contêineres do formulário reconhecedor e do Reconhecimento de Texto, observe que o contêiner **reconhecimento de texto** é [detalhado fora deste artigo.](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-by-using-the-docker-run-command) com as configurações de cobrança necessárias. Há outros [exemplos](form-recognizer-container-configuration.md#example-docker-run-commands) do comando `docker run` disponíveis.
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Execute o contêiner usando o comando docker run

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. Consulte [coletando parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os `{COMPUTER_VISION_ENDPOINT_URI}` `{COMPUTER_VISION_API_KEY}` valores, `{FORM_RECOGNIZER_ENDPOINT_URI}` e `{FORM_RECOGNIZER_API_KEY}` .

[Exemplos](form-recognizer-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

### <a name="form-recognizer"></a>Reconhecimento de Formulários

> [!NOTE]
> Os diretórios usados para `--mount` nesses exemplos são caminhos de diretório do Windows. Se você estiver usando o Linux ou o macOS, altere o parâmetro para o seu ambiente. 

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

O contêiner fornece APIs do ponto de extremidade de consulta baseadas em WebSocket, que são acessadas por meio da [documentação do SDK de serviços do Reconhecimento de Formulários](./index.yml).

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

Se você executar o contêiner com uma [montagem](form-recognizer-container-configuration.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem durante a inicialização ou execução do contêiner.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Cobrança

Os contêineres do Reconhecimento de Formulários enviam informações de cobrança para o Azure usando um recurso do _Reconhecimento de Formulários_ em sua conta do Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](form-recognizer-container-configuration.md).

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

* Examine [configurar contêineres](form-recognizer-container-configuration.md) para definições de configuração.
* Use mais [contêineres de serviços cognitivas](../cognitive-services-container-support.md).