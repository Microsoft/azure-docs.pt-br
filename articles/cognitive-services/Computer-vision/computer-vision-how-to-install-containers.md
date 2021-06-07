---
title: Instalar contêineres de OCR de Leitura do Docker da Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Use contêineres de OCR de Leitura do Docker da Pesquisa Visual Computacional para extrair textos de imagens e documentos locais.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: on-premises, OCR, Docker, container
ms.openlocfilehash: 53d59822b378a658f8b6c048de1a32db53a795d1
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285715"
---
# <a name="install-read-ocr-docker-containers-preview"></a>Instalar contêineres de OCR de Leitura do Docker (Versão Prévia) 

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

Os contêineres permitem executar as APIs de Pesquisa Visual Computacional em seu próprio ambiente. Contêineres são excelentes para especificar requisitos de segurança e governança de dados. Neste artigo, você aprenderá a baixar, instalar e executar contêineres da Pesquisa Visual Computacional.

O contêiner de OCR de *Leitura* permite extrair um texto impresso e manuscrito de imagens e documentos compatíveis com os formatos de arquivos JPEG, PNG, BMP, PDF e TIFF. Para obter mais informações, confira o [Guia de instruções da API de Leitura](Vision-API-How-to-Topics/call-read-api.md).

## <a name="read-32-preview-container"></a>Contêiner de Leitura 3.2 – versão prévia

> [!NOTE]
> O contêiner de Leitura 3.0 – versão prévia foi preterido. 

O contêiner de OCR de Leitura 3.2 – versão prévia fornece:
* Novos modelos para obter uma precisão aprimorada.
* Suporte para vários idiomas no mesmo documento.
* Suporte para 73 idiomas. Confira a lista completa de [idiomas compatíveis com o OCR](./language-support.md#optical-character-recognition-ocr).
* Uma operação única para documentos e imagens.
* Suporte para documentos e imagens maiores.
* Pontuações de confiança.
* Suporte para documentos com texto impresso e manuscrito.
* Capacidade de extrair textos somente de páginas selecionadas em um documento.
* Escolha a ordem de saída da linha de texto da opção padrão para obter uma ordem de leitura que seja mais natural. Essa opção está disponível somente para idiomas latinos.
* A classificação ou não de linha de texto no estilo manuscrito está disponível somente para idiomas latinos.

Caso esteja usando contêineres de leitura 2.0 no momento, confira o [guia de migração](read-container-migration-guide.md) para saber mais sobre alterações das novas versões.

## <a name="prerequisites"></a>Pré-requisitos

Será necessário atender aos seguintes pré-requisitos antes de usar os contêineres:

|Obrigatório|Finalidade|
|--|--|
|Mecanismo do Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.| 
|Recurso da Pesquisa Visual Computacional |Para usar o contêiner, você precisará ter:<br><br>Um recurso da **Pesquisa Visual Computacional** do Azure, bem como a chave de API e o URI do ponto de extremidade associados. Os dois valores estão disponíveis nas páginas Visão geral e Chaves e são necessários para iniciar o contêiner.<br><br>**{API_KEY}** : uma das duas chaves de recurso disponíveis na página **Chaves**<br><br>**{ENDPOINT_URI}** : o ponto de extremidade fornecido na página **Visão Geral**|

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="request-approval-to-run-the-container"></a>Solicitar aprovação para executar o contêiner

Preencha e envie o [formulário de solicitação](https://aka.ms/csgate) a fim de solicitar aprovação para executar o contêiner. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Suporte à extensão avançada do vetor

**Host** é o computador que executa o contêiner do Docker. O host *deverá ser compatível* com [Extensões Avançadas do Vetor](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). É possível verificar o suporte para AVX2 em hosts Linux usando o seguinte comando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> O computador host será *necessário* para dar suporte ao AVX2. O contêiner *não* funcionará de modo correto sem o suporte ao AVX2.

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

Imagens de contêiner estão disponíveis para leitura.

| Contêiner | Registro de Contêiner/Repositório/Nome da Imagem |
|-----------|------------|
| Leitura 2.0 – versão prévia | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| 3\.2-preview da Leitura | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2` |

Use o comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner.

### <a name="docker-pull-for-the-read-ocr-container"></a>Executar o comando docker pull para o contêiner de OCR de Leitura

# <a name="version-32-preview"></a>[Versão 3.2 – versão prévia](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2
```

# <a name="version-20-preview"></a>[Versão 2.0 – versão prévia](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run) com as configurações de cobrança necessárias. Há outros [exemplos](computer-vision-resource-container-config.md) do comando `docker run` disponíveis. 
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. Confira [como coletar parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os valores `{ENDPOINT_URI}` e `{API_KEY}`.

[Exemplos](computer-vision-resource-container-config.md#example-docker-run-commands) do comando `docker run` estão disponíveis.

# <a name="version-32-preview"></a>[Versão 3.2 – versão prévia](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Esse comando:

* Executa o contêiner de OCR de Leitura da imagem de contêiner.
* Aloca oito núcleos de CPU e 18 GB (gigabytes) de memória.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

# <a name="version-20-preview"></a>[Versão 2.0 – versão prévia](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Esse comando:

* Executa o contêiner de OCR de Leitura da imagem de contêiner.
* Aloca oito núcleos de CPU e 16 GB (gigabytes) de memória.
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner.
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

---


Há outros [exemplos](./computer-vision-resource-container-config.md#example-docker-run-commands) do comando `docker run` disponíveis. 

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

Caso precise de uma taxa de transferência mais alta (ao processar arquivos de várias páginas, por exemplo), considere implantar vários contêineres [em um cluster do Kubernetes](deploy-computer-vision-on-premises.md) usando o [Armazenamento do Azure](../../storage/common/storage-account-create.md) e a [Fila do Azure](../../storage/queues/storage-queues-introduction.md).

Caso esteja usando o Armazenamento do Azure para armazenar imagens a fim de executar um processamento, será possível criar uma [cadeia de conexão](../../storage/common/storage-configure-connection-string.md) para ser usada durante uma chamada ao contêiner.

Para localizar a cadeia de conexão:

1. Acesse a opção **Contas de armazenamento** no portal do Azure e localize sua conta.
2. Clique em **Chaves de acesso** na lista de navegação esquerda.
3. A cadeia de conexão estará localizada abaixo da **Cadeia de conexão**

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta baseadas em REST. 

# <a name="version-32-preview"></a>[Versão 3.2 – versão prévia](#tab/version-3-2)

Use o host, `http://localhost:5000`, para as APIs do contêiner. Será possível ver o caminho do Swagger em: `http://localhost:5000/swagger/vision-v3.2-preview-read/swagger.json`.

# <a name="version-20-preview"></a>[Versão 2.0 – versão prévia](#tab/version-2)

Use o host, `http://localhost:5000`, para as APIs do contêiner. Será possível ver o caminho do Swagger em: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json`.

---

### <a name="asynchronous-read"></a>Leitura assíncrona


# <a name="version-32-preview"></a>[Versão 3.2 – versão prévia](#tab/version-3-2)

É possível usar as operações `POST /vision/v3.2/read/analyze` e `GET /vision/v3.2/read/operations/{operationId}` em conjunto para ler uma imagem de modo assíncrono, do mesmo modo que o serviço de Pesquisa Visual Computacional usa operações REST correspondentes. O método POST assíncrono retornará um `operationId`, usado como identificador para a solicitação HTTP GET.


Na interface do usuário do Swagger, selecione `Analyze` para expandi-lo no navegador. Depois, clique em **Experimentar** > **Escolher arquivo**. Neste exemplo, usaremos a seguinte imagem:

![Guias versus espaços](media/tabs-vs-spaces.png)

Após a execução com êxito do método POST assíncrono, ele retornará um código de status **HTTP 202**. Como parte da resposta, há um cabeçalho `operation-location` que contém o ponto de extremidade do resultado para executar a solicitação.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

O `operation-location` é uma URL totalmente qualificada. Além disso, é possível acessá-lo por meio de um HTTP GET. Veja a resposta JSON da execução da URL `operation-location` da imagem anterior:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          },
          {
            "boundingBox": [
              286,
              171,
              415,
              165,
              417,
              197,
              287,
              201
            ],
            "text": "paces",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.746
              }
            },
            "words": [
              {
                "boundingBox": [
                  286,
                  179,
                  404,
                  166,
                  405,
                  198,
                  290,
                  201
                ],
                "text": "paces",
                "confidence": 0.938
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[Versão 2.0 – versão prévia](#tab/version-2)

É possível usar as operações `POST /vision/v2.0/read/core/asyncBatchAnalyze` e `GET /vision/v2.0/read/operations/{operationId}` em conjunto para ler uma imagem de modo assíncrono, do mesmo modo que o serviço de Pesquisa Visual Computacional usa operações REST correspondentes. O método POST assíncrono retornará um `operationId`, usado como identificador para a solicitação HTTP GET.

Na interface do usuário do Swagger, selecione `asyncBatchAnalyze` para expandi-lo no navegador. Depois, clique em **Experimentar** > **Escolher arquivo**. Neste exemplo, usaremos a seguinte imagem:

![Guias versus espaços](media/tabs-vs-spaces.png)

Após a execução com êxito do método POST assíncrono, ele retornará um código de status **HTTP 202**. Como parte da resposta, há um cabeçalho `operation-location` que contém o ponto de extremidade do resultado para executar a solicitação.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

O `operation-location` é uma URL totalmente qualificada. Além disso, é possível acessá-lo por meio de um HTTP GET. Veja a resposta JSON da execução da URL `operation-location` da imagem anterior:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

---

> [!IMPORTANT]
> Caso implante vários contêineres de OCR de Leitura atrás de um balanceador de carga, como no Docker Compose ou no Kubernetes, será necessário obter um cache externo. Como o contêiner de processamento e o contêiner de solicitação GET podem não ser o mesmo, um cache externo armazena os resultados e compartilha-os entre os contêineres. Para obter detalhes sobre as configurações de cache, confira [Configurar contêineres do Docker da Pesquisa Visual Computacional](./computer-vision-resource-container-config.md).

### <a name="synchronous-read"></a>Leitura síncrona

É possível usar a operação a seguir para ler uma imagem de modo síncrono. 

# <a name="version-32-preview"></a>[Versão 3.2 – versão prévia](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

# <a name="version-20-preview"></a>[Versão 2.0 – versão prévia](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

Somente após ler uma imagem por completo, uma API retornará determinada resposta JSON. A única exceção para esse cenário é a ocorrência de um erro. Após a ocorrência de um erro, a seguinte resposta JSON retornará:

```json
{
    "status": "Failed"
}
```

O objeto de resposta JSON tem o mesmo grafo de objetos da versão assíncrona. Caso seja um usuário do JavaScript e queira executar a segurança de tipos, considere usar o TypeScript para converter a resposta JSON.

Para obter um exemplo de caso de uso, confira uma <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">área restrita do TypeScript aqui</a>, depois clique em **Executar** para visualizar a facilidade de uso.

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Solução de problemas

Se você executar o contêiner com uma [montagem](./computer-vision-resource-container-config.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem durante a inicialização ou execução do contêiner.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Cobrança

Os contêineres dos Serviços Cognitivos enviam informações de cobrança do Azure usando um recurso correspondente em sua conta do Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](./computer-vision-resource-container-config.md). 

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para baixar, instalar e executar os contêineres de Pesquisa Visual Computacional. Em resumo:

* A Pesquisa Visual Computacional fornece um contêiner do Linux para o Docker, encapsulando a Leitura.
* As imagens de contêiner são baixadas do registro de contêiner "Versão Prévia de Contêiner" do Azure.
* Imagens de contêiner são executadas no Docker.
* É possível usar a API REST ou o SDK para executar uma chamada às operações em contêineres de OCR de Leitura especificando o URI host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](computer-vision-resource-container-config.md) para configurações
* Examine a [Visão geral de OCR](overview-ocr.md) para saber mais sobre como reconhecer um texto impresso e manuscrito
* Confira esta [API de Leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) para obter detalhes sobre métodos compatíveis com o contêiner.
* Veja as [Perguntas frequentes](FAQ.md) para resolver problemas relacionados à funcionalidade de Pesquisa Visual Computacional.
* Use mais [Contêineres de Serviços Cognitivos](../cognitive-services-container-support.md)
