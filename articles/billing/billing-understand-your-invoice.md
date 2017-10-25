---
title: Compreenda sua fatura do Azure | Microsoft Docs
description: Saiba como ler e entender o uso e a fatura da sua assinatura do Azure
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: edd4702ccab9162329bf83993d236eec9ec40b3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Compreenda os termos na sua fatura do Microsoft Azure
A fatura fornece um resumo de seus encargos e fornece instruções para pagamento. Ela está disponível para download no formato .pdf (Portable Document Format) no [Portal do Azure](https://portal.azure.com/) ou pode ser enviada via email. Para saber mais, confira [How to get your Azure billing invoice and daily usage data](billing-download-azure-invoice-daily-usage-date.md) (Como obter sua fatura de cobrança e os dados de uso diário do Azure).

Algumas coisas a serem observadas:

-   Se você estiver usando uma assinatura de avaliação gratuita, você poderá obter suas informações de uso diário do Portal do Azure, mas não terá uma fatura.

-   Sua fatura atual pode apresentar até 24 horas de uso do final do período de cobrança anterior.

-   Os encargos listados nos demonstrativos de cobrança para clientes internacionais servem somente para fins de estimativa. Os bancos podem ter custos diferentes para as taxas de conversão.

<div style="padding-top: 56.25%; position: relative; width: 100%;">
<iframe style="position: absolute;top: 0;left: 0;right: 0;bottom: 0;" width="100%" height="100%" src="https://www.youtube.com/embed/jWG1lyJe3Mg" frameborder="0" allowfullscreen></iframe>
</div>

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Descrições e termos detalhados da fatura
As seções a seguir listam os termos importantes que você vê em sua fatura, junto com descrições para cada termo.

### <a name="account-information"></a>Informações da conta

A seção de dados da conta da fatura é na parte superior da primeira página e mostra informações sobre seu perfil e assinatura.

![Seção de dados da conta da fatura](./media/billing-understand-your-invoice/1.png)

| Termo | Descrição |
| --- | --- |
| OC do cliente No. |Um número de ordem de compra opcional, atribuído por você para acompanhamento |
| Nº da Fatura |Um número de fatura exclusivo gerado pela Microsoft, usado para fins de acompanhamento |
| Ciclo de cobrança |Intervalo de datas coberto por esta nota fiscal |
| Data da fatura |Data em que a fatura foi gerada, normalmente um dia após o fim do ciclo de cobrança |
| Método de pagamento |Tipo de pagamento usado na conta (fatura ou cartão de crédito) |
| Enviar cobrança para |Endereço de cobrança listado para a conta |
| Oferta de assinatura (pré-pago) |Tipo de oferta de assinatura que tiver sido adquirida (Pré-pago, BizSpark Plus, Azure Pass etc.). Para obter mais informações, consulte [Tipos de oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/). |
| Email do proprietário da conta | O endereço de email da conta no qual a conta do Microsoft Azure está registrada. <br /><br />Para alterar seu endereço de email, confira [Como alterar as informações de perfil de sua conta do Azure, como email de contato, endereço e número de telefone](billing-how-to-change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Entender o resumo da fatura
A seção **Resumo da Fatura** da fatura lista os valores totais de transações desde seu último período de cobrança, além de seus encargos de uso atuais.

![Seção Resumo da fatura](./media/billing-understand-your-invoice/2.png)

O nome da assinatura ("Armazenamento de Produção") é o nome da assinatura para esta fatura.

#### <a name="understand-the-previous-charges"></a>Entender as cobranças anteriores
A seção saldo anterior, pagamentos e saldo pendente da fatura resume as transações desde o último período de cobrança.

| Termo | Descrição |
| --- | --- |
| Saldo anterior |O valor total devido no último período de cobrança |
| Pagamentos |Os pagamentos totais e créditos aplicados ao último período de cobrança |
| Saldo pendente (do ciclo de cobrança anterior) |Quaisquer créditos ou saldo restante em sua conta desde o último período de cobrança |

#### <a name="understand-the-current-charges"></a>Entender os encargos atuais
A seção Encargos Atuais da fatura mostra detalhes sobre seus encargos mensais para o último período de cobrança.

| Termo | Descrição |
| --- | --- |
| Encargos de uso |Encargos de uso são o total de encargos mensais em uma assinatura para o período de cobrança atual|
| Descontos |Descontos de serviço aplicados ao período de cobrança atual|
| Ajustes |Créditos diversos (Uso Gratuito, Créditos, etc.) ou encargos pendentes aplicados ao período de cobrança atual.<br/><br/>Por exemplo, se você tiver a oferta Visual Studio Enterprise com MSDN, verá um crédito mensal. Se você cancelar sua assinatura, verá quaisquer encargos mensais pelo uso que excedem o crédito mensal obtido com sua oferta de assinatura. Os encargos incorrem no começo de seu período de cobrança atual até a data de cancelamento da assinatura. |

#### <a name="sold-to-and-payment-instructions"></a>Instruções de venda e pagamento

A tabela a seguir descreve as instruções de venda e pagamento mostradas na segunda página da fatura.

| Termo |Descrição |
| --- | --- |
| Vendido para |O endereço de perfil que está na conta. <br/><br/>Se você precisar alterar o endereço, confira [Como alterar as informações de perfil de sua conta do Azure, como email de contato, endereço e número de telefone](billing-how-to-change-azure-account-profile.md).|
| Instruções de pagamento |Instruções sobre como pagar dependendo da forma de pagamento (por exemplo, por cartão de crédito ou por fatura). |

#### <a name="usage-charges"></a>Encargos de Uso

A seção Encargos de uso da fatura exibe informações de nível de medidor nos encargos.

![Seção Encargos de uso](./media/billing-understand-your-invoice/3.png)

A tabela a seguir descreve os cabeçalhos de coluna de encargos de uso mostrados na fatura.

| Termo |Descrição |
| --- | --- |
| Nome |Identifica o serviço de nível superior para o uso |
| Tipo |Define o tipo de serviço do Azure e pode afetar a tarifa |
| Recurso |Identifica a unidade de medida para o medidor sendo consumido |
| Região |Identifica o local do datacenter para determinados serviços que são cobrados com base no local do datacenter |
| Consumido |O valor do medidor usado durante o período de cobrança |
| Incluso |O valor do medidor que está incluído gratuitamente em seu período de cobrança atual |
| Faturável |Mostra a diferença entre a Quantidade Consumida e a Quantidade Incluída. A cobrança é feita com base nessa quantidade. Para ofertas pré-pagas sem quantidade incluída na oferta, esse total será igual à Quantidade Consumida |
| Tarifa |A tarifa cobrada por unidade faturável |
| Valor |Mostra o resultado da multiplicação da coluna Quantidade Excedente pela coluna Tarifa. Se a Quantidade Consumida não exceder a Quantidade Incluída, nenhum encargo será exibido nessa coluna. |
| Subtotal |A soma de todos os seus encargos antes do imposto para esse período de cobrança |
| Grande Total |A soma de todos os seus encargos após o imposto para esse período de cobrança |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Como fazer para ter certeza de que os encargos na minha fatura estão corretos?
Se há um encargo na fatura sobre o qual você deseja obter mais detalhes, consulte [Entenda sua fatura do Microsoft Azure.](billing-understand-your-bill.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
