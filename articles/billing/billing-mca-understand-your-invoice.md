---
title: Entenda sua fatura do contrato de cliente da Microsoft no Azure
description: Saiba como ler e entender sua fatura do contrato de cliente da Microsoft no Azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: a5f77120c1d4e8a6721f3bc207132bee19a7772f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383547"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Termos em sua fatura do contrato de cliente da Microsoft

Este artigo se aplica a uma conta de cobrança do Azure para um contrato de cliente da Microsoft. [Verifique se você tem acesso a um contrato do cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

Sua fatura fornece um resumo de seus encargos e instruções de pagamento. Ela está disponível para download no formato .pdf (Portable Document Format) no [Portal do Azure](https://portal.azure.com/) ou pode ser enviada via email. Para obter mais informações, consulte [Exibir e baixar sua fatura Microsoft Azure](billing-download-azure-invoice.md).

## <a name="billing-period"></a>Período de cobrança

Você é faturado mensalmente. Você pode descobrir em qual dia do mês você recebe notas fiscais, verificando *data da fatura* em Propriedades do perfil de cobrança na [portal do Azure](https://portal.azure.com/). Os encargos que ocorrem entre o fim do período de cobrança e a data da fatura são incluídos na nota fiscal do próximo mês, pois estão no próximo período de cobrança. As datas de início e término do período de cobrança de cada fatura são listadas no PDF da nota fiscal acima do **Resumo de cobrança**.

## <a name="invoice-terms-and-descriptions"></a>Termos e descrições da fatura

As seções a seguir listam os termos importantes que você vê em sua fatura e fornecem descrições para cada termo.

### <a name="invoice-summary"></a>Resumo da fatura

O **Resumo da fatura** está na parte superior da primeira página e mostra informações sobre seu perfil de cobrança e como você paga.

![Seção Resumo da fatura](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Termo | Descrição |
| --- | --- |
| Vendido para |Endereço da sua entidade legal, encontrado nas propriedades da conta de cobrança|
| Enviar cobrança para |Endereço de cobrança do perfil de cobrança que recebe a nota fiscal, encontrado nas propriedades do perfil de cobrança|
| Perfil de cobrança |O nome do perfil de cobrança que recebe a fatura |
| PEDIDO número |Um número de ordem de compra opcional, atribuído por você para acompanhamento |
| Número da nota fiscal |Um número de fatura exclusivo gerado pela Microsoft usado para fins de acompanhamento |
| Data da fatura |Data em que a nota fiscal é gerada, normalmente de cinco a 12 dias após o término do ciclo de cobrança. Você pode verificar a data da fatura nas propriedades do perfil de cobrança.|
| Termos de pagamento |Como você paga pela sua fatura da Microsoft. *Net 30 dias* significa que você paga dentro de 30 dias da data da fatura. |

### <a name="billing-summary"></a>Resumo de cobrança

O **Resumo de cobrança** mostra os encargos em relação ao perfil de cobrança desde o período de cobrança anterior, todos os créditos que foram aplicados, impostos e o valor total devido.

![Seção de Resumo de cobrança](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Termo | Descrição |
| --- | --- |
| Encargos|Número total de encargos da Microsoft para este perfil de cobrança desde o último período de cobrança |
| Créditos |Créditos recebidos de Devoluções |
| Créditos do Azure aplicados | Os créditos do Azure que são aplicados automaticamente ao Azure cobram cada período de cobrança |
| Subtotal |O valor de pré-imposto devido |
| Imposto |O tipo e a quantidade de imposto que você paga, dependendo do país/região do seu perfil de cobrança. Se você não tiver que pagar o imposto, não verá o imposto em sua fatura. |
| Total de economias estimadas |O valor total estimado que você salvou dos descontos efetivos. Se aplicável, as tarifas de desconto efetivas são listadas abaixo da seção itens de linha de compra em detalhes por fatura. |

### <a name="invoice-sections"></a>Seções de fatura

Para cada seção de fatura sob seu perfil de cobrança, você verá os encargos, a quantidade de créditos do Azure aplicados, impostos e o valor total devido.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Detalhes por seção de fatura

Os detalhes mostram o custo de cada seção da fatura dividida por ordem de produto. Em cada ordem de produto, o custo é dividido pelo tipo de serviço. Você pode encontrar cobranças diárias para seus produtos e serviços no portal do Azure e no CSV de uso e encargos do Azure. Para saber mais, consulte [entender os encargos em sua fatura para um contrato de cliente da Microsoft](billing-mca-understand-your-bill.md).

O valor total devido para cada família de serviços é calculado subtraindo *créditos do Azure* de *créditos/* encargos e adicionando *impostos*:


![Detalhes por seção de fatura](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Termo |Descrição |
| --- | --- |
| Preço unitário | O preço unitário efetivo do serviço (em moeda de preço) que é usado para a taxa de uso. Isso é exclusivo para um produto, uma família de serviços, um medidor e uma oferta. |
| Quantidade | Quantidade comprada ou consumida durante o período de cobrança |
| Cobranças/créditos | Quantidade líquida de encargos após os créditos/reembolsos serem aplicados |
| Crédito do Azure | A quantidade de créditos do Azure aplicados aos encargos/créditos|
| Taxa de imposto | Taxa (s) de imposto dependendo do país/região |
| Valor do imposto | Valor de imposto aplicado à compra com base na taxa de imposto |
| Total | O valor total devido para a compra |

### <a name="how-to-pay"></a>Como pagar

Na parte inferior da nota fiscal, há instruções para pagar sua fatura. Você pode pagar por cheque, fio ou online. Se você pagar online, poderá usar um cartão de crédito ou créditos do Azure, se aplicável.

### <a name="publisher-information"></a>Informações do editor

Se você tiver serviços de terceiros em sua fatura, o nome e o endereço de cada publicador serão listados na parte inferior da fatura.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato do cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [Entender os encargos na fatura do seu perfil de cobrança](billing-mca-understand-your-bill.md)
- [Como obter sua fatura de cobrança e dados de uso diário do Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Exibir os preços do Azure de sua organização](billing-ea-pricing.md)
- [Exibir documentos de imposto para seu perfil de cobrança](billing-mca-download-tax-document.md)
