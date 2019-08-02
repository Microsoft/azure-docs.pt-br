---
title: Continuidade dos negócios e recuperação de desastres no Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 2da92b4fcd98024ada8d852d65e08fe8c70e3884
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704046"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Continuidade dos negócios e recuperação de desastres no Azure Dev Spaces

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Examinar as diretrizes de recuperação de desastres para o AKS (Serviço de Kubernetes do Azure)

O Azure Dev Spaces é um recurso do AKS (Serviço de Kubernetes do Azure). Você deve estar ciente das diretrizes de recuperação de desastre no AKS e verificar se elas se aplicam aos clusters do AKS que você usa para o Dev Spaces. Para mais informações, confira [Práticas recomendadas para continuidade dos negócios e recuperação de desastres no AKS (Serviço de Kubernetes do Azure)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Habilitar o Dev Spaces em clusters do AKS em regiões diferentes

Habilitar o Dev Spaces em clusters do AKS em regiões diferentes permite que você retome o uso do Dev Spaces imediatamente após a falha de uma região do Azure.

Para obter informações gerais sobre implantações de várias regiões do AKS, confira [Planejar para implantações em várias regiões](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment)

Para obter informações sobre como implantar um cluster do AKS compatível com o Azure Dev Spaces, confira [Criar um cluster do Kubernetes usando o Azure Cloud Shell](https://docs.microsoft.com/azure/dev-spaces/how-to/create-cluster-cloud-shell)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Habilitar o Dev Spaces por meio do portal do Azure

Clique no item de navegação **Dev Spaces** nas propriedades de cada cluster no portal do Azure. Em seguida, escolha a opção para habilitar o Dev Spaces.

![Habilitando o Dev Spaces por meio do portal do Azure](../media/common/enable-dev-spaces.jpg)

Repita esse processo para cada cluster.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Habilitar o Dev Spaces por meio da CLI do Azure

Você também pode habilitar o Dev Spaces na linha de comando:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Implantar a linha de base da sua equipe em cada cluster

Ao trabalhar com o Dev Spaces, você normalmente implanta o aplicativo inteiro em um espaço de desenvolvimento pai em seu cluster do Kubernetes. Por padrão, o espaço `default` é usado. A implantação inicial inclui todos os serviços, bem como os recursos externos dos quais esses serviços dependem, como bancos de dados ou filas. Isso é conhecido como a *linha de base*. Depois de configurar uma linha de base no espaço de desenvolvimento pai, você itera e depurar os serviços individuais dentro dos espaços de desenvolvimento filho.

Você deve implantar as versões mais recentes de seu conjunto de linha de base de serviços em clusters em várias regiões. Atualizar seus serviços de linha de base dessa maneira garante que você possa continuar a usar o Dev Spaces caso ocorra a falha de uma região do Azure. Por exemplo, se você implantar a linha de base por meio de um pipeline de CI/CD, modifique o pipeline para que ele implante em vários clusters em regiões diferentes.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Selecionar o cluster do AKS correto a ser usado para o Dev Spaces

Depois de configurar corretamente um cluster de backup executando a linha de base da sua equipe, você poderá mudar rapidamente para o cluster de backup a qualquer momento. Em seguida, você pode executar novamente os serviços individuais nos quais está trabalhando no Dev Spaces.

Selecione um cluster diferente com o seguinte comando da CLI:

```cmd
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Você pode listar os espaços de desenvolvimento disponíveis no novo cluster com o seguinte comando:

```cmd
azds space list
```

Você pode criar um novo espaço de desenvolvimento para trabalhar ou selecionar um espaço de desenvolvimento existente com o seguinte comando:

```cmd
azds space select -n <space name>
```

Depois de executar esses comandos, o cluster e o espaço de desenvolvimento selecionados serão usados para operações da CLI subsequentes e para a depuração de projetos usando a extensão do Visual Studio Code para Azure Dev Spaces.

Se você estiver usando o Visual Studio, poderá alternar o cluster usado por um projeto existente por meio das seguintes etapas:

1. Abra o projeto no Visual Studio.
1. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções, clique em **Propriedades**
1. No painel esquerdo, clique em **Depurar**
1. Na página de propriedades de Depurar, clique na lista suspensa de **Perfil** e escolha **Azure Dev Spaces**.
1. Clique no botão **Alterar**.
1. Na caixa de diálogo que aparece, selecione o cluster do AKS que você deseja usar. Se desejado, escolha um espaço de desenvolvimento diferente no qual trabalhar ou crie um novo espaço de desenvolvimento, selecionando a opção apropriada na lista suspensa **Espaço**.

Depois de selecionar o cluster e o espaço corretos, você poderá pressionar F5 para executar o serviço nos Dev Spaces.

Repita essas etapas para todos os outros projetos configurados para usar o cluster original.

## <a name="access-a-service-on-a-backup-cluster"></a>Acessar um serviço em um cluster de backup

Se você tiver configurado seu serviço para usar um nome DNS público, o serviço terá uma URL diferente se você executá-lo em um cluster de backup. Os nomes DNS públicos estão sempre no formato `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io`. Se você alternar para um cluster diferente, o GUID do cluster e, possivelmente, a região serão alterados.

O Dev Spaces sempre mostra a URL correta para o serviço ao executar `azds up` ou a janela de Saída no Visual Studio em **Azure Dev Spaces**.

Você também pode encontrar a URL executando o comando `azds list-uris`:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Use essa URL ao acessar o serviço.
