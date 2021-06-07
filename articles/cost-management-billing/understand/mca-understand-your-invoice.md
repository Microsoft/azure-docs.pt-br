---
title: Entender sua fatura do Contrato de Cliente da Microsoft no Azure
description: Saiba como ler e entender sua fatura do Contrato de Cliente da Microsoft no Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 996c974473c53833dd26abae2fb61585999f33b4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050436"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Termos em sua fatura do Contrato de Cliente da Microsoft

Este artigo se aplica a uma conta de cobrança do Azure para um Contrato de Cliente da Microsoft. [Verifique se você tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

Sua fatura fornece um resumo de seus encargos e instruções para pagamento. Ela está disponível para download no formato .pdf (Portable Document Format) no [Portal do Azure](https://portal.azure.com/) ou pode ser enviada via email. Para obter mais informações, confira [Exibir e baixar sua fatura do Microsoft Azure](download-azure-invoice.md).

Assista ao vídeo [Noções básicas da sua fatura do Contrato de Cliente da Microsoft](https://www.youtube.com/watch?v=e2LGZZ7GubA) para saber mais sobre sua fatura e como analisar os preços nela.

>[!VIDEO https://www.youtube.com/embed/e2LGZZ7GubA]

## <a name="billing-period"></a>Período de pagamento

Você é faturado mensalmente. Você pode descobrir em qual dia do mês você recebe faturas, verificando a *data da fatura* em propriedades do perfil de cobrança no [portal do Azure](https://portal.azure.com/). Os encargos incorridos entre o fim do período de cobrança e a data da fatura são incluídos na fatura do mês seguinte, pois estarão no próximo período de cobrança. As datas de início e de término do período de cobrança de cada fatura são listadas no PDF da fatura acima do **Resumo de Cobrança**.

## <a name="invoice-terms-and-descriptions"></a>Termos e descrição da fatura

As seções a seguir listam importantes termos que você vê em sua fatura e fornecem descrições de cada termo.

### <a name="invoice-summary"></a>Resumo da fatura

O **Resumo da Fatura** está na parte superior da primeira página e mostra informações sobre seu perfil de cobrança e quanto você paga.

![Seção Resumo da fatura](./media/mca-understand-your-invoice/invoicesummary.png)

| Termo | DESCRIÇÃO |
| --- | --- |
| Vendido para |Endereço de sua pessoa jurídica, encontrado nas propriedades da conta de cobrança|
| Enviar cobrança para |Endereço para cobrança do perfil de cobrança que recebe a fatura, encontrado nas propriedades do perfil de cobrança|
| Perfil de Cobrança |O nome do perfil de cobrança que recebe a fatura |
| OC número |Um número de ordem de compra opcional, atribuído por você para acompanhamento |
| Número da fatura |Um número de fatura exclusivo gerado pela Microsoft, usado para fins de acompanhamento |
| Data da fatura |Data em que a fatura foi gerada, normalmente de 5 a 12 dias após o término do Ciclo de cobrança. É possível verificar a data da sua fatura em propriedades do perfil de cobrança.|
| Termos de pagamento |Como você paga pela fatura da Microsoft. *30 dias líquidos* significa que você paga dentro de 30 dias da data da fatura. |

### <a name="billing-summary"></a>Resumo da cobrança

O **Resumo da Cobrança** mostra os encargos em relação ao perfil de cobrança desde o período de cobrança anterior, os créditos que foram aplicados, os impostos e o valor total devido.

![Seção Resumo da cobrança](./media/mca-understand-your-invoice/billingsummary.png)

| Termo | Descrição |
| --- | --- |
| Charges|Número total de encargos da Microsoft desse perfil de cobrança desde o último período de cobrança |
| Credits |Créditos recebidos de retornos |
| Créditos Azure aplicados | Os créditos Azure aplicados automaticamente ao Azure são cobrados a cada período de cobrança |
| Subtotal |O valor devido antes da incidência de impostos |
| Imposto |O tipo e o valor do imposto que você paga, dependendo do país/região de seu perfil de cobrança. Se você não precisar pagar impostos, não os verá em sua fatura. |
| Economia total estimada |O valor total estimado que você economizou com os descontos efetivos. Se aplicável, as taxas de desconto efetivo são listadas embaixo dos itens de linha de compra na seção Detalhes por Fatura. |

### <a name="invoice-sections"></a>Seções da fatura

Para cada seção da fatura em seu perfil de cobrança, você verá os encargos, a quantidade de créditos Azure aplicados, os impostos e o valor total devido.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Seção Detalhes por fatura

Os detalhes mostram o custo de cada seção da fatura detalhado por pedido do produto. Em cada pedido do produto, o custo é detalhado pelo tipo de serviço. Você pode encontrar encargos diários para seus produtos e serviços no portal do Azure e no CSV de uso e encargos do Azure. Para obter mais informações, confira [Noções básicas sobre os encargos em sua fatura para um Contrato de Cliente da Microsoft](review-customer-agreement-bill.md).

O valor total devido para cada família de serviços é calculado subtraindo os *créditos Azure* dos *Créditos/encargos* e adicionando o *Imposto*:


![Seção Detalhes por fatura](./media/mca-understand-your-invoice/invoicesectiondetails.png)

| Termo |Descrição |
| --- | --- |
| Preço unitário | O preço unitário efetivo do serviço (na moeda do preço) usado à razão do uso. Ele é exclusivo para um produto, uma família de serviços, um medidor e uma oferta. |
| Qtd | Quantidade comprada ou consumida durante o período de cobrança |
| Encargos/créditos | Valor líquido dos encargos após a aplicação de créditos/reembolsos |
| Crédito Azure | A quantidade de créditos Azure aplicados aos Encargos/créditos|
| Taxa de imposto | Taxas de imposto dependendo do país/região |
| Valor do imposto | Valor do imposto aplicado à compra com base na taxa de imposto |
| Total | O valor total devido da compra |

### <a name="how-to-pay"></a>Como pagar

Na parte inferior da fatura, há instruções para pagar sua fatura. Você pode pagar por cheque, transferência ou online. Se você pagar online, poderá usar um cartão de crédito ou créditos Azure, se aplicável.

### <a name="publisher-information"></a>Informações do editor

Se você tiver serviços de terceiros em sua fatura, o nome e o endereço de cada editor estará listado na parte inferior da fatura.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [Noções básicas sobre os encargos na fatura do seu perfil de cobrança](review-customer-agreement-bill.md)
- [Como obter sua fatura de cobrança e dados de uso diário do Azure](../manage/download-azure-invoice-daily-usage-date.md)
- [Exibir os preços do Azure da sua organização](../manage/ea-pricing.md)
- [Exibir documentos de imposto do seu perfil de cobrança](mca-download-tax-document.md)
