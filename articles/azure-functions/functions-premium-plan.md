---
title: O plano Premium de funções do Azure (visualização) | Microsoft Docs
description: Planejam de detalhes e opções de configuração (rede virtual, nenhuma inicialização a frio, a duração de execução ilimitado) para as funções do Azure Premium.
services: functions
author: jeffhollan
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: jehollan
ms.openlocfilehash: d327146c4a1fa61e55bb904308038c1ce717123d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031145"
---
# <a name="azure-functions-premium-plan-preview"></a>Plano Premium de funções do Azure (visualização)

O plano Premium do Azure Functions é uma opção de hospedagem para aplicativos de funções. O plano Premium fornece recursos como conectividade de rede virtual, nenhuma inicialização a frio e hardware premium.  Vários aplicativos de funções podem ser implantados para o mesmo plano Premium e o plano permite que você configure o tamanho da instância de computação, o tamanho do plano de base e o tamanho máximo do plano.  Para obter uma comparação do plano Premium e outro plano e tipos de hospedagem, consulte [escala e as opções de hospedagem de função](functions-scale.md).

> [!NOTE]
> A visualização de plano Premium atualmente oferece suporte a funções em execução no .NET, nó ou Java por meio da infraestrutura do Windows.

## <a name="create-a-premium-plan"></a>Criar um plano Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Você também pode criar um plano Premium da CLI do Azure

```azurecli-interactive
az functionapp plan create -g <resource-group> -n <plan-name> -l <region> --number-of-workers 1 --sku EP1
```

## <a name="features"></a>Recursos

Os seguintes recursos estão disponíveis para aplicativos de funções implantados em um plano Premium.

### <a name="pre-warmed-instances"></a>Pré-começando instâncias

Se nenhum eventos e execuções de hoje ocorrem no plano de consumo, seu aplicativo pode reduzir verticalmente para instâncias de zero. Quando novos eventos chegam, uma nova instância precisa ser especializado com seu aplicativo em execução nele.  Novas instâncias de especialização podem levar algum tempo dependendo do aplicativo.  Essa latência adicional na primeira chamada é frequentemente chamada de inicialização a frio aplicativo.

No plano Premium, você pode ter seu aplicativo previamente começando em um número especificado de instâncias, até o tamanho mínimo de plano.  Pré-começando instâncias também permitem que você previamente escalar um aplicativo antes de alta carga. Conforme o aplicativo for escalado horizontalmente, ele é dimensionado primeiro nas instâncias da warmed previamente. Continuam instâncias adicionais para armazenar em buffer out e passiva imediatamente em preparação para a próxima operação de escala. Por ter um buffer de instâncias previamente prontas, você pode evitar efetivamente latências de inicialização a frio.  Instâncias previamente prontas é um recurso do plano Premium e você precisa manter pelo menos uma instância em execução e disponíveis em todos os momentos em que o plano está ativo.

Você pode configurar o número de instâncias prontas previamente no portal do Azure, selecionando **Scale Out** na **recursos de plataforma** guia.

![Configurações de escala elástica](./media/functions-premium-plan/scale-out.png)

Você também pode configurar instâncias previamente prontas para um aplicativo com a CLI do Azure

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Conectividade de rede privada

Funções do Azure implantadas em um plano Premium aproveita [nova integração da rede virtual para aplicativos web](../app-service/web-sites-integrate-with-vnet.md#new-vnet-integration).  Quando configurado, seu aplicativo pode se comunicar com recursos dentro de sua rede virtual ou protegida por meio de pontos de extremidade de serviço.  Restrições de IP também estão disponíveis no aplicativo para restringir o tráfego de entrada.

Ao atribuir uma sub-rede ao seu aplicativo de funções em um plano Premium, você precisará de uma sub-rede com endereços IP suficientes para cada instância potencial. Embora o número máximo de instâncias pode variar durante a visualização, exigimos um bloco de IP com pelo menos 100 endereços disponíveis.

Para obter mais informações, consulte [integrar seu aplicativo de função com uma rede virtual](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Dimensionamento Elástico rápido

Instâncias de computação adicionais são adicionadas automaticamente para seu aplicativo usando a mesma lógica de dimensionamento rápida que o plano de consumo.  Para saber mais sobre como dimensionamento funciona, consulte [escala e hospedagem de função](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="unbounded-run-duration"></a>Unbounded a duração da execução

Azure Functions em um plano de consumo são limitado a 10 minutos para que uma única execução.  No plano Premium, a duração da execução padrão é 30 minutos para impedir que as execuções de fuga. No entanto, você pode [modificar a configuração do host. JSON](./functions-host-json.md#functiontimeout) para tornar isso unbounded para aplicativos de plano Premium.

Na visualização, sua duração não é garantida após 12 minutos e terá a melhor chance de executar além de 30 minutos, se seu aplicativo não for dimensionado além da sua contagem mínima de trabalho.

## <a name="plan-and-sku-settings"></a>Configurações de plano e SKU

Quando você cria o plano, você definir duas configurações: o número mínimo de instâncias (ou tamanho do plano) e o limite máximo de intermitência.  As instâncias do mínimo de um plano Premium é 1, e o máximo de intermitência durante a visualização é 20.  Instâncias mínimas são reservados e sempre em execução.

> [!IMPORTANT]
> Você é cobrado por cada instância alocada na contagem de instâncias mínima independentemente se funções estiverem em execução ou não.

Se seu aplicativo requer instâncias além do tamanho do seu plano, ele poderá continuar a escalar horizontalmente até que o número de instâncias atinge o limite máximo de intermitência.  Você será cobrado pela instâncias além do tamanho de seu plano somente enquanto eles estão em execução e alugado para você.  Faremos um melhor esforço em dimensionando seu aplicativo-out para seu limite máximo definido, enquanto as instâncias do plano mínimo são garantidas para seu aplicativo.

Você pode configurar o tamanho do plano e limites máximos no portal do Azure por selecionado a **Scale Out** opções em um aplicativo de funções implantado para esse plano ou o plano (sob **recursos de plataforma**).

Você também pode aumentar o limite máximo de intermitente da CLI do Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>SKUs de instância disponíveis

Ao criar nosso dimensionando seu plano, você pode escolher entre três tamanhos de instância.  Você será cobrado para o número total de núcleos e memória consumida por segundo.  Seu aplicativo pode dimensionar automaticamente para várias instâncias conforme necessário.  

|SKU|Núcleos|Memória|Armazenamento|
|--|--|--|--|
|EP1|1|3.5 GB|250 GB|
|EP2|2|7 GB|250 GB|
|EP3|4|14 GB|250 GB|

## <a name="regions"></a>Regiões

Abaixo estão as regiões com suporte no momento para a visualização pública.

|Região|
|--|
|Leste da Austrália|
|Sudeste da Austrália|
|Canadá Central|
|Centro dos EUA|
|Ásia Oriental|
|Leste dos EUA 2|
|França Central|
|Oeste do Japão|
|Coreia Central|
|Norte da Europa|
|Centro-Sul dos Estados Unidos|
|Sul da Índia|
|Sudeste Asiático|
|Oeste do Reino Unido|
|Europa Ocidental|
|Índia Ocidental|
|Oeste dos EUA|

## <a name="known-issues"></a>Problemas conhecidos

Você pode acompanhar o status dos problemas conhecidos do [visualização pública no GitHub](https://github.com/Azure/Azure-Functions/wiki/Premium-plan-known-issues).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entender a escala do Azure Functions e as opções de hospedagem](functions-scale.md)
