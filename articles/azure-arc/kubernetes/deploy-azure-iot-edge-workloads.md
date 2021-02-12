---
title: Implantar cargas de trabalho do Azure IoT Edge (versão preliminar)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Implantar cargas de trabalho do Azure IoT Edge
keywords: Kubernetes, Arc, Azure, K8s, contêineres
ms.openlocfilehash: 88c480f93bfe28a424441a1c5857c623efb4e1d3
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091640"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Implantar cargas de trabalho do Azure IoT Edge (versão preliminar)

## <a name="overview"></a>Visão geral

O Azure Arc e o Azure IoT Edge complementam muito os recursos uns dos outros. O Azure Arc fornece mecanismos para operadores de cluster configurarem os componentes fundamentais de um cluster, bem como aplicarem e imporem políticas de cluster. O IoT Edge permite que os operadores de aplicativo implantem e gerenciem remotamente as cargas de trabalho em escala com ingestão de nuvem conveniente e primitivos de comunicação bidirecional. O diagrama abaixo ilustra isso:

![Configuração de IoT Arc](./media/edge-arc.png)

## <a name="pre-requisites"></a>Pré-requisitos

* [Registrar um dispositivo IoT Edge](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) e [implantar o módulo sensor de temperatura simulado](../../iot-edge/quickstart-linux.md#deploy-a-module). Anote a cadeia de conexão do dispositivo.

* Use o [suporte do IoT Edge para Kubernetes](https://aka.ms/edgek8sdoc) para implantá-lo por meio do operador de fluxo do Azure Arc.

* Baixe o arquivo [**values.yaml**](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) para o gráfico do IoT Edge Helm e substitua o espaço reservado **deviceConnectionString** no final do arquivo pelo que foi anotado na etapa 1. Você pode definir qualquer outra opção de instalação de gráfico compatível, conforme necessário. Crie um namespace para a carga de trabalho de IoT Edge e crie um segredo nele:

    ```
    $ kubectl create ns iotedge

    $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
    ```

    Você também pode configurar isso remotamente usando o [exemplo de configuração de cluster](./use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Conectar um cluster

Use a extensão `connectedk8s` da CLI `az` para conectar um cluster Kubernetes ao Azure Arc:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Criar uma configuração para IoT Edge

Repositório de exemplo: https://github.com/veyalla/edgearc

Esse repositório aponta para o gráfico do Helm do IoT Edge e faz referência ao segredo criado na seção de pré-requisitos.

1. Use a extensão `k8sconfiguration` da CLI `az` para criar uma configuração para vincular o cluster conectado ao repositório git:

    ```
    az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
    ```

    Em um ou dois minutos, você deve ver os módulos da carga de trabalho do IoT Edge implantados no namespace `iotedge` no cluster. Você pode ver os logs do pod de `SimulatedTemperatureSensor` nesse namespace para conferir os valores de exemplo que estão sendo gerados. Você também pode exibir as mensagens que são recebidas pelo seu Hub IoT usando a [Extensão de Kit de Ferramentas do Hub IoT do Azure para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Limpeza

Você pode remover a configuração usando:

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Próximas etapas

[Usar Azure Policy para controlar a configuração do cluster](./use-azure-policy.md)
