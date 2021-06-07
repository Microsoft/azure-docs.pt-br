---
title: Guia de programação do UDO do U-SQL para Azure Data Lake
description: Saiba mais sobre o Azure Data Lake Analytics de programação UDO do U-SQL para permitir que você crie um bom script USQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 02360c68e5e830ceee69075fd5532b126d85bec2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512506"
---
# <a name="u-sql-user-defined-objects-overview"></a>Visão geral dos objetos definidos pelo usuário do U-SQL


## <a name="u-sql-user-defined-objects-udo"></a>U-SQL: objetos definidos pelo usuário: UDO
O U-SQL permite definir objetos de programação personalizados que são chamados de objetos definidos pelo usuário ou UDO.

Veja a seguinte lista de UDO no U-SQL:

* Extratores definidos pelo usuário
    * Extrair linha por linha
    * Usado para implementar extração de dados de arquivos estruturados personalizados

* Outputters definidos pelo usuário
    * Gerar linha por linha
    * Usado para gerar tipos de dados personalizados ou formatos de arquivo personalizado

* Processadores definidos pelo usuário
    * Usar uma linha e gerar uma linha
    * Usado para reduzir o número de colunas ou produzir novas colunas com valores derivados de um conjunto de colunas existente

* Aplicadores definidos pelo usuário
    * Usar uma linha e gerar 0 para n linhas
    * Usado com OUTER/CROSS APPLY

* Combinadores definidos pelo usuário
    * Combina conjuntos de linhas — JOINs definidos pelo usuário

* Redutores definidos pelo usuário
    * Usar n linhas e gerar uma linha
    * Usado para reduzir o número de linhas

Geralmente, o UDO é chamado explicitamente no script U-SQL como parte das seguintes instruções U-SQL:

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

> [!NOTE]  
> UDOs são limitados para consumir 0,5 GB de memória.  Essa limitação de memória não se aplica a execuções locais.

## <a name="next-steps"></a>Próximas etapas
* [Guia de programação do U-SQL-visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programação do U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)