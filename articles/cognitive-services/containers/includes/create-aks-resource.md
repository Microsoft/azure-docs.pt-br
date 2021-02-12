---
title: Criar um recurso de cluster do serviço kubernetes do Azure
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de AKS (serviço de kubernetes do Azure).
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e7f5b6f3685a94b5497784360f8f12b22fb95012
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017825"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Criar um recurso de cluster do serviço kubernetes do Azure

1. Vá para o [serviço kubernetes do Azure](https://ms.portal.azure.com/#create/microsoft.aks)e selecione **criar**.

1. Na guia **Noções básicas**, insira as seguintes informações:

    |Configuração|Valor|
    |--|--|
    |Subscription|Selecione a assinatura apropriada.|
    |Grupo de recursos|Selecione um grupo de recursos disponível.|
    |Nome do cluster kubernetes|Insira um nome (em minúsculas).|
    |Região|Selecione uma localização próxima.|
    |Versão do Kubernetes|Qualquer que seja o valor marcado como **(padrão)**.|
    |Prefixo do nome DNS|Criado automaticamente, mas você pode substituir.|
    |Tamanho do nó|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Contagem de nós|Deixe o controle deslizante no valor padrão.|

1. Na guia **pools de nós** , deixe os **nós virtuais** e **conjuntos de dimensionamento de VM** definidos com seus valores padrão.
1. Na guia **autenticação** , deixe a **entidade de serviço** e **habilite o RBAC** definido com seus valores padrão.
1. Na guia **rede** , insira as seguintes seleções:

    |Configuração|Valor|
    |--|--|
    |Roteamento de aplicativo HTTP|No|
    |Configuração de rede|Básico|

1. Na guia **integrações** , verifique se o **monitoramento do contêiner** está definido como **habilitado** e deixe **log Analytics espaço de trabalho** como o valor padrão.
1. Na guia **marcas** , deixe os pares nome/valor em branco por enquanto.
1. Selecione **revisar e criar**.
1. Depois que a validação for aprovada, selecione **criar**.

> [!NOTE]
> Se a validação falhar, isso pode ser devido a um erro de "entidade de serviço". Volte para a guia **autenticação** e, em seguida, volte para **revisar + criar**, em que a validação deve ser executada e, em seguida, passada.
