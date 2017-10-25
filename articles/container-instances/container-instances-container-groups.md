---
title: "Grupos de contêineres das Instâncias de Contêiner do Azure"
description: "Entenda como funcionam os grupos de contêineres em Instâncias de Contêiner do Azure"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 25eab41c3f0c986bcce33123f86f4c9638b77191
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="container-groups-in-azure-container-instances"></a>Grupos de contêineres em Instâncias de Contêiner do Azure

O recurso de nível superior em Instâncias de Contêiner do Azure é um grupo de contêiner. Este artigo descreve quais são os grupos de contêineres e quais tipos de cenários que eles permitem.

## <a name="how-a-container-group-works"></a>Como um grupo de contêineres funciona

Um grupo de contêineres é uma coleção de contêineres que são agendados no mesmo computador host e compartilham um ciclo de vida, uma rede local e volumes de armazenamento. Ele é semelhante ao conceito de um *pod* no [Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/pod/) e [DC/SO](https://dcos.io/docs/1.9/deploying-services/pods/).

O diagrama a seguir mostra um exemplo de um grupo de contêineres que inclui vários contêineres.

![Exemplo de grupos de contêineres][container-groups-example]

Observe que:

- O grupo está agendado em um único computador host.
- O grupo expõe um único endereço IP público, com uma porta exposta.
- O grupo é composto por dois contêineres. Um contêiner escuta na porta 80, enquanto o outro escuta na porta 5000.
- O grupo inclui dois compartilhamentos de arquivos do Azure como montagens de volume e cada contêiner monta um dos compartilhamentos localmente.

### <a name="networking"></a>Rede

Grupos de contêineres compartilham um endereço IP e um namespace de porta nesse endereço IP. Para permitir que clientes externos alcancem um contêiner dentro do grupo, você deve expor a porta no endereço IP e do contêiner. Já que contêineres dentro do grupo compartilham um namespace de porta, não há suporte para mapeamento de porta. Contêineres dentro de um grupo podem acessar uns aos outros por meio de localhost nas portas que eles têm expostos, mesmo se essas portas não estão expostas externamente no endereço IP do grupo.

### <a name="storage"></a>Armazenamento

Você pode especificar volumes externos para montar dentro de um grupo de contêineres. Você pode mapear os volumes para caminhos específicos dentro dos contêineres individuais em um grupo.

## <a name="common-scenarios"></a>Cenários comuns

Grupos de vários contêineres são úteis em casos nos quais você deseja dividir uma única tarefa funcional em um pequeno número de imagens de contêiner, que podem ser entregues por equipes diferentes e têm requisitos de recursos separados. Os exemplos de uso podem incluir:

- Um contêiner de aplicativo e um contêiner de log. O contêiner de log coleta logs e métricas de saída do aplicativo principal e grava-as em armazenamento de longo prazo.
- Um aplicativo e um contêiner de monitoramento. O contêiner de monitoramento faz uma solicitação periodicamente para o aplicativo para garantir que ele esteja em execução e respondendo corretamente e emite um alerta em caso negativo.
- Um contêiner que atende a um aplicativo Web e um contêiner efetuando pull do conteúdo mais recente do controle do código-fonte.

## <a name="next-steps"></a>Próximas etapas

Saiba como [implantar um grupo de vários contêineres](container-instances-multi-container-group.md) com um modelo do Azure Resource Manager.

<!-- IMAGES -->

[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png