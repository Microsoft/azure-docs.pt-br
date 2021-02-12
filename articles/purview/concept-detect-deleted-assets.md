---
title: Como as verificações detectam ativos excluídos
description: Este artigo explica como uma conta do Azure alcance detecta ativos excluídos durante as verificações.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 9b1515ef00355c831e161c01227678197792cc20
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551854"
---
# <a name="how-scans-detect-deleted-assets"></a>Como as verificações detectam ativos excluídos

Este artigo descreve como o Azure alcance usa os resultados da verificação para detectar ativos excluídos.

## <a name="background-info"></a>Informações em segundo plano

Um catálogo alcance do Azure está ciente do estado de um armazenamento de dados somente quando ele o verifica. Para que o catálogo saiba se um arquivo, uma tabela ou um contêiner foi excluído, ele compara a saída da última verificação com a saída da verificação atual. Por exemplo, suponha que a última vez que você examinou uma conta de Azure Data Lake Storage Gen2, ela incluiu uma pasta chamada *Pasta1*. Quando a mesma conta é verificada novamente, *Pasta1* está faltando. Portanto, o catálogo assume que a pasta foi excluída.

## <a name="detecting-deleted-files"></a>Detectando arquivos excluídos

A lógica para detectar arquivos ausentes funciona para várias verificações pelo mesmo usuário, bem como por usuários diferentes. Por exemplo, suponha que um usuário execute uma verificação única em um armazenamento de dados Data Lake Storage Gen2 nas pastas A, B e C. Posteriormente, um usuário diferente na mesma conta executa uma verificação única diferente nas pastas C, D e e do mesmo armazenamento de dados. Como a pasta C foi verificada duas vezes, o catálogo verifica se há possíveis exclusões. As pastas A, B, D e e e, no entanto, foram verificadas apenas uma vez e o catálogo não as verificará em busca de ativos excluídos.

Para manter os arquivos excluídos fora do catálogo, é importante executar verificações regulares. O intervalo de verificação é importante, pois o catálogo não pode detectar ativos excluídos até que outra verificação seja executada. Portanto, se você executar verificações uma vez por mês em um repositório específico, o catálogo não poderá detectar quaisquer ativos de dados excluídos nessa loja até que você execute a próxima verificação um mês depois.

Quando você enumera armazenamentos de dados grandes como Data Lake Storage Gen2, há várias maneiras (incluindo erros de enumeração e eventos ignorados) para perder informações. Uma verificação específica pode perder a criação ou a exclusão de um arquivo. Portanto, a menos que o catálogo tenha certeza de que um arquivo foi excluído, ele não o excluirá do catálogo. Essa estratégia significa que pode haver erros quando um arquivo que não existe no repositório de dados verificado ainda existir no catálogo. Em alguns casos, um armazenamento de dados pode precisar ser examinado duas ou três vezes antes de capturar determinados ativos excluídos.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar os catálogos do Azure alcance, consulte [início rápido: criar uma conta do alcance](create-catalog-portal.md).
