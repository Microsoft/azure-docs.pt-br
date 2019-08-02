---
title: Executar serviço kubernetes do Azure-Análise de Texto
titleSuffix: Azure Cognitive Services
description: Implante os contêineres de Análise de Texto com a imagem de análise de sentimentos no serviço kubernetes do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 44ee5fab5b4e8900b823453e5674fc9bdb5fe9ac
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552270"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-service"></a>Implantar um contêiner de análise de sentimentos no serviço kubernetes do Azure

Saiba como implantar o contêiner de [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) de serviços cognitivas do Azure com a imagem de análise de sentimentos no AKs (serviço kubernetes do Azure). Este procedimento mostra como criar um recurso de Análise de Texto, como criar uma imagem de análise de opiniões associada e como exercitar essa orquestração dos dois em um navegador. Usar contêineres pode mudar sua atenção para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que devem ser instaladas e executadas localmente. Não use Azure Cloud Shell. Você precisará do seguinte:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um editor de texto, por exemplo, [Visual Studio Code](https://code.visualstudio.com/download).
* O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) instalado.
* A [CLI do kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/) instalada.
* Um recurso do Azure com o tipo de preço correto. Nem todos os tipos de preços funcionam com esse contêiner:
    * O recurso **análise de texto do Azure** somente com tipos de preço F0 ou Standard.
    * Recurso de **Serviços cognitivas do Azure** com o tipo de preço S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-a-text-analytics-container-to-an-aks-cluster"></a>Implantar um contêiner de Análise de Texto em um cluster AKS

1. Abra o CLI do Azure e entre no Azure.

    ```azurecli
    az login
    ```

1. Entre no cluster AKS. Substitua `your-cluster-name` e`your-resource-group` pelos valores apropriados.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Depois que esse comando é executado, ele relata uma mensagem semelhante à seguinte:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Se você tiver várias assinaturas disponíveis em sua conta do Azure e o `az aks get-credentials` comando retornar com um erro, um problema comum é que você está usando a assinatura incorreta. Defina o contexto de sua sessão de CLI do Azure para usar a mesma assinatura com a qual você criou os recursos e tente novamente.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Abra o editor de texto de sua escolha. Este exemplo usa Visual Studio Code.

    ```azurecli
    code .
    ```

1. No editor de texto, crie um novo arquivo chamado _sentimentos. YAML_e cole o YAML a seguir nele. Certifique-se de `billing/value` substituir `apikey/value` e por suas próprias informações.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # < Your endpoint >
            - name: apikey
              value: # < Your API Key >
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. Salve o arquivo e feche o editor de texto.
1. Execute o comando `apply` kubernetes com _sentimentos. YAML_ como seu destino:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    Depois que o comando aplicar com êxito a configuração de implantação, uma mensagem será semelhante à seguinte saída:

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Verifique se o Pod foi implantado:

    ```console
    kubectl get pods
    ```

    A saída do status de execução do pod:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Verifique se o serviço está disponível e obtenha o endereço IP.

    ```console
    kubectl get services
    ```

    A saída do status de execução do serviço de _sentimentos_ no pod:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the sentiment analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Próximas etapas

* Usar mais [contêineres de serviços cognitivas](../../cognitive-services-container-support.md)
* Usar o [serviço conectado do análise de texto](../vs-text-connected-service.md)
