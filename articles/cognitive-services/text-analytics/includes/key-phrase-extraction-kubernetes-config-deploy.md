---
title: Extração de Frases-chave as etapas de configuração e implantação do kubernetes
titleSuffix: Azure Cognitive Services
description: Extração de Frases-chave as etapas de configuração e implantação do kubernetes
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 27c78566877f27e80ae5ae27c5250f228c7ae676
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017828"
---
### <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>Implantar o contêiner de Extração de Frases-chave em um cluster AKS

1. Abra o CLI do Azure e entre no Azure.

    ```azurecli
    az login
    ```

1. Entre no cluster AKS. Substitua `your-cluster-name` e `your-resource-group` pelos valores apropriados.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Depois que esse comando é executado, ele relata uma mensagem semelhante à seguinte:

    ```output
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

1. No editor de texto, crie um novo arquivo chamado *keyphrase. YAML* e cole o YAML a seguir nele. Certifique-se de substituir `billing/value` e `apikey/value` por suas próprias informações.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
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
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. Salve o arquivo e feche o editor de texto.
1. Execute o `apply` comando kubernetes com o arquivo *keyphrase. YAML* como seu destino:

    ```console
    kubectl apply -f keyphrase.yaml
    ```

    Depois que o comando aplicar com êxito a configuração de implantação, uma mensagem será semelhante à seguinte saída:

    ```output
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Verifique se o Pod foi implantado:

    ```console
    kubectl get pods
    ```

    A saída do status de execução do pod:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Verifique se o serviço está disponível e obtenha o endereço IP.

    ```console
    kubectl get services
    ```

    A saída do status de execução do serviço *keyphrase* no pod:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
