---
title: "Preço e cobrança - Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Saiba como os preços e a cobrança funcionam para Aplicativos Lógicos do Azure."
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: LADocs; klam
ms.openlocfilehash: 63784c5e3af360b2f3f8cb330a9df8b27a85d859
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-pricing-model"></a>Modelo de preços de Aplicativos Lógicos
Os Aplicativos Lógicos do Azure permitem que você dimensione e execute fluxos de trabalho de integração na nuvem.  A seguir, detalhes sobre a cobrança e os planos de preços para os Aplicativos Lógicos.
## <a name="consumption-pricing"></a>Preço por consumo
Aplicativos Lógicos recém-criado usam um plano de consumo. Com o modelo de preços de consumo dos Aplicativos Lógicos, você só paga pelo que usa.  Os Aplicativos Lógicos não sofrem limitações ao usar um plano de consumo.
Todas as ações executadas em uma execução de uma instância de aplicativo lógico são limitadas.
### <a name="what-are-action-executions"></a>O que são execuções de ação?
Cada etapa em uma definição de aplicativo lógico é uma ação que inclui gatilhos, etapas de fluxo de controle, como condições, escopos, loops for each e loops do until, chamadas a conectores e chamadas para ações nativas.
Os gatilhos são ações especiais projetadas para criar uma nova instância do aplicativo lógico quando um evento específico ocorrer.  Há diversos comportamentos diferentes para gatilhos que podem afetar o modo como o aplicativo lógico é monitorado.
* **Gatilho de sondagem** – esse gatilho sonda continuamente um ponto de extremidade até receber uma mensagem que satisfaça os critérios para a criação de uma instância de um aplicativo lógico.  O intervalo de sondagem pode ser configurado no gatilho no Designer dos Aplicativos Lógicos.  Cada solicitação de sondagem, mesmo se não criar uma instância de um aplicativo lógico, contará como uma execução da ação.
* **Gatilho de webhook** – esse disparador espera o envio de uma solicitação por parte do cliente em um ponto de extremidade específico.  Cada solicitação enviada ao ponto de extremidade do webhook conta como uma execução da ação. A Solicitação e o gatilho Webhook HTTP são gatilhos de webhook.
* **Gatilho de recorrência** – esse gatilho cria uma nova instância do aplicativo lógico com base no intervalo de recorrência configurado no gatilho.  Por exemplo, um gatilho de recorrência pode ser configurado para executar a cada três dias ou até mesmo a cada minuto.

As execuções do gatilho podem ser vistas na folha de recursos dos Aplicativos Lógicos na parte do Histórico de Gatilhos.

Todas as ações executadas, bem-sucedidas ou não, são limitadas como uma execução de ação.  As ações que foram ignoradas devido a uma condição não atendida, ou ações que não foram executadas porque o aplicativo lógico foi encerrado antes da conclusão, não são contadas como execuções de ação.

Ações executadas em loops são contadas por iteração do loop.  Por exemplo, uma única ação em um loop for each iterando por uma lista de 10 itens será contada como o número de itens na lista (10) multiplicada pelo número de ações no loop (1), mais um para o início do loop que, neste exemplo, seria (10 * 1) + 1 = 11 execuções de ação.
Os Aplicativos Lógicos desabilitados não podem ter novas instâncias criadas e, portanto, não serão cobrados durante o tempo em que estiverem desabilitados.  Lembre-se de que depois de desabilitar um aplicativo lógico pode demorar algum tempo para as instâncias fecharem para novas sessões antes de serem completamente desabilitadas.
### <a name="integration-account-usage"></a>Uso da conta de integração
Incluído no consumo com base em uso está uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) para exploração, desenvolvimento e testes, permitindo que você use os recursos [B2B/EDI](logic-apps-enterprise-integration-b2b.md) e [processamento XML](logic-apps-enterprise-integration-xml.md) de Aplicativos Lógicos sem custo adicional. É possível criar no máximo uma conta por região e armazenar até 10 contratos e 25 mapas. Parceiros, certificados e esquemas não têm limites e você pode carregar quantos forem necessários.

Além da inclusão de contas de integração com consumo, você também pode criar contas de integração padrão sem esses limites e com nosso SLA de aplicativos lógicos padrão. Para saber mais, veja [Preços do Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="app-service-plans"></a>Planos do Serviço de Aplicativo
Os Aplicativos Lógicos criados anteriormente com referência a um Plano do Serviço de Aplicativo continuarão a se comportar como antes. Dependendo do plano escolhido, eles são limitados após as execuções diárias prescritas serem excedidas mas são cobradas usando o medidor de execução da ação.
Os clientes do EA que têm um plano do serviço de aplicativo em sua assinatura, a qual não precisa ser explicitamente associada ao aplicativo lógico, obtêm o benefício das quantidades incluídas.  Por exemplo, se você tiver um plano do serviço de aplicativo Standard em sua assinatura de EA e um aplicativo lógico na mesma assinatura, você não será cobrado por 10.000 execuções de ação por dia (veja a tabela a seguir). 

Planos do Serviço de Aplicativo e suas execuções diária de ação permitida:
|  | Gratuito/Compartilhado/Básico | Standard | Premium |
| --- | --- | --- | --- |
| Execuções de ação por dia |200 |10.000 |50.000 |
### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>Converter de preços do Plano do Serviço de Aplicativo para consumo
Para alterar um Aplicativo Lógico que tem um Plano do Serviço de Aplicativo associado para um modelo de consumo, remova a referência ao Plano do Serviço de Aplicativo na definição do Aplicativo Lógico.  Essa alteração pode ser feita com uma chamada a um cmdlet do PowerShell: `Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`
## <a name="pricing"></a>Preços
Para obter detalhes sobre preços, confira [Preços de Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Próximas etapas
* [Uma visão geral dos Aplicativos Lógicos][whatis]
* [Criar seu primeiro aplicativo lógico][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-what-are-logic-apps.md
[create]: logic-apps-create-a-logic-app.md

