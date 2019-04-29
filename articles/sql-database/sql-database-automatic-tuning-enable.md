---
title: Habilitar o ajuste automático para o Banco de Dados SQL do Azure | Microsoft Docs
description: Habilite o ajuste automático do Banco de Dados SQL do Azure com facilidade.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: f94d731fd7b9a9fa85ae42d22949c7ca4024aabe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61416470"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Habilite o ajuste automático para monitorar consultas e melhorar o desempenho da carga de trabalho

O Banco de Dados SQL do Azure é um serviço de dados gerenciados automaticamente que monitora as consultas constantemente e identifica a ação que pode ser executada para melhorar o desempenho da carga de trabalho. Examine as recomendações e aplique-as manualmente ou permita que o Banco de Dados SQL do Azure aplique as ações corretivas automaticamente – isso é conhecido como **modo de ajuste automático**.

O ajuste automático pode ser ativado no nível do servidor ou do banco de dados por meio das chamadas do [Portal do Azure](sql-database-automatic-tuning-enable.md#azure-portal), [API REST](sql-database-automatic-tuning-enable.md#rest-api) e dos comandos [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

> [!NOTE]
> Para a instância gerenciada, a opção com suporte: FORCE_LAST_GOOD_PLAN pode ser configurada por meio [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) apenas. Portal com base em configuração e opções de ajuste do índice automático descritas neste artigo não se aplicam à instância gerenciada.

> [!NOTE]
> Configurar opções de ajuste automático por meio do modelo do ARM (Azure Resource Manager) não é suportado no momento.

## <a name="enable-automatic-tuning-on-server"></a>Habilitar o ajuste automático no servidor

No nível do servidor, você pode escolher entre herdar ou não a configuração de ajuste automático de "Padrões do Azure". Os padrões do Azure são: FORCE_LAST_GOOD_PLAN está habilitado, CREATE_INDEX está habilitado e DROP_INDEX está desabilitado.

### <a name="azure-portal"></a>Portal do Azure

Para habilitar o ajuste automático no **servidor** lógico de Banco de Dados SQL do Azure, navegue até o servidor no portal do Azure e, em seguida, selecione **Ajuste automático** no menu.

![Servidor](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Observe que a opção **DROP_INDEX** no momento não é compatível com aplicativos que usam dicas de índice e de alternância de partição e não deve ser habilitada nesses casos.
>

Selecione as opções de ajuste automático que você deseja habilitar e selecione **Aplicar**.

As opções de ajuste automático em um servidor são aplicadas a todos os bancos de dados deste servidor. Por padrão, todos os bancos de dados herdam a configuração de seu servidor pai, mas isso pode ser substituído e especificado para cada banco de dados individualmente.

### <a name="rest-api"></a>API REST

Saiba mais sobre o uso da API REST para ativar o Ajuste automático em um servidor, consulte [SQL Server, ajuste automático dos métodos UPDATE e GET HTTP](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Habilitar o ajuste automático em um banco de dados individual

O Banco de Dados SQL do Azure permite especificar a configuração de ajuste automático individualmente para cada banco de dados. No nível do banco de dados, você pode escolher entre herdar ou não a configuração de ajuste automático do servidor pai, "Padrões do Azure". Os padrões do Azure são definidos para FORCE_LAST_GOOD_PLAN está habilitado, CREATE_INDEX está habilitado e DROP_INDEX está desabilitado.

> [!TIP]
> A recomendação geral é gerenciar a configuração de ajuste automático no **nível de servidor**, de forma que as mesmas definições de configuração possam ser aplicadas em cada banco de dados automaticamente. Configure o ajuste automático em um banco de dados individual somente se você precisar que esse banco de dados tenha configurações diferentes de outras configurações herdadas do mesmo servidor.
>

### <a name="azure-portal"></a>Portal do Azure

Para habilitar o ajuste automático em um **banco de dados individual**, navegue para o banco de dados no portal do Azure e selecione **Ajuste automático**.

As configurações de ajustes automático individuais podem ser definidas separadamente para cada banco de dados. Manualmente, você pode configurar uma opção de ajuste automático individual ou especificar que uma opção herde as configurações do servidor.

![Banco de dados](./media/sql-database-automatic-tuning-enable/database.png)

Observe que a opção DROP_INDEX no momento não é compatível com aplicativos que usam dicas de índice e de alternância de partição e não deve ser habilitada nesses casos.

Depois de selecionar a configuração desejada, clique em **Aplicar**.

### <a name="rest-api"></a>API Rest

Saiba mais sobre como usar a API REST para habilitar o ajuste automático em um único banco de dados, consulte [banco de dados ajuste automático do SQL métodos UPDATE e HTTP GET](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Para habilitar o ajuste automático em um único banco de dados por meio de T-SQL, conecte-se ao banco de dados e execute a seguinte consulta:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Definir o ajuste automático como AUTO aplicará os Padrões do Azure. Definindo-o como INHERIT, a configuração de ajuste automático será herdada do servidor pai. Se escolher CUSTOM, você precisará configurar manualmente o ajuste automático.

Para configurar opções de ajuste automático individuais por meio do T-SQL, conecte-se ao banco de dados e execute a consulta de modo similar a esta:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

Definir a opção de ajuste individual como ON substituirá qualquer configuração de banco de dados herdada e habilitará a opção de ajuste. Defini-la a como OFF substituirá qualquer configuração herdada por esse banco de dados e desabilitará a opção de ajuste. Definir a opção de ajuste automático como DEFAULT fará com que a configuração seja herdada da configuração de ajuste automático do nível de banco de dados.  

> [!IMPORTANT]
> No caso da [replicação geográfica ativa](sql-database-auto-failover-group.md), o Ajuste automático precisa ser configurado somente no banco de dados primário. As ações de ajuste aplicadas automaticamente, por exemplo, criação ou exclusão de índice, serão replicadas automaticamente para o secundário somente leitura. A tentativa de habilitar o Ajuste automático por meio do T-SQL no secundário somente leitura resultará em uma falha, pois não há suporte para uma configuração de ajuste diferente no secundário somente leitura.
>

Encontre mais sobre como as opções de T-SQL para configurar o ajuste automático, confira [opções ALTER DATABASE SET (Transact-SQL) para o servidor do Banco de Dados SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Desabilitado pelo sistema

O ajuste automático está monitorando todas as ações que ele realiza no banco de dados e, em alguns casos, ele pode determinar que o ajuste automático não pode funcionar adequadamente no banco de dados. Nessa situação, a opção de ajuste será desabilitada pelo sistema. Na maioria dos casos, isso ocorre porque o Repositório de Consultas não está habilitado ou está em estado somente leitura em um banco de dados específico.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Configurar o ajuste automático das notificações por email

Ver guia [notificações por e-mail de ajuste automático](sql-database-automatic-tuning-email-notifications.md).

## <a name="next-steps"></a>Próximos passos

* Leia o [artigo Ajuste automático](sql-database-automatic-tuning.md) para saber mais sobre o ajuste automático e como ele pode ajudar você a melhorar o desempenho.
* Consulte [Recomendações de desempenho](sql-database-advisor.md) para obter uma visão geral das recomendações de desempenho do Banco de Dados SQL do Azure.
* Consulte [Análise de desempenho de consultas](sql-database-query-performance.md) para saber mais sobre como visualizar o impacto no desempenho de suas principais consultas.
