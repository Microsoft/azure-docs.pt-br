---
title: Configurar o dimensionamento automático para aplicativos de microsserviço
description: Este artigo descreve como configurar o Dimensionamento automático para seus aplicativos usando o portal do Microsoft Azure ou a CLI do Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/22/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: eaa4a219a1020639c930f8a07bfcaa1c45d0cef8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877750"
---
# <a name="set-up-autoscale-for-microservice-applications"></a>Configurar o dimensionamento automático para aplicativos de microsserviço

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

O dimensionamento automático é um recurso interno do Azure Spring Cloud que ajuda os aplicativos de microsserviço a apresentarem seu melhor desempenho diante de alterações de demanda. Isso inclui modificar o número de CPUs virtuais, memória e instâncias de aplicativo. Este artigo descreve como configurar o Dimensionamento automático para seus aplicativos usando o portal do Microsoft Azure ou a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir estes procedimentos, você precisará:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Uma instância de serviço do Azure Spring Cloud implantada. Seguir o [início rápido sobre como implantar um aplicativo por meio da CLI do Azure](./spring-cloud-quickstart.md) para obter uma introdução.
* Pelo menos um aplicativo já criado na instância de serviço.

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>Navegue até a página Dimensionamento automático no portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Vá até a página **Visão geral** do Azure Spring Cloud.
3. Escolha o grupo de recursos que contém o serviço.
4. Selecione a guia **Aplicativos** em **Configurações** no menu no painel de navegação esquerdo.
5. Selecione o aplicativo para o qual deseja configurar o Dimensionamento automático. Neste exemplo, selecione o aplicativo chamado **demonstração**. Em seguida, você deverá ver a página **Visão Geral** do aplicativo.
6. Selecione a guia **Escalar horizontalmente** em **Configurações** no menu no painel de navegação esquerdo.
7. Selecione a implantação para a qual deseja configurar o Dimensionamento automático. Você deve ver as opções do Dimensionamento automático na próxima seção.


![Menu de dimensionamento automático](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>Configurar o Dimensionamento automático de seu aplicativo no portal do Azure

Há duas opções de gerenciamento de demanda com o Dimensionamento automático:

* Dimensionamento manual: mantém uma contagem instâncias fixa. Na camada Standard, é possível escalar horizontalmente para um máximo de 500 instâncias. Esse valor altera o número de instâncias em execução separadas do aplicativos de microsserviço.
* Dimensionamento automático personalizado: escala segundo qualquer agendamento, com base em qualquer métrica.

Na portal do Azure, escolha como deseja escalar.  A figura a seguir mostra as opções de **Dimensionamento automático personalizado** e as configurações de modo.

![Dimensionamento automático personalizado](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>Configurar o Dimensionamento automático de seu aplicativo na CLI do Azure
Você também pode definir modos do Dimensionamento automático usando a CLI do Azure.  Os comandos a seguir criam uma configuração e uma regra de Dimensionamento automático.

* Criar configuração de Dimensionamento automático
  ```
  az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
  ```
* Criar regra de Dimensionamento automático
  ```
  az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
  ```

## <a name="upgrade-to-the-standard-tier"></a>Atualizar para a camada Standard

Se você estiver na camada básica e restringido por um ou mais desses limites, poderá atualizar para a camada Standard. Para fazer isso, vá para o menu de tipo de **Preço** selecionando primeiro a coluna do tipo *Standard* e clicando no botão **Atualizar**.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do dimensionamento automático no Microsoft Azure](../azure-monitor/autoscale/autoscale-overview.md)
* [Monitoramento do dimensionamento automático na CLI do Azure](/cli/azure/monitor/autoscale)
