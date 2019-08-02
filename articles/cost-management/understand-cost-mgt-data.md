---
title: Entender os dados do Gerenciamento de Custos do Azure | Microsoft Docs
description: Este artigo ajuda você a entender melhor os dados incluídos no gerenciamento de custos do Azure e com que frequência eles são processados, coletados, mostrados e fechados.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 8aaaa6465b501cee83f4c2d8cb60729282651967
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384860"
---
# <a name="understand-cost-management-data"></a>Entender os dados de Gerenciamento de Custos

Este artigo ajuda você a entender melhor os dados de uso e de custo do Azure incluídos no gerenciamento de custos do Azure. Ele explica com que frequência os dados são processados, coletados, mostrados e fechados. Você é cobrado mensalmente pelo uso do Azure. Embora os ciclos de cobrança sejam períodos mensais, as datas de início e término do ciclo variam por tipo de assinatura. A frequência com que o Gerenciamento de Custos recebe dados de uso varia com base em diferentes fatores. Esses fatores incluem o tempo necessário para processar os dados e a frequência com que os serviços do Azure transmitem o uso para o sistema de cobrança.

O gerenciamento de custos inclui todo o uso e as compras, incluindo reservas e ofertas de terceiros para contas de Enterprise Agreement (EA). As contas do Microsoft Customer Agreement (MCA) e as assinaturas individuais com tarifas pagas pelo uso incluem apenas o uso do Azure e dos serviços do Marketplace. O suporte e outros custos não são incluídos. Os custos são estimados até que uma nota fiscal seja gerada e não Fatore os créditos.

## <a name="supported-microsoft-azure-offers"></a>Ofertas de Microsoft Azure com suporte

As informações a seguir mostram as atuais [ofertas do Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) com suporte no Gerenciamento de Custos do Azure. Uma oferta do Azure é o tipo de assinatura do Azure que você tem. Os dados estão disponíveis no gerenciamento de custos a partir dos **dados disponíveis a partir da** Data. Se uma assinatura alterar as ofertas, os custos antes da data de alteração da oferta não estarão disponíveis. 

| **Categoria**  | **Nome da oferta** | **ID da cota** | **Número da oferta** | **Dados disponíveis de** |
| --- | --- | --- | --- | --- |
| **Azure Alemanha** | [Pagamento Conforme o Uso do Azure Alemanha](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P | 2 de outubro de 2018<sup>2</sup> |
| **Azure Governamental** | Azure Governamental Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Maio de 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | Desenvolvimento/Teste Enterprise                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Maio de 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Maio de 2014<sup>1</sup> |
| **Contrato com o cliente da Microsoft** | [Plano de Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/D | Mar 2019<sup>3</sup> |
| **Contrato com o cliente da Microsoft** | [Plano de Microsoft Azure para desenvolvimento/teste](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N/D | Mar 2019<sup>3</sup> |
| **Microsoft Developer Network (MSDN)** | [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p) <sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | [Avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) <sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | [Azure via Open](https://azure.microsoft.com/offers/ms-azr-0111p) <sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | [Azure para estudantes](https://azure.microsoft.com/offers/ms-azr-0170p) <sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P – MS-AZR-0125P, MS-AZR-0128P – MS-AZR-0130P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p) <sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p) <sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p) <sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 de outubro de 2018<sup>2</sup> |

_<sup>**1**</sup> para os dados antes de 2014 de maio, visite o [portal do Azure Enterprise](https://ea.azure.com)._

_<sup>**2**</sup> para dados antes de 2 de outubro de 2018, visite o [centro de contas do Azure](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> contratos de clientes da Microsoft iniciados em março de 2019 e não têm nenhum dado histórico antes desse ponto._

_<sup>**4**</sup> os dados históricos para assinaturas com base em crédito e de pagamento em adiantamento podem não corresponder à sua fatura. Consulte [os dados históricos podem não corresponder à fatura](#historical-data-might-not-match-invoice) abaixo._

As ofertas a seguir ainda não têm suporte:

| Categoria  | **Nome da oferta** | **ID da cota** | **Número da oferta** |
| --- | --- | --- | --- |
| **Azure Alemanha** | [Pagamento Conforme o Uso do Azure Alemanha](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **CSP (Provedor de Soluções na Nuvem)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **CSP (Provedor de Soluções na Nuvem)** | CSP do Azure Governamental                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **CSP (Provedor de Soluções na Nuvem)** | CSP do Azure na Alemanha para o Microsoft Cloud Alemanha   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pré-paga**                 | Iniciador do Azure para Alunos | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pré-paga**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Planos de suporte** | Suporte Standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Planos de suporte** | Suporte Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Planos de suporte** | Suporte Developer                   | Default_2014-09-01 | MS-AZR-0043P |
| **Planos de suporte** | Planos de suporte na Alemanha                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Planos de suporte** | Suporte Standard do Azure Governamental   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Planos de suporte** | Suporte Pro-Direct do Azure Governamental | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Planos de suporte** | Suporte Developer do Azure Governamental  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Determinar o tipo de oferta
Se não vir os dados de uma assinatura e quiser determinar se sua assinatura se enquadra nas ofertas com suporte, você poderá validar se sua assinatura tem suporte. Para validar se uma assinatura do Azure tem suporte, entre no [portal do Azure](https://portal.azure.com). Em seguida, selecione **Todos os serviços** no menu do painel esquerdo. Na lista de serviços, selecione **Assinaturas**. No menu de lista de assinaturas, clique na assinatura que deseja verificar. Sua assinatura é mostrada na guia Visão Geral e você pode ver a **Oferta** e a **ID da Oferta**. A imagem a seguir mostra um exemplo.

![Exemplo da guia Visão Geral da Assinatura mostrando a Oferta e a ID da Oferta](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Custos incluídos no Gerenciamento de Custos

As tabelas a seguir mostram os dados que estão incluídos ou não no Gerenciamento de Custos. Todos os custos são estimados até que uma fatura seja gerada. Os custos mostrados não incluem créditos gratuitos e pré-pagos.

**Dados de uso e de custo**

| **Incluído** | **Não incluído** |
| --- | --- |
| Uso do serviço do Azure<sup>5</sup>        | Encargos de suporte – Para obter mais informações, consulte [Termos da fatura explicados](../billing/billing-understand-your-invoice.md). |
| Uso<sup>6</sup> da oferta do Marketplace | Impostos – Para obter mais informações, consulte [Termos da fatura explicados](../billing/billing-understand-your-invoice.md). |
| Compras do Marketplace<sup>6</sup>      | Créditos – Para obter mais informações, consulte [Termos da fatura explicados](../billing/billing-understand-your-invoice.md). |
| Compras de reserva<sup>7</sup>      |  |
| Amortização de compras de reserva<sup>7</sup>      |  |

_<sup>**5**</sup> o uso do serviço do Azure baseia-se na reserva e nos preços negociados._

_<sup>**6**</sup> as compras do Marketplace não estão disponíveis para ofertas de pagamento conforme o uso, MSDN e Visual Studio no momento._

_<sup>**7**</sup> as compras de reserva estão disponíveis somente para contas de Enterprise Agreement (ea) neste momento._

**Metadados**

| **Incluído** | **Não incluído** |
| --- | --- |
| Marcas de recurso<sup>8</sup> | Marcas de grupo de recursos |

_<sup>**8**</sup> marcas de recurso são aplicadas conforme o uso é emitido de cada serviço e não está disponível retroativamente para uso histórico._

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

Se você usar as [APIs de gerenciamento de custos](index.yml), Power bi ou o portal do Azure para recuperar dados, espere que os encargos do período de cobrança atual sejam reclassificados e, consequentemente, sejam alterados até que a fatura seja fechada.

## <a name="usage-data-update-frequency-varies"></a>A frequência de atualização dos dados de uso varia

A disponibilidade de seus dados de uso incorridos no Gerenciamento de Custos depende de alguns fatores, incluindo:

- A frequência com que os serviços do Azure (como Armazenamento, Computação, CDN e SQL) transmitem o uso.
- O tempo necessário para processar os dados de uso por meio do mecanismo de classificação e dos pipelines de gerenciamento de custos.

Alguns serviços transmitem o uso com mais frequência do que outros. Portanto, talvez você veja dados no Gerenciamento de Custos para alguns serviços antes do que para outros serviços que emitem dados menos frequentemente. Normalmente, o uso de serviços leva 8 a 24 horas para aparecer no Gerenciamento de Custos. Tenha em mente que os dados de um mês em aberto são atualizados conforme você incorre em mais uso porque as atualizações são cumulativas.

## <a name="historical-data-might-not-match-invoice"></a>Os dados históricos podem não corresponder à fatura

Os dados históricos para ofertas com base em crédito e de pagamento antecipado podem não corresponder à fatura. Algumas ofertas pagas conforme o uso do Azure, MSDN e Visual Studio podem ter créditos do Azure e pagamentos avançados aplicados à fatura. No entanto, os dados históricos mostrados no gerenciamento de custos baseiam-se apenas nos encargos de consumo estimado. Os dados históricos do gerenciamento de custos não incluem pagamentos e créditos. Como resultado, os dados históricos mostrados para as ofertas a seguir podem não corresponder exatamente à sua fatura.

- Azure para estudantes (MS-AZR-0170P)
- Azure via Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Avaliação gratuita (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Consulte também

- Se você ainda não concluiu o primeiro início rápido de Gerenciamento de Custos, leia-o em [Iniciar a análise dos custos](quick-acm-cost-analysis.md).
