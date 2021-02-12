---
title: Noções básicas dos dados do Gerenciamento de Custos do Azure
description: Este artigo ajuda você a entender melhor os dados incluídos em seu Gerenciamento de Custos do Azure e a frequência com que eles são processados, coletados, mostrados e fechados.
author: bandersmsft
ms.author: banders
ms.date: 01/06/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: contperf-fy21q2
ms.openlocfilehash: e6096c259ec1870a711a515bf02d5d00b4f75345
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964143"
---
# <a name="understand-cost-management-data"></a>Entender os dados de Gerenciamento de Custos

Este artigo ajuda você a entender melhor os dados de uso e custos incluídos no Gerenciamento de Custos do Azure. Ele explica a frequência com que esses dados são processados, coletados, mostrados e fechados. Você é cobrado mensalmente pelo uso do Azure. Embora os ciclos de cobrança sejam períodos mensais, as datas de início e término do ciclo variam por tipo de assinatura. A frequência com que o Gerenciamento de Custos recebe dados de uso varia com base em diferentes fatores. Esses fatores incluem o tempo necessário para processar os dados e a frequência com que os serviços do Azure transmitem o uso para o sistema de cobrança.

O Gerenciamento de Custos inclui todo o uso e as compras, incluindo reservas e ofertas de terceiros para contas de EA (Contrato Enterprise). As contas de Contrato de Cliente da Microsoft e as assinaturas individuais com tarifas pagas conforme o uso incluem apenas os serviços do Azure e do Marketplace. O suporte e outros custos não estão incluídos. Os custos são estimados até que uma fatura seja gerada e não são fatorados em créditos.

Se você tiver uma nova assinatura, não poderá usar imediatamente os recursos do Gerenciamento de Custos. Poderá levar até 48 horas para você poder usar todos os recursos do Gerenciamento de Custos.

## <a name="supported-microsoft-azure-offers"></a>Ofertas compatíveis do Microsoft Azure

As informações a seguir mostram as atuais [ofertas do Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) com suporte no Gerenciamento de Custos do Azure. Uma oferta do Azure é o tipo de assinatura do Azure que você tem. Os dados estarão disponíveis no Gerenciamento de Custos a partir da data de **disponibilidade dos dados**. Se uma assinatura alterar as ofertas, os custos antes da data de alteração da oferta não estarão disponíveis.

| **Categoria**  | **Nome da oferta** | **ID da Cota** | **Número da oferta** | **Data de disponibilidade dos dados** |
| --- | --- | --- | --- | --- |
| **Azure Governamental** | Azure Governamental Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Maio de 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | Desenvolvimento/Teste Enterprise                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Maio de 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | Microsoft Azure Enterprise | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Maio de 2014<sup>1</sup> |
| **Contrato de Cliente da Microsoft** | Plano do Microsoft Azure | EnterpriseAgreement_2014-09-01 | N/D | Março de 2019<sup>3</sup> |
| **Contrato de Cliente da Microsoft** | Plano do Microsoft Azure para Desenvolvimento/Teste | MSDNDevTest_2014-09-01 | N/D | Março de 2019<sup>3</sup> |
| **Contrato de Cliente da Microsoft compatível para parceiros** | Plano do Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01 e CSPDEVTEST_2018-05-01<br><br>A ID da cota é reutilizada para o Contrato de Cliente da Microsoft e as assinaturas CSP herdadas. Atualmente, há compatibilidade apenas para assinaturas do Contrato de Cliente da Microsoft. | N/D | Outubro de 2019 |
| **Microsoft Developer Network (MSDN)** | Plataformas MSDN<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | Pré-paga                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | Desenvolvimento/Teste Pago Conforme o Uso         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | Microsoft Partner Network      | MPN_2014-09-01 | MS-AZR-0025P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | Avaliação gratuita<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | Azure via Open<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P – MS-AZR-0125P, MS-AZR-0128P – MS-AZR-0130P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise – MPN<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Professional<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Test Professional<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise: BizSpark<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 de outubro de 2018<sup>2</sup> |

_<sup>**1**</sup> Para dados antes de maio de 2014, acesse o [Azure Enterprise Portal](https://ea.azure.com)._

_<sup>**2**</sup> Para dados antes de 2 de outubro de 2018, acesse o [Centro de Contas do Azure](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> Os Contratos de Clientes da Microsoft foram iniciados em março de 2019 e não apresentam dados históricos antes disso._

_<sup>**4**</sup> Os dados históricos para assinaturas com base em crédito e de pagamento antecipado podem não corresponder à fatura. Confira [Os dados históricos podem não corresponder à fatura](#historical-data-might-not-match-invoice) abaixo._

Ainda não há compatibilidade para as seguintes ofertas:

| Categoria  | **Nome da oferta** | **ID da Cota** | **Número da oferta** |
| --- | --- | --- | --- |
| **Azure Alemanha** | Pagamento Conforme o Uso do Azure Alemanha | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Governamental** | Azure Government de Pagamento Conforme o Uso | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **CSP (Provedor de Soluções na Nuvem)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **CSP (Provedor de Soluções na Nuvem)** | CSP do Azure Governamental                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **CSP (Provedor de Soluções na Nuvem)** | CSP do Azure na Alemanha para o Microsoft Cloud Alemanha   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pré-paga**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pré-paga** | Azure for Students<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Pré-paga**                 | Microsoft Azure Sponsorship | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Planos de suporte** | Suporte Standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Planos de suporte** | Suporte Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Planos de suporte** | Suporte Developer                   | Default_2014-09-01 | MS-AZR-0043P |
| **Planos de suporte** | Planos de suporte na Alemanha                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Planos de suporte** | Suporte Standard do Azure Governamental   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Planos de suporte** | Suporte Pro-Direct do Azure Governamental | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Planos de suporte** | Suporte Developer do Azure Governamental  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

### <a name="free-trial-to-pay-as-you-go-upgrade"></a>Atualização da versão de avaliação gratuita para a paga conforme o uso

Para obter informações sobre a disponibilidade de serviços de nível gratuito depois de atualizar de uma Avaliação gratuita para o preço pago conforme o uso, confira as [perguntas frequentes sobre a conta gratuita do Azure](https://azure.microsoft.com/free/free-account-faq/).

### <a name="determine-your-offer-type"></a>Determinar o tipo de oferta

Se não vir os dados de uma assinatura e quiser determinar se sua assinatura se enquadra nas ofertas com suporte, você poderá validar se sua assinatura tem suporte. Para validar se uma assinatura do Azure é compatível, entre no portal do Azure. Em seguida, selecione **Todos os serviços** no menu do painel esquerdo. Na lista de serviços, selecione **Assinaturas**. No menu de lista de assinaturas, selecione a assinatura que deseja verificar. Sua assinatura é mostrada na guia Visão Geral e você pode ver a **Oferta** e a **ID da Oferta**. A imagem a seguir mostra um exemplo.

![Exemplo da guia Visão Geral da Assinatura mostrando a Oferta e a ID da Oferta](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Custos incluídos no Gerenciamento de Custos

As tabelas a seguir mostram os dados que estão incluídos ou não no Gerenciamento de Custos. Todos os custos são estimados até que uma fatura seja gerada. Os custos mostrados não incluem créditos gratuitos e pré-pagos.

| **Incluído** | **Não incluído** |
| --- | --- |
| Uso de serviço do Azure<sup>5</sup>        | Encargos de suporte – Para obter mais informações, consulte [Termos da fatura explicados](../understand/understand-invoice.md). |
| Uso de oferta do Marketplace<sup>6</sup> | Impostos – Para obter mais informações, consulte [Termos da fatura explicados](../understand/understand-invoice.md). |
| Compras no Marketplace<sup>6</sup>      | Créditos – Para obter mais informações, consulte [Termos da fatura explicados](../understand/understand-invoice.md). |
| Compras de reserva<sup>7</sup>      |  |
| Amortização de compras de reserva<sup>7</sup>      |  |

_<sup>**5**</sup> A utilização do serviço do Azure é baseada nos preços negociados e de reserva._

_<sup>**6**</sup> As compras do Marketplace não estão disponíveis para ofertas do MSDN e do Visual Studio no momento._

_<sup>**7**</sup> As compras de reserva estão disponíveis somente para contas de EA (Contrato Enterprise) e do Contrato de Cliente da Microsoft no momento._

## <a name="how-tags-are-used-in-cost-and-usage-data"></a>Como as marcas são usadas em dados de custo e de uso

O Gerenciamento de Custos do Azure recebe marcas como parte de cada registro de uso enviado por serviços individuais. As restrições a seguir se aplicam a essas marcas:

- As marcas devem ser aplicadas diretamente aos recursos e não são herdadas implicitamente do grupo de recursos pai.
- As marcas de recurso só têm suporte em recursos implantados em grupos de recursos.
- Talvez alguns recursos implantados não deem suporte a marcas ou não incluam marcas em dados de uso.
- As marcas de recurso só são incluídas nos dados de uso enquanto a marca estiver aplicada. As marcas não são aplicadas aos dados históricos.
- As marcas de recurso só estão disponíveis no Gerenciamento de Custos após a atualização dos dados.
- As marcas de recurso só ficam disponíveis no Gerenciamento de Custos quando o recurso está ativo/em execução e produzindo registros de uso. Por exemplo, quando uma VM é desalocada.
- O gerenciamento de marcas exige acesso de colaborador a cada recurso.
- O gerenciamento de políticas de marca exige acesso de proprietário ou de colaborador de política a um grupo de gerenciamento, assinatura ou grupo de recursos.
    
Se você não vir alguma marca específica em Gerenciamento de Custos, considere as seguintes questões:

- A marca foi aplicada diretamente ao recurso?
- A marca foi aplicada há mais de 24 horas?
- O tipo de recurso dá suporte a marcas? Os tipos de recurso a seguir não dão suporte a marcas em dados de uso desde 1º de dezembro de 2019. Confira [Suporte a marcas para recursos do Azure](../../azure-resource-manager/management/tag-support.md) para obter a lista completa do que tem suporte.
    - Diretórios do Azure Active Directory B2C
    - Azure Bastion
    - Firewalls do Azure
    - Azure NetApp Files
    - Data Factory
    - Databricks
    - Balanceadores de carga
    - Observador de Rede
    - Hubs de Notificação
    - Barramento de Serviço
    - Time Series Insights
    
Confira algumas dicas para trabalhar com marcas:

- Planeje antecipadamente e defina uma estratégia de marcação que permita dividir os custos por organização, aplicativo, ambiente etc.
- Use o Azure Policy para copiar marcas de grupo de recursos para recursos individuais e impor sua estratégia de marcação.
- Use a API de Tags com Query ou o UsageDetails para obter todo o custo com base nas marcas atuais.

## <a name="cost-and-usage-data-updates-and-retention"></a>Atualizações de dados de custo e de uso e retenção

Em geral, os dados de custo e de uso estão disponíveis em Gerenciamento de Custos + Cobrança no portal do Azure e nas APIs compatíveis no prazo de 8 a 24 horas. Ao avaliar os custos, tenha em mente os seguintes pontos:

- Cada serviço do Azure (como Armazenamento, Computação e SQL) emite o uso em intervalos diferentes; você pode ver os dados de alguns serviços mais rapidamente do que outros.
- Os encargos estimados do período de cobrança atual são atualizados seis vezes por dia.
- Os encargos estimados do período de cobrança atual podem ser alterados conforme você incorre em mais uso.
- Cada atualização é cumulativa e inclui todos os itens de linha e informações da atualização anterior.
- O Azure finaliza ou _encerra_ o período de cobrança atual até 72 horas (três dias corridos) após o fim período de cobrança.
- Durante o período do mês aberto (não faturado), os dados de gerenciamento de custos devem ser considerados apenas uma estimativa. Em alguns casos, os encargos podem ainda não ter chegado ao sistema depois que o uso de fato ocorreu.

Os seguintes exemplos ilustram como os períodos de cobrança podem ser encerrados:

* Assinaturas de EA (Contrato Enterprise) – Se o mês de cobrança terminar em 31 de março, os encargos estimados serão atualizados até 72 horas mais tarde. Neste exemplo, até meia-noite (UTC) de 4 de abril.
* Assinaturas pagas conforme o uso – Se o mês de cobrança terminar no dia 15 de maio, os encargos estimados poderão ser atualizados até 72 horas depois. Neste exemplo, até meia-noite (UTC) de 19 de maio.

Depois que os dados de custo e de uso estiverem disponíveis em Gerenciamento de Custos e Cobrança, eles serão mantidos por, pelo menos, sete anos.

### <a name="rerated-data"></a>Dados recalculados

Se você usa as APIs de Gerenciamento de Custos, o Power BI ou o portal do Azure para recuperar dados, espere que os encargos do período de cobrança atual sejam recalculados e, assim, alterados até que a fatura seja fechada.

## <a name="cost-rounding"></a>Arredondamento de custo

Os custos mostrados no Gerenciamento de Custos são arredondados. Os custos retornados pela API de Consulta não são arredondados. Por exemplo:

- análise de custo no portal do Azure – as cobranças são arredondadas usando regras de arredondamento padrão: valores iguais a 0,5 e superiores são arredondados para cima, caso contrário os custos são arredondados para baixo. O arredondamento ocorre somente quando os valores são mostrados. O arredondamento não ocorre durante o processamento de dados e a agregação. Por exemplo, a análise de custo agrega os custos da seguinte maneira:
  -    Preço 1: $0,004
  - Preço 2: $0,004
  -    Preço agregado renderizado: 0,004 + 0,004 = 0,008. O preço mostrado será $0,01.
- API de consulta – os preços são mostrados em oito casas decimais e o arredondamento não ocorre.

## <a name="historical-data-might-not-match-invoice"></a>Os dados históricos podem não corresponder à fatura

Os dados históricos para ofertas com base em crédito e de pagamento antecipado podem não corresponder à fatura. Algumas ofertas do MSDN, do Visual Studio e pagas conforme o uso do Azure podem ter créditos Azure e pagamentos antecipados aplicados à fatura. No entanto, os dados históricos mostrados no Gerenciamento de Custos baseiam-se apenas nos encargos de consumo estimados. Os dados históricos do Gerenciamento de Custos não incluem pagamentos e créditos. Assim, os dados históricos mostrados para as ofertas a seguir podem não corresponder exatamente à sua fatura.

- Azure for Students (MS-AZR-0170P)
- Azure via Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Avaliação Gratuita (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não concluiu o primeiro início rápido de Gerenciamento de Custos, leia-o em [Iniciar a análise dos custos](./quick-acm-cost-analysis.md).