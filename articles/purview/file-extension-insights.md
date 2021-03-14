---
title: Relatórios de extensão de arquivo em seus dados no Azure alcance usando o alcance insights
description: Este guia de instruções descreve como exibir e usar os relatórios de extensão de arquivo alcance em seus dados.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: 5cbfb41d50e055f745864e4d5f8bc15a55d925e7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668562"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Informações de extensão de arquivo sobre seus dados do Azure alcance 

Este guia de instruções descreve como acessar, exibir e filtrar informações sobre as extensões de arquivo, ou tipos de arquivo, encontradas em seus dados.

As fontes de dados com suporte incluem: armazenamento de BLOBs do Azure, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Buckets do Amazon S3

Neste guia de instruções, você aprenderá a:
> [!div class="checklist"]
> * Inicie sua conta do alcance do Azure 
> - Exibir informações de extensão de arquivo em seus dados
> - Faça uma busca detalhada para obter mais detalhes de extensão de arquivo em seus dados

## <a name="prerequisites"></a>Pré-requisitos 

Antes de começar a usar o alcance insights, verifique se você concluiu as seguintes etapas:

- Configurar os recursos do Azure e preencher as contas relevantes com dados de teste

- Configurar e concluir uma verificação nos dados de teste em cada fonte de dados. Para obter mais informações, consulte [gerenciar fontes de dados no Azure alcance (versão prévia)](manage-data-sources.md) e [criar um conjunto de regras de verificação](create-a-scan-rule-set.md).

- Conectado ao alcance com uma [função de leitor de dados ou de curador dos dados](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles).


Para obter mais informações, consulte [gerenciar fontes de dados no Azure alcance (versão prévia)](manage-data-sources.md).

## <a name="use-purview-file-extension-insights"></a>Usar informações de extensão de arquivo alcance

Ao verificar seus ativos, o Azure alcance é capaz de detectar os tipos de arquivo encontrados em seu espaço de dados e fornecer mais detalhes sobre cada tipo de arquivo. Os detalhes incluem quantos arquivos de cada tipo você tem, onde esses arquivos são e se são verificáveis quanto a dados confidenciais.

> [!NOTE]
> Depois de ter examinado os tipos de origem, forneça informações de **extensão de arquivo** a algumas horas para refletir os novos ativos.

**Para exibir informações de extensão de arquivo:**

1. Vá para a tela instância **do Azure alcance** [na portal do Azure](https://aka.ms/purviewportal) e selecione sua conta do alcance.

1. Na página **visão geral** , **na seção Introdução** , selecione o bloco **Iniciar conta do alcance** .

1. Em alcance, selecione o item de menu **insights** à :::image type="icon" source="media/insights/ico-insights.png" border="false"::: esquerda para acessar sua área do **insights** .
    
1. Em **percepções**, selecione a guia **extensões de arquivo** .

    O relatório exibe um resumo de quantas extensões de arquivo exclusivas são encontradas, bem como um grafo das 10 principais extensões encontradas, durante o período de tempo selecionado (padrão: 30 dias).

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="Relatório de extensão de arquivo-visão geral" lightbox="media/file-extension-insights/file-extension-overview.png":::

    Siga um destes procedimentos para saber mais:

    - Selecione o seletor de **tempo** na parte superior do relatório para alterar o período de tempo para o qual as extensões de arquivo foram encontradas.
    
    - Selecione **Exibir mais** abaixo do grafo para exibir uma lista completa das extensões de arquivo encontradas. Para obter mais informações, consulte [análise da extensão de arquivo insights](#file-extension-insights-drilldown). 

### <a name="file-extension-insights-drilldown"></a>Busca detalhada de extensão de arquivo

Depois de exibir as informações de alto nível sobre os tipos de arquivo encontrados no seu espaço de dados, faça uma busca detalhada para obter mais detalhes sobre onde eles estão localizados e se eles podem ser verificados quanto a dados confidenciais.

Por exemplo: 

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="Relatório de extensão de arquivo-análise" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

A grade mostra detalhes de cada extensão de arquivo encontrada, incluindo:

- **Contagem de arquivos**. O número de arquivos com a extensão especificada.
- **Verificação de conteúdo**. Se a extensão de arquivo tem suporte ou não para a verificação de dados confidenciais.
- **Assinaturas**. O número de assinaturas em que a extensão especificada foi encontrada.
- **Fontes**. O número de fontes encontradas com a extensão de arquivo especificada.



Use os filtros acima da grade para filtrar os dados mostrados:

|Opção  |Descrição  |
|---------|---------|
|**Filtrar por palavra-chave**     |    Digite o texto na caixa **Filtrar por palavra-chave**  para exibir o filtro dos tipos de arquivo por nome. Por exemplo, para exibir apenas os PDFs, insira `PDF` .     |
|**Hora**        | Selecione para filtrar por um período de tempo específico para quando os dados foram criados. <br>**Padrão:** 30 dias  |
|**Extensão de arquivo**     |Selecione para filtrar a grade por um ou mais tipos de arquivo.        |
|**Fontes**    |Selecione para filtrar a grade pelas fontes de dados específicas. |
|**Verificação de conteúdo**     |Selecione para escolher **com suporte** ou **sem suporte**, para mostrar apenas os tipos de arquivo que podem ser verificados em busca de dados confidenciais ou dados que não podem ser examinados, como arquivos **. cert** ou **. jpg** . |
| | |

Acima dos filtros, selecione **Editar colunas** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: para exibir mais ou menos colunas em sua grade ou para alterar a ordem. 

Para classificar a grade, selecione um cabeçalho de coluna para classificar por essa coluna.
## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os relatórios do Azure alcance Insight
> [!div class="nextstepaction"]
> [Ideias de Glossário](glossary-insights.md)

> [!div class="nextstepaction"]
> [Examinar informações](scan-insights.md)

> [!div class="nextstepaction"]
> [Informações de classificação](./classification-insights.md)

> [!div class="nextstepaction"]
> [Informações de rótulo de sensibilidade](sensitivity-insights.md)
