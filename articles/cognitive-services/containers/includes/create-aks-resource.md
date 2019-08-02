---
title: Criar um recurso de cluster do serviço kubernetes do Azure
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de AKS (serviço de kubernetes do Azure).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 5e442f38a8e81170eeeac2f31f00a7d0eeb08d2b
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377402"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Criar um recurso de cluster do serviço kubernetes do Azure

1. Vá para o [serviço kubernetes do Azure](https://ms.portal.azure.com/#create/microsoft.aks)e selecione **criar**.

1. Na guia **noções básicas** , insira as seguintes informações:

    |Configuração|Valor|
    |--|--|
    |Assinatura|Selecione a assinatura apropriada.|
    |Grupo de recursos|Selecione um grupo de recursos disponível.|
    |Nome do cluster do Kubernetes|Insira um nome (em minúsculas).|
    |Região|Selecione um local próximo.|
    |Versão do Kubernetes|1.12.8 (padrão).|
    |Prefixo do nome DNS|Criado automaticamente, mas você pode substituir.|
    |Tamanho do nó|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Contagem de nós|Deixe o controle deslizante no valor padrão.|

1. Na guia **escala** , deixe **nós virtuais** e **conjuntos de dimensionamento de máquinas virtuais (versão prévia)** definidos com os valores padrão.
1. Na guia **autenticação** , saia da **entidade de serviço** e habilite o **RBAC** definido com os valores padrão.
1. Na guia **rede** , insira as seguintes seleções:

    |Configuração|Valor|
    |--|--|
    |Roteamento de aplicativos HTTP|Não|
    |Configuração de rede|Basic|

1. Na guia **monitoramento** , verifique se habilitar o **monitoramento de contêiner** está definido como **Sim**e deixe **log Analytics espaço de trabalho** como o valor padrão.
1. Na guia **marcas** , deixe os pares nome/valor em branco por enquanto.
1. Selecione **revisar e criar**.
1. Depois que a validação for aprovada, selecione **criar**.

> [!NOTE]
> Se a validação falhar, isso pode ser devido a um erro de "entidade de serviço". Volte para a guia **autenticação** e, em seguida, volte para revisar **+ criar**, em que a validação deve ser executada e, em seguida, passada.
