---
title: Configurar a camada PremiumV3
description: Saiba como melhorar o desempenho para seu aplicativo Web, móvel e de API em Azure App serviço, dimensionando para o novo tipo de preço PremiumV3.
keywords: serviço de aplicativo, serviço de aplicativo do azure, escala, escalonável, plano de serviço de aplicativo, custo de serviço de aplicativo
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 7ade24b6478f78a51e0be68ae69ae0b076ecff1f
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96607867"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>Configurar a camada PremiumV3 para o serviço Azure App

O novo tipo de preço **PremiumV3** fornece processadores mais rápidos, armazenamento SSD e profundidade de memória para núcleo dos tipos de preço existentes (o dobro da camada **PremiumV2** ). Com a vantagem de ter um melhor desempenho, você pode economizar dinheiro executando seus aplicativos em menos instâncias. Neste artigo, você aprende a criar um aplicativo na camada **PremiumV3** ou a escalar verticalmente um aplicativo para a camada **PremiumV3** .

## <a name="prerequisites"></a>Pré-requisitos

Para escalar verticalmente um aplicativo para o **PremiumV3**, você precisa ter um aplicativo de serviço Azure app que é executado em um tipo de preço inferior a **PremiumV3** e o aplicativo deve estar em execução em uma implantação do serviço de aplicativo que dá suporte a PremiumV3.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>Disponibilidade do PremiumV3

A camada **PremiumV3** está disponível para aplicativos nativos e de contêiner, incluindo contêineres do Windows e contêineres do Linux.

> [!NOTE]
> Todos os contêineres do Windows em execução na camada de **contêiner Premium** durante o período de visualização continuam a funcionar como estão, mas a camada de **contêiner Premium** continuará a permanecer na versão prévia. A camada **PremiumV3** é a substituição oficial da camada de **contêiner Premium** . 

O **PremiumV3** está disponível em algumas regiões do Azure e a disponibilidade em regiões adicionais está sendo adicionada continuamente. Para ver se ele está disponível em sua região, execute o comando da CLI do Azure a seguir no [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>Criar um aplicativo na camada PremiumV3

O tipo de preço de um aplicativo do Serviço de Aplicativo é definido no [Plano do Serviço de Aplicativo](overview-hosting-plans.md) em que ele é executado. Você pode criar um Plano do Serviço de Aplicativo individualmente ou como parte da criação de um aplicativo.

Ao configurar o Plano do Serviço de Aplicativo no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>, selecione **Tipo de preço**. 

Selecione **produção** e, em seguida, selecione **P1V3**, **P2V3** ou **P3V3**, em seguida, clique em **aplicar**.

![Captura de tela mostrando os tipos de preço recomendados para seu aplicativo.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Se você não vir **P1V3**, **P2V3** e **P3V3** como opções ou se as opções estiverem esmaecidas, **PremiumV3** provavelmente não estará disponível na implantação do serviço de aplicativo subjacente que contém o plano do serviço de aplicativo. Consulte [Expandir de uma combinação de regiões e de um grupo de recursos sem suporte](#unsupported) para obter mais detalhes.

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>Escalar verticalmente um aplicativo existente para a camada PremiumV3

Antes de dimensionar um aplicativo existente para a camada **PremiumV3** , verifique se **PremiumV3** está disponível. Para obter informações, consulte [disponibilidade do PremiumV3](#availability). Se não estiver disponível, consulte [Expandir de uma combinação de regiões e de um grupo de recursos sem suporte](#unsupported).

Dependendo do seu ambiente de hospedagem, a expansão poderá exigir etapas adicionais. 

No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>, abra a página do seu aplicativo do Serviço de Aplicativo.

No painel de navegação à esquerda da página do seu aplicativo do Serviço de Aplicativo, selecione **Expandir (Plano do Serviço de Aplicativo)**.

![Captura de tela mostrando como escalar verticalmente seu plano do serviço de aplicativo.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selecione **produção** e, em seguida, selecione **P1V3**, **P2V3** ou **P3V3**, em seguida, clique em **aplicar**.

![Captura de tela mostrando os tipos de preço recomendados para seu aplicativo.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Se a operação for concluída com êxito, a página Visão geral do aplicativo mostrará que agora está em uma camada **PremiumV3** .

![Captura de tela mostrando o tipo de preço PremiumV3 na página de visão geral do seu aplicativo.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Se você receber um erro

Alguns planos do serviço de aplicativo não podem ser escalados verticalmente para a camada PremiumV3 se a implantação do serviço de aplicativo subjacente não oferecer suporte a PremiumV3. Consulte [Expandir de uma combinação de regiões e de um grupo de recursos sem suporte](#unsupported) para obter mais detalhes.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Expandir de uma combinação de regiões e de um grupo de recursos sem suporte

Se seu aplicativo for executado em uma implantação do serviço de aplicativo em que **PremiumV3** não está disponível ou se seu aplicativo for executado em uma região que atualmente não dá suporte a **PremiumV3**, você precisará reimplantar seu aplicativo para tirar proveito do **PremiumV3**.  Você tem duas opções:

- Crie um aplicativo em um novo grupo de recursos e com um novo plano do serviço de aplicativo. Ao criar o plano do serviço de aplicativo, selecione uma camada **PremiumV3** . Essa etapa garante que o plano do serviço de aplicativo seja implantado em uma unidade de implantação que dê suporte a **PremiumV3**. Em seguida, reimplante o código do aplicativo no aplicativo recém-criado. Mesmo que você dimensione o plano do serviço de aplicativo para uma camada mais baixa para economizar custos, você pode sempre dimensionar de volta para **PremiumV3** porque a unidade de implantação dá suporte a ele.
- Se seu aplicativo já for executado em uma camada **Premium** existente, você poderá clonar seu aplicativo com todas as configurações do aplicativo, cadeias de conexão e a configuração de implantação em um novo grupo de recursos em um novo plano do serviço de aplicativo que usa **PremiumV3**.

    ![Captura de tela mostrando como clonar seu aplicativo.](media/app-service-configure-premium-tier/clone-app.png)

    Na página **clonar aplicativo** , você pode criar um plano do serviço de aplicativo usando **PremiumV3** na região desejada e especificar as configurações do aplicativo e a configuração que deseja clonar.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Mudando do contêiner Premium para o SKU Premium v3

Se você tiver um aplicativo que está usando a SKU do contêiner Premium de visualização e deseja mudar para a nova SKU Premium v3, será necessário reimplantar seu aplicativo para tirar proveito do **PremiumV3**. Para fazer isso, consulte a primeira opção em [escalar verticalmente de uma combinação de grupo de recursos sem suporte e região](#scale-up-from-an-unsupported-resource-group-and-region-combination)

## <a name="automate-with-scripts"></a>Automatizar com scripts

Você pode automatizar a criação de aplicativos na camada **PremiumV3** com scripts, usando o [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>CLI do Azure

O comando a seguir cria um plano do serviço de aplicativo no _P1V3_. Você pode executá-lo no Cloud Shell. As opções para `--sku` são P1V3, _P2V3_ e _P3V3_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O comando a seguir cria um plano do serviço de aplicativo no _P1V3_. As opções para `-WorkerSize` são _Pequeno_, _Médio_ e _Grande_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>Mais recursos

[Escalar verticalmente um aplicativo no Azure](manage-scale-up.md) 
 [Dimensionar a contagem de instâncias manual ou automaticamente](../azure-monitor/platform/autoscale-get-started.md)
