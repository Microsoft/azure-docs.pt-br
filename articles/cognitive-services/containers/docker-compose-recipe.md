---
title: Usar o Docker Compose para implantar vários contêineres
titleSuffix: Azure Cognitive Services
description: Saiba como implantar vários contêineres de serviços cognitivas. Este artigo mostra como orquestrar várias imagens de contêiner do Docker usando Docker Compose.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: aahi
ms.openlocfilehash: cedcf8a3fcd656c4af0ca7493c598791d35d20d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996111"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Usar o Docker Compose para implantar vários contêineres

Este artigo mostra como implantar vários contêineres de serviços cognitivas do Azure. Especificamente, você aprenderá a usar Docker Compose para orquestrar várias imagens de contêiner do Docker.

> [Docker Compose](https://docs.docker.com/compose/) é uma ferramenta para definir e executar aplicativos de vários contêineres do Docker. No Compose, você usa um arquivo YAML para configurar os serviços do aplicativo. Em seguida, você cria e inicia todos os serviços de sua configuração executando um único comando.

Pode ser útil orquestrar várias imagens de contêiner em um único computador host. Neste artigo, vamos reunir os contêineres de leitura e reconhecedor de formulário.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que devem ser instaladas e executadas localmente:

* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services) antes de começar.
* [Mecanismo do Docker](https://www.docker.com/products/docker-engine). Confirme se a CLI do Docker funciona em uma janela de console.
* Um recurso do Azure com o tipo de preço correto. Somente os seguintes tipos de preço funcionam com este contêiner:
  * **Pesquisa Visual computacional** recurso somente com o tipo de preço F0 ou Standard.
  * Recurso de **reconhecimento de formulário** com o tipo de preço F0 ou Standard somente.
  * O recurso **Serviços Cognitivos** com o tipo de preço S0.
* Se você estiver usando um contêiner de visualização restringido, será necessário concluir o [formulário de solicitação online](https://aka.ms/csgate/) para usá-lo.

## <a name="docker-compose-file"></a>Arquivo de Docker Compose

O arquivo YAML define todos os serviços a serem implantados. Esses serviços dependem de um `DockerFile` ou de uma imagem de contêiner existente. Nesse caso, usaremos duas imagens de visualização. Copie e cole o seguinte arquivo YAML e salve-o como *Docker-Compose. YAML*. Forneça os valores apropriados de **apiKey**, **cobrança** e **EndpointUri** no arquivo.

```yaml
version: '3.7'
services:
  forms:
    image: "mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Crie os diretórios no computador host que são especificados no nó **volumes** . Essa abordagem é necessária porque os diretórios devem existir antes de tentar montar uma imagem usando associações de volume.

## <a name="start-the-configured-docker-compose-services"></a>Iniciar os serviços de Docker Compose configurados

Um arquivo de Docker Compose permite o gerenciamento de todos os estágios em um ciclo de vida de um serviço definido: Iniciando, parando e recriando serviços; exibindo o status do serviço; e o streaming de log. Abra uma interface de linha de comando do diretório do projeto (onde o arquivo Docker-Compose. YAML está localizado).

> [!NOTE]
> Para evitar erros, verifique se o computador host compartilha corretamente unidades com o mecanismo do Docker. Por exemplo, se *E:\publicpreview* for usado como um diretório no arquivo *Docker-Compose. YAML* , compartilhe a unidade **E** com o Docker.

Na interface de linha de comando, execute o seguinte comando para iniciar (ou reiniciar) todos os serviços definidos no arquivo *Docker-Compose. YAML* :

```console
docker-compose up
```

A primeira vez que o Docker executa o comando **Docker-Compose** usando essa configuração, ele extrai as imagens configuradas no nó **Serviços** e, em seguida, baixa e monta-as:

```console
Pulling forms (mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout:)...
latest: Pulling from azure-cognitive-services/form-recognizer/layout
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview:)...
latest: Pulling from /azure-cognitive-services/vision/read:3.1-preview
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

Depois que as imagens são baixadas, os serviços de imagem são iniciados:

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>Verificar a disponibilidade do serviço

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Veja a seguir um exemplo de saída:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout          latest
4be104c126c5        mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview         latest
```

### <a name="test-containers"></a>Contêineres de teste

Abra um navegador no computador host e vá para **localhost** usando a porta especificada do arquivo *Docker-Compose. YAML* , como http://localhost:5021/swagger/index.html . Por exemplo, você pode usar o recurso **experimentar** na API para testar o ponto de extremidade do reconhecedor de formulário. Ambas as páginas do Swagger de contêineres devem estar disponíveis e ser testada.

![Contêiner do reconhecedor de formulário](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Contêineres de serviços cognitivas](../cognitive-services-container-support.md)
