---
title: Configurar a camada PremiumV2 para o Serviço de Aplicativo do Azure | Microsoft Docs
description: Aprenda a melhorar o desempenho para seu aplicativo Web, móvel e de API no Serviço de Aplicativo do Azure, escalonando para o novo tipo de preço PremiumV2.
keywords: serviço de aplicativo, serviço de aplicativo do azure, escala, escalonável, plano de serviço de aplicativo, custo de serviço de aplicativo
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: cephalin
ms.openlocfilehash: 59e5825554843ef7265cbccdd9a63db0426891fa
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226302"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Configurar a camada PremiumV2 para o Serviço de Aplicativo do Azure

O novo tipo de preço **PremiumV2** fornece processadores mais rápidos, armazenamento SSD e dobra o índice memória/núcleo dos tipos de preço existentes. Com a vantagem de ter um melhor desempenho, você pode economizar dinheiro executando seus aplicativos em menos instâncias. Neste artigo, você aprenderá a criar um aplicativo na camada **PremiumV2** ou expandir para uma camada **PremiumV2**.

## <a name="prerequisites"></a>Pré-requisitos

Para expandir um aplicativo Web para **PremiumV2**, é necessário ter um aplicativo Web no Serviço de Aplicativo do Azure que é executado em um tipo de preço inferior a **PremiumV2**, e o aplicativo Web deve estar em execução em uma implantação do Serviço de Aplicativo com suporte para PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Disponibilidade de PremiumV2

A camada **PremiumV2** está atualmente disponível para o Serviço de Aplicativo no _Windows_ e no _Linux_.

**PremiumV2** está disponível na maioria das regiões do Azure. Para ver se ele está disponível em sua região, execute o comando da CLI do Azure a seguir no [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Criar um aplicativo no tipo de preço PremiumV2

O tipo de preço de um aplicativo do Serviço de Aplicativo é definido no [Plano do Serviço de Aplicativo](azure-web-sites-web-hosting-plans-in-depth-overview.md) em que ele é executado. Você pode criar um Plano do Serviço de Aplicativo individualmente ou como parte da criação de um aplicativo Web.

Ao configurar o Plano do Serviço de Aplicativo no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>, selecione **Tipo de preço**. 

Selecione **produção**, em seguida, selecione **P1V2**, **P2V2**, ou **P3V2**, em seguida, clique em **aplicar**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [IMPORTANTE!] 
> Se você não ver **P1V2**, **P2V2** e **P3V2** como opções, ou se as opções não estiverem disponíveis, **PremiumV2** provavelmente não está disponível na implantação do Serviço de Aplicativo subjacente que contém o plano do Serviço de Aplicativo. Consulte [Expandir de uma combinação de regiões e de um grupo de recursos sem suporte](#unsupported) para obter mais detalhes.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Expandir um aplicativo existente para o tipo de preço PremiumV2

Antes de expandir um aplicativo existente para a camada **PremiumV2**, certifique-se de que **PremiumV2** está disponível. Para obter informações, consulte [Disponibilidade do PremiumV2](#availability). Se não estiver disponível, consulte [Expandir de uma combinação de regiões e de um grupo de recursos sem suporte](#unsupported).

Dependendo do seu ambiente de hospedagem, a expansão poderá exigir etapas adicionais. 

No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>, abra a página do seu aplicativo do Serviço de Aplicativo.

No painel de navegação à esquerda da página do seu aplicativo do Serviço de Aplicativo, selecione **Expandir (Plano do Serviço de Aplicativo)**.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selecione **produção**, em seguida, selecione **P1V2**, **P2V2**, ou **P3V2**, em seguida, clique em **aplicar**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Se a operação for concluída com êxito, a página de visão geral do aplicativo mostrará que ele está no tipo de preço **PremiumV2**.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Se você obtiver um erro

Alguns planos de serviço de aplicativo podem não expandir para a camada PremiumV2 se a implantação do Serviço de Aplicativo subjacente não oferecer suporte para PremiumV2.  Consulte [Expandir de uma combinação de regiões e de um grupo de recursos sem suporte](#unsupported) para obter mais detalhes.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Expandir de uma combinação de regiões e de um grupo de recursos sem suporte

Se o aplicativo for executado em uma implantação do Serviço de Aplicativo em que **PremiumV2** não estiver disponível, ou se o aplicativo for executado em uma região que atualmente não suporte **PremiumV2**, será necessário reimplantar o aplicativo para aproveitar o **PremiumV2**.  Você tem duas opções:

- Crie um **novo** grupo de recursos e, em seguida, crie um **novo** aplicativo Web e plano do serviço de aplicativo no **novo** grupo de recursos, escolhendo a região do Azure desejada durante o processo de criação.  Você **deve** selecionar o plano **PremiumV2** no momento em que o novo plano do serviço de aplicativo for criado.  Isso garante que a combinação do grupo de recursos, plano do Serviço de Aplicativo, e a região do Azure resulte no plano do Serviço de Aplicativo sendo criado em uma implantação do Serviço de Aplicativo com suporte a **PremiumV2**.  Em seguida, reimplante o código do aplicativo para o aplicativo e o plano do serviço de aplicativo recém-criados. Caso queira, posteriormente, é possível reduzir verticalmente o plano do Serviço de Aplicativo do **PremiumV2** para economizar custos, e você ainda poderá escalar verticalmente outra vez para uso futuro usando **PremiumV2**.
- Se o aplicativo já é executado em uma camada **Premium** existente, é possível clonar o aplicativo com todas as configurações do aplicativo, cadeias de conexão e configuração de implantação em um novo plano de serviço de aplicativo que use **PremiumV2**.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    Na página **Clonar aplicativo**, você pode criar um plano do Serviço de Aplicativo usando **PremiumV2** na região escolhida e especificar as configurações de aplicativo e as configurações que deseja clonar.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Você pode automatizar a criação do aplicativo no tipo de preço **PremiumV2** com scripts, usando a [CLI do Azure](/cli/azure/install-azure-cli) ou o [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>CLI do Azure

O comando a seguir cria um Plano de Serviço de Aplicativo em _P1V2_. Você pode executá-lo no Cloud Shell. As opções para `--sku` são P1V2, _P2V2_ e _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

O comando a seguir cria um Plano de Serviço de Aplicativo em _P1V2_. As opções para `-WorkerSize` são _Pequeno_, _Médio_ e _Grande_.

```PowerShell
New-AzureRmAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Mais recursos

[Expandir um aplicativo no Azure](web-sites-scale.md)  
[Escalonar a contagem de instâncias manualmente ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md)
