---
title: "Configurar alertas de cobrança ou de crédito para assinaturas do Azure | Microsoft Docs"
description: "Descreve como você pode configurar alertas na sua conta do Azure para que possa evitar surpresas na cobrança."
keywords: "alerta de crédito, alerta de cobrança"
services: 
documentationcenter: 
author: vikdesai
manager: tonguyen
editor: 
tags: billing
ms.assetid: 9b7b3eeb-cd9d-4690-86a3-51b1e2a8974f
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: vikdesai
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1fc0cb2b036e835450ee0fc404cce12439fabc77
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>Configurar alertas de cobrança ou de crédito para assinaturas do Microsoft Azure
Caso seja o Administrador da Conta de uma assinatura do Azure, você poderá usar o Serviço de Alerta de Cobrança do Azure para criar alertas personalizados de cobrança que ajudam a monitorar e gerenciar a atividade de cobrança de suas contas do Azure.

Esse serviço está no modo preview, de modo que você precisa habilitá-lo primeiro na página Recursos do Preview.

## <a name="set-the-alert-threshold-and-email-recipients"></a>Definir os alertas de limite e destinatários de email
1. Visite [a página Recursos do Preview](https://account.windowsazure.com/PreviewFeatures) e habilite **Serviço de Alerta de Cobrança**.

1. Depois de receber o email de confirmação de que o serviço de cobrança foi ativado para a sua assinatura, visite [a página Assinaturas](https://account.windowsazure.com/Subscriptions) no portal da conta. Clique na assinatura que você deseja monitorar e, em seguida, clique em **Alertas**.

    ![Captura de tela da exibição de assinaturas do Centro de Contas do Azure, com Alertas realçado][Image1]

2. Em seguida, clique em **Adicionar Alerta** para criar o primeiro. Você pode configurar até cinco alertas de cobrança por assinatura, com um limite diferente e até dois destinatários de email para cada alerta.

    ![Captura de tela da exibição Alertas, na qual é possível adicionar alertas][Image2]

3. Ao adicionar um alerta, você informa um nome exclusivo, escolhe um limite de gastos e escolhe os endereços de email para os quais os alertas serão enviados. Ao configurar o limite, você pode escolher um **Total de cobrança** ou um **Crédito Monetário** na lista **Alerta para**. Para total de cobrança, um alerta é enviado quando o gasto com a assinatura excede o limite. Para crédito monetário, um alerta é enviado quando os créditos monetários reduzem abaixo do limite. Créditos monetários geralmente se aplicam a assinaturas de Avaliação Gratuita e do Visual Studio.

    ![Captura de tela da exibição de adição de alerta, na qual é possível configurar destinatários][Image3]

O Azure é compatível com qualquer endereço de email, mas não verifica se este funciona, por isso, verifique atentamente se digitou corretamente.

## <a name="check-on-your-alerts"></a>Verificar os seus alertas
Depois de configurar os alertas, o Centro de Contas lista e mostra quantos mais você pode configurar. Para cada alerta, você verá a data e a hora de envio, se é um alerta do Total de Cobrança ou de Crédito Monetário e o limite definido. O formato de data e hora é 24 horas - Hora Universal Coordenada (UTC) e a data usa o formato aaaa-mm-dd. Clique no sinal de adição de um alerta na lista para editá-lo ou clique na lixeira para excluí-lo.

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>Alertas de cobrança para clientes do EA (Enterprise Agreement)
Os clientes do EA podem obter alertas para cada departamento em um registro configurando cotas de gasto. Para começar, confira [Cotas de Gasto por Departamento](https://ea.azure.com/helpdocs/departmentSpendingQuotas) no portal do EA.

## <a name="learn-more-about-azure-cost-management"></a>Saiba mais sobre o gerenciamento de custos do Azure
- Faça uma estimativa dos custos usando a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/), a [calculadora do custo total de propriedade](https://aka.ms/azure-tco-calculator) e quando você adiciona um serviço.
- [Analise o uso e os custos regularmente no Portal do Azure](billing-getting-started.md#costs).
- Ative [Recomendações de custo do Assistente do Azure](../advisor/advisor-cost-recommendations.md).

Para saber mais, confira [Orientações sobre o gerenciamento de custos do Azure](billing-getting-started.md).

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 
