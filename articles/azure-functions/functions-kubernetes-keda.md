---
title: Azure Functions em Kubernetes com KEDA
description: Entenda como executar o Azure Functions em Kubernetes na nuvem ou no local usando o KEDA, o dimensionamento automático controlado por eventos baseado em Kubernetes.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 525635ef40437fe308c52e2d5aba2c97ed8f20e7
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927525"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions em Kubernetes com KEDA

O tempo de execução do Azure Functions fornece flexibilidade na hospedagem onde e como você deseja.  [KEDA](https://keda.sh) (dimensionamento automático controlado por eventos baseado em Kubernetes) funciona perfeitamente com o tempo de execução e as ferramentas do Azure Functions a fim de fornecer um dimensionamento controlado por eventos em Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Como trabalham as funções baseadas em Kubernetes

O serviço do Azure Functions é composto por dois componentes principais: um tempo de execução e um controlador de escala.  O tempo de execução do Functions é executado e executa seu código.  O tempo de execução inclui a lógica sobre como disparar, registrar e gerenciar execuções de função.  O tempo de execução do Azure Functions pode ser executado *em qualquer lugar* .  O outro componente é um controlador de escala.  O controlador de escala monitora a taxa de eventos que estão direcionando sua função e dimensiona de forma proativa o número de instâncias que executam seu aplicativo.  Para saber mais, confira [Escala e hospedagem do Azure Functions](functions-scale.md).

As funções baseadas em Kubernetes fornecem o tempo de execução do Functions em um [contêiner do Docker](functions-create-function-linux-custom-image.md) com dimensionamento controlado por eventos por meio de KEDA.  KEDA pode ser dimensionado para 0 instâncias (quando nenhum evento está ocorrendo) e para *n* instâncias. Ele faz isso expondo métricas personalizadas para o dimensionador automático do Kubernetes (Dimensionador automático de pod horizontal).  O uso de contêineres do Functions com KEDA permite replicar recursos de função sem servidor em qualquer cluster de Kubernetes.  Essas funções também podem ser implantadas usando o recurso [nós virtuais do AKS (Serviços de Kubernetes do Azure)](../aks/virtual-nodes-cli.md) para a infraestrutura sem servidor.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Como gerenciar KEDA e Functions no Kubernetes

Para executar Functions em seu cluster do Kubernetes, você deverá instalar o componente KEDA. Você pode instalar esse componente usando [Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-helm"></a>Instalação com Helm

Há várias maneiras de instalar o KEDA em qualquer cluster do Kubernetes, incluindo Helm.  As opções de implantação estão documentadas no [site do KEDA](https://keda.sh/docs/1.4/deploy/).

## <a name="deploying-a-function-app-to-kubernetes"></a>Implantação de um aplicativo de funções no Kubernetes

Você pode implantar qualquer aplicativo de funções em um cluster do Kubernetes que executa o KEDA.  Como suas funções são executadas em um contêiner do Docker, seu projeto precisa de um `Dockerfile`.  Se ele ainda não tiver um, você poderá adicionar um Dockerfile executando o seguinte comando na raiz do seu projeto do Functions:

> [!NOTE]
> As ferramentas principais criam automaticamente o Dockerfile para Azure Functions gravados em .NET, Node, Python ou PowerShell. Para aplicativos de funções escritos em Java, o Dockerfile deve ser criado manualmente. Use a [lista de imagens](https://github.com/Azure/azure-functions-docker) Azure Functions para localizar a imagem correta para basear a função do Azure.

```cli
func init --docker-only
```

Para criar uma imagem e implantar suas funções no Kubernetes, execute o seguinte comando:

> [!NOTE]
> As ferramentas principais aproveitarão a CLI do Docker para criar e publicar a imagem. Verifique se o Docker já está instalado e conectado à sua conta com `docker login`.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Substitua `<name-of-function-deployment>` pelo nome do aplicativo de funções.

O comando implantar executa uma série de ações:
1. O Dockerfile criado anteriormente é usado para criar uma imagem local para o aplicativo de funções.
2. A imagem local é marcada e enviada por push para o registro de contêiner em que o usuário está conectado.
3. Um manifesto é criado e aplicado ao cluster que define um recurso kubernetes `Deployment` , um `ScaledObject` recurso e `Secrets` , que inclui as variáveis de ambiente importadas do seu `local.settings.json` arquivo.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Implantação de um aplicativo de funções a partir de um registro privado

O fluxo acima também funciona para registros privados.  Se você estiver extraindo a imagem do contêiner de um registro privado, inclua o sinalizador `--pull-secret` que faz referência ao segredo do Kubernetes que contém as credenciais do registro particular ao executar o `func kubernetes deploy`.

## <a name="removing-a-function-app-from-kubernetes"></a>Remoção de um aplicativo de funções do Kubernetes

Depois de implantar, você poderá remover uma função removendo o `Deployment` associado, `ScaledObject`, um `Secrets` criado.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Desinstalação do KEDA do Kubernetes

As etapas para desinstalar o KEDA são documentadas [no site do KEDA](https://keda.sh/docs/1.4/deploy/).

## <a name="supported-triggers-in-keda"></a>Gatilhos com suporte no KEDA

O KEDA tem suporte para os seguintes gatilhos do Azure Functions:

* [Filas de Armazenamento do Azure](functions-bindings-storage-queue.md)
* [Filas do Barramento de Serviço do Azure](functions-bindings-service-bus.md)
* [Hubs de evento/IoT do Azure](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [Fila RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>Suporte ao gatilho HTTP

Você pode usar o Azure Functions que expõem gatilhos HTTP, mas o KEDA não os gerencia diretamente.  Você pode aproveitar o gatilho KEDA prometheus para [dimensionar o Azure Functions de HTTP de 1 para *n* instâncias](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42).

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir:

* [Crie uma função usando uma imagem personalizada](functions-create-function-linux-custom-image.md)
* [Codificar e testar o Azure Functions localmente](functions-develop-local.md)
* [Como funciona o plano de consumo de função do Azure](functions-scale.md)