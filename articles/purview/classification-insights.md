---
title: Relatórios de classificação em seus dados no Azure alcance usando o alcance insights
description: Este guia de instruções descreve como exibir e usar o relatório de classificação do alcance em seus dados.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: b6ea3e762ad098e373baa8487d8926105820f226
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101666521"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>Informações de classificação sobre seus dados do Azure alcance

Este guia de instruções descreve como acessar, exibir e filtrar relatórios de insights de classificação alcance para seus dados.

As fontes de dados com suporte incluem: armazenamento de BLOBs do Azure, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Azure Cosmos DB (API do SQL), Azure Synapse Analytics (anteriormente conhecido como SQL DW), banco de dados SQL do Azure, Azure SQL Instância Gerenciada, SQL Server, Buckets do Amazon S3

Neste guia de instruções, você aprenderá a:

> [!div class="checklist"]
> - Inicie sua conta do alcance do Azure
> - Exibir informações de classificação em seus dados
> - Faça uma busca detalhada para obter mais detalhes de classificação sobre seus dados

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a usar o alcance insights, verifique se você concluiu as seguintes etapas:

- Configurar os recursos do Azure e preencher as contas relevantes com dados de teste

- Configurar e concluir uma verificação nos dados de teste em cada fonte de dados. Para obter mais informações, consulte [gerenciar fontes de dados no Azure alcance (versão prévia)](manage-data-sources.md) e [criar um conjunto de regras de verificação](create-a-scan-rule-set.md).

- Conectado ao alcance com uma [função de leitor de dados ou de curador dos dados](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles).

Para obter mais informações, consulte [gerenciar fontes de dados no Azure alcance (versão prévia)](manage-data-sources.md).

## <a name="use-purview-classification-insights"></a>Usar percepções de classificação alcance

No Azure alcance, as classificações são semelhantes às marcas de assunto e são usadas para marcar e identificar dados de um tipo específico que é encontrado no seu estado de dados durante a verificação.

O alcance usa os mesmos tipos de informações confidenciais que Microsoft 365, permitindo que você amplie suas políticas de segurança e proteção existentes em todo o seu espaço de dados.

> [!NOTE]
> Depois de ter examinado os tipos de origem, dê informações de **classificação** a algumas horas para refletir os novos ativos.

**Para exibir informações de classificação:**

1. Vá para a tela instância **do Azure alcance** [na portal do Azure](https://aka.ms/purviewportal) e selecione sua conta do alcance.

1. Na página **visão geral** , **na seção Introdução** , selecione o bloco **Iniciar conta do alcance** .

1. Em alcance, selecione o item de menu **insights** à :::image type="icon" source="media/insights/ico-insights.png" border="false"::: esquerda para acessar sua área do **insights** .

1. Na área **insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: , selecione **classificação** para exibir o relatório de **informações de classificação** alcance.

   :::image type="content" source="./media/insights/select-classification-labeling-small.png" alt-text="Relatório de informações de classificação" lightbox="media/insights/select-classification-labeling.png":::

   A página principal de **informações de classificação** exibe as seguintes áreas:

   |Área  |Descrição  |
   |---------|---------|
   |**Visão geral de fontes com classificações**     |Exibe blocos que fornecem: <br>-O número de assinaturas encontradas em seus dados <br>-O número de classificações exclusivas encontradas em seus dados <br>-O número de fontes classificadas encontradas <br>-O número de arquivos classificados encontrados <br>-O número de tabelas classificadas encontradas         |
   |**Principais fontes com dados classificados (últimos 30 dias)**     |Mostra a tendência, nos últimos 30 dias, do número de fontes encontradas com dados classificados.            |
   |**Principais categorias de classificação por fontes**     |Mostra o número de fontes encontradas por categoria de classificação, como **financeira** ou **governamental**.      |
   |**Principais classificações para arquivos**     |Mostra as principais classificações aplicadas aos arquivos em seus dados, como números de cartão de crédito ou números de identificação nacional.         |
   |**Principais classificações para tabelas**     | Mostra as principais classificações aplicadas a tabelas em seus dados, como informações pessoais de identificação. |   
   |  **Atividade de classificação** <br>(arquivos e tabelas) |  Exibe grafos separados para arquivos e tabelas, cada um mostrando o número de arquivos ou tabelas classificados no período de tempo selecionado. <br>**Padrão**: 30 dias<br>Selecione o filtro de **tempo** acima dos grafos para selecionar um período de tempo diferente a ser exibido.    |
   |    |    |

## <a name="classification-insights-drilldown"></a>Análise de informações de classificação

Em qualquer um dos seguintes gráficos de **informações de classificação** , selecione o link **Exibir mais** para fazer uma busca detalhada para obter mais detalhes:

- **Principais categorias de classificação por fontes**
- **Principais classificações para arquivos**
- **Principais classificações para tabelas**
- **Atividade de classificação > dados de classificação**

Por exemplo:

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="Exibir todas as classificações" lightbox="media/insights/view-classifications.png":::

Siga um destes procedimentos para saber mais:

|Opção  |Descrição  |
|---------|---------|
|**Filtrar seus dados**     |  Use os filtros acima da grade para filtrar os dados mostrados, incluindo o nome da classificação, o nome da assinatura ou o tipo de origem. <br><br>Se você não tiver certeza do nome exato da classificação, poderá inserir parte ou todo o nome na caixa **Filtrar por palavra-chave** .       |
|**Classificar a grade** |Selecione um cabeçalho de coluna para classificar a grade por essa coluna. | 
|**Editar colunas**     |  Para exibir mais ou menos colunas em sua grade, selecione **Editar colunas** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: e, em seguida, selecione as colunas que você deseja exibir ou alterar a ordem.   |
|**Detalhar ainda mais**     | Para fazer uma busca detalhada em uma classificação específica, selecione um nome na coluna **classificação** para exibir a **classificação por** relatório de origem. <br><br>Este relatório exibe dados para a classificação selecionada, incluindo o nome da origem, o tipo de origem, a ID da assinatura e os números de arquivos e tabelas classificados.      |
|**Procurar ativos**     |  Para navegar pelos ativos encontrados com uma classificação ou origem específica, selecione uma classificação ou origem, dependendo do relatório que você está exibindo e, em seguida, selecione **procurar ativos** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: acima dos filtros. <br><br>Os resultados da pesquisa exibem todos os ativos classificados encontrados para o filtro selecionado.  Para obter mais informações, consulte [Pesquisar o catálogo de dados do Azure alcance](how-to-search-catalog.md).       |
| | |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os relatórios do Azure alcance Insight
> [!div class="nextstepaction"]
> [Ideias de Glossário](glossary-insights.md)

> [!div class="nextstepaction"]
> [Examinar informações](scan-insights.md)

> [!div class="nextstepaction"]
> [Informações de rótulo de sensibilidade](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Informações de extensão de arquivo](file-extension-insights.md)