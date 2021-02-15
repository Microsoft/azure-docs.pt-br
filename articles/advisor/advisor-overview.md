---
title: Introdução ao Azure Advisor
description: Use o Azure Advisor para otimizar as implantações do Azure.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 12e56bf44a29a32b2149bca14f7c99f319c9c4ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91405200"
---
# <a name="introduction-to-azure-advisor"></a>Introdução ao Azure Advisor

Saiba mais sobre as principais funcionalidades do Assistente do Azure e obtenha respostas para perguntas frequentes.

## <a name="what-is-advisor"></a>O que é o Assistente?
O Assistente é um consultor de nuvem personalizado que ajuda você a seguir as melhores práticas para otimizar suas implantações do Azure. Ele analisa a configuração de recursos e a telemetria de uso e, em seguida, recomenda soluções que podem ajudá-lo a melhorar a eficácia, o desempenho e a confiabilidade do custo (anteriormente chamado de alta disponibilidade) e a segurança dos recursos do Azure.

Com o Assistente, é possível:
* Obter recomendações de melhores práticas proativas, personalizadas e prontas para uso. 
* Melhore o desempenho, a segurança e a confiabilidade de seus recursos, conforme você identifica oportunidades para reduzir o gasto geral do Azure.
* Obter recomendações com ações propostas embutidas.

Você pode acessar o Advisor por meio do [portal do Azure](https://aka.ms/azureadvisordashboard). Entre no [Portal](https://portal.azure.com), localize o **Assistente** no menu de navegação ou pesquise por ele no menu **Todos os serviços**.

O painel do Assistente exibe recomendações personalizadas para todas as assinaturas.  Você pode aplicar filtros para exibir as recomendações para assinaturas e tipos de recursos específicos.  As recomendações são divididas em cinco categorias: 

* **Confiabilidade (anteriormente chamada de alta disponibilidade)**: para garantir e melhorar a continuidade dos seus aplicativos críticos para os negócios. Para obter mais informações, consulte [recomendações de confiabilidade do Advisor](advisor-high-availability-recommendations.md).
* **Segurança**: para detectar ameaças e vulnerabilidades que podem levar a violações de segurança. Para saber mais, veja [Advisor Security recommendations](advisor-security-recommendations.md) (Recomendações de segurança do Advisor).
* **Desempenho**: para melhorar a velocidade de seus aplicativos. Para saber mais, veja [Advisor Performance recommendations](advisor-performance-recommendations.md) (Recomendações de desempenho do Advisor).
* **Custo**: para otimizar e reduzir os gastos gerais do Azure. Para saber mais, veja [Advisor Cost recommendations](advisor-cost-recommendations.md) (Recomendações de custo do Advisor).
* **Excelência operacional**: para ajudá-lo a obter eficiência de processo e fluxo de trabalho, gerenciamento de recursos e práticas recomendadas de implantação. . Para obter mais informações, consulte [recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md).

  ![Tipos de recomendação do Advisor](./media/advisor-overview/advisor-dashboard.png)

Você pode clicar em uma categoria para exibir a lista de recomendações nessa categoria e selecionar uma recomendação para saber mais sobre ela.  Saiba mais sobre as ações que você pode realizar para aproveitar uma oportunidade ou resolver um problema.

![Categoria de recomendação do Assistente](./media/advisor-overview/advisor-ha-category-example.png) 

Clique na ação recomendada para implementá-la.  Será aberta uma interface simples que permite que você implemente a recomendação ou fornece documentação que ajuda você com a implementação.  Quando você implementa uma recomendação, pode demorar até um dia para que o Assistente reconheça isso.

Se você não pretende tomar uma ação imediata em uma recomendação, você pode adiá-la por um período de tempo especificado ou ignorá-la.  Se você não deseja receber as recomendações para uma assinatura ou grupo de recursos específico, você poderá configurar o Assistente para gerar apenas recomendações para grupos de recursos e assinaturas especificados.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-do-i-access-advisor"></a>Como acesso o Advisor?
Você pode acessar o Advisor por meio do [portal do Azure](https://aka.ms/azureadvisordashboard). Entre no [Portal](https://portal.azure.com), localize o **Assistente** no menu de navegação ou pesquise por ele no menu **Todos os serviços**.

Você também pode exibir as recomendações do Assistente por meio interface de recursos da máquina virtual. Escolha uma máquina virtual e role até as recomendações do Advisor no menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>De quais permissões preciso para acessar o Advisor?
 
Você pode acessar as recomendações do Advisor como *proprietário*, *colaborador*ou *leitor* de uma assinatura, grupo de recursos ou recurso.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Para quais recursos o Advisor fornece recomendações?

O Advisor fornece recomendações para o gateway de aplicativo, serviços de aplicativos, conjuntos de disponibilidade, cache do Azure, Azure Data Factory, banco de dados do Azure para MySQL, banco de dados do Azure para PostgreSQL, banco de dados do Azure para MariaDB, Azure ExpressRoute, Azure Cosmos DB, endereços IP públicos do Azure, análise de Synapse, SQL Server, contas de armazenamento, perfis do Gerenciador de tráfego e máquinas virtuais.

O Azure Advisor também inclui suas recomendações da [central de segurança do Azure](../security-center/security-center-recommendations.md) , que podem incluir recomendações para tipos de recursos adicionais.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Posso adiar ou ignorar uma recomendação?

Para adiar ou ignorar uma recomendação, clique no link **Adiar**. Você pode especificar um período de adiamento ou selecione **Nunca** para ignorar a recomendação.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as recomendações do Assistente, consulte:

* [Introdução ao Assistente](advisor-get-started.md)
* [Pontuação do supervisor](azure-advisor-score.md)
* [Recomendações de confiabilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md)
