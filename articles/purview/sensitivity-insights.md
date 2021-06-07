---
title: Rótulo de confidencialidade de relatórios sobre seus dados no Azure alcance usando o alcance insights
description: Este guia de instruções descreve como exibir e usar os relatórios de rótulo de sensibilidade de alcance em seus dados.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: bb8ac82b2e59ec86db89c7eba0ce607fcfc0ac2d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676567"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>Informações de rótulo de confidencialidade sobre seus dados no Azure alcance

Este guia de instruções descreve como acessar, exibir e filtrar informações de segurança fornecidas por rótulos de sensibilidade aplicados aos seus dados.

As fontes de dados com suporte incluem: armazenamento de BLOBs do Azure, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, SQL Server, banco de dados SQL do Azure, Azure SQL Instância Gerenciada, Buckets do Amazon S3

Neste guia de instruções, você aprenderá a:

> [!div class="checklist"]
> - Inicie sua conta do alcance do Azure.
> - Exibir informações de rótulo de confidencialidade em seus dados
> - Faça uma busca detalhada para obter detalhes de rótulos de confidencialidade sobre seus dados

> [!NOTE]
> Os rótulos de sensibilidade encontrados em [ativos de Power bi](register-scan-power-bi-tenant.md) que são verificados pelo alcance não são mostrados no momento no relatório de informações de rótulo de sensibilidade. 
>
> Para exibir rótulos de sensibilidade em ativos de Power BI, exiba o ativo no [Catálogo de dados alcance](how-to-search-catalog.md).
> 
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a usar o alcance insights, verifique se você concluiu as seguintes etapas:

- Configurar os recursos do Azure e preencher as contas relevantes com dados de teste

- [Microsoft 365 estendidos rótulos de sensibilidade a ativos no Azure alcance](create-sensitivity-label.md)e criou ou selecionou os rótulos que você deseja aplicar aos seus dados.

- Configurar e concluir uma verificação nos dados de teste em cada fonte de dados. Para obter mais informações, consulte [gerenciar fontes de dados no Azure alcance (versão prévia)](manage-data-sources.md) e [criar um conjunto de regras de verificação](create-a-scan-rule-set.md).

- Conectado ao alcance com uma [função de leitor de dados ou de curador dos dados](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles).

Para obter mais informações, consulte [gerenciar fontes de dados no Azure alcance (versão prévia)](manage-data-sources.md) e [rotular automaticamente seus dados no alcance do Azure](create-sensitivity-label.md).

## <a name="use-purview-sensitivity-labeling-insights"></a>Usar informações de rótulo de sensibilidade de alcance

No alcance, as classificações são semelhantes às marcas de assunto e são usadas para marcar e identificar dados de um tipo específico que é encontrado em seu estado de dados durante a verificação.

Os rótulos de sensibilidade permitem que você declare a confidencialidade de determinados dados em sua organização. Por exemplo, um nome de projeto específico pode ser altamente confidencial em sua organização, enquanto esse mesmo termo não é confidencial para outras organizações. 

As classificações são correspondidas diretamente, como um número do seguro social, que tem uma classificação de número do seguro **social**. 

Por outro lado, os rótulos de sensibilidade são aplicados quando uma ou mais classificações e condições são encontradas em conjunto. Nesse contexto, as [condições](/microsoft-365/compliance/apply-sensitivity-label-automatically) se referem a todos os parâmetros que você pode definir para dados não estruturados, como **proximidade a outra classificação** e **% de confiança**. 

O alcance usa as mesmas classificações, também conhecidas como [tipos de informações confidenciais](/microsoft-365/compliance/sensitive-information-type-entity-definitions), como Microsoft 365. Isso permite que você estenda os rótulos de sensibilidade existentes nos ativos de alcance do Azure.

> [!NOTE]
> Depois de ter examinado seus tipos de origem, conceda a **sensibilidade de rotular** informações a algumas horas para refletir os novos ativos.

**Para exibir as informações de rótulo de confidencialidade:**

1. Vá para o home page **alcance do Azure** .

1. Na página **visão geral** , **na seção Introdução** , selecione o bloco **Iniciar conta do alcance** .

1. Em alcance, selecione o item de menu **insights** à :::image type="icon" source="media/insights/ico-insights.png" border="false"::: esquerda para acessar sua área do **insights** .

1. Na área **insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: , selecione **Rótulos de sensibilidade** para exibir o relatório de alcance de **confidencialidade de informações** .

    > [!NOTE]
    > Se esse relatório estiver vazio, talvez você não tenha estendido seus rótulos de sensibilidade para o Azure alcance. Para obter mais informações, consulte [rotular os dados automaticamente no Azure alcance](create-sensitivity-label.md).

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="Informações de rótulo de sensibilidade" lightbox="media/insights/sensitivity-labeling-insights.png":::

   A página **confidencialidade de rótulo de informações** exibe as seguintes áreas:

   |Área  |Descrição  |
   |---------|---------|
   |**Visão geral das fontes com rótulos de sensibilidade**     |Exibe blocos que fornecem: <br>-O número de assinaturas encontradas em seus dados. <br>-O número de rótulos de sensibilidade exclusivos aplicados aos seus dados <br>-O número de fontes com rótulos de sensibilidade aplicados <br>-O número de arquivos e tabelas encontrados com rótulos de sensibilidade aplicados|
   |**Principais fontes com dados rotulados (últimos 30 dias)**     | Mostra a tendência, nos últimos 30 dias, do número de fontes com rótulos de sensibilidade aplicados.       |
   |**Rótulos principais aplicados entre origens**     |Mostra os principais rótulos aplicados em todos os seus recursos de dados do alcance. |
   |**Principais rótulos aplicados em arquivos**     |Mostra os rótulos de sensibilidade mais importantes aplicados aos arquivos em seus dados.          |
   |**Rótulos principais aplicados em tabelas**     | Mostra os rótulos de sensibilidade mais importantes aplicados às tabelas de banco de dados em seu dado. |   
   |  **Rotulando atividade**  |  Exibe grafos separados para arquivos e tabelas, cada um mostrando o número de arquivos ou tabelas rotuladas no intervalo de tempo selecionado. <br>**Padrão**: 30 dias<br>Selecione o filtro de **tempo** acima dos grafos para selecionar um período de tempo diferente a ser exibido.    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>Sensibilidade da confidencialidade das ideias

Em qualquer uma das seguintes **confidencialidades de rótulo de insights** , selecione o link **Exibir mais** para detalhar para obter mais detalhes:

- **Rótulos principais aplicados entre origens**
- **Principais rótulos aplicados em arquivos**
- **Rótulos principais aplicados em tabelas**
- **Rotulando a atividade > dados rotulados**

Por exemplo:

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="Busca detalhada de rótulo de sensibilidade" lightbox="media/insights/sensitivity-label-drilldown.png":::

Siga um destes procedimentos para saber mais:

|Opção  |Descrição  |
|---------|---------|
|**Filtrar seus dados**     |  Use os filtros acima da grade para filtrar os dados mostrados, incluindo o nome do rótulo, o nome da assinatura ou o tipo de origem. <br><br>Se você não tiver certeza do nome exato do rótulo, poderá inserir parte ou todo o nome na caixa **Filtrar por palavra-chave** .       |
|**Classificar a grade** |Selecione um cabeçalho de coluna para classificar a grade por essa coluna. | 
|**Editar colunas**     |  Para exibir mais ou menos colunas em sua grade, selecione **Editar colunas** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: e, em seguida, selecione as colunas que você deseja exibir ou alterar a ordem.    <br><br>Selecione um cabeçalho de coluna para classificar a grade por essa coluna.   |
|**Detalhar ainda mais**     | Para fazer uma busca detalhada em um rótulo específico, selecione um nome na coluna **rótulo de sensibilidade** para exibir o **rótulo por** relatório de origem. <br><br>Este relatório exibe dados para o rótulo selecionado, incluindo o nome da origem, o tipo de origem, a ID da assinatura e os números de arquivos e tabelas classificados.      |
|**Procurar ativos**     |  Para navegar pelos ativos encontrados com um rótulo ou origem específico, selecione um ou mais rótulos ou fontes, dependendo do relatório que você está exibindo e, em seguida, selecione **procurar ativos** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: acima dos filtros. <br><br>Os resultados da pesquisa exibem todos os ativos rotulados encontrados para o filtro selecionado.  Para obter mais informações, consulte [Pesquisar o catálogo de dados do Azure alcance](how-to-search-catalog.md).       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>Integração de rótulo de sensibilidade com conformidade de Microsoft 365

Feche a integração com a [proteção de informações da Microsoft](/microsoft-365/compliance/information-protection) oferecida em Microsoft 365 significa que o alcance permite maneiras diretas de estender a visibilidade para seu estado de dados e classificar e rotular seus dados.

Para que seus Microsoft 365 rótulos de sensibilidade sejam estendidos para seus ativos no Azure alcance, você deve ativar ativamente a proteção de informações para o Azure alcance, no centro de conformidade Microsoft 365.

Para obter mais informações, consulte [rotular os dados automaticamente no Azure alcance](create-sensitivity-label.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre estes relatórios do Azure alcance Insight:

- [Ideias de Glossário](glossary-insights.md)
- [Examinar informações](scan-insights.md)
- [Informações de classificação](./classification-insights.md)
- [Informações de extensão de arquivo](file-extension-insights.md)
