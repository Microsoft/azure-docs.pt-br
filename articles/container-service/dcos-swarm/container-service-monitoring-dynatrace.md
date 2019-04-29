---
title: (PRETERIDO) Monitorar cluster de DC/SO do Azure - Dynatrace
description: Monitorar um cluster DC/OS do Serviço de Contêiner do Azure com Dynatrace. Implantar o Dynatrace OneAgent usando o painel do DC/OS.
services: container-service
author: MartinGoodwell
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 8f34a00d9256c288a2842e905c06d5336522eece
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119837"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(PRETERIDO) Monitorar um cluster de DC/SO do Serviço de Contêiner do Azure com Dynatrace SaaS/gerenciado

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, mostramos como implantar o [Dynatrace](https://www.dynatrace.com/) OneAgent para monitorar todos os nós do agente no seu cluster do Serviço de Contêiner do Azure. Você precisa de uma conta com o Dynatrace SaaS/gerenciado para essa configuração. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/gerenciado
O Dynatrace é uma solução de monitoramento nativa da nuvem para ambientes de cluster e contêiner altamente dinâmicos. Ele permite otimizar ainda mais suas implantações de contêiner e alocações de memória usando dados de uso em tempo real. Ele é capaz de identificar automaticamente problemas de infraestrutura e aplicativo, fornecendo linha de base, correlação de problemas e detecção de causa-raiz automatizadas.

A figura a seguir mostra a interface do usuário do Dynatrace:

![Interface do usuário do Dynatrace](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Pré-requisitos 
[Implantar](container-service-deployment.md) e [conectar-se](./../container-service-connect.md) a um cluster configurado pelo Serviço de Contêiner do Azure. Explorar a [interface do usuário do Marathon](container-service-mesos-marathon-ui.md). Vá para [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) para configurar uma conta Dynatrace SaaS.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Configurar uma implantação do Dynatrace com o Marathon
Estas etapas mostram como configurar e implantar aplicativos do Dynatrace em seu cluster com o Marathon.

1. Acesse a interface do usuário do DC/OS via [http://localhost:80/](http://localhost:80/). Na interface do usuário do DC?OS, navegue até a guia **Universo** e pesquise pelo **Dynatrace**.

    ![Dynatrace no universo de DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Para concluir a configuração, é necessária uma conta de SaaS do Dynatrace ou uma conta de avaliação gratuita. Depois de fazer logon no painel do Dynatrace, selecione **Implantar Dynatrace**.

    ![Configurar integração de PaaS do Dynatrace](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Na página, selecione **Configurar integração de PaaS**. 

    ![Token da API do Dynatrace](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Insira seu token da API na configuração do Dynatrace OneAgent no Universo de DC/OS. 

    ![Configuração do Dynatrace OneAgent no Universo de DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Defina as instâncias segundo o número de nós que você pretende executar. Configurando um número mais alto também funciona, mas o DC/OS continuará tentando localizar novas instâncias até que esse número seja alcançado. Se preferir, você também poderá definir um valor como 1000000. Nesse caso, sempre que um novo nó for adicionado ao cluster, o Dynatrace implantará automaticamente um agente no novo nó, com o preço do DC/OS tentando constantemente implantar instâncias adicionais.

    ![Configuração do Dynatrace nas instâncias de Universo de DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Próximas etapas

Depois de instalar o pacote, navegue de volta para o painel do Dynatrace. Você pode explorar as métricas de uso diferentes para os contêineres no seu cluster. 