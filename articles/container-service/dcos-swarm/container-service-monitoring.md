---
title: Monitorar cluster de DC/OS do Azure - Datadog | Microsoft Docs
description: "Monitore um cluster do Serviço de Contêiner do Azure com o Datadog. Use a interface do usuário da Web do DC/OS para implantar os agentes Datadog para seu cluster."
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Contêineres, DC/OS, Docker Swarm, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 9dd451f994940d7cc3a59bd7fd08a8f067345e34
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>Monitorar um cluster DC/OS do Serviço de Contêiner do Azure com Datadog
Neste artigo, implantaremos agentes do Datadog em todos os nós de agente em seu cluster do Serviço de Contêiner do Azure. Você precisará de uma conta no Datadog para fazer esta configuração. 

## <a name="prerequisites"></a>Pré-requisitos
[Implantar](container-service-deployment.md) e [conectar](../container-service-connect.md) um cluster configurado pelo Serviço de Contêiner do Azure. Explorar a [interface do usuário do Marathon](container-service-mesos-marathon-ui.md). Vá para [http://datadoghq.com](http://datadoghq.com) para configurar uma conta do Datadog. 

## <a name="datadog"></a>Datadog
O Datadog é um serviço de monitoramento que reúne dados de monitoramento de seus contêineres em seu cluster do Serviço de Contêiner do Azure. O Datadog tem um Painel de Integração do Docker, onde você pode ver as métricas específicas em seus contêineres. As métricas obtidas de seus contêineres são organizadas por CPU, Memória, Rede e E/S. O Datadog divide as métricas em contêineres e em imagens. A seguir, um exemplo da aparência da interface do usuário de uso da CPU.

![Interface do usuário do Datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Configurar uma implantação do Datadog com Marathon
Estas etapas mostram como configurar e implantar aplicativos do Datadog no cluster com Marathon. 

Acesse a interface do usuário do DC/OS via [http://localhost:80/](http://localhost:80/). Na interface do usuário do DC/OS, navegue até o “Universo”, que é o canto inferior esquerdo, procure "Datadog" e clique em "Instalar".

![Pacote do Datadog no Universo DC/OS](./media/container-service-monitoring/datadog1.png)

Agora, para concluir a configuração, será necessária uma conta do Datadog ou de uma conta de avaliação gratuita. Depois de fazer logon no site do Datadog, olhe à sua esquerda e vá para Integrations -> depois [APIs](https://app.datadoghq.com/account/settings#api). 

![Chave de API do Datadog](./media/container-service-monitoring/datadog2.png)

Em seguida, insira sua chave de API na configuração do Datadog no Universo do DC/OS. 

![Configuração do Datadog no Universo DC/OS](./media/container-service-monitoring/datadog3.png) 

Nas configuração acima, as instâncias são configuradas como 10000000 para que sempre que um novo nó for adicionado ao cluster, o Datadog implante automaticamente um agente para o novo nó. Essa é uma solução temporária. Depois de instalar o pacote, você deverá navegar de volta para o site do Datadog e localizar “[Dashboards](https://app.datadoghq.com/dash/list).” A partir daí, você verá os painéis Custom e Integration. O [painel de Docker](https://app.datadoghq.com/screen/integration/docker) terá todas as métricas de contêiner de que você precisa para monitorar seu cluster. 

