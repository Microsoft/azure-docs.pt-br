---
title: Entenda sua fatura do Azure | Microsoft Docs
description: Saiba como ler e entender o uso e a fatura da sua assinatura do Azure
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: 668b32e99ba9a3bdf8e8f16ac51c35c609444cd9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Entenda sua fatura do Microsoft Azure
Para entender sua fatura do Azure, compare sua fatura com o arquivo de uso diário detalhado e os relatórios de gerenciamento de custo no Portal do Azure.

>[!NOTE]
>Este artigo não se aplica aos clientes do EA (Enterprise Agreement). Se você for um cliente do EA, [você pode encontrar a documentação de fatura no Enterprise Portal.](https://ea.azure.com/helpdocs/understandingYourInvoice)  

Para obter um PDF de sua fatura e uma cópia do download do arquivo de uso diário detalhado em CSV, consulte [Como obter sua fatura de cobrança do Azure e dados de uso diário](billing-download-azure-invoice-daily-usage-date.md). 

Para termos e descrições detalhados de sua fatura e arquivo de uso diário detalhado, consulte [Compreender os termos em sua fatura do Microsoft Azure](billing-understand-your-invoice.md) e [Compreender os termos em seu uso detalhado do Microsoft Azure](billing-understand-your-usage.md). 

Para obter detalhes sobre os relatórios de gerenciamento de custo, consulte [Gerenciamento de custos do portal do Azure](https://docs.microsoft.com/en-us/azure/billing/billing-getting-started).

## <a name="charges"></a>Como fazer para ter certeza de que os encargos na minha fatura estão corretos?
<div style="padding-top: 56.25%; position: relative; width: 100%;">
<iframe style="position: absolute;top: 0;left: 0;right: 0;bottom: 0;" width="100%" height="100%" src="https://www.youtube.com/embed/3YegFD769Pk" frameborder="0" allowfullscreen></iframe>
</div>

Se há um encargo na fatura sobre o qual você deseja obter mais detalhes, há duas opções.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>Opção 1: examine a fatura e compare o uso e os custos com o arquivo CSV de uso detalhado

O arquivo CSV de uso detalhado mostra os encargos por período de cobrança e de uso diário. Para obter o arquivo CSV de uso detalhado, veja [How to get your Azure billing invoice and daily usage data](https://docs.microsoft.com/en-us/azure/billing/billing-download-azure-invoice-daily-usage-date) (Como obter sua fatura de cobrança e os dados de uso diário do Azure).

Os encargos de uso são exibidos no nível do medidor. Os termos na lista a seguir têm o mesmo significado na fatura e no arquivo de uso detalhado. Por exemplo, o ciclo de cobrança na fatura é equivalente ao período de cobrança mostrado no arquivo de uso detalhado.

 | Fatura (PDF) | Uso detalhado (CSV)|
 | --- | --- |
|Ciclo de cobrança | Período de Cobrança |
 |Nome |Categoria de medidor |
 |Tipo |Subcategoria de medidor |
 |Recurso |Nome do medidor |
 |Região |Região do medidor |
 |Consumido |Quantidade consumida |
 |Incluso |Quantidade incluída |
 |Faturável |Quantidade de excesso |

A seção de **Encargos de Uso** da sua fatura tem o valor total de cada medidor que foi consumido durante o período de cobrança. Por exemplo, a captura de tela a seguir mostra um encargo de uso para o serviço Agendador do Azure.

![Encargos de uso da fatura](./media/billing-understand-your-bill/1.png)

O mesmo encargo é mostrado na seção **Instrução** de seu CSV de uso detalhado. A quantidade em *Consumido* e o *Valor* correspondem à fatura.

![Encargos de uso de CSV](./media/billing-understand-your-bill/2.png)

Para ver uma divisão desse encargo dia a dia, vá para a seção **Uso Diário** do CSV. Filtre por "Agendador" em *Categoria de Medidor* e você poderá ver em quais dias o medidor foi usado e quanto foi consumido. As informações *Recurso* e *Grupo de Recursos* também são listadas para comparação. A soma dos valores de *Consumido* devem equivaler ao que é mostrado na nota fiscal.

![Seção Uso Diário no CSV](./media/billing-understand-your-bill/3.png)

Para obter o custo por dia, multiplique os valores de *Consumido* com o valor de *Taxa* da seção **Instrução**.

Para saber mais sobre a nota fiscal, consulte [Entenda sua fatura do Azure](billing-understand-your-invoice.md).

Para saber mais sobre cada uma das colunas no CSV, consulte [Entenda seu uso detalhado do Azure](billing-understand-your-invoice.md).

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Opção 2: examine a fatura e compare com o uso e os custos no Portal do Azure

O portal do Azure também pode ajudar a verificar seus encargos. O portal do Azure fornece gráficos de gerenciamento de custos para uma visão geral rápida de seu uso e dos encargos na sua fatura.

Para continuar com o exemplo acima, visite a [página Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), selecione sua assinatura e, em seguida, escolha **Análise de custo**. Daí em diante, você pode especificar o período de tempo e ver os encargos de uso para o serviço Agendador do Azure.

![Exibição de análise de custo no portal do Azure](./media/billing-understand-your-bill/4.png)

Para ver a análise de custo diário em **Histórico de custo**, clique na linha.

![Exibição de histórico de custo no Portal do Azure](./media/billing-understand-your-bill/5.png)

Para saber mais, veja [Evitar custos inesperados com o gerenciamento de custos e cobrança do Azure](billing-getting-started.md#costs).

## <a name="external"></a>E quanto a cobranças de serviço externo?
Serviços externos (também conhecidos como pedidos do Azure Marketplace) são fornecidos por fornecedores de serviços independentes e são cobrados separadamente. Os encargos não aparecem na fatura do Azure. Para saber mais, confira [Entender os encargos dos serviços externos do Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>Como fazer um pagamento?

Se você tiver configurado um cartão de crédito ou débito como método de pagamento, o pagamento será cobrado automaticamente em até dez dias após o término do período de cobrança. Na sua declaração de cartão de crédito, o item de linha diria **MSFT Azure**.

Se você [pagar por fatura](billing-how-to-pay-by-invoice.md), envie seu pagamento para o local indicado na parte inferior da fatura. Para obter mais ajuda, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Como faço para verificar o status de um pagamento feito com cartão de crédito?

[Crie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para solicitar o status do pagamento. 

## <a name="tips-for-cost-management"></a>Dicas para gerenciamento de custos
- Estimar os custos usando a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) e a [calculadora de custo total de propriedade](https://aka.ms/azure-tco-calculator) e obtenha as [informações de preços detalhadas para cada serviço](https://azure.microsoft.com/en-us/pricing/).
- [Configurar alertas de cobrança](billing-set-up-alerts.md).
- [Examine o uso e os custos regularmente no Portal do Azure](billing-getting-started.md#costs).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
