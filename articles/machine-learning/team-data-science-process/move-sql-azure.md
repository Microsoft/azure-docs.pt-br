---
title: Mover dados para um Banco de Dados SQL do Azure - Processo de Ciência de Dados de Equipe
description: Mover dados de arquivos simples (formatos CSV ou TSV) ou de dados armazenados em um SQL Server para um banco de dados SQL do Azure.
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
ms.openlocfilehash: 42bac2df7abe00be8c0e6ffddcc9bef7ef28ba9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93309531"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Mover dados para um banco de dados SQL do Azure para Azure Machine Learning

Este artigo descreve as opções para mover dados de arquivos simples (formatos CSV ou TSV) ou de dados armazenados no SQL Server para um banco de dado SQL do Azure. Essas tarefas para movimentar dados para a nuvem fazem parte do Processo de Ciência de Dados de Equipe.

Para obter um tópico que descreve as opções para mover dados para SQL Server para Machine Learning, consulte [mover dados para SQL Server em uma máquina virtual do Azure](move-sql-server-virtual-machine.md).

A tabela a seguir resume as opções para mover dados para um Banco de Dados SQL do Azure.

| <b>ORIGINAL</b> | <b>DESTINO: Banco de Dados SQL do Azure</b> |
| --- | --- |
| <b>Arquivo simples (CSV ou TSV formatado)</b> |[Consulta SQL de inserção em massa](#bulk-insert-sql-query) |
| <b>SQL Server local</b> |1.[Exportar para arquivo simples](#export-flat-file)<br> 2. [Assistente de migração de banco de dados SQL](#insert-tables-bcp)<br> 3. [backup e restauração de banco de dados](#db-migration)<br> 4. [Azure data Factory](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>Pré-requisitos
O procedimento descrito aqui requer que você tenha:

* Uma **assinatura do Azure**. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento do Azure**. Você usará uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md) . Depois de criar a conta de armazenamento, você precisa obter a chave de conta usada para acessar o armazenamento. Consulte [gerenciar chaves de acesso da conta de armazenamento](../../storage/common/storage-account-keys-manage.md).
* Acesso a um **Banco de dados SQL do Azure**. Se você precisa configurar um Banco de Dados SQL do Azure, o [Guia de Introdução ao Banco de Dados SQL do Microsoft Azure](../../azure-sql/database/single-database-create-quickstart.md) fornece informações sobre como provisionar uma nova instância de um Banco de Dados SQL do Azure.
* **Azure PowerShell** instalado e configurado localmente. Para saber mais, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/).

**Dados**: os processos de migração são demonstrados usando o [Conjunto de Dados de Táxi de NYC](https://chriswhong.com/open-data/foil_nyc_taxi/). O conjunto de dados de Táxis de NYC contém informações sobre dados de viagem e feiras e está disponível no armazenamento de blobs do Azure: [dados de táxi de NYC](https://www.andresmh.com/nyctaxitrips/). Um exemplo e uma descrição desses arquivos são fornecidos na [Descrição do Conjunto de Dados de Viagens de Táxi de NYC](sql-walkthrough.md#dataset).

Você pode adaptar os procedimentos descritos aqui para um conjunto de seus próprios dados ou seguir as etapas conforme descrito usando o conjunto de dados de Táxi de NYC. Para carregar o conjunto de dados de táxi de NYC em seu banco de dados SQL Server, siga o procedimento descrito em [importação em massa para o banco de SQL Server](sql-walkthrough.md#dbload).

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a> Movendo dados de uma fonte de arquivo simples para um banco de dados SQL do Azure
Os dados em arquivos simples (CSV ou TSV formatado) podem ser movidos para um banco de dado SQL do Azure usando uma consulta SQL de inserção em massa.

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a> Consulta SQL de inserção em massa
As etapas para o procedimento usando a consulta SQL de inserção em massa são semelhantes às instruções para mover dados de uma fonte de arquivo simples para SQL Server em uma VM do Azure. Para obter detalhes, consulte [Consulta SQL de inserção em massa](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="moving-data-from-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a> Movendo dados de SQL Server para um banco de dado SQL do Azure
Se os dados de origem forem armazenados em SQL Server, haverá várias possibilidades para mover os dados para um banco de dado SQL do Azure:

1. [Exportar para arquivo simples](#export-flat-file)
2. [Assistente de migração do banco de dados SQL](#insert-tables-bcp)
3. [Backup e restauração de banco de dados](#db-migration)
4. [Azure Data Factory](#adf)

As etapas para os três primeiros são semelhantes às seções em [mover dados para SQL Server em uma máquina virtual do Azure](move-sql-server-virtual-machine.md) que abrange esses mesmos procedimentos. Os links para as seções apropriadas deste tópico são fornecidos nas instruções a seguir.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportar para Arquivo simples
As etapas para exportar para um arquivo simples são semelhantes às instruções abordadas em [exportar para arquivo simples](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>Assistente de migração de Banco de Dados SQL
As etapas para usar o SQL Database Migration Wizard são semelhantes às instruções abordadas no [SQL Database Migration Wizard](move-sql-server-virtual-machine.md#sql-migration).

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>Backup e restauração do banco de dados
As etapas para usar o backup e a restauração de banco de dados são semelhantes às instruções listadas em [backup e restauração de banco de dados](move-sql-server-virtual-machine.md#sql-backup).

### <a name="azure-data-factory"></a><a name="adf"></a>Azure Data Factory (ADF)
Saiba como mover dados para um banco de dado SQL do Azure com Azure Data Factory (ADF) neste tópico, [mover dados de um SQL Server para SQL Azure com Azure data Factory](move-sql-azure-adf.md). Este tópico mostra como usar o ADF para mover dados de um banco de dado SQL Server para um banco de dados SQL do Azure por meio do armazenamento de BLOBs do Azure.

Considere o uso do ADF quando os dados precisarem ser migrados continuamente com fontes híbridas locais e na nuvem.  O ADF também ajuda quando os dados precisam de transformações ou precisa de uma nova lógica de negócios durante a migração. O ADF permite o planejamento e monitoramento de trabalhos usando scripts simples de JSON que gerenciam a movimentação de dados em intervalos periódicos. O ADF também possui outros recursos, como suporte para operações complexas.