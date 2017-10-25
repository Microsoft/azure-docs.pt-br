---
title: "Como descobrir fontes de dados no Catálogo de Dados do Azure | Microsoft Docs"
description: "Este artigo destaca como descobrir ativos de dados registrados com o Catálogo de Dados do Azure, incluindo pesquisa e filtragem e o uso dos recursos de destaque realce de ocorrências do portal do Catálogo de Dados do Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: f72ae3a3-6573-4710-89a7-f13555e1968c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 9ff67dcb5ecb00440f73f979fd8d2b79a570c674
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Como descobrir fontes de dados no Catálogo de Dados do Azure
## <a name="introduction"></a>Introdução
O Catálogo de Dados do Azure é um serviço de nuvem totalmente gerenciado que atua como um sistema de registro e descoberta para fontes de dados da empresa. Em outras palavras, o Catálogo de Dados ajuda as pessoas a descobrir, entender e usar fontes de dados, além de ajudar as organizações a obterem mais valor dos seus dados existentes. Depois que uma fonte de dados for registrada com o Catálogo de Dados, seus metadados serão indexados pelo serviço de modo que você possa facilmente pesquisar para descobrir os dados de que precisa.

## <a name="searching-and-filtering"></a>Pesquisando e filtrando
A descoberta no Catálogo de Dados usa dois mecanismos principais: pesquisa e filtragem.

A pesquisa foi projetada para ser intuitiva e eficiente. Por padrão, os termos de pesquisa são correspondidos com alguma propriedade no catálogo, incluindo anotações fornecidas pelo usuário.

A filtragem foi desenvolvida para complementar a pesquisa. Você pode selecionar características específicas, como especialistas, tipo de fonte de dados, tipo de objeto e marcas. Você pode exibir somente ativos de dados correspondentes e restringir os resultados da pesquisa a ativos correspondentes.

Ao usar uma combinação de pesquisa e filtragem, você pode navegar rapidamente pelas fontes de dados que foram registradas com o Catálogo de Dados para descobrir as fontes de dados de que você precisa.

## <a name="search-syntax"></a>Sintaxe de pesquisa
Embora a pesquisa de texto livre padrão seja simples e intuitiva, você também pode usar a sintaxe de pesquisa do Catálogo de Dados para maior controle sobre os resultados da pesquisa. A pesquisa do Catálogo de Dados dá suporte às seguintes técnicas:

| Técnica | Uso | Exemplo |
| --- | --- | --- |
| Pesquisa básica |Pesquisa básica que usa um ou mais termos de pesquisa. Os resultados são quaisquer ativos que correspondam a qualquer propriedade com um ou mais dos termos especificados. |`sales data` |
| Escopo de propriedade |Retornar apenas as fontes de dados em que o termo de pesquisa corresponda à propriedade especificada. |`name:finance` |
| Operadores boolianos |Ampliar ou restringir uma pesquisa usando operações boolianas. |`finance NOT corporate` |
| Agrupamento com parênteses |Use parênteses para agrupar partes da consulta para obter o isolamento lógico, principalmente em conjunto com operadores boolianos. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Operadores de comparação |Use comparações que não sejam de igualdade para propriedades que tenham tipos de dados numéricos e de data. |`modifiedTime > "11/05/2014"` |

Para obter mais informações sobre a pesquisa no Catálogo de Dados, consulte o artigo [Catálogo de Dados do Azure](https://msdn.microsoft.com/library/azure/mt267594.aspx).

## <a name="hit-highlighting"></a>Realce de ocorrência
Quando você exibe o resultados da pesquisa, todas as propriedades exibidas que correspondem aos termos de pesquisa especificados (como o nome do ativo de dados, a descrição e as marcas) são destacadas para facilitar a identificação do motivo pelo qual um determinado ativo de dados foi retornado por uma determinada pesquisa.

> [!NOTE]
> Para desligar o destaque de ocorrências, use a opção **Destacar** no portal do Catálogo de Dados.
>
>

Ao exibir os resultados da pesquisa, talvez não seja sempre óbvio o motivo pelo qual um ativo de dados é incluído, mesmo com o destaque de ocorrências habilitado. Como todas as propriedades são pesquisadas por padrão, um ativo de dados pode ser retornado devido a uma correspondência em uma propriedade no nível de coluna. E uma vez que vários usuários podem anotar ativos de dados registrados com suas próprias marcas e descrições, talvez nem todos os metadados sejam exibidos na lista de resultados da pesquisa.

No modo de exibição em bloco padrão, cada bloco exibido nos resultados da pesquisa inclui um ícone **Exibir correspondências do termo de pesquisa** para que você possa rapidamente exibir o número de correspondências e sua localização e acessá-las, se desejar.

 ![Realce de ocorrências e correspondências de pesquisa no portal do Catálogo de Dados do Azure](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Resumo
Uma vez que registrar uma fonte de dados com o catálogo de dados copia os metadados estruturais e descritivos da fonte de dados para o serviço de catálogo, fica mais fácil descobrir e entender a fonte de dados. Após ter registrado uma fonte de dados, você pode descobri-la usando a filtragem e a pesquisa dentro do Catálogo de Dados.

## <a name="next-steps"></a>Próximas etapas
* Para obter detalhes passo a passo sobre como descobrir fontes de dados, consulte [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md).
