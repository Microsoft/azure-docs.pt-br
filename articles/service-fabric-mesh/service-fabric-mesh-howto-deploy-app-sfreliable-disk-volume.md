---
title: Service Fabric volume de disco confiável com malha de Service Fabric
description: Saiba como armazenar estado em uma plicativo da Malha do Service Fabric montando o volume baseado em Disco Confiável do Service Fabric dentro do contêiner usando a CLI do Azure.
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: ac65693f2513338695e07cd8a19acb13333e7281
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625779"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Monte o volume baseado em Disco Confiável do Microsoft Azure Service Fabric altamente disponível em um aplicativo de Malha do Service Fabric 

> [!IMPORTANT]
> A versão prévia da Malha do Azure Service Fabric foi desativada. Não serão mais permitidas novas implantações por meio da API da Malha do Service Fabric. O suporte para as implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, confira [Desativação da versão prévia da Malha do Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

O método comum de persistir o estado com aplicativos de contêiner é usar o armazenamento remoto, como o armazenamento de arquivos do Azure ou banco de dados como o Azure Cosmos DB. Isso resulta em significativa leitura e gravação de latência de rede e o armazenamento remoto.

Este artigo mostra como armazenar o estado em Disco Confiável do Microsoft Azure Service Fabric altamente disponível ao montar um volume dentro do contêiner de uma aplicativo de Malha do Service Fabric.
O Disco Confiável do Service Fabric fornece volumes para leituras locais com gravações replicadas dentro do Cluster do Service Fabric para alta disponibilidade. Isso remove as chamadas de rede para leituras e reduz a latência de rede para gravações. Se o contêiner é reiniciado ou movido para outro nó, a nova instância de contêiner verá o mesmo volume como antigo. Portanto, é eficiente e altamente disponível.

Neste exemplo, o aplicativo de Contador tem um serviço ASP.NET Core com uma página da web que mostra o valor do contador em um navegador.

O `counterService` periodicamente lê um valor do contador de um arquivo, incrementa-o e o grava novamente para o arquivo. O arquivo é armazenado em uma pasta que está montada no volume de apoio pelo Disco Confiável do Service Fabric.

## <a name="prerequisites"></a>Pré-requisitos

Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir essa tarefa. Para usar a CLI do Azure com este artigo, certifique-se de que `az --version` retorna pelo menos `azure-cli (2.0.43)`.  Instale (ou atualize) o módulo de extensão da CLI de Malha do Azure Service Fabric, seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Azure e defina sua assinatura.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos para implantar o aplicativo. O comando a seguir cria um grupo de recursos nomeado `myResourceGroup` em um local no leste dos Estados Unidos. Se você alterar o nome do grupo de recursos no comando abaixo, lembre-se de alterá-lo em todos os comandos a seguir.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Implantar o modelo

>[!NOTE]
> Desde 2 de novembro de 2020, [limites de taxa de download se aplicam](https://docs.docker.com/docker-hub/download-rate-limit/) a solicitações anônimas e autenticadas para o Docker Hub de contas do plano gratuito do Docker e são impostas por endereço IP. 
> 
> Este modelo usa imagens públicas do Hub do Docker. Observe que você pode ter uma taxa limitada. Para obter mais detalhes, confira [Autenticar-se com o Docker Hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub).

O comando a seguir implanta um aplicativo do Linux usando o [modelo counter.sfreliablevolume.linux.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Para implantar um aplicativo do Windows, use o [modelo counter.sfreliablevolume.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Esteja ciente de que imagens de contêiner maiores podem demorar mais para serem implantadas.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Você também pode ver o estado da implantação com o comando

```azurecli-interactive
az deployment group show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Observe o nome do recurso de gateway que tem o recurso de tipo como `Microsoft.ServiceFabricMesh/gateways`. Isso será usado na obtenção do endereço IP público do aplicativo.

## <a name="open-the-application"></a>Abrir o aplicativo

Depois que o aplicativo é implantado com êxito, obtenha o endereço IP do recurso de gateway para o aplicativo. Use o nome do gateway observado na seção acima.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

A saída deve ter uma propriedade `ipAddress` que é o endereço IP público para o ponto de extremidade de serviço. Abra-o em um navegador. Ele exibirá uma página da Web com o valor do contador sendo atualizado a cada segundo.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Verifique se o aplicativo é capaz de usar o volume

O aplicativo cria um arquivo chamado `counter.txt` no volume dentro da `counter/counterService` pasta. O conteúdo desse arquivo é o valor do contador que está sendo exibido na página da web.

## <a name="delete-the-resources"></a>Excluir os recursos

Exclua com frequência os recursos que não estão mais sendo utilizados no Azure. Para excluir os recursos relacionados a esse exemplo, exclua o grupo de recursos no qual foram implantados (que exclui tudo associado ao grupo de recursos) com o comando a seguir:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

- Exiba o aplicativo de exemplo do Disco de para Volume Confiável do Service Fabric no [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Para saber mais sobre o modelo de recursos do Service Fabric, consulte [modelo de recursos de Malha do Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Para saber mais sobre Malha do Service Fabric, leia [visão geral sobre Malha do Service Fabric](service-fabric-mesh-overview.md).