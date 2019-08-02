---
title: Início Rápido – bancos de dados individuais no Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como se familiarizar rapidamente com os bancos de dados individuais no Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 7b52453bab661531461a2bec2f15f7659ec15a1c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441713"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Introdução aos bancos de dados individuais no Banco de Dados SQL do Azure

[Um banco de dados individual](sql-database-single-index.yml) é um DBaaS (banco de dados como serviço) de PaaS totalmente gerenciado que é o mecanismo de armazenamento ideal para aplicativos modernos criados na nuvem. Nesta seção, você aprenderá a configurar e criar rapidamente um banco de dados individual no Banco de Dados SQL.

## <a name="quickstart-overview"></a>Visão geral do início rápido

Nesta seção, você terá uma visão geral dos artigos disponíveis que podem ajudá-lo a se familiarizar rapidamente com os bancos de dados individuais. Os seguintes Guias de Início Rápido permitem criar rapidamente um banco de dados individual, configurar uma regra de firewall do servidor de banco de dados e, em seguida, importar um banco de dados para o novo banco de dados individual usando um arquivo `.bacpac`:

- [Criar um banco de dados individual usando o portal do Azure](sql-database-single-database-get-started.md).
- Após a criação do banco de dados, você precisará [proteger seu banco de dados configurando regras de firewall](sql-database-server-level-firewall-rule.md).
- Caso você tenha um banco de dados no SQL Server que deseja migrar para o Azure, instale o [AMD (Assistente de Migração de Dados),](https://www.microsoft.com/download/details.aspx?id=53595) que analisará seus bancos de dados no SQL Server e encontrará qualquer problema que possa bloquear a migração para a opção de implantação de banco de dados individual. Se não encontrar nenhum problema, você poderá exportar seu banco de dados como um arquivo `.bacpac` e [importá-lo usando o portal do Azure ou o SqlPackage](sql-database-import.md).

## <a name="automating-management-operations"></a>Automação das operações de gerenciamento

Use o PowerShell ou a CLI do Azure para criar, configurar e dimensionar seu banco de dados.

- [Criar e configurar um banco de dados individual usando o PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)
- [Criar e configurar um banco de dados individual usando a CLI do Azure](scripts/sql-database-create-and-configure-database-cli.md)
- [Atualizar seu banco de dados individual e dimensionar os recursos usando o PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)
- [Atualizar seu banco de dados individual e dimensionar os recursos usando a CLI do Azure](scripts/sql-database-monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrando para um banco de dados individual com tempo de inatividade mínimo

Esses Guias de Início Rápido permitem criar ou importar rapidamente seu banco de dados para o Azure usando um arquivo `.bacpac`. No entanto, os arquivos `.bacpac` e `.dacpac` foram projetados para mover bancos de dados rapidamente entre diferentes versões do SQL Server e opções de implantação no Banco de Dados SQL do Azure ou para implementar a integração contínua no pipeline de DevOps. No entanto, esse método não foi projetado para a migração de bancos de dados de produção com tempo de inatividade mínimo, pois você precisará parar de adicionar novos dados, aguardar a conclusão da exportação do banco de dados de origem para um arquivo `.bacpac` e, em seguida, aguardar a conclusão da importação para o Banco de Dados SQL do Azure. Toda essa espera resulta em tempo de inatividade do aplicativo, especialmente para bancos de dados grandes. Para mover o banco de dados de produção, você precisa de uma maneira melhor para migrá-lo que garanta tempo de inatividade mínimo. Para isso, use o [DMS (Serviço de Migração de Banco de Dados)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) para migrar seu banco de dados com tempo de inatividade mínimo. O DMS faz isso enviando por push as alterações feitas no banco de dados de origem em incrementos para o banco de dados individual que está sendo restaurado. Dessa forma, você pode alternar rapidamente seu aplicativo do banco de dados de origem para o de destino com tempo de inatividade mínimo.

## <a name="next-steps"></a>Próximas etapas

- Localizar uma [lista de alto nível dos recursos compatíveis no Banco de Dados SQL do Azure](sql-database-features.md).
- Saiba como [tornar seu banco de dados mais seguro](sql-database-security-tutorial.md).
- Encontre instruções mais avançadas em [Como usar um banco de dados individual no Banco de Dados SQL do Azure](sql-database-howto-single-database.md).
- Encontre mais exemplos de scripts escritos no [PowerShell](sql-database-powershell-samples.md) e na [CLI do Azure](sql-database-cli-samples.md).
- Saiba mais sobre a [API de Gerenciamento](sql-database-single-databases-manage.md) que pode ser usada para configurar os bancos de dados.
- [Identificar o Banco de dados SQL/SKU de instância gerenciada correto do Azure para seu banco de dados local](/sql/dma/dma-sku-recommend-sql-db/).