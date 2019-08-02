---
title: Preço e cobrança - Aplicativos Lógicos do Azure | Microsoft Docs
description: Saiba como os preços e a cobrança funcionam para Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 05/22/2019
ms.openlocfilehash: 04b1d0eda85972517155f80488ad590fb56619ab
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190674"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modelo de preços para os Aplicativos Lógicos do Azure

[Aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) ajuda você a cria e executa fluxos de trabalho de integração automatizada que pode dimensionar na nuvem. Este artigo descreve como cobrança e preços funcionam para aplicativos lógicos do Azure. Para informações específico sobre preços, consulte [preços de aplicativos lógicos do Azure](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modelo de preço por consumo

Para novos aplicativos lógicos que são executados no serviço de aplicativo lógico do Azure público ou "global", você paga apenas pelo que usar. Esses aplicativos lógicos usam um plano baseado em consumo e modelo de preços. Na sua definição de aplicativo lógico, cada etapa é uma ação. Por exemplo, as ações incluem:

* Gatilhos são ações especiais. Todos os aplicativos lógicos precisam de um gatilho como a primeira etapa.
* "Internas" ou nativas ações, como HTTP, chamadas para o Azure Functions e o gerenciamento de API e assim por diante
* Chamadas a conectores, como Outlook 365, Dropbox e assim por diante
* Controlar as etapas de fluxo, como loops, instruções condicionais e assim por diante

Aplicativos lógicos do Azure medidores todas as ações que são executados em seu aplicativo lógico. Saiba mais sobre como a cobrança funciona para [disparadores](#triggers) e [ações](#actions).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modelo de preços fixo

Uma [ *ambiente do serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) fornece uma maneira particular, isolada e dedicada para que você possa criar e executar aplicativos lógicos que podem acessar recursos em uma rede virtual do Azure. Para novos aplicativos lógicos que são executados dentro de um ISE, você paga um [preço mensal fixo](https://azure.microsoft.com/pricing/details/logic-apps) para gatilhos e ações internas e também para conectores padrão.

O ISE também inclui um conector gratuito do Enterprise, que inclui como muitas *conexões* conforme desejado. O uso de conectores adicionais do Enterprise é cobrado com base nas [preço de consumo de Enterprise](https://azure.microsoft.com/pricing/details/logic-apps). Em geral conectores do Enterprise disponíveis são cobrados pelo preço de consumo do Enterprise. Conectores empresariais de visualização pública são cobrados a [taxa de conector padrão](https://azure.microsoft.com/pricing/details/logic-apps).

> [!NOTE]
> Dentro de um ISE, interna de gatilhos e ações de exibir o **Core** rotular e executar no ISE do mesmo como seus aplicativos lógicos. Conectores Standard e Enterprise que exibem a **ISE** rótulo executado no ISE do mesmo como seus aplicativos lógicos. Os conectores que não exibem o rótulo ISE executados no serviço global de aplicativos lógicos.

A unidade base do ISE corrigiu capacidade, portanto, se você precisar de mais taxa de transferência, você pode [adicionar mais unidades de escala](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), durante a criação ou posteriormente. Aplicativos lógicos que são executados em um ISE não incorrem em custos de retenção de dados.

Para informações específico sobre preços, consulte [preços de aplicativos lógicos do Azure](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="connectors"></a>

## <a name="connectors"></a>Conectores

Conectores de aplicativos lógicos do Azure ajudam seus aplicativos de acesso do aplicativo lógico, serviços e sistemas na nuvem ou no local, fornecendo [disparadores](#triggers), [ações](#actions), ou ambos. Conectores são classificados como Standard ou Enterprise. Para obter uma visão geral sobre esses conectores, consulte [conectores para aplicativos lógicos do Azure](../connectors/apis-list.md). Se não há conectores pré-compilados estão disponíveis para as APIs REST que você deseja usar em seus aplicativos lógicos, você pode criar [conectores personalizados](https://docs.microsoft.com/connectors/custom-connectors), que são apenas wrappers em torno de as APIs REST. Conectores personalizados são cobrados como conectores Standard. As seções a seguir fornecem mais informações sobre como a cobrança por gatilhos e ações funcionam.

<a name="triggers"></a>

## <a name="triggers"></a>Gatilhos

Os gatilhos são ações especiais que criam uma instância de aplicativo lógico quando ocorre um evento específico. Dispara ações de diferentes formas, o que afeta o modo como o aplicativo lógico é monitorado. Aqui estão os vários tipos de gatilhos que existem no aplicativo lógico do Azure:

* **Gatilho de sondagem**: Esse gatilho sonda continuamente um ponto de extremidade para mensagens que satisfaçam os critérios para criar uma instância do aplicativo lógico e iniciar o fluxo de trabalho. Mesmo quando nenhuma instância de aplicativo lógico é criada, os Aplicativos Lógicos medem cada solicitação de pesquisa como uma execução. Para especificar o intervalo de sondagem, configure o gatilho no Designer de Aplicativos Lógicos.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook trigger**: Esse disparador espera por um cliente enviar uma solicitação para um ponto de extremidade específico. Cada solicitação enviada ao ponto de extremidade do webhook conta como uma execução da ação. Por exemplo, a Solicitação e o gatilho Webhook HTTP são gatilhos de webhook.

* **Gatilho de recorrência**: Esse gatilho cria uma instância de aplicativo lógico com base no intervalo de recorrência que você configurou no gatilho. Por exemplo, você pode definir um gatilho de recorrência que é executado a cada três dias ou em um agendamento mais complexo.

<a name="actions"></a>

## <a name="actions"></a>Ações

Aplicativos lógicos do Azure limita as ações "internas", como HTTP, como ações nativas. Por exemplo, as ações internas incluem chamadas HTTP, chamadas do Azure Functions ou gerenciamento de API e controlam as etapas de fluxo, como condições, loops e instruções switch. Cada ação tem seu próprio tipo de ação. Por exemplo, as ações que chamem [conectores](https://docs.microsoft.com/connectors) têm o tipo de "ApiConnection". Esses conectores são classificados como padrão ou conectores empresariais, que são medidos com base em suas respectivas [preços](https://azure.microsoft.com/pricing/details/logic-apps). Conectores empresariais na *visualização* são cobradas como conectores Standard.

Aplicativos lógicos do Azure medidores todas as ações bem-sucedidas e malsucedidas como execuções. No entanto, aplicativos lógicos não monitora estas ações:

* Ações que são ignoradas devido a condições não atendidas
* Ações que não são executadas porque o aplicativo lógico parou antes de terminar

Para ações que são executadas em loops, aplicativos lógicos do Azure conta cada ação para cada ciclo no loop. Por exemplo, suponha que você tenha um loop "para cada" que processe uma lista. Os aplicativos lógicos medem uma ação nesse loop multiplicando o número de itens da lista pelo número de ações no loop e adiciona a ação que inicia o loop. Portanto, o cálculo para obter uma lista de itens de 10 é (10 * 1) + 1, o que resulta em 11 execuções de ação.

## <a name="disabled-logic-apps"></a>Aplicativos lógicos desabilitados

Desabilitada a lógica de aplicativos não sejam cobrados porque eles não é possível criar novas instâncias, enquanto eles permanecerão desabilitados.
Depois de desativar um aplicativo lógico, qualquer instância em execução no momento pode demorar algum tempo antes de parar completamente.

## <a name="integration-accounts"></a>Contas de integração

O modelo de preço fixo se aplica ao [contas de integração](logic-apps-enterprise-integration-create-integration-account.md) onde você pode explorar, desenvolver e testar o [B2B e EDI](logic-apps-enterprise-integration-b2b.md) e [processamento XML](logic-apps-enterprise-integration-xml.md) recursos em aplicativos lógicos do Azure sem nenhum custo adicional.
Você pode ter uma conta de integração em cada região do Azure. Cada conta de integração pode armazenar até [números específicos de artefatos](../logic-apps/logic-apps-limits-and-config.md), que incluem parceiros comerciais, contratos, mapas, esquemas, montagens, certificados, configurações de lote e assim por diante.

Aplicativos lógicos do Azure oferece as contas de integração gratuita, Basic e Standard. As camadas básica e Standard têm suporte pelo contrato de nível de serviço (SLA), aplicativos lógicos enquanto a camada gratuita não é suportada por um SLA e tem limites sobre o uso e a taxa de transferência.

Para escolher entre uma conta de integração gratuito, básico ou padrão:

* **Grátis**: Para quando você deseja tentar exploratórios cenários, não os cenários de produção.

* **Básico**: Quando é preciso apenas a manipulação de mensagens ou para atuar como um parceiro de pequenas empresas que tem uma relação de parceiro comercial com uma entidade de negócios maior.

* **Standard**: Para quando você tem relações de B2B mais complexas e um número maior de entidades que você deve gerencie.

Para informações específico sobre preços, consulte [preços de aplicativos lógicos do Azure](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="data-retention"></a>

## <a name="data-retention"></a>Retenção de dados

Exceto para aplicativos lógicos que executar em um ambiente de serviço de integração (ISE), todas as entradas e saídas que são armazenadas no histórico de execuções do aplicativo lógico serão cobradas com base em um aplicativo lógico [execute o período de retenção](logic-apps-limits-and-config.md#run-duration-retention-limits). Aplicativos lógicos que são executados em um ISE não incorrem em custos de retenção de dados. Para informações específico sobre preços, consulte [preços de aplicativos lógicos do Azure](https://azure.microsoft.com/pricing/details/logic-apps).

Para ajudá-lo a monitorar o consumo de armazenamento do seu aplicativo lógico, você pode:

* Exiba o número de unidades de armazenamento em GB que seu aplicativo lógico usa mensalmente.
* Exiba os tamanhos para entradas e saídas de uma ação específica no histórico de execução do aplicativo lógico.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Consumo de armazenamento do aplicativo de lógica de exibição

1. No portal do Azure, encontre e abra seu aplicativo lógico.

1. No menu do aplicativo lógico, sob **Monitoring**, selecione **métricas**.

1. No painel direito, sob **título do gráfico**, da **métrica** lista, selecione **uso de cobrança de execuções de consumo de armazenamento**.

   Essa métrica fornece o número de consumo de unidades de armazenamento em GB por mês que estão sendo faturadas.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Exibir a entrada da ação e tamanhos de saída

1. No portal do Azure, encontre e abra seu aplicativo lógico.

1. No menu do aplicativo lógico, selecione **visão geral**.

1. No painel direito, sob **histórico de execuções**, selecione a execução que tem as entradas e saídas você deseja verificar.

1. Sob **execução do aplicativo lógico**, escolha **Run Details**.

1. No **detalhes de execução do aplicativo lógico** painel, na tabela de ações, que lista o status e a duração de cada ação, selecione a ação que você deseja exibir.

1. No **ação de aplicativo lógico** painel, localize os tamanhos para essa ação entradas e saídas são exibidos em respectivamente **Inputs link** e **link saídas**.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre aplicativos lógicos do Azure](logic-apps-overview.md)
* [Criar seu primeiro aplicativo lógico](quickstart-create-first-logic-app-workflow.md)
