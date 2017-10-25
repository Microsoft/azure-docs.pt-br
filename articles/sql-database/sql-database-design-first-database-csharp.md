---
title: Criar seu primeiro banco de dados SQL do Azure - C# | Microsoft Docs
description: "Aprenda a criar seu primeiro banco de dados SQL do Azure e conectá-lo a um programa em C# usando o ADO.NET."
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg-msft
editor: CarlRabeler
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: develop databases, mvc
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 08/25/2017
ms.author: genemi
ms.openlocfilehash: 1deffdd05c195597f4c63e3b5570ed94566b1f7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Criar um banco de dados SQL do Azure e conectar-se com C&#x23 e o ADO.NET

O Banco de Dados SQL do Azure é um DBaaS (banco de dados como serviço) no Microsoft Cloud (“Azure”). Neste tutorial, você aprenderá a usar o portal do Azure e o ADO.NET com o Visual Studio para: 

> [!div class="checklist"]
> * Criar um banco de dados no Portal do Azure
> * Configurar uma regra de firewall de nível de servidor no Portal do Azure
> * Conecte-se ao banco de dados com o ADO.NET e o Visual Studio
> * Criar tabelas com o ADO.NET
> * Inserir, atualizar e excluir dados com o ADO.NET 
> * Consultar ADO.NET de dados

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Uma instalação do [Visual Studio Community 2017, Visual Studio Professional 2017 ou Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/).

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu as tarefas básicas de banco de dados, como criar um banco de dados e tabelas, carregar e consultar dados e restaurar o banco de dados para um ponto anterior no tempo. Você aprendeu como:
> [!div class="checklist"]
> * Criar um banco de dados
> * Configurar uma regra de firewall
> * Conectar-se ao banco de dados com o [Visual Studio e C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Criar tabelas
> * Inserir, atualizar e excluir dados
> * Consultar dados

Avance para o próximo tutorial para saber mais sobre como migrar seus dados.

> [!div class="nextstepaction"]
>[Migrar seu banco de dados do SQL Server para o banco de dados SQL do Azure](sql-database-migrate-your-sql-server-database.md)

