---
title: Análise de Sentimento as etapas de configuração e implantação do kubernetes
titleSuffix: Azure Cognitive Services
description: Análise de Sentimento as etapas de configuração e implantação do kubernetes
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: ca8d4d725ff25687d1005ddab1964316a147c730
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017827"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>Implantar o contêiner de Análise de Sentimento em um cluster AKS

1. Abra o CLI do Azure e entre no Azure.

    ```azurecli
    az login
    ```

1. Entre no cluster AKS. Substitua `your-cluster-name` e `your-resource-group` pelos valores apropriados.

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

    ```console
    code .
    ```

1. No editor de texto, crie um novo arquivo chamado *sentimentos. YAML* e cole o YAML a seguir nele. Certifique-se de substituir `billing/value` e `apikey/value` por suas próprias informações.

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
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
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
1. Execute o `apply` comando kubernetes com o arquivo *sentimentos. YAML* como seu destino:

    ```console
    kubectl apply -f sentiment.yaml
    ```

    Depois que o comando aplicar com êxito a configuração de implantação, uma mensagem será semelhante à seguinte saída:

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Verifique se o Pod foi implantado:

    ```console
    kubectl get pods
    ```

    A saída do status de execução do pod:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Verifique se o serviço está disponível e obtenha o endereço IP.

    ```console
    kubectl get services
    ```

    A saída do status de execução do serviço de *sentimentos* no pod:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
