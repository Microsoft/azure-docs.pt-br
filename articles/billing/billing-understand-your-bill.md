---
title: Entender sua fatura do Azure
description: Saiba como ler e entender seu uso e cobrança para sua assinatura do Azure.
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
ms.openlocfilehash: 69659e700ee47c8961ec21f0fb23573e13d72588
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383637"
---
# <a name="understand-your-microsoft-azure-bill"></a>Entenda sua fatura de Microsoft Azure
Para entender sua fatura do Azure, você compara sua fatura com o arquivo de uso diário detalhado e com relatórios de gerenciamento de custos no portal do Azure.

Este artigo não se aplica aos clientes do Azure com um Contrato Enterprise (clientes da EA). Se você for um cliente do EA, consulte [entender sua fatura para clientes do Azure com um Enterprise Agreement](billing-understand-your-bill-ea.md).

Este artigo não se aplica aos clientes do Azure com um [contrato de cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement). Se você tiver um contrato com o cliente da Microsoft, consulte [entender os encargos do Azure em sua fatura do contrato de clientes da Microsoft](billing-mca-understand-your-bill.md).

Para obter uma explicação sobre como o faturamento funciona no programa Provedor de Soluções de Nuvem do Azure (Azure CSP), incluindo o ciclo de cobrança, o preço e o uso, confira [Visão geral da cobrança do Azure CSP](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Examine os encargos

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Se houver uma cobrança em sua fatura sobre a qual você deseja obter mais informações, compare o uso e os custos com o arquivo de uso ou com o portal do Azure.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Opção 1: Comparar o uso e os custos com o arquivo de uso

O arquivo CSV de uso detalhado mostra os encargos por período de cobrança e de uso diário. Para baixar ou exibir o arquivo, consulte [obter sua fatura de cobrança do Azure e dados de uso diário](billing-download-azure-invoice-daily-usage-date.md).

Os encargos de uso são exibidos no nível do medidor. Os termos na lista a seguir têm o mesmo significado na fatura e no arquivo de uso detalhado. Por exemplo, o ciclo de faturamento na fatura é o mesmo que o período de faturamento mostrado no arquivo de uso detalhado.

 | Fatura (PDF) | Uso detalhado (CSV)|
 | --- | --- |
|Ciclo de cobrança | Período de cobrança |
 |Nome |Categoria de medidor |
 |Tipo |Subcategoria de medidor |
 |Recurso |Medir Nome |
 |Região |Região de Medição |
 |Consumido |Quantidade Consumida |
 |Incluídas |Quantidade Incluída |
 |Cobrável |Quantidade Excedente |

A seção encargos de **uso** de sua fatura mostra o valor total de cada medidor que foi consumido durante o período de cobrança. Por exemplo, a imagem a seguir mostra um encargo de uso para o serviço Agendador do Azure.

![Encargos de uso da fatura](./media/billing-understand-your-bill/1.png)

O mesmo encargo é mostrado na seção **Instrução** de seu CSV de uso detalhado. A quantidade em *Consumido* e o *Valor* correspondem à fatura.

![Encargos de uso de CSV](./media/billing-understand-your-bill/2.png)

Para ver uma análise diária do encargo, vá para a seção **uso diário** do arquivo CSV. Filtro para  o Agendador na *categoria de medidor*. Você pode ver em quais dias o medidor foi usado e quanto foi consumido. As informações do *grupo* de *recursos e recursos* também são mostradas para comparação. Os  valores consumidos devem somar e corresponder ao que é mostrado na nota fiscal.

![Seção Uso Diário no CSV](./media/billing-understand-your-bill/3.png)

Para obter o custo por dia, multiplique os valores de *Consumido* com o valor de *Taxa* da seção **Instrução**.

Para obter mais informações, consulte:

- [Entenda sua fatura do Azure](billing-understand-your-invoice.md)
- [Entenda o uso detalhado do Azure](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Opção 2: Compare o uso e os custos no portal do Azure

O portal do Azure também pode lhe ajudar a verificar seus encargos. Para obter uma visão geral rápida do uso e das cobranças faturadas, consulte os gráficos de gerenciamento de custos.

1. No portal do Azure, vá para [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione sua assinatura> **Análise de custo**.
1. Filtrar por **Timespan**.
1. Para continuar o exemplo anterior, você vê uma taxa de uso para o serviço Agendador do Azure.

   ![Exibição de análise de custo no portal do Azure](./media/billing-understand-your-bill/4.png)

1. Selecione a linha que mostra a cobrança para ver a divisão de custo diário.

   ![Exibição de histórico de custo no Portal do Azure](./media/billing-understand-your-bill/5.png)

Para saber mais, veja [Evitar custos inesperados com o gerenciamento de custos e cobrança do Azure](billing-getting-started.md#costs).

## <a name="external"></a>Serviços externos cobrados separadamente

Serviços externos, ou cobranças de mercado, são para recursos que foram criados por fornecedores de software de terceiros. Esses recursos estão disponíveis para uso no mercado do Azure. Por exemplo, um Barracuda Firewall é um recurso de mercado do Azure oferecido por terceiros. Todos os encargos para o firewall e seus medidores correspondentes aparecem como cobranças de serviços externos.

As taxas de serviço externas são cobradas separadamente. Os encargos não aparecem na fatura do Azure. Para saber mais, confira [Entender os encargos dos serviços externos do Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Recursos cobrados por medidores de uso

O Azure não fatura diretamente com base no custo do recurso. Os encargos de um recurso são calculados usando um ou mais medidores. Os medidores são usados para rastrear o uso de um recurso durante seu tempo de vida. Esses medidores são usados para calcular a conta.

Por exemplo, quando você cria um único recurso do Azure, como uma máquina virtual, ele tem uma ou mais instâncias de medidor criadas. Os medidores são usados para controlar o uso do recurso ao longo do tempo. Cada medidor emite registros de uso usados pelo Azure para calcular a fatura.

Por exemplo, uma única máquina virtual (VM) criada no Azure pode ter os seguintes medidores criados para rastrear seu uso:

- Horas de Computação
- Horas de endereço IP
- Transferência de dados em
- Transferência de dados
- Disco gerenciado padrão
- Operações de disco gerenciado padrão
- Disco IO padrão
- Leitura de blob padrão do bloco IO
- Escrita de Blob Padrão de Bloqueio de E/S
- Bloqueio de blobs padrão IO-Block

Quando a VM é criada, cada medidor começa a emitir registros de uso. Esse uso e o preço do medidor são rastreados no sistema de medição do Azure.

## <a name="payment"></a>Pagar sua fatura

Se você configurar um cartão de cartão de crédito como seu método de pagamento, o pagamento será cobrado automaticamente dentro de 10 dias após o término do período de cobrança. Na sua declaração de cartão de crédito, o item de linha diria **MSFT Azure**.

Para alterar o cartão de crédito cobrado, consulte [Adicionar, atualizar ou remover um cartão de crédito do Azure](billing-how-to-change-credit-card.md).

Se você [pagar pela fatura](billing-how-to-pay-by-invoice.md), envie seu pagamento para o local listado na parte inferior da fatura.

Para verificar o status de seu pagamento, [crie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Dicas para gerenciamento de custos

- Estimar os custos usando o:
  - [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)
  - [Custo total de Calculadora de propriedade](https://aka.ms/azure-tco-calculator)
  - [Obter informações para cada serviço de preço](https://azure.microsoft.com/pricing/)
- [Examine o uso e os custos regularmente no Portal do Azure](billing-getting-started.md#costs).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato do cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Saiba mais

- [Obter sua fatura de cobrança do Azure e os dados de uso diário](billing-download-azure-invoice-daily-usage-date.md)
- [Compreender os termos na sua fatura do Microsoft Azure](billing-understand-your-invoice.md)
- [Compreender os termos em uso detalhado do Microsoft Azure](billing-understand-your-usage.md)
- [Gerenciamento de custos do portal do Azure](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Evitar custos inesperados com o gerenciamento de custos e cobrança do Azure](billing-getting-started.md#costs)
