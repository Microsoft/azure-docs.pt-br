---
title: Como usar fontes de dados de criação de perfil de dados no Catálogo de Dados do Azure
description: Artigo de instruções destacando como incluir os perfis de dados nos níveis da tabela e da coluna ao registrar as fontes de dados no Catálogo de Dados do Azure e como usar os perfis de dados para entender as fontes de dados.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: b27ff631ce13d70d15a6fd2b6dd5ad5bccaf5450
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021904"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Como fontes de dados de perfil de dados no catálogo de dados do Azure

## <a name="introduction"></a>Introdução

**Catálogo de Dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que atua como um sistema de registro e sistema de descoberta em fontes de dados da empresa. Em outras palavras, o **Catálogo de dados do Azure** está prestes a ajudar as pessoas a descobrir, compreender e usar fontes de dados e ajudar as organizações a obter mais valor de seus dados existentes. Quando uma fonte de dados é registrada no **Catálogo de Dados do Azure**, seus metadados são copiados e indexados pelo serviço, mas a história não para por aí.

O recurso **Criação do Perfil de Dados** do **Catálogo de Dados do Azure** examina os dados nas fontes de dados com suporte no catálogo e coleta estatísticas e informações sobre esses dados. É fácil incluir um perfil de seus ativos de dados. Ao registrar um ativo de dados, escolha **Incluir Perfil de Dados** na ferramenta de registro de fonte de dados.

## <a name="what-is-data-profiling"></a>O que é a criação de perfil de dados

A criação de perfil de dados examina os dados na fonte de dados que está sendo registrada e coleta estatísticas e informações sobre esses dados. Durante a descoberta de fonte de dados, as estatísticas podem ajudar você a determinar a adequação dos dados para resolver seu problema de negócios.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

As seguintes fontes de dados dão suporte à criação de perfil de dados:

* Tabelas e exibições do SQL Server (incluindo o BD SQL do Azure e o Azure Synapse Analytics)
* Tabelas e exibições do oracle
* Tabelas e exibições do Teradata
* Tabelas do Hive

A inclusão de perfis de dados ao registrar ativos de dados ajuda os usuários a responder a perguntas sobre fontes de dados, incluindo:

* Ele pode ser usado para resolver meu problema de negócios?
* Os dados estão em conformidade com padrões específicos?
* Quais são algumas das anomalias da fonte de dados?
* Quais são os possíveis desafios de integração desses dados a meu aplicativo?

> [!NOTE]
> Você também pode adicionar documentação a um ativo para descrever como os dados podem ser integrados a um aplicativo. Confira [Como documentar fontes de dados](data-catalog-how-to-documentation.md).
>

<a name="howto"></a>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Como incluir um perfil de dados ao registrar uma fonte de dados

É fácil incluir um perfil de sua fonte de dados. Quando você registra uma fonte de dados, no painel **Objetos a ser registrados** da ferramenta de registro da fonte de dados, escolha **Incluir Perfil dos Dados**.

![Caixa de seleção incluir perfil de dados](media/data-catalog-data-profile/data-catalog-register-profile.png)

Para saber mais sobre como registrar as fontes de dados, consulte [Como registrar as fontes de dados](data-catalog-how-to-register.md) e [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtragem de ativos de dados que incluem perfis de dados

Para descobrir ativos de dados que incluem um perfil de dados, você pode incluir `has:tableDataProfiles`ou `has:columnsDataProfiles`como um dos seus termos de pesquisa.

> [!NOTE]
> A seleção de **Incluir Dados de Perfil** na ferramenta de registro de fonte de dados inclui informações de perfil de nível de coluna e da tabela. No entanto, a API de Catálogo de Dados permite que os ativos de dados sejam registrados com um único conjunto de informações de perfil incluído.
>

## <a name="viewing-data-profile-information"></a>Exibição de informações de perfil de dados

Depois de encontrar uma fonte de dados adequada com um perfil, você pode exibir os detalhes do perfil de dados. Para exibir o perfil de dados, selecione um ativo de dados e escolha **Perfil de Dados** na janela do portal do Catálogo de Dados.

![Guia perfil de dados](media/data-catalog-data-profile/data-catalog-view.png)

Um perfil de dados no **Catálogo de Dados do Azure** mostra informações de perfil de tabela e coluna, incluindo:

### <a name="object-data-profile"></a>Perfil de dados de objeto

* Número de linhas
* Tamanho de tabela
* Quando o objeto foi atualizado pela última vez

### <a name="column-data-profile"></a>Perfil de dados de coluna

* Tipo de dados de coluna
* Número de valores distintos
* Número de linhas com valores NULL
* Desvio mínimo, máximo, médio e padrão para valores de colunas

## <a name="summary"></a>Resumo

A criação de perfil de dados fornece estatísticas e informações sobre ativos de dados registrados para ajudar você a determinar a adequação dos dados para solucionar problemas de negócios. Além de anotar e documentar fontes de dados, os perfis de dados podem dar aos usuários uma compreensão mais profunda dos dados.

## <a name="see-also"></a>Consulte Também

* [Como registrar fontes de dados](data-catalog-how-to-register.md)
* [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md)
