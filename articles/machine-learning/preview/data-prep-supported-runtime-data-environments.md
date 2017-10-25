---
title: "Suporte para combinações de ambientes de execução e dados para Preparação de Dados de Machine Learning do Azure | Microsoft Docs"
description: "Este documento fornece uma lista completa de combinações de diferentes tempos de execução e fontes de dados com suporte para a preparação de dados do Azure ML"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 02e0ca96bff7781c242b4c5e965b229065e71725
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="supported-matrix-for-this-release"></a>Matriz com suporte para esta versão 
Quando seu código estiver carregando dados usando Fontes de Dados ou com Preparações de Dados, obter um dataframe Pandas ou Spark, há suporte para as seguintes combinações de ambientes de computação de experimento e locais de dados:

|     |Arquivos locais  |BLOB do Azure  |Banco de Dados do SQL Server***  |
|---------|---------|---------|---------|---------|
|Python local    |     Suportado    |Sem suporte         | Sem suporte        |         |
|Docker (Linux VM) Python     |Com suporte somente em arquivos de projeto*         | Sem suporte        |        Sem suporte |         |
|Docker (Linux VM) PySpark     |Com suporte somente em Arquivos de Projeto*     |Suportado         | Com suporte**        |         |
|Máquina Virtual de Ciência de Dados do Azure em Python     |Com suporte somente em arquivos de projeto*         |Sem suporte         |Sem suporte         |         |
|Máquina Virtual de Ciência de Dados do Azure em PySPark     | Com suporte somente em arquivos de projeto*        |Sem suporte         |Sem suporte         |         |
|Azure HDInsight PySpark     | Sem suporte        |Suportado         |Com suporte**         |         |
|Azure HDInsight Python     | Sem suporte        | Sem suporte        | Sem suporte        |         |

O Azure Data Lake Store não tem suporte para nenhum destino de computação no momento.

* Quando caminhos de arquivo local são usados, os arquivos no projeto são copiados para o ambiente de computação e, em seguida, são lidos lá. Arquivos fora do projeto não são copiados e os caminhos não serão mais resolvidos no ambiente de computação. Considere o uso de Substituição de Fonte de Dados de modo que seu código possa usar um arquivo local ao executar de maneira lógica, então mude para um Azure Storage Blob para uma configuração de execução diferente. Você também pode usar o suporte de amostragem em Fontes de Dados para gerenciar execuções em dados grandes somente em determinadas configurações de execução.

**Usa o driver do SQL Server Maven JDBC 6.2.1. Você deve garantir que esse pacote (ou um compatível) esteja incluído no arquivo spark_dependencies.yml para o ambiente de computação.

***Dá suporte ao Banco de Dados SQL do Azure, SQL Data Warehouse do Azure ou Microsoft SQL Server, desde que o banco de dados possa ser alcançado do ambiente de computação. 
