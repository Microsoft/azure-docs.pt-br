---
title: Dados de exemplo em diferentes locais de armazenamento do Azure-processo de ciência de dados de equipe
description: Dados de exemplo no Azure contêineres, SQL Server e tabelas Hive para reduzi-lo para um tamanho menor, mas representativo e mais gerenciável.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 878a2a47dfaddf1d3399a1dbd98865332838e148
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321743"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Exemplo de dados em contêineres de blob do Azure, SQL Server e tabelas Hive

Os artigos a seguir descrevem como exemplos de dados armazenados em um dos três locais diferentes do Azure:

* [**Os dados do contêiner de blobs do Azure**](sample-data-blob.md) são amostrados fazendo o download programaticamente e, em seguida, amostrando-os com código Python de amostra.
* [**Dados do SQL Server**](sample-data-sql-server.md) são amostrados usando o SQL e a linguagem de programação Python. 
* [**Dados da tabela do hive**](sample-data-hive.md) é realizada usando consultas de Hive.

Essa tarefa de amostragem é uma etapa do [TDSP (Processo de Ciência de Dados de Equipe)](./index.yml).

**Por que fazer a amostragem de dados?**

Se o conjunto de dados que você deseja analisar for grande, geralmente, é uma boa ideia reduzir os dados para um tamanho menor, mas representativo e mais gerenciável. O downsizing pode facilitar a compreensão dos dados, a exploração e a engenharia de recursos. Essa função de amostragem no processo do Cortana Analytics é habilitar o rápido protótipo das funções de processamento de dados e dos modelos de aprendizado de máquina.