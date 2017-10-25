---
title: "Solução de problemas de Instâncias de Contêiner do Azure"
description: "Saiba como solucionar problemas com as Instâncias de Contêiner do Azure"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: ff6da0ce95d0405714602c3872da34a2bff344d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Solucionar problemas de implantação com as Instâncias de Contêiner do Azure

Este artigo mostra como solucionar problemas ao implantar contêineres para Instâncias de Contêiner do Azure. Ele também descreve alguns dos problemas comuns que você pode encontrar.

## <a name="getting-diagnostic-events"></a>Obtendo eventos de diagnóstico

Para exibir logs do seu código de aplicativo em um contêiner, você pode usar o comando [az container logs](/cli/azure/container#logs). Mas, se o contêiner não implantar com êxito, você deve examinar as informações de diagnóstico fornecidas pelo provedor de recursos das Instâncias de Contêiner do Azure. Para exibir os eventos para o contêiner, execute o seguinte comando:

```azurecli-interactive
az container show -n mycontainername -g myresourcegroup
```

A saída inclui as propriedades principais do contêiner, juntamente com eventos de implantação:

```bash
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...

      "events": [
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:52+00:00",
        "lastTimestamp": "2017-08-03T22:12:52+00:00",
        "message": "Pulling: pulling image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Pulled: Successfully pulled image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Created: Created container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Started: Started container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      }
    ],
    "name": "helloworld",
      "ports": [
        {
          "port": 80
        }
      ],
    ...
  ]
}
```

## <a name="common-deployment-issues"></a>Tarefas de implantação comuns

Há alguns problemas comuns responsáveis pela maioria dos erros na implantação.

### <a name="unable-to-pull-image"></a>Não é possível efetuar pull da imagem

Se o Instâncias de Contêiner do Azure não for capaz de efetuar pull da imagem inicialmente, ele tentará novamente por um período, até eventualmente falhar. Se não for possível efetuar pull da imagem, eventos como a seguir serão mostrados:

```bash
"events": [
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:31+00:00",
    "lastTimestamp": "2017-08-03T22:19:31+00:00",
    "message": "Pulling: pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:32+00:00",
    "lastTimestamp": "2017-08-03T22:19:32+00:00",
    "message": "Failed: Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image microsoft/aci-hellowrld:latest not found",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:33+00:00",
    "lastTimestamp": "2017-08-03T22:19:33+00:00",
    "message": "BackOff: Back-off pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  }
]
```

Para resolver, excluir o contêiner e tente novamente realizar a implantação dele, prestando atenção para digitar corretamente o nome da imagem.

### <a name="container-continually-exits-and-restarts"></a>Contêiner sai e reinicia continuamente

Atualmente, o Instâncias de Contêiner do Azure dá suporte apenas a serviços de execução longa. Se o contêiner executa rumo à conclusão e sai, ele automaticamente reinicia e executa novamente. Se isso acontecer, eventos como os mostrados a seguir serão mostrados. Observe que o contêiner é iniciado com êxito e, em seguida, reinicia rapidamente. A API de Instâncias de Contêiner inclui uma propriedade `retryCount` que mostra quantas vezes um contêiner específico foi reiniciado.

```bash
"events": [
  {
    "count": 5,
    "firstTimestamp": "2017-08-03T22:21:55+00:00",
    "lastTimestamp": "2017-08-03T22:23:22+00:00",
    "message": "Pulling: pulling image \"alpine\"",
    "type": "Normal"
  },
  {
    "count": 5,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:23:23+00:00",
    "message": "Pulled: Successfully pulled image \"alpine\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:21:57+00:00",
    "message": "Created: Created container with id ad2bf9bc51761c5f935260b4bab53b164d52d9cbc045b16afcb26fb4d14d0a70",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:21:57+00:00",
    "message": "Started: Started container with id ad2bf9bc51761c5f935260b4bab53b164d52d9cbc045b16afcb26fb4d14d0a70",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:58+00:00",
    "lastTimestamp": "2017-08-03T22:21:58+00:00",
    "message": "Created: Created container with id 7687b9bd15dc01731fa66fc45f6f0241495600602dd03841e559453245e7f70b",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:58+00:00",
    "lastTimestamp": "2017-08-03T22:21:58+00:00",
    "message": "Started: Started container with id 7687b9bd15dc01731fa66fc45f6f0241495600602dd03841e559453245e7f70b",
    "type": "Normal"
  },
  {
    "count": 13,
    "firstTimestamp": "2017-08-03T22:21:59+00:00",
    "lastTimestamp": "2017-08-03T22:24:36+00:00",
    "message": "BackOff: Back-off restarting failed container",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:22:13+00:00",
    "lastTimestamp": "2017-08-03T22:22:13+00:00",
    "message": "Created: Created container with id 72e347e891290e238135e4a6b3078748ca25a1275dbbff30d8d214f026d89220",
    "type": "Normal"
  },
  ...
```

> [!NOTE]
> A maioria das imagens de contêiner para as distribuições de Linux definem um shell (assim como um bash) como o comando padrão. Já que um shell por si só não é um serviço de execução longa, esses contêineres saem imediatamente e entram em um loop de reinicialização.

### <a name="container-takes-a-long-time-to-start"></a>Contêiner leva muito tempo para iniciar

Se o contêiner leva muito tempo para iniciar mas eventualmente tem êxito, comece observando o tamanho da sua imagem de contêiner. Já que o Instâncias de Contêiner do Azure efetua pull de sua imagem de contêiner sob demanda, o tempo de inicialização que você experiencia está diretamente relacionado ao tamanho dela.

Você pode exibir o tamanho da sua imagem de contêiner usando a CLI do Docker:

```bash
docker images
```

Saída:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
microsoft/aci-helloworld               latest              7f78509b568e        13 days ago         68.1MB
```

A chave para manter os tamanhos de imagem pequenos é garantir que sua imagem final não contenha nada que não deja necessário no tempo de execução. Uma forma de fazer isso é com [builds de vários estágios](https://docs.docker.com/engine/userguide/eng-image/multistage-build/). Builds de vários estágios tornam fácil assegurar que a imagem final contenha somente os artefatos necessários para seu aplicativo, sem nenhum conteúdo extra que foi necessário no momento do build.

A outra maneira de reduzir o impacto do pull da imagem no tempo de inicialização do contêiner é hospedar a imagem de contêiner usando o Registro de Contêiner do Azure na mesma região em que você pretende usar Instâncias de Contêiner do Azure. Isso reduz o caminho de rede que a imagem de contêiner precisa percorrer, reduzindo significativamente o tempo de download.

### <a name="resource-not-available-error"></a>Erro de recurso não disponível

Devido a diversas cargas de recursos regionais no Azure, você poderá receber o seguinte erro durante a tentativa de implantar uma instância de contêiner:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Esse erro indica que devido à carga pesada na região em que você está tentando implantar, os recursos especificados para o contêiner não poderão ser alocados no momento. Use uma ou mais das seguintes etapas de mitigação para ajudar a resolver o problema.

* Verifique se suas configurações de implantação do contêiner caem dentro dos parâmetros definidos na [Disponibilidade de região para Instâncias de Contêiner do Azure](container-instances-region-availability.md)
* Especificar configurações de CPU e memória inferiores para o contêiner
* Implantar em uma região diferente do Azure
* Implantar em um momento posterior
