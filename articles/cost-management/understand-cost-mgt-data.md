---
title: Entender os dados do Gerenciamento de Custos do Azure | Microsoft Docs
description: Este artigo ajuda você a entender melhor os dados incluídos em seu Gerenciamento de Custos do Azure e a frequência com que eles são processados, coletados, mostrados e fechados.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 81947baccf723ab759f2dc24df40b81af4502e09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61003969"
---
# <a name="understand-cost-management-data"></a>Entender os dados de Gerenciamento de Custos

Este artigo ajuda você a entender melhor os dados incluídos no Gerenciamento de Custos do Azure. Além disso, ele explica a frequência com que esses dados são processados, coletados, mostrados e fechados. Você é cobrado mensalmente pelo uso do Azure. No entanto, o tipo de sua assinatura do Azure determina quando o mês de cobrança termina. A frequência com que o Gerenciamento de Custos recebe dados de uso varia com base em diferentes fatores. Esses fatores incluem o tempo necessário para processar os dados e a frequência com que os serviços do Azure transmitem o uso para o sistema de cobrança.

Gerenciamento de custos inclui o uso e compras, incluindo as reservas e ofertas de terceiros para contas de Enterprise Agreement (EA). Contas de contrato de cliente da Microsoft (MCA) e as assinaturas pré-pagas incluem apenas o uso de serviços do Azure Marketplace. Suporte e outros custos não são incluídos. Os custos são calculados até que uma fatura é gerada e não fatorar créditos.

## <a name="supported-microsoft-offers"></a>Ofertas da Microsoft com suporte

As informações a seguir mostram as atuais [ofertas do Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) com suporte no Gerenciamento de Custos do Azure.  Uma oferta do Azure é o tipo de assinatura do Azure que você tem.

| Category  | **Nome da oferta** | **ID da cota** | **Número da oferta** |
| --- | --- | --- | --- |
| **Azure Alemanha** | [Pagamento Conforme o Uso do Azure Alemanha](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Governamental** | Azure Governamental Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P |
| **Enterprise Agreement (EA)** | Desenvolvimento/Teste Enterprise                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P |
| **Enterprise Agreement (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P |
| **Contrato do cliente da Microsoft** | [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/D |
| **Contrato do cliente da Microsoft** | [Plano do Microsoft Azure para desenvolvimento/teste](https://azure.microsoft.com/offers/ms-azr-0148g)  | MSDNDevTest_2014-09-01 | N/D |
| **Microsoft Developer Network (MSDN)** | [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p) | MSDN_2014-09-01 | MS-AZR-0062P |
| **Pré-paga** | [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p)                       | PayAsYouGo_2014-09-01 | MS-AZR-0003P |
| **Pré-paga** | [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p)              | MSDNDevTest_2014-09-01 | MS-AZR-0023P |
| **Pré-paga** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)           | MPN_2014-09-01 | MS-AZR-0025P |
| **Pré-paga** | [Teste Gratuito](https://azure.microsoft.com/offers/ms-azr-0044p)                          | FreeTrial_2014-09-01 | MS-AZR-0044P |
| **Pré-paga** | [Azure via Open](https://azure.microsoft.com/offers/ms-azr-0111p)                       | AzureInOpen_2014-09-01 | MS-AZR-0111P |
| **Pré-paga** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)                  | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Pré-paga** | Azure Pass                                                                             | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P – MS-AZR-0125P, MS-AZR-0128P – MS-AZR-0130P |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)      | MPN_2014-09-01 | MS-AZR-0029P |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)          | MSDN_2014-09-01 | MS-AZR-0059P |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)     | MSDNDevTest_2014-09-01 | MS-AZR-0060P |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)            | MSDN_2014-09-01 | MS-AZR-0063P |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)  | MSDN_2014-09-01 | MS-AZR-0064P |

A tabela a seguir mostra ofertas sem suporte.

| Category  | **Nome da oferta** | **ID da cota** | **Número da oferta** |
| --- | --- | --- | --- |
| **CSP (Provedor de Soluções na Nuvem)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **CSP (Provedor de Soluções na Nuvem)** | CSP do Azure Governamental                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **CSP (Provedor de Soluções na Nuvem)** | CSP do Azure na Alemanha para o Microsoft Cloud Alemanha   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pré-paga**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pré-paga**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Planos de suporte** | Suporte Standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Planos de suporte** | Suporte Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Planos de suporte** | Suporte Developer                   | Default_2014-09-01 | MS-AZR-0043P |
| **Planos de suporte** | Planos de suporte na Alemanha                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Planos de suporte** | Suporte Standard do Azure Governamental   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Planos de suporte** | Suporte Pro-Direct do Azure Governamental | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Planos de suporte** | Suporte Developer do Azure Governamental  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

Para clientes com categorias de oferta paga conforme o uso, do MSDN e do Visual Studio, os dados estão disponíveis no Gerenciamento de Custos a partir de 02/10/2018. Para acessar os dados para sua assinatura antes de 02/10/2018, você pode usar o [Centro de contas do Azure](https://account.azure.com/subscriptions) baixar o uso de detalhes em um arquivo CSV ou você pode usar o [API detalhes de uso](/rest/api/consumption/usagedetails).

## <a name="determine-your-offer-type"></a>Determinar o tipo de oferta
Se não vir os dados de uma assinatura e quiser determinar se sua assinatura se enquadra nas ofertas com suporte, você poderá validar se sua assinatura tem suporte. Para validar se uma assinatura do Azure tem suporte, entre no [portal do Azure](https://portal.azure.com). Em seguida, selecione **Todos os serviços** no menu do painel esquerdo. Na lista de serviços, selecione **Assinaturas**. No menu de lista de assinaturas, clique na assinatura que deseja verificar. Sua assinatura é mostrada na guia Visão Geral e você pode ver a **Oferta** e a **ID da Oferta**. A imagem a seguir mostra um exemplo.

![Exemplo da guia Visão Geral da Assinatura mostrando a Oferta e a ID da Oferta](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Custos incluídos no Gerenciamento de Custos

As tabelas a seguir mostram os dados que estão incluídos ou não no Gerenciamento de Custos. Todos os custos são calculados até que uma fatura é gerada. Custos mostrados não incluem créditos pré-pagos e gratuitos.

**Dados de uso e de custo**

| **Incluído** | **Não incluído** |
| --- | --- |
| Uso do serviço do Azure<sup>1</sup> | Compras de reserva – Para obter mais informações, consulte [APIs para automação de reserva do Azure](../billing/billing-reservation-apis.md). |
| Uso de oferta de Marketplace<sup>2</sup> | Compras do Marketplace – Para obter mais informações, consulte [Encargos de serviço de terceiros](../billing/billing-understand-your-azure-marketplace-charges.md). |
|   | Encargos de suporte – Para obter mais informações, consulte [Termos da fatura explicados](../billing/billing-understand-your-invoice.md). |
|   | Impostos – Para obter mais informações, consulte [Termos da fatura explicados](../billing/billing-understand-your-invoice.md). |
|   | Créditos – Para obter mais informações, consulte [Termos da fatura explicados](../billing/billing-understand-your-invoice.md). |

<sup>1</sup> O uso do serviço do Azure é baseado nos preços negociados e de reserva.

<sup>2</sup> mercado a oferecer o uso não está disponível para o pré-pago, MSDN, e o Visual Studio oferece no momento.

**Metadados**

| **Incluído** | **Não incluído** |
| --- | --- |
| As marcas de recurso<sup>3</sup> | Marcas de grupo de recursos |

<sup>3</sup> marcas de recurso são aplicadas conforme o uso é emitido de cada serviço e não estão disponíveis retroativamente a histórico de uso.

## <a name="rated-usage-data-refresh-schedule"></a>Agenda de atualização de dados de uso calculados

Dados de uso e de custo estão disponíveis em Gerenciamento de Custos + Cobrança no portal do Azure e nas [APIs com suporte](index.yml). Ao avaliar os custos, tenha em mente os seguintes pontos:

- Os encargos estimados do período de cobrança atual são atualizados seis vezes por dia.
- Os encargos estimados do período de cobrança atual podem ser alterados conforme você incorre em mais uso.
- Cada atualização é cumulativa e inclui todos os itens de linha e informações da atualização anterior.
- O Azure finaliza ou _encerra_ o período de cobrança atual até 72 horas (três dias corridos) após o fim período de cobrança.

Os exemplos a seguir ilustram como os períodos de cobrança podem ser encerrados.

Assinaturas de EA (Contrato Enterprise) – Se o mês de cobrança terminar em 31 de março, os encargos estimados serão atualizados até 72 horas mais tarde. Neste exemplo, até meia-noite (UTC) de 4 de abril.

Assinaturas pagas conforme o uso – Se o mês de cobrança terminar no dia 15 de maio, os encargos estimados poderão ser atualizados até 72 horas depois. Neste exemplo, até meia-noite (UTC) de 19 de maio.

### <a name="rerated-data"></a>Dados recalculados

Se você usar o [APIs de gerenciamento de custo](https://aka.ms/costmgmt/docs), Power BI ou o portal do Azure para recuperar dados, esperar que os encargos do período de cobrança atual classifique novamente e, consequentemente, alterar, até que a nota fiscal seja fechada.

## <a name="usage-data-update-frequency-varies"></a>A frequência de atualização dos dados de uso varia

A disponibilidade de seus dados de uso incorridos no Gerenciamento de Custos depende de alguns fatores, incluindo:

- A frequência com que os serviços do Azure (como Armazenamento, Computação, CDN e SQL) transmitem o uso.
- O tempo necessário para processar os dados de uso por meio do mecanismo de classificação e dos pipelines de gerenciamento de custos.

Alguns serviços transmitem o uso com mais frequência do que outros. Portanto, talvez você veja dados no Gerenciamento de Custos para alguns serviços antes do que para outros serviços que emitem dados menos frequentemente. Normalmente, o uso de serviços leva 8 a 24 horas para aparecer no Gerenciamento de Custos. Tenha em mente que os dados de um mês em aberto são atualizados conforme você incorre em mais uso porque as atualizações são cumulativas.

## <a name="historical-data-might-not-match-invoice"></a>Os dados históricos podem não corresponder da fatura

Os dados históricos para ofertas de crédito baseado e pagamento antecipado podem não corresponder a sua fatura. Algumas ofertas pago pelo uso do Azure, do MSDN e do Visual Studio podem ter os créditos do Azure e avançados pagamentos aplicados à fatura. No entanto, os dados históricos mostrados no gerenciamento de custos se baseia apenas os encargos de consumo estimado. Dados históricos de gerenciamento de custos não incluem os pagamentos e créditos. Como resultado, os dados históricos mostrados para as ofertas a seguir podem não corresponder exatamente a sua fatura.

-   MS-AZR-0029P
-   MS-AZR-0064P
-   MS-AZR-0170P
-   MS-AZR-0062P
-   MS-AZR-0123P
-   MS-AZR-0129P
-   MS-AZR-0044P
-   MS-AZR-0128P
-   MS-AZR-0120P
-   MS-AZR-0125P
-   MS-AZR-0059P
-   MS-AZR-0063P
-   MS-AZR-0060P
-   MS-AZR-0111P

## <a name="see-also"></a>Consulte também

- Se você ainda não concluiu o primeiro início rápido de Gerenciamento de Custos, leia-o em [Iniciar a análise dos custos](quick-acm-cost-analysis.md).
