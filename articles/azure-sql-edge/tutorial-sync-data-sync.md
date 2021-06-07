---
title: Sincronizar dados do SQL do Azure no Edge usando a Sincronização de Dados SQL
description: Saiba mais sobre a sincronização de dados do SQL do Azure no Edge usando a Sincronização de Dados SQL do Azure
keywords: SQL do Azure no Edge, sincronizar dados do SQL do Azure no Edge, sincronização de dados do SQL do Azure no Edge
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5659ce5fa6f99463f58a33662563d768248fd8cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93394893"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Tutorial: Sincronizar dados do SQL do Azure no Edge para o Banco de Dados SQL do Azure usando a Sincronização de Dados SQL

Neste tutorial, você aprenderá a usar o *grupo de sincronização* da Sincronização de Dados SQL do Azure para sincronizar de forma incremental dados do SQL do Azure no Edge para o Banco de Dados SQL do Azure. Sincronização de Dados SQL é um serviço baseado no Banco de Dados SQL do Azure que lhe permite sincronizar os dados que você selecionar bidirecionalmente em vários bancos de dados no Banco de Dados SQL do Azure e em instâncias do SQL Server. Para obter mais informações sobre a Sincronização de Dados SQL, confira [Sincronização de Dados SQL do Azure](../azure-sql/database/sql-data-sync-data-sql-server-sql-database.md).

Já que o SQL do Azure no Edge é criado nas versões mais recentes do [Mecanismo de Banco de Dados do SQL Server](/sql/sql-server/sql-server-technical-documentation/), qualquer mecanismo de sincronização de dados aplicável a uma instância do SQL Server também pode ser usado para sincronizar dados de ou para uma instância do SQL do Azure no Edge em execução em um dispositivo de borda.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer um computador Windows configurado com o [Data Sync Agent para a Sincronização de Dados SQL do Azure](../azure-sql/database/sql-data-sync-agent-overview.md).

## <a name="before-you-begin"></a>Antes de começar

* Crie um banco de dados no Banco de Dados SQL do Azure. Para saber como criar um banco de dados usando o portal do Azure, confira [Criar um banco de dados individual no Banco de Dados SQL do Azure](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

* Crie as tabelas e outros objetos necessários na implantação do Banco de Dados SQL do Azure.

* Crie as tabelas e objetos necessários na implantação do SQL do Azure no Edge. Para saber mais, confira [Como usar pacotes de DAC do Banco de Dados SQL com o SQL do Azure no Edge](deploy-dacpac.md).

* Registre a instância do SQL do Azure no Edge com o Data Sync Agent para a Sincronização de Dados SQL do Azure. Para obter mais informações, confira [Adicionar um banco de dados do SQL Server](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-on-prem).

## <a name="sync-data-between-a-database-in-azure-sql-database-and-sql-edge"></a>Sincronizar dados entre um banco de dados no Banco de Dados SQL do Azure e o SQL no Edge

A configuração da sincronização entre um banco de dados no Banco de Dados SQL do Azure e uma instância do SQL no Edge usando a Sincronização de Dados SQL envolve três etapas fundamentais:  


1. Use o portal do Azure para criar um grupo de sincronização. Para saber mais, confira [Criar um grupo de sincronização](../azure-sql/database/sql-data-sync-sql-server-configure.md#create-sync-group). Você pode usar um banco de dados de *hub* para criar vários grupos de sincronização para sincronizar dados de instâncias diferentes do SQL no Edge para um ou mais bancos de dados no Banco de Dados SQL do Azure. 

2. Adicione membros de sincronização ao grupo de sincronização. Para obter mais informações, confira [Adicionar membros de sincronização](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

3. Configure o grupo de sincronização para escolher as tabelas que farão parte da sincronização. Para obter mais informações, confira [Configurar um grupo de sincronização](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

Depois de concluir as etapas acima, você terá um grupo de sincronização que incluirá um banco de dados no Banco de Dados SQL do Azure e uma instância do SQL no Edge.

Para saber mais informações sobre a Sincronização de Dados SQL, confira estes artigos:

* [Agente de Sincronização de Dados para a Sincronização de Dados SQL do Azure](../azure-sql/database/sql-data-sync-agent-overview.md)

* [Práticas recomendadas](../azure-sql/database/sql-data-sync-best-practices.md) e [Como solucionar problemas comuns com a Sincronização de Dados SQL do Azure](../azure-sql/database/sql-data-sync-troubleshoot.md)

* [Monitorar a Sincronização de Dados SQL com logs do Azure Monitor](../azure-sql/database/monitor-tune-overview.md)

* [Atualizar o esquema de sincronização com o Transact-SQL](../azure-sql/database/sql-data-sync-update-sync-schema.md) ou o [PowerShell](../azure-sql/database/scripts/update-sync-schema-in-sync-group.md)

## <a name="next-steps"></a>Próximas etapas


* [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um SQL do Azure no Edge](../azure-sql/database/scripts/sql-data-sync-sync-data-between-azure-onprem.md). Neste tutorial, substitua os detalhes do banco de dados `OnPremiseServer` pelos detalhes do SQL do Azure no Edge.