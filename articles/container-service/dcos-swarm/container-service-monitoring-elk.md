---
title: Monitorar um cluster DC/OS do Azure - pilha ELK | Microsoft Docs
description: "Monitore um cluster de SO/DC no cluster do serviço de contêiner do Azure com ELK (Elasticsearch, Logstash e Kibana)."
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: "Contêineres, CD/OS, Azure, monitoramento, elk"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: fcfa277cdd0f3cebc0fbbb23e771fb23ffbe2ca6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-elk"></a>Monitorar um cluster do Serviço de Contêiner do Azure com ELK
Neste artigo, demonstraremos como implantar a pilha ELK (Elasticsearch, Logstash, Kibana) em um cluster de SO/controlador de domínio no serviço de contêiner do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
[Implantar](container-service-deployment.md) e [conectar](../container-service-connect.md) um cluster DC/OS configurado pelo Serviço de Contêiner do Azure. Explorar o painel de DC/sistema operacional e serviços maratona [aqui](container-service-mesos-marathon-ui.md). Instale também o [balanceador de carga maratona](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
ELK pilha é uma combinação de Elasticsearch, Logstash e Kibana que fornece uma pilha de ponta a ponta que pode ser usada para monitorar e analisar os logs no cluster.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Configurar a pilha ELK em um cluster de DC/sistema operacional
Acessar a interface do usuário do DC/sistema operacional por meio de [http://localhost:80/](http://localhost:80/) uma vez na interface do usuário do controlador de domínio/sistema operacional, navegue até **universo**. Pesquisar e instalar Elasticsearch, Logstash e Kibana do universo DC/sistema operacional e em que ordem específica. Você pode aprender mais sobre a configuração se você for para o **instalação avançada** link.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Uma vez os contêineres ELK e está em funcionamento, você precisa habilitar Kibana para serem acessados pelo maratona LB. Navegue até **Serviços** > **kibana**e clique em **Editar** conforme mostrado abaixo.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Alterne para o **modo JSON** e role para baixo até a seção de rótulos.
Você precisa adicionar uma entrada `"HAPROXY_GROUP": "external"` aqui conforme mostrado abaixo.
Depois que você clicar em **Implantar alterações**, seu contêiner será reinicializado.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Se você quiser verificar se Kibana está registrado como um serviço no painel HAPROXY, você precisará abrir a porta 9090 no cluster do agente como HAPROXY é executado na porta 9090.
Por padrão, podemos abrir portas 80, 8080 e 443 no cluster agente DC/sistema operacional.
São fornecidas instruções para abrir uma porta e forneça público avaliar [aqui](container-service-enable-public-access.md).

Para acessar o painel HAPROXY, abra a interface de administração maratona LB em: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Depois que você navegue até a URL, você deve ver o painel HAPROXY, conforme mostrado abaixo e você deverá ver uma entrada de serviço para Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Para acessar o painel de Kibana, que é implantado na porta 5601, você precisa abrir a porta 5601. Siga as instruções [aqui](container-service-enable-public-access.md). Abra o painel de Kibana em: `http://localhost:5601`.

## <a name="next-steps"></a>Próximas etapas

* Para configuração e encaminhamento de log de aplicativo e sistema, confira [Log Management in DC/OS with ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/) (Gerenciamento de Log no DC/SO com ELK).

* Para filtrar logs, confira[Filtering Logs with ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/) (Filtrando logs com ELK). 

 

