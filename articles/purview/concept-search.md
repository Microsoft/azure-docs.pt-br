---
title: Entender os recursos de pesquisa no Azure alcance (versão prévia)
description: Este artigo explica como o Azure alcance permite a descoberta de dados por meio de recursos de pesquisa.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: af8ec9e0aac38240c7da92edd614892ff65712e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96551449"
---
# <a name="understand-search-features-in-azure-purview"></a>Entender os recursos de pesquisa no Azure alcance

Este artigo fornece uma visão geral da experiência de pesquisa no Azure alcance. A pesquisa é uma funcionalidade de plataforma principal do alcance, que capacita a descoberta de dados e as experiências de governança de uso de dados em uma organização.

## <a name="search"></a>Search

A experiência de pesquisa do alcance é alimentada por um índice de pesquisa gerenciado. Depois que uma fonte de dados é registrada com alcance, seus metadados são indexados pelo serviço de pesquisa para permitir a descoberta fácil. O índice fornece recursos de relevância de pesquisa e conclui solicitações de pesquisa consultando milhões de ativos de metadados. A pesquisa ajuda você a descobrir, compreender e usar os dados para obter o máximo de valor.

A experiência de pesquisa no alcance é um processo de três estágios:

1. A caixa de pesquisa mostra o histórico que contém os ativos e as palavras-chave usadas recentemente.
1. Quando você começa a digitar os pressionamentos de teclas, a pesquisa sugere as palavras-chave e os ativos correspondentes. 
1. A página resultado da pesquisa é mostrada com ativos que correspondem à palavra-chave inserida.

## <a name="reduce-the-time-to-discover-data"></a>Reduzir o tempo de descoberta de dados

A descoberta de dados é a primeira etapa para uma análise de dados ou uma carga de trabalho de governança de dados para consumidores de dados. A descoberta de dados pode ser demorada, porque você pode não saber onde encontrar os dados desejados. Mesmo depois de encontrar os dados, você pode ter dúvida sobre se você pode ou não confiar nos dados e fazer dependências nele. 

O objetivo da pesquisa no Azure alcance é acelerar o processo de descoberta de dados fornecendo gestos para localizar rapidamente os dados que importa.

## <a name="recent-search-and-suggestions"></a>Pesquisa e sugestões recentes

Muitas vezes, você pode estar trabalhando em vários projetos ao mesmo tempo. Para facilitar a retomada de projetos anteriores, a pesquisa do alcance fornece a capacidade de ver palavras-chave de pesquisa recentes e sugestões. Além disso, você pode gerenciar o histórico de pesquisa recente selecionando **Exibir tudo** na lista suspensa caixa de pesquisa.

## <a name="filters"></a>Filtros

Os filtros (também conhecidos como *facetas*) são projetados para complementar a pesquisa. Os filtros são mostrados na página de resultados da pesquisa. Os filtros na página de resultados da pesquisa incluem classificação, rótulo de confidencialidade, fonte de dados e proprietários. Os usuários podem selecionar valores específicos em um filtro para ver apenas os ativos de dados correspondentes e restringir o resultado da pesquisa aos ativos correspondentes.

## <a name="hit-highlighting"></a>Realce de ocorrência

As palavras-chave correspondentes na página de resultados da pesquisa são realçadas para facilitar a identificação do motivo pelo qual um ativo de dados foi retornado pela pesquisa. A correspondência de palavra-chave pode ocorrer em vários campos, como nome do ativo de dados, descrição e proprietário.

Talvez não seja óbvio por que um ativo de dados está incluído na pesquisa, mesmo com o realce de clique habilitado. Todas as propriedades são pesquisadas por padrão. Portanto, um ativo de dados pode ser retornado devido a uma correspondência em uma propriedade em nível de coluna. E como vários usuários podem anotar os ativos de dados com suas próprias classificações e descrições, nem todos os metadados são exibidos na lista de resultados da pesquisa.

## <a name="sort"></a>Sort

Os usuários têm duas opções para classificar os resultados da pesquisa. Eles podem classificar pelo nome do ativo ou por relevância de pesquisa padrão.

## <a name="search-relevance"></a>Relevância de pesquisa

Relevância é a ordem de classificação padrão na página de resultados da pesquisa. A relevância da pesquisa localiza documentos que incluem a palavra-chave Search (algumas ou todas). Os ativos que contêm muitas instâncias da palavra-chave de pesquisa são classificados como mais altos. Além disso, os mecanismos de Pontuação personalizados são constantemente ajustados para melhorar a relevância de pesquisa.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar uma conta do Azure Purview no portal do Azure](create-catalog-portal.md)
* [Início Rápido: Criar uma conta do Azure Purview usando o Azure PowerShell/a CLI do Azure](create-catalog-powershell.md)
* [Início rápido: usar o alcance Studio](use-purview-studio.md)
