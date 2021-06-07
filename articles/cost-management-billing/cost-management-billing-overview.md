---
title: Visão geral do Gerenciamento de Custos do Azure + Cobrança
description: Você usa os recursos do Gerenciamento de Custos do Azure + Cobrança para realizar tarefas administrativas de cobrança e gerenciar o acesso da cobrança aos custos. Você também usa esses recursos para monitorar e controlar os gastos e otimizar o uso de recursos do Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/03/2021
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 9fe658a1755ce3731f220ec656845da1f861fa9b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050521"
---
# <a name="what-is-azure-cost-management--billing"></a>O que é o Gerenciamento de Custos do Azure + Cobrança?

Usando a nuvem da Microsoft, você pode melhorar significativamente o desempenho técnico de suas cargas de trabalho de negócios. Ela também pode reduzir os custos e a sobrecarga necessária para gerenciar os ativos organizacionais. No entanto, a oportunidade de negócios cria um risco devido ao potencial de desperdício e às ineficiências que são introduzidos nas implantações na nuvem. O Gerenciamento de Custos do Azure + Cobrança é um conjunto de ferramentas fornecidas pela Microsoft que ajudam você a analisar, gerenciar e otimizar os custos das cargas de trabalho. O uso do conjunto ajuda a garantir que a organização aproveite ao máximo os benefícios fornecidos pela nuvem.

Você pode considerar as cargas de trabalho do Azure como as luzes da sua casa. Ao sair de casa de manhã para mais uma jornada diária, você deixa as luzes acesas? Você pode usar lâmpadas diferentes, que sejam mais eficientes, para ajudar a reduzir a fatura mensal de energia? Você tem em uma sala mais luzes do que o necessário? Você pode usar o Gerenciamento de Custos do Azure + Cobrança para aplicar um raciocínio semelhante às cargas de trabalho usadas pela sua organização.

Com os produtos e serviços do Azure, você paga apenas pelo que usar. Ao criar e usar os recursos do Azure, você será cobrado por eles. Devido à facilidade de implantação de novos recursos, os custos das cargas de trabalho podem saltar significativamente sem análise e monitoramento adequados. Você usa os recursos do Gerenciamento de Custos do Azure + Cobrança para:

- Realizar tarefas administrativas de cobrança, como pagar sua fatura
- Gerenciar o acesso da cobrança aos custos
- Baixar os dados de custo e uso que foram usados para gerar sua fatura mensal
- Aplicar proativamente a análise de dados aos seus custos
- Definir limites de gastos
- Identificar oportunidades de alteração da carga de trabalho a fim de otimizar gastos

Para saber mais sobre como abordar o gerenciamento de custos como uma organização, consulte o artigo [práticas recomendadas do Azure Cost Management](./costs/cost-mgt-best-practices.md).

![Diagrama do processo de otimização do Gerenciamento de Custos e Cobrança.](./media/cost-management-optimization-process.png)

## <a name="understand-azure-billing"></a>Entender a Cobrança do Azure

Os recursos da Cobrança do Azure são usados para examinar os custos faturados e gerenciar o acesso às informações de cobrança. Em organizações maiores, as equipes de suprimento e finanças geralmente realizam as tarefas de faturamento.

Uma conta de cobrança é criada quando você se inscreve para usar o Azure. Você usa a conta de cobrança para gerenciar faturas, pagamentos e acompanhar os custos. Você pode ter acesso a várias contas de cobrança. Por exemplo, você pode ter se inscrito no Azure para seus projetos pessoais. Portanto, você pode ter uma assinatura individual do Azure com uma conta de cobrança. Você também pode ter acesso por meio do Contrato Enterprise de sua organização ou do Contrato de Cliente da Microsoft. Para cada cenário, você terá uma conta de cobrança separada.

### <a name="billing-accounts"></a>Contas de cobrança

Atualmente, o portal do Azure dá suporte aos seguintes tipos de contas de cobrança:

- **Programa Microsoft Online Services**: Uma conta de cobrança individual para o Programa Microsoft Online Services é criada quando você se inscreve no Azure por meio do site do Azure. Por exemplo, quando você se inscreve em uma [conta gratuita do Azure](./manage/create-free-services.md), uma conta com taxas pagas conforme o uso ou como assinante do Visual Studio.

- **Contrato Enterprise**: Uma conta de cobrança para um Contrato Enterprise é criada quando sua organização assina um EA (Contrato Enterprise) para usar o Azure.

- **Contrato de Cliente da Microsoft**: Uma conta de cobrança para um Contrato de Cliente da Microsoft é criada quando sua organização decide assinar um Contrato de Cliente da Microsoft por meio de um representante da Microsoft. Alguns clientes em regiões selecionadas, que se inscrevem por meio do site do Azure em uma conta com taxas pagas conforme o uso ou atualizam sua [conta gratuita do Azure](./manage/create-free-services.md), também podem ter uma conta de cobrança para um Contrato de Cliente da Microsoft.

## <a name="understand-azure-cost-management"></a>Entender o Gerenciamento de Custos do Azure

Embora relacionado, o faturamento difere do gerenciamento de custos. O faturamento é o processo de faturar os clientes por bens ou serviços e gerenciar o relacionamento comercial.

O gerenciamento de custos mostra os padrões de custo e uso da organização com análises avançadas. Os relatórios no Gerenciamento de Custos mostram os custos baseados em uso consumidos pelos serviços do Azure e de ofertas do Marketplace de terceiros. Os custos são baseados nos fatores e preços negociados na reserva e nos descontos do Benefício Híbrido do Azure. Coletivamente, os relatórios mostram seus custos internos e externos de uso e os encargos do Microsoft Azure Marketplace. Outros encargos, como compras de reserva, suporte e impostos ainda não são mostrados nos relatórios. Os relatórios ajudam você a entender seus gastos e uso de recursos e podem ajudar a encontrar anomalias de gastos. A análise preditiva também está disponível. O Gerenciamento de Custos usa grupos de gerenciamento, orçamentos e recomendações do Azure para mostrar claramente como suas despesas são organizadas e como você pode reduzir custos.

Você pode usar o portal do Azure ou várias APIs para automação de exportação para integrar dados de custo com sistemas e processos externos. A exportação automática de dados de faturamento e relatórios agendados também estão disponíveis.

Assista ao vídeo de visão geral do Gerenciamento de Custos do Azure para obter uma visão geral sobre como o Gerenciamento de Custos do Azure pode ajudar você a economizar dinheiro no Azure. Para assistir a outros vídeos, visite o [Canal do YouTube do Gerenciamento de Custos](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

### <a name="plan-and-control-expenses"></a>Plano e controle de despesas

As maneiras como o Gerenciamento de Custos o ajuda a planejar e controlar os custos incluem: Análise de custos, orçamentos, recomendações e exportação de dados de gerenciamento de custos.

Você usa a análise de custos para explorar e analisar seus custos organizacionais. Você pode visualizar os custos agregados por organização para entender onde os custos são acumulados e para identificar tendências de gastos. E você pode ver os custos acumulados ao longo do tempo para estimar tendências de custo mensais, trimestrais ou mesmo anuais em comparação a um orçamento.

Os orçamentos ajudam você a planejar e cumprir a responsabilidade financeira em sua organização. Eles ajudam a evitar que limites ou limites de custo sejam superados. Os orçamentos também podem ajudar você a informar os outros sobre seus gastos para gerenciar os custos de forma proativa. E com eles, você pode ver como os gastos progridem com o tempo.

As recomendações mostram como você pode otimizar e melhorar a eficiência identificando recursos ociosos e subutilizados. Ou, eles podem mostrar opções de recursos menos dispendiosas. Quando você age de acordo com as recomendações, você muda a maneira como usa seus recursos para economizar dinheiro. Para agir, você primeiro visualiza as recomendações de otimização de custos para visualizar possíveis ineficiências de uso. Em seguida, você age em uma recomendação para modificar o uso de recursos do Azure para uma opção mais econômica. Em seguida, você verifica a ação para garantir que a alteração realizada seja bem-sucedida.

Se você usar sistemas externos para acessar ou revisar dados de gerenciamento de custos, poderá exportar facilmente os dados do Azure. E você pode definir uma exportação agendada diariamente no formato CSV e armazenar os arquivos de dados no armazenamento do Azure. Então, você pode acessar os dados do seu sistema externo.

### <a name="cloudyn-deprecation"></a>Preterição do Cloudyn

O Cloudyn é um serviço do Azure relacionado ao Gerenciamento de Custos que será preterido até o final de 2020. Os recursos do Cloudyn existentes estão sendo integrados diretamente no portal do Azure, sempre que possível. Nenhum novo cliente está sendo integrado no momento, mas o suporte permanecerá para o produto até que ele seja totalmente preterido.
 
### <a name="additional-azure-tools"></a>Ferramentas do Azure adicionais

O Azure tem outras ferramentas que não fazem parte do conjunto de recursos de Gerenciamento de Custos do Azure + Cobrança. No entanto, elas têm um papel importante no processo de gerenciamento de custos. Para saber mais sobre essas ferramentas, consulte os links a seguir.

- [Calculadora de preço do Azure](https://azure.microsoft.com/pricing/calculator/): use essa ferramenta para estimar seus custos iniciais de nuvem.
- [Azure Migrate](../migrate/migrate-services-overview.md) - Avalie sua carga de trabalho atual do data center para obter insights sobre o que é necessário de uma solução de substituição do Azure.
- [Azure Advisor](../advisor/advisor-overview.md) - Identifique VMs não usadas e receba recomendações sobre compras de instâncias reservadas do Azure.
- [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) - Use suas licenças atuais do Windows Server ou do SQL Server no local para VMs no Azure para salvar.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com o Gerenciamento de Custos + Cobrança, a próxima etapa é começar a usar o serviço.

- Comece a usar o Azure Cost Management para [analisar os custos](./costs/quick-acm-cost-analysis.md).
- Você também pode ler mais sobre [práticas recomendadas de gerenciamento de custos do Azure](./costs/cost-mgt-best-practices.md).