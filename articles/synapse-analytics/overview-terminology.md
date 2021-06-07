---
title: Terminologia – Azure Synapse Analytics
description: Guia de referência que orienta o usuário pelo Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 828f37030ae567cacbaad25849b7ba24c561c20c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98132759"
---
# <a name="azure-synapse-analytics-terminology"></a>Terminologia do Azure Synapse Analytics

Este documento descreve os conceitos básicos do Azure Synapse Analytics.

## <a name="basics"></a>Noções básicas

Um **workspace do Azure Synapse** é um limite de colaboração protegível para fazer análises empresariais baseadas em nuvem no Azure. Um workspace é implantado em uma região específica e tem uma conta do ADLS Gen2 e um sistema de arquivos associados (para armazenar dados temporários). Um workspace está localizado em um grupo de recursos.

Um workspace permite que você execute análises com o SQL e o Apache Spark. Os recursos disponíveis para análises de SQL e do Spark são organizados em **pools** de SQL e do Spark. 

## <a name="linked-services"></a>Serviços vinculados

Um workspace pode conter qualquer número de **Serviço vinculado**, essencialmente cadeias de conexão que definem as informações de conexão necessárias para que o workspace se conecte a recursos externos.

## <a name="synapse-sql"></a>SQL do Synapse

O **SQL do Synapse** é a capacidade de fazer análises baseadas em T-SQL no workspace do Azure Synapse. O SQL do Synapse tem dois modelos de consumo: dedicado e sem servidor.  Para o modelo dedicado, use **pools de SQL dedicados**. Um workspace pode ter qualquer quantidade desses pools. Para usar o modelo sem servidor, use os **pools de SQL sem servidor**. Todo workspace tem um desses pools.

No Synapse Studio, você pode trabalhar com pools de SQL criando e executando **scripts SQL**.

## <a name="apache-spark-for-synapse"></a>Apache Spark para o Synapse

Para usar a análise do Spark, crie e use **pools do Apache Spark sem servidor** no seu workspace do Azure Synapse. Quando você começa a usar um Pool do Spark, os workspaces criam uma **sessão do Spark** para processar os recursos associados à sessão. 

Há duas maneiras de usar o Spark no Azure Synapse:
* **Notebooks do Spark** para realizar ciência e engenharia de dados. Use o Scala, o PySpark, o C# e o Spark SQL
* **Definições de trabalho do Spark** para executar trabalhos em lote do Spark usando arquivos JAR.

## <a name="pipelines"></a>Pipelines

Os pipelines são como o Azure Synapse oferece a Integração de Dados – isso permite que você movimente dados entre serviços e orquestre atividades.

* **Pipeline** é um agrupamento lógico de atividades que executam uma tarefa juntas.
* **Atividade** define as ações em um Pipeline a serem executadas nos dados, como copiar dados, executar um Notebook ou um script SQL.
* **Fluxos de dados** são um tipo específico de atividade que fornece uma experiência sem código para fazer a transformação de dados que usa o Synapse Spark nos bastidores.
* **Gatilho** – executa um pipeline. Pode ser executado manual ou automaticamente (agenda, janela em cascata ou baseada em evento)
* **Conjunto de dados de integração** – exibição nomeada de dados que apenas apontam para os dados ou referenciam os dados a serem usados em uma atividade como entrada e saída. Pertence a um serviço vinculado.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Synapse Analytics](get-started.md)
* [Criar um workspace](quickstart-create-workspace.md)
* [Usar o pool de SQL sem servidor](quickstart-sql-on-demand.md)

