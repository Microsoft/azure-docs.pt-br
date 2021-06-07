---
title: Referência de conteúdo do início rápido do banco de dados individual
description: Encontre uma referência de conteúdo de todos os guias de início rápido que vão ajudar você a começar a usar rapidamente bancos de dados individuais no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 392f361f6fe487a15bdd63c2f3281ccc78b2f86d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184409"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Introdução aos bancos de dados individuais no Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Um banco de dados individual](../index.yml) é um DbaaS (banco de dados como serviço) de PaaS (plataforma como serviço) totalmente gerenciado que é o mecanismo de armazenamento ideal para aplicativos modernos criados na nuvem. Nesta seção, você aprenderá a configurar e criar rapidamente um banco de dados individual no Banco de Dados SQL do Azure.

## <a name="quickstart-overview"></a>Visão geral do início rápido

Nesta seção, haverá uma visão geral dos artigos disponíveis que podem ajudar você a se familiarizar rapidamente com os bancos de dados individuais. Os seguintes guias de início rápido permitem criar rapidamente um banco de dados individual, configurar uma regra de firewall no nível do servidor e importar um banco de dados para o novo banco de dados individual usando um arquivo `.bacpac`:

- [Criar um banco de dados individual usando o portal do Azure](single-database-create-quickstart.md).
- Após a criação do banco de dados, você precisará [proteger seu banco de dados configurando regras de firewall](firewall-create-server-level-portal-quickstart.md).
- Caso tenha um banco de dados existente no SQL Server que deseja migrar para o Banco de Dados SQL do Azure, instale o [AMD (Assistente de Migração de Dados),](https://www.microsoft.com/download/details.aspx?id=53595) que analisará seus bancos de dados no SQL Server e encontrará qualquer problema que possa bloquear a migração. Se não encontrar nenhum problema, você poderá exportar seu banco de dados como um arquivo `.bacpac` e [importá-lo usando o portal do Azure ou o SqlPackage](database-import.md).


## <a name="automating-management-operations"></a>Automação das operações de gerenciamento

Use o PowerShell ou a CLI do Azure para criar, configurar e dimensionar seu banco de dados.

- [Criar e configurar um banco de dados individual usando o PowerShell](scripts/create-and-configure-database-powershell.md) ou a [CLI do Azure](scripts/create-and-configure-database-cli.md)
- [Atualizar o banco de dados individual e escalar os recursos usando o PowerShell](scripts/monitor-and-scale-database-powershell.md) ou a [CLI do Azure](scripts/monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrando para um banco de dados individual com tempo de inatividade mínimo

Esses Guias de Início Rápido permitem criar ou importar rapidamente seu banco de dados para o Azure usando um arquivo `.bacpac`. No entanto, os arquivos `.bacpac` e `.dacpac` foram projetados para mover bancos de dados rapidamente entre diferentes versões do SQL Server e no SQL do Azure ou implementar a integração contínua no pipeline de DevOps. No entanto, esse método não foi projetado para a migração de bancos de dados de produção com tempo de inatividade mínimo, pois você precisará parar de adicionar novos dados, aguardar a conclusão da exportação do banco de dados de origem para um arquivo `.bacpac` e, em seguida, aguardar a conclusão da importação para o Banco de Dados SQL do Azure. Toda essa espera resulta em tempo de inatividade do aplicativo, especialmente para bancos de dados grandes. Para mover o banco de dados de produção, você precisa de uma maneira melhor para migrá-lo que garanta tempo de inatividade mínimo. Para isso, use o [DMS (Serviço de Migração de Banco de Dados)](../../dms/tutorial-sql-server-to-azure-sql.md?toc=%2fazure%2fsql-database%2ftoc.json) para migrar seu banco de dados com tempo de inatividade mínimo. O DMS faz isso enviando por push as alterações feitas no banco de dados de origem em incrementos para o banco de dados individual que está sendo restaurado. Dessa forma, você pode alternar rapidamente seu aplicativo do banco de dados de origem para o de destino com tempo de inatividade mínimo.

## <a name="hands-on-learning-modules"></a>Módulos de aprendizado prático

Os módulos do Microsoft Learn a seguir ajudam você a aprender gratuitamente sobre o Banco de Dados SQL do Azure.

- [Provisionar um banco de dados no Banco de Dados SQL para armazenar dados de aplicativo](/learn/modules/provision-azure-sql-db/)
- [Desenvolver e configurar um aplicativo ASP.NET que consulta um banco de dados no Banco de Dados SQL do Azure](/learn/modules/develop-app-that-queries-azure-sql/)
- [Proteger seu banco de dados no Banco de Dados SQL do Azure](/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Próximas etapas

- Localizar uma [lista de alto nível dos recursos compatíveis no Banco de Dados SQL do Azure](features-comparison.md).
- Saiba como [tornar seu banco de dados mais seguro](secure-database-tutorial.md).
- Encontre instruções mais avançadas em [Como usar um banco de dados individual no Banco de Dados SQL do Azure](how-to-content-reference-guide.md).
- Encontre mais exemplos de scripts escritos no [PowerShell](powershell-script-content-guide.md) e na [CLI do Azure](az-cli-script-samples-content-guide.md).
- Saiba mais sobre a [API de Gerenciamento](single-database-manage.md) que pode ser usada para configurar os bancos de dados.
- [Identificar o Banco de Dados SQL do Azure ou o SKU da Instância Gerenciada de SQL do Azure correto para o seu banco de dados local](/sql/dma/dma-sku-recommend-sql-db/).