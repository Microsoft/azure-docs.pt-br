---
title: "Pools de agentes DC/OS para Serviço de Contêiner do Azure | Microsoft Docs"
description: "Como os pools de agentes públicos e privados funcionam com um cluster DC/OS do Serviço de Contêiner do Azure"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Mesos, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: da4a196b1a73c78dfff7d8310edcc349b8d10665
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Pools de agentes DC/OS para Serviço de Contêiner do Azure
Os clusters DC/OS no Serviço de Contêiner do Azure contêm nós de agente em dois pools, um público e um privado. Um aplicativo pode ser implantado em qualquer um dos pools, afetando a acessibilidade entre computadores no seu serviço de contêiner. Os computadores podem ser expostos à Internet (público) ou mantidos internamente (privado). Este artigo fornece uma visão geral sobre o motivo da existência de pools público e privado.


* **Agentes privados**: nós de agente privado são executados por meio de uma rede não roteável. Esta rede só é acessível por meio da zona de administrador ou por meio do roteador de borda de zona pública. Por padrão, o DC/SO inicia aplicativos em nós de agente privada. 

* **Agentes públicos**: nós de agente público executam aplicativos e serviços DC/OS por meio de uma rede acessível publicamente. 

Para obter mais informações sobre segurança de rede DC/OS, confira a [documentação do DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/).

## <a name="deploy-agent-pools"></a>Implantar pools de agentes

Os pools de agentes DC/OS no Serviço de Contêiner do Azure são criados como se segue:

* O **pool privado** contém o número de nós de agente que você especifica quando [implanta o cluster DC/OS](container-service-deployment.md). 

* O **pool público** inicialmente contém um número predeterminado de nós de agente. Esse pool é adicionado automaticamente quando o cluster DC/OS é provisionado.

O pool privado e o pool público pool são conjuntos de dimensionamento de máquinas virtuais do Azure. Você pode redimensionar esses pools após a implantação.

## <a name="use-agent-pools"></a>Usar pools de agentes
Por padrão, **Maratona** implanta qualquer novo aplicativo a nós de agente *particular* . Você deve implantar explicitamente o aplicativo nos nós *públicos* durante a criação do aplicativo. Selecione a guia **Opcional** e insira **slave_public** para o valor **Funções de Recurso Aceitas**. Esse processo está documentado [aqui](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) e na documentação do [DC/OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/).

## <a name="next-steps"></a>Próximas etapas
* Leia mais sobre [como gerenciar seus contêineres DC/OS](container-service-mesos-marathon-ui.md).

* Saiba como [abrir o firewall](container-service-enable-public-access.md) fornecido pelo Azure para permitir acesso público aos seus contêineres DC/OS.

