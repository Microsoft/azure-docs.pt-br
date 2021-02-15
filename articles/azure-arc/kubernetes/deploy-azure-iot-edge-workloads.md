---
title: Implantar cargas de trabalho do Azure IoT Edge (versão preliminar)
services: azure-arc
ms.service: azure-arc
ms.date: 02/10/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Implantar cargas de trabalho do Azure IoT Edge
keywords: Kubernetes, Arc, Azure, K8s, contêineres
ms.openlocfilehash: f228b79f14ab24281415cd4bd5964fc86a095d3c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390429"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Implantar cargas de trabalho do Azure IoT Edge (versão preliminar)

## <a name="overview"></a>Visão geral

O Arc do Azure e o Azure IoT Edge facilmente complementam os recursos uns dos outros. 

O Arc do Azure fornece mecanismos para que os operadores de cluster configurem os componentes fundamentais de um cluster e apliquem e imponham políticas de cluster. 

Azure IoT Edge permite que os operadores de aplicativo implantem e gerenciem remotamente as cargas de trabalho em escala com ingestão de nuvem conveniente e primitivos de comunicação bidirecional. 

O diagrama a seguir ilustra o arco do Azure e a relação do Azure IoT Edge:

![Configuração de IoT Arc](./media/edge-arc.png)

## <a name="pre-requisites"></a>Pré-requisitos

* [Registrar um dispositivo IoT Edge](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) e [implantar o módulo sensor de temperatura simulado](../../iot-edge/quickstart-linux.md#deploy-a-module). Observe a cadeia de conexão do dispositivo para os *valores. YAML* mencionados abaixo.

* Use o [suporte do IoT Edge para Kubernetes](https://aka.ms/edgek8sdoc) para implantá-lo por meio do operador de fluxo do Azure Arc.

* Baixe o arquivo [*Values. YAML*](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) para IOT Edge gráfico Helm e substitua o `deviceConnectionString` espaço reservado no final do arquivo pela cadeia de conexão que você anotou anteriormente. Defina qualquer outra opção de instalação de gráfico com suporte, conforme necessário. Crie um namespace para a carga de trabalho de IoT Edge e gere um segredo nele:

  ```
  $ kubectl create ns iotedge

  $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
  ```

  Você também pode configurar remotamente usando o [exemplo de configuração de cluster](./use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Conectar um cluster

Use a `az` `connectedk8s` extensão CLI do Azure para conectar um cluster kubernetes ao arco do Azure:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Criar uma configuração para IoT Edge

O [repositório git de exemplo](https://github.com/veyalla/edgearc) aponta para o gráfico IOT Edge Helm e faz referência ao segredo criado na seção pré-requisitos.

Use a `az` `k8sconfiguration` extensão CLI do Azure para criar uma configuração que vincula o cluster conectado ao repositório git:

  ```
  az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
  ```

Em alguns minutos, você deve ver os módulos de carga de trabalho IoT Edge implantados no namespace do cluster `iotedge` . 

Exiba os `SimulatedTemperatureSensor` logs de Pod nesse namespace para ver os valores de exemplo que estão sendo gerados. Você também pode exibir as mensagens que são recebidas pelo seu Hub IoT usando a [Extensão de Kit de Ferramentas do Hub IoT do Azure para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Limpeza

Remova a configuração usando:

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Próximas etapas

[Usar Azure Policy para controlar a configuração do cluster](./use-azure-policy.md)
