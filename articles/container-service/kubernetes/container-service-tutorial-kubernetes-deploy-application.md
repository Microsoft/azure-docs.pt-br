---
title: "Tutorial do Serviço de Contêiner do Azure – implantar aplicativo | Microsoft Docs"
description: "Tutorial do Serviço de Contêiner do Azure – implantar aplicativo"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Kubernetes, DC/SO, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: b8f747b15bf491b7221a71b5beaa595aa7f1b49b
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---

# <a name="run-applications-in-kubernetes"></a>Executar aplicativos no Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Neste tutorial, parte quatro de sete, um aplicativo de exemplo é implantado em um cluster Kubernetes. As etapas concluídas incluem:

> [!div class="checklist"]
> * Atualizar os arquivos de manifesto do Kubernetes
> * Executar um aplicativo no Kubernetes
> * Testar o aplicativo

Nos tutoriais subsequentes, esse aplicativo é escalado horizontalmente, atualizado e o Operations Management Suite configurado para monitorar o cluster Kubernetes.

Este tutorial assume uma compreensão básica dos conceitos de Kubernetes; para obter informações detalhadas sobre Kubernetes consulte a [documentação do Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um aplicativo foi empacotado em uma imagem de contêiner, essa imagem foi carregada no Registro de Contêiner do Azure e um cluster Kubernetes foi criado. 

Para concluir este tutorial, você precisa do arquivo de manifesto do Kubernetes `azure-vote-all-in-one-redis.yml` pré-criado. Esse arquivo foi baixado com o código-fonte do aplicativo em um tutorial anterior. Verifique se você clonou repositório e se você alterou os diretórios para o repositório clonado.

Se você ainda não realizou essas etapas e deseja continuar acompanhando, retorne ao [Tutorial 1 – Criar imagens de contêiner](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>Atualizar arquivo de manifesto

Neste tutorial, o ACR (Registro de Contêiner do Azure) foi usado para armazenar uma imagem de contêiner. Antes de executar o aplicativo, o nome do servidor de logon do ACR precisa ser atualizado no arquivo de manifesto do Kubernetes.

Obter o nome do servidor de logon ACR com o comando [az acr list](/cli/azure/acr#list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

O arquivo de manifesto foi pré-criado com um nome do servidor de logon `microsoft`. Abra o arquivo com qualquer editor de texto. Neste exemplo, o arquivo é aberto com o `vi`.

```bash
vi azure-vote-all-in-one-redis.yml
```

Substitua `microsoft` pelo nome do servidor de logon do ACR. Esse valor pode ser encontrado na linha **47** do arquivo de manifesto.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

Salve e feche o arquivo.

## <a name="deploy-application"></a>Implantar um aplicativo

Use o comando [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) para executar o aplicativo. Esse comando analisa o arquivo de manifesto e cria objetos Kubernetes definidos.

```azurecli-interactive
kubectl create -f azure-vote-all-in-one-redis.yml
```

Saída:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Testar aplicativo

Um [serviço Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) é criado que expõe o aplicativo para a internet. Esse processo pode levar alguns minutos. 

Para monitorar o andamento, use o comando [kubectl get service](https://review.docs.microsoft.com/en-us/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) com o argumento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Inicialmente, o **EXTERNAL-IP** do serviço `azure-vote-front` aparece como `pending`. Depois que o endereço EXTERNAL-IP for alterado de `pending` para um `IP address`, use `CTRL-C` para interromper o processo de inspeção do kubectl.

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Para consultar o aplicativo, navegue até o endereço IP externo.

![Imagem do cluster Kubernetes no Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, o aplicativo Azure Vote foi implantado em um cluster Kubernetes do Serviço de Contêiner do Azure. As tarefas concluídas incluem:  

> [!div class="checklist"]
> * Baixar arquivos de manifesto Kubernetes
> * Executar um aplicativo no Kubernetes
> * Testado o aplicativo

Avance para o próximo tutorial para saber mais sobre como dimensionar um aplicativo Kubernetes e a infraestrutura do Kubernetes subjacente. 

> [!div class="nextstepaction"]
> [Dimensionar um aplicativo do Kubernetes e sua infraestrutura](./container-service-tutorial-kubernetes-scale.md)
