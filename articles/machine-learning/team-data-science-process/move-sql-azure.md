---
title: Mover dados para um Banco de Dados SQL do para o Azure Machine Learning | Microsoft Docs
description: Criar tabela SQL e carregar dados para a tabela SQL
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 50f8b862-4d32-44b2-a1e2-4fbc8024acaa
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: bradsev
ms.openlocfilehash: 2d0c36990ba897bef83601bd4295518618224733
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Mover dados para um banco de dados do SQL Azure para Azure Machine Learning
Este tópico descreve as opções para movimentação de dados de arquivos simples (formatos CSV ou TSV) ou de dados armazenados em um SQL Server local para um Banco de Dados SQL do Azure. Essas tarefas para movimentar dados para a nuvem fazem parte do Processo de Ciência de Dados de Equipe.

Para um tópico que descreve as opções para mover dados para um SQL Server local para o Machine Learning, consulte [Mover dados para o SQL Server em uma máquina virtual do Azure](move-sql-server-virtual-machine.md).

O **menu** a seguir leva a tópicos que descrevem com ingerir dados em ambientes de destino em que os dados podem ser armazenados e processados durante o TDSP (Processo de Ciência de Dados de Equipe).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

A tabela a seguir resume as opções para mover dados para um Banco de Dados SQL do Azure.

| <b>FONTE</b> | <b>DESTINO: Banco de Dados SQL do Azure</b> |
| --- | --- |
| <b>Arquivo simples (CSV ou TSV formatado)</b> |<a href="#bulk-insert-sql-query">Consulta SQL de inserção em massa |
| <b>SQL Server local</b> |1. <a href="#export-flat-file">Exportar para Arquivo simples<br> 2. <a href="#insert-tables-bcp">Assistente de migração de Banco de Dados SQL<br> 3. <a href="#db-migration">Backup e restauração do banco de dados<br> 4. <a href="#adf">Azure Data Factory (ADF) |

## <a name="prereqs"></a>Pré-requisitos
O procedimento descrito aqui requer que você tenha:

* Uma **assinatura do Azure**. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento do Azure**. Você usará uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account) . Depois de criar a conta de armazenamento, você precisa obter a chave de conta usada para acessar o armazenamento. Consulte [Manage your storage access keys (Gerenciar as chaves de acesso de armazenamento)](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Acesso a um **Banco de dados do SQL Azure**. Se você precisa configurar um Banco de Dados SQL do Azure, o [Guia de Introdução ao Banco de Dados SQL do Microsoft Azure](../../sql-database/sql-database-get-started.md) fornece informações sobre como provisionar uma nova instância de um Banco de Dados SQL do Azure.
* **Azure PowerShell** instalado e configurado localmente. Para saber mais, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

**Dados**: os processos de migração são demonstrados usando o [Conjunto de Dados de Táxi de NYC](http://chriswhong.com/open-data/foil_nyc_taxi/). O conjunto de dados de Táxis de NYC contém informações sobre dados de viagem e feiras e está disponível no armazenamento de blobs do Azure: [dados de táxi de NYC](http://www.andresmh.com/nyctaxitrips/). Um exemplo e uma descrição desses arquivos são fornecidos na [Descrição do Conjunto de Dados de Viagens de Táxi de NYC](sql-walkthrough.md#dataset).

Você pode adaptar os procedimentos descritos aqui para um conjunto de seus próprios dados ou seguir as etapas conforme descrito usando o conjunto de dados de Táxi de NYC. Para carregar o conjunto de dados de Táxi de NYC em seu banco de dados do SQL Server local, siga o procedimento descrito em [Importação de dados em massa para o Banco de Dados do SQL Server](sql-walkthrough.md#dbload). Essas instruções são para um SQL Server em uma máquina virtual do Azure, mas o procedimento para carregar o SQL Server local é o mesmo.

## <a name="file-to-azure-sql-database"></a> Movendo dados de uma fonte de arquivo simples para um Banco de Dados SQL do Azure
Dados em arquivos simples (CSV ou TSV formatado) podem ser movidos para um banco de dados do SQL Azure usando uma consulta SQL de inserção em massa.

### <a name="bulk-insert-sql-query"></a> Consulta SQL de inserção em massa
As etapas para o procedimento usando a consulta SQL de inserção em massa são semelhantes àquelas abordados nas seções para mover dados de uma fonte de arquivo simples para o SQL Server em uma VM do Azure. Para obter detalhes, consulte [Consulta SQL de inserção em massa](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a> Movendo dados do SQL Server local para um Banco de Dados SQL do Azure
Se os dados de origem forem armazenados em um SQL Server local, existem várias possibilidades para mover os dados para um Banco de Dados SQL do Azure:

1. [Exportar para arquivo simples](#export-flat-file)
2. [Assistente de Migração de Banco de Dados SQL](#insert-tables-bcp)
3. [Backup e restauração de banco de dados](#db-migration)
4. [Azure Data Factory](#adf)

As etapas para as três primeiras são muito semelhantes às seções em [Mover dados para o SQL Server em uma máquina virtual do Azure](move-sql-server-virtual-machine.md) que abordam esses mesmos procedimentos. Os links para as seções apropriadas deste tópico são fornecidos nas instruções a seguir.

### <a name="export-flat-file"></a>Exportar para arquivo simples
As etapas para essa exportação para um arquivo simples são semelhantes àquelas tratadas em [Exportar para arquivo simples](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Assistente de Migração de Banco de Dados SQL
As etapas para usar o Assistente de Migração de Banco de Dados SQL são semelhantes àquelas tratadas no [Assistente de Migração de Banco de Dados SQL](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Backup e restauração de banco de dados
As etapas para usar o backup e restauração de banco de dados são semelhantes àquelas tratadas em [Backup e restauração de banco de dados](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Azure Data Factory
O procedimento para mover dados para um Banco de Dados SQL do Azure com o ADF (Azure Data Factory) é fornecido no tópico [Mover dados de um SQL Server local para o SQL Azure com o Azure Data Factory](move-sql-azure-adf.md). Este tópico mostra como mover dados de um banco de dados do SQL Server local para um Banco de Dados SQL do Azure por meio do Armazenamento de Blobs do Azure usando o ADF.

Considere usar o ADF quando os dados precisarem ser migrados continuamente em um cenário híbrido que acessa os recursos locais e de nuvem e quando os dados forem transacionados ou precisarem ser modificados ou tiverem lógica de negócios adicionada a eles durante a migração. O ADF permite o planejamento e monitoramento de trabalhos usando scripts simples de JSON que gerenciam a movimentação de dados em intervalos periódicos. O ADF também possui outros recursos, como suporte para operações complexas.
