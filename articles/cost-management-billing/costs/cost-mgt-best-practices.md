---
title: Otimizar seu investimento na nuvem com o Gerenciamento de Custos do Azure
description: Este artigo ajuda a obter o valor máximo dos seus investimentos de nuvem, reduzir os custos e avaliar onde seu dinheiro está sendo gasto.
author: bandersmsft
ms.author: banders
ms.date: 05/27/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: fc9b6c4edc5d08c30b1eadb5084155b10d0e55a3
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601507"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Como otimizar seu investimento na nuvem com o Gerenciamento de Custos do Azure

O Gerenciamento de Custos do Azure fornece as ferramentas para planejar, analisar e reduzir os gastos para maximizar seu investimento na nuvem. Este documento fornece uma abordagem metódica do gerenciamento de custos e destaca as ferramentas disponíveis conforme você resolve os desafios de custo da sua organização. O Azure facilita a criação e a implantação de soluções de nuvem. No entanto, é importante que essas soluções sejam otimizadas para minimizar os custos da organização. Siga os princípios descritos neste documento e use nossas ferramentas para ajudar a assegurar que sua organização esteja preparada para o sucesso.

## <a name="methodology"></a>Metodologia

O gerenciamento de custos é um problema organizacional e deve ser uma prática contínua que começa antes de você gastar dinheiro em recursos de nuvem. Para implementar o gerenciamento de custos e otimizar os custos com êxito, sua organização precisa:

- Preparar-se com as ferramentas adequadas para o sucesso
- Responsabilizar-se pelos custos
- Tomar as devidas providências para otimizar os gastos

Os três grupos principais, descritos abaixo, precisam estar alinhados na organização para garantir o sucesso do gerenciamento de custos.

- **Departamento Financeiro** – equipe responsável pela aprovação de solicitações de orçamento na empresa, com base nas previsões de gastos com a nuvem. Eles pagam a fatura correspondente e atribuem os custos a várias equipes para permitir a responsabilização pelos custos.
- **Gerentes** – os tomadores de decisão empresariais em uma organização que precisam entender os gastos com a nuvem para encontrar os melhores resultados de gastos.
- **Equipes do aplicativo** – os engenheiros que gerenciam os recursos de nuvem no dia a dia, desenvolvendo serviços para atender às necessidades da organização. Essas equipes precisam de flexibilidade para aproveitar ao máximo seus orçamentos definidos.

### <a name="key-principles"></a>Principais princípios

Use os princípios descritos abaixo para preparar sua organização para o sucesso do gerenciamento de custos da nuvem.

Para saber mais, assista ao vídeo [Gerenciamento de Custos configurando para o sucesso](https://www.youtube.com/watch?v=dVuwITdSAZ4). Para assistir a outros vídeos, visite o [Canal do YouTube do Gerenciamento de Custos](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/dVuwITdSAZ4]

#### <a name="planning"></a>Planejamento

Um planejamento antecipado abrangente permite personalizar o uso da nuvem de acordo com seus requisitos empresariais específicos. Pergunte-se:

- Qual é o problema empresarial que estou resolvendo?
- Quais são os padrões de uso esperados dos meus recursos?

Suas respostas o ajudarão a selecionar a oferta ideal para você. Elas determinam a infraestrutura a ser usada e como ela é usada para maximizar a eficiência do Azure.

#### <a name="visibility"></a>Visibilidade

Quando bem estruturado, o Gerenciamento de Custos ajuda a informar as pessoas sobre os custos do Azure pelos quais elas são responsáveis ou sobre o valor de seus gastos. O Azure tem serviços projetados para fornecer insights sobre *em que* seu dinheiro é gasto. Aproveite essas ferramentas. Elas podem ajudá-lo a encontrar recursos subutilizados, remover o desperdício e maximizar as oportunidades de economia de custo.

#### <a name="accountability"></a>Responsabilidade

Atribua os custos em sua organização para garantir que as pessoas certas sejam responsabilizadas pelos gastos de suas equipes. Para entender completamente o gastos do Azure da sua organização, organize seus recursos para maximizar os insights sobre a atribuição de custos. Uma boa organização ajuda a gerenciar e reduzir os custos e a responsabilizar pessoas pela eficiência dos gastos na organização.

#### <a name="optimization"></a>Optimization

Execute ações para reduzir seus gastos. Aproveite-a ao máximo com base nas descobertas coletadas por meio do planejamento e do aumento da visibilidade dos custos. Você poderá considerar otimizações de compra e de licenciamento juntamente com alterações de implantação da infraestrutura, que serão discutidas em detalhes mais adiante neste documento.

#### <a name="iteration"></a>Iteração

Todos em sua organização precisam participar do ciclo de vida do gerenciamento de custos. Eles precisam permanecer envolvidos continuamente para otimizar os custos. Seja rigoroso quanto a esse processo iterativo e faça com que ele seja um locatário fundamental da governança de nuvem responsável em sua organização.

![Diagrama de princípios-chave que mostra a visibilidade, responsabilidade e otimização](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Planejar com os custos em mente

Antes de implantar recursos de nuvem, avalie os seguintes itens:

- A oferta do Azure que melhor atende às suas necessidades
- Os recursos que você planeja usar
- Quanto eles podem custar

O Azure fornece ferramentas para ajudá-lo no processo de avaliação. As ferramentas podem dar uma boa ideia do investimento necessário para habilitar suas cargas de trabalho. Em seguida, você pode selecionar a melhor configuração para sua situação.

### <a name="azure-onboarding-options"></a>Opções de integração do Azure

A primeira etapa para maximizar sua experiência com o Gerenciamento de Custos é investigar e decidir qual oferta do Azure é melhor para você. Pense em como você planeja usar o Azure futuramente. Além disso, considere como você deseja que o modelo de cobrança seja configurado. Ao tomar sua decisão, considere as seguintes perguntas:

- Por quanto tempo planejo usar o Azure? Estou testando ou planejando criar uma infraestrutura de prazo mais longo?
- Como desejo pagar pelo Azure? Devo pagar antecipadamente por um preço reduzido ou ser faturado no final do mês?

Para saber mais sobre as diversas opções, visite [Como comprar o Azure](https://azure.microsoft.com/pricing/purchase-options/). Vários dos modelos de cobrança mais comuns são identificados abaixo.

#### <a name="free"></a>[Gratuito](https://azure.microsoft.com/free/)

- 12 meses de serviços populares gratuitos
- $ 200 de crédito para explorar os serviços por 30 dias
- No mínimo 25 serviços sempre são gratuitos

#### <a name="pay-as-you-go"></a>[Pago conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p)

- Sem taxas mínimas ou compromissos
- Preços competitivos
- Pague apenas pelo que usar
- Cancele a qualquer momento

#### <a name="enterprise-agreement"></a>[Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Opções para antecipar o Pagamento antecipado do Azure (anteriormente conhecido como compromisso monetário)
- Acesso a preços do Azure reduzidos

#### <a name="azure-in-csp"></a>[Azure no CSP](https://azure.microsoft.com/offers/ms-azr-0145p/)

- Os parceiros CSP são o primeiro ponto de contato para atender às necessidades de seus clientes e o centro do relacionamento com o cliente
- Eles provisionam novos clientes, solicitam e gerenciam assinaturas e executam tarefas administrativas em nome de seus clientes
- Além disso, eles agrupam serviços com soluções exclusivas ou revendem o Azure enquanto controlam os preços, os termos e a cobrança

## <a name="estimate-the-cost-of-your-solution"></a>Estimar o custo da sua solução

Antes de implantar uma infraestrutura, avalie quanto a solução custará. A avaliação ajudará você a criar um orçamento antecipado para a carga de trabalho da organização. Assim, ao longo do tempo, você poderá usar o orçamento como um parâmetro de comparação para avaliar a validade da estimativa inicial. E poderá compará-lo com o custo real da solução implantada.

### <a name="azure-pricing-calculator"></a>Calculadora de preços do Azure

A calculadora de preços do Azure permite que você misture e faça diferentes combinações dos serviços do Azure para obter uma estimativa dos custos. Você pode implementar a solução de diferentes maneiras no Azure, e cada uma delas poderá influenciar seus gastos gerais. Pensar com antecedência em todas as necessidades de infraestrutura da implantação de nuvem ajuda a usar a ferramenta com mais eficiência. Assim, você pode obter uma estimativa sólida dos gastos no Azure estimados.

Para obter mais informações, confira a [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Migrações para Azure

As Migrações para Azure são um serviço que avalia as cargas de trabalho atuais da organização nos datacenters locais. Ele fornece insights sobre o que uma solução de substituição do Azure deverá lhe oferecer. Primeiro, as Migrações analisam os computadores locais para determinar se a migração é viável. Em seguida, ela recomenda o dimensionamento da VM no Azure para maximizar o desempenho. Por fim, ele também cria uma estimativa de custo de uma solução baseada no Azure.

Para obter mais informações, confira [Migrações para Azure](../../migrate/migrate-services-overview.md).

## <a name="analyze-and-manage-your-costs"></a>Analisar e gerenciar seus custos

Mantenha-se informado sobre como os custos da sua organização evoluem ao longo do tempo. Use as técnicas a seguir para entender e gerenciar seus gastos corretamente.

### <a name="organize-resources-to-maximize-cost-insights-and-accountability"></a>Organize os recursos para maximizar os insights sobre custos e a responsabilidade

Uma estrutura organizacional bem planejada para as suas hierarquias de recursos e cobrança do Azure ajuda a fornecer uma boa compreensão e controle sobre os custos à medida que você cria sua infraestrutura de nuvem. Assista ao vídeo [Como configurar hierarquias de entidade](https://www.youtube.com/watch?v=n3TLRaYJ1NY) para obter uma melhor compreensão das ferramentas organizacionais disponíveis e como aproveitá-las. Para assistir a outros vídeos, visite o [Canal do YouTube do Gerenciamento de Custos](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/n3TLRaYJ1NY]

Ao avaliar e criar uma hierarquia que atende às suas necessidades, responda às perguntas a seguir.

*Qual hierarquia de cobrança está disponível para mim e quais são os diferentes escopos que posso usar?*

Identifique a organização de cobrança da sua organização determinando o tipo de oferta do Azure. Os escopos disponíveis para cada organização de cobrança do Azure estão documentados em [Entender e trabalhar com escopos](understand-work-scopes.md).

*Se eu tiver várias equipes, como devo organizar minhas assinaturas e meus grupos de recursos?*

A criação de uma assinatura ou um grupo de recursos para cada equipe é uma prática comum. Eles podem ajudar você a diferenciar os custos e a responsabilizar as equipes. No entanto, os custos ficam limitados à assinatura ou ao grupo de recursos.

Se você já tiver equipes com várias assinaturas, considere o agrupamento das assinaturas em grupos de gerenciamento para analisar os custos juntos. Grupos de gerenciamento, assinaturas e grupos de recursos fazem parte da hierarquia do RBAC do Azure. Use-os em conjunto para o controle de acesso nas suas equipes.

Os recursos podem se estender por vários escopos, especialmente quando são compartilhados por várias equipes ou cargas de trabalho. Considere a possibilidade de identificar os recursos com marcas. As marcas são abordadas mais detalhadamente na próxima seção.

*Tenho ambientes de desenvolvimento e de produção?*

Considere a possibilidade de criar assinaturas de Desenvolvimento/Teste para seus ambientes de desenvolvimento a fim de aproveitar os preços reduzidos. Se as cargas de trabalho abrangem várias equipes ou vários escopos do Azure, considere o uso de marcas para identificá-las.

### <a name="tag-shared-resources"></a>Marcar os recursos compartilhados

As marcas são uma forma eficaz de entender os custos que abrangem várias equipes e vários escopos do Azure. Por exemplo, você pode ter um recurso como um servidor de email utilizado por muitas equipes. Você pode colocar um recurso compartilhado, como o servidor de email, em uma assinatura dedicada a recursos compartilhados ou colocá-lo em uma assinatura existente. Se você o colocar em uma assinatura existente, o proprietário da assinatura poderá não querer que o custo seja acumulado na equipe dele todos os meses. Para este exemplo, use uma marca para identificar o recurso como compartilhado.

Da mesma forma, você também pode ter aplicativos Web ou ambientes, como teste ou produção, que usam recursos em várias assinaturas pertencentes a diferentes equipes. Para entender melhor o custo total das cargas de trabalho, marque os recursos utilizados por elas. Quando as marcas são aplicadas corretamente, você pode aplicá-las como um filtro na análise de custo para entender melhor as tendências.

Depois de planejar a marcação de recursos, configure uma política do Azure para impor a marcação aos recursos. Assista ao vídeo [Como examinar as políticas de marca com o Gerenciamento de Custos do Azure](https://www.youtube.com/watch?v=nHQYcYGKuyw) para conhecer as ferramentas disponíveis que ajudarão você a impor uma marcação de recursos escalonável. Para assistir a outros vídeos, visite o [Canal do YouTube do Gerenciamento de Custos](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/nHQYcYGKuyw]

### <a name="use-cost-analysis"></a>Usar análise de custo

A análise de custo permite que você faça uma análise detalhada dos custos organizacionais dividindo e repartindo os custos usando propriedades de recurso padrão. Considere as seguintes perguntas comuns como um guia para sua análise. A resposta regular a essas perguntas o ajudarão a manter-se mais informado e a permitir decisões mais atentas aos custos.

- **Custos estimados para o mês atual** – quanto gastei até o momento neste mês? Vou continuar dentro do orçamento?
- **Investigar anomalias** – faça verificações de rotina para verificar se os custos permanecem dentro de um intervalo razoável de uso normal. Quais são as tendências? Há exceções?
- **Reconciliação de fatura** – o custo da minha fatura mais recente é maior do que a do mês anterior? Como os hábitos de gastos se alteraram mês a mês?
- **Estorno interno** – agora que já sei por quanto estou sendo cobrado, como essas cobranças devem ser divididas em minha organização?

Para obter mais informações, confira [análise de custo](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Exportar dados de cobrança mediante um agendamento

Você precisa importar os dados de cobrança para um sistema externo, como um painel ou sistema financeiro? Configurar exportações automatizadas no Armazenamento do Microsoft Azure fazendo o download de arquivos manualmente todos os meses. Você pode, em seguida, configurar facilmente integrações automáticas com outros sistemas para manter os dados de cobrança em sincronia.

Para obter mais informações de como exportar dados de cobrança, confira [Criar e gerenciar dados exportados](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Criar orçamentos

Depois identificar e analisar seus padrões de gastos, é importante começar a configurar limites para você e suas equipes. Os orçamentos do Azure oferecem a capacidade de definir um orçamento baseado em custo ou em uso com vários limites e alertas. Examine os orçamentos que você cria regularmente para ver o progresso do consumo do orçamento e fazer as alterações necessárias. Os orçamentos do Azure também permitem que você configure um gatilho de automação quando um determinado limite de orçamento é atingido. Por exemplo, você pode configurar o serviço para desligar as VMs. Ou você mover a infraestrutura para um tipo de preço diferente em resposta a um gatilho de orçamento.

Para obter mais informações, confira [Orçamentos Azure](tutorial-acm-create-budgets.md).

Para obter mais informações sobre a automação com base no orçamento, confira [Automação com base em orçamento](../manage/cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>Agir para otimizar
Adote as seguintes maneiras de otimizar os gastos.

### <a name="cut-out-waste"></a>Cortar o desperdício

Depois de implantar sua infraestrutura no Azure, é importante garantir que ela esteja sendo usada. A maneira mais fácil de começar a economizar imediatamente é examinar seus recursos e remover os que não estão sendo usados. Neste ponto, você deve determinar se os recursos estão sendo usados da forma mais eficiente possível.

#### <a name="azure-advisor"></a>Assistente do Azure

O Assistente do Azure é um serviço que, entre outras coisas, identifica as máquinas virtuais com baixa utilização do ponto de vista de uso da CPU ou de rede. Neste ponto, você pode decidir desligar ou redimensionar a máquina com base no custo estimado para continuar a executar as máquinas. O Assistente também fornece recomendações para compras de instância reservada. As recomendações baseiam-se em seus últimos 30 dias de uso das máquinas virtuais. Quando acionadas, as recomendações podem ajudar a reduzir seus gastos.

Para obter mais informações, confira [Assistente do Azure](../../advisor/advisor-overview.md).

### <a name="size-your-vms-properly"></a>Dimensionar suas VMs corretamente

O dimensionamento de VM tem um impacto significativo em seu custo geral do Azure. O número de VMs necessárias no Azure talvez não seja igual ao número que está implantado no momento em um datacenter local. Escolha o tamanho certo para as cargas de trabalho que você planeja executar.

Para obter mais informações, confira [IaaS do Azure: dimensionamento e custo adequados](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/).

### <a name="use-purchase-discounts"></a>Usar descontos de compra

O Azure oferece vários descontos que sua organização pode aproveitar para economizar dinheiro.

#### <a name="azure-reservations"></a>Reservas do Azure

As Reservas do Azure permitem que você pague antecipadamente por um ou três anos de máquina virtual ou pela capacidade de computação de Banco de Dados SQL. Pagar previamente permitirá obter um desconto nos recursos que você usar. As Reservas do Azure podem reduzir significativamente os custos de sua máquina virtual ou os custos de computação do Banco de Dados SQL em até 72% com base em preços pagos conforme o uso, com um compromisso antecipado de um ou três anos. As reservas fornecem um desconto de faturamento e não afetam o estado de runtime de suas máquinas virtuais ou bancos de dados SQL.

Para obter mais informações, confira [Quais são as Reservas do Azure?](../reservations/save-compute-costs-reservations.md).

#### <a name="use-azure-hybrid-benefit"></a>Usar o Benefício Híbrido do Azure

Se você já tiver licenças do Windows Server ou do SQL Server em suas implantações locais, você poderá usar o programa Benefício Híbrido do Azure para economizar no Azure. Com o benefício do Windows Server, cada licença abrange o custo do sistema operacional (até duas máquinas virtuais), e você paga apenas pelos custos de computação base. Você pode usar as licenças existentes do SQL Server para economizar até 55% em opções de Banco de Dados SQL baseadas em vCore. As opções incluem o SQL Server em máquinas virtuais do Azure e o SQL Server Integration Services.

Para obter mais informações, confira [Calculadora de economia do Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Outros recursos

O Azure também tem um serviço que permite que você crie serviços que aproveitam a capacidade excedente no Azure com taxas reduzidas. Para obter mais informações, confira [Usar VMs de baixa prioridade com o Lote](../../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>Próximas etapas
- Se você é novo no Gerenciamento de Custos, leia [O que é o Gerenciamento de Custos do Azure?](../cost-management-billing-overview.md) para saber como ele ajuda a monitorar e controlar os gastos do Azure e para otimizar o uso de recursos.