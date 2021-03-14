---
title: Habilitar o ajuste automático
description: Você pode habilitar o ajuste automático em seu banco de dados facilmente usando o portal do Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/03/2021
ms.openlocfilehash: d60810c291984e0f57df1968f69678de8179273c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042514"
---
# <a name="enable-automatic-tuning-in-the-azure-portal-to-monitor-queries-and-improve-workload-performance"></a>Habilitar o ajuste automático no portal do Azure para monitorar consultas e melhorar o desempenho da carga de trabalho
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O banco de dados SQL do Azure gerencia automaticamente os serviços do data que monitoram constantemente suas consultas e identifica a ação que você pode executar para melhorar o desempenho da carga de trabalho. Examine as recomendações e aplique-as manualmente ou permita que o Banco de Dados SQL do Azure aplique as ações corretivas automaticamente – isso é conhecido como **modo de ajuste automático**.

O ajuste automático pode ser habilitado no nível do servidor ou do banco de dados por meio de:

- O [Portal do Azure](automatic-tuning-enable.md#azure-portal)
- Chamadas à [API REST](automatic-tuning-enable.md#rest-api)
- Comandos [T-SQL](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true)

> [!NOTE]
> Para o Azure SQL Instância Gerenciada, a opção com suporte FORCE_LAST_GOOD_PLAN só pode ser configurada por meio [de T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) . A configuração baseada em portal do Azure e as opções de ajuste de índice automático descritas neste artigo não se aplicam ao Azure SQL Instância Gerenciada.

> [!NOTE]
> Não há suporte para a configuração de opções de ajuste automático por meio do modelo ARM (Azure Resource Manager) no momento.

## <a name="enable-automatic-tuning-on-server"></a>Habilitar o ajuste automático no servidor

No nível do servidor, você pode escolher entre herdar ou não a configuração de ajuste automático de "Padrões do Azure". Os padrões do Azure são FORCE_LAST_GOOD_PLAN estão habilitados, o CREATE_INDEX está desabilitado e DROP_INDEX está desabilitado.

> [!IMPORTANT]
> A partir de março, 2020 novos padrões do Azure para ajuste automático são os seguintes:
>
> - FORCE_LAST_GOOD_PLAN = habilitado, CREATE_INDEX = desabilitado e DROP_INDEX = desabilitado.
> - Os servidores existentes sem preferências de ajuste automático configuradas são automaticamente configurados para HERDAr os padrões do Azure. Isso se aplica a todos os clientes que atualmente têm configurações de servidor para ajuste automático em um estado indefinido.
> - Novos servidores criados serão automaticamente configurados para HERDAr os padrões do Azure (diferentemente de antes quando a configuração de ajuste automático estava em um estado indefinido na criação do novo servidor).

### <a name="azure-portal"></a>Portal do Azure

Para habilitar o ajuste automático em um [servidor](logical-servers.md) no banco de dados SQL do Azure, navegue até o servidor na portal do Azure e, em seguida, selecione **ajuste automático** no menu.

![A captura de tela mostra o ajuste automático no portal do Azure, onde você pode aplicar opções para um servidor.](./media/automatic-tuning-enable/server.png)

> [!NOTE]
> Observe que a opção **DROP_INDEX** no momento não é compatível com aplicativos que usam a alternância de partição e dicas de índice e não deve ser habilitada nesses casos. Não há suporte para a remoção de índices não utilizados para as camadas de serviço Premium e Comercialmente Crítico.

Selecione as opções de ajuste automático que você deseja habilitar e selecione **aplicar**.

As opções de ajuste automático em um servidor são aplicadas a todos os bancos de dados deste servidor. Por padrão, todos os bancos de dados herdam a configuração de seu servidor pai, mas isso pode ser substituído e especificado para cada banco de dados individualmente.

### <a name="rest-api"></a>API REST

Para saber mais sobre como usar uma API REST para habilitar o ajuste automático em um **servidor**, consulte [atualização de ajuste automático do servidor e obter métodos http](/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Habilitar o ajuste automático em um banco de dados individual

O banco de dados SQL do Azure permite especificar individualmente a configuração de ajuste automático para cada banco de dados. No nível do banco de dados, você pode escolher entre herdar ou não a configuração de ajuste automático do servidor pai, "Padrões do Azure". Os padrões do Azure são definidos como FORCE_LAST_GOOD_PLAN está habilitado, CREATE_INDEX está desabilitado e DROP_INDEX está desabilitado.

> [!TIP]
> A recomendação geral é gerenciar a configuração de ajuste automático no **nível do servidor** para que as mesmas definições de configuração possam ser aplicadas automaticamente em cada banco de dados. Configure o ajuste automático em um banco de dados individual somente se você precisar que esse banco de dados tenha configurações diferentes de outras configurações herdadas do mesmo servidor.

### <a name="azure-portal"></a>Portal do Azure

Para habilitar o ajuste automático em um **único banco de dados**, navegue até o banco de dados no portal do Azure e selecione **ajuste automático**.

As configurações de ajustes automático individuais podem ser definidas separadamente para cada banco de dados. Manualmente, você pode configurar uma opção de ajuste automático individual ou especificar que uma opção herde as configurações do servidor.

![A captura de tela mostra o ajuste automático no portal do Azure, onde você pode aplicar opções para um único banco de dados.](./media/automatic-tuning-enable/database.png)

Observe que a opção DROP_INDEX no momento não é compatível com aplicativos que usam dicas de índice e de alternância de partição e não deve ser habilitada nesses casos.

Depois de selecionar a configuração desejada, clique em **Aplicar**.

### <a name="rest-api"></a>API Rest

Para saber mais sobre como usar uma API REST para habilitar o ajuste automático em um único banco de dados, consulte [atualização de ajuste automático do banco de dados SQL do Azure e obter métodos http](/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Para habilitar o ajuste automático em um único banco de dados por meio de T-SQL, conecte-se ao banco de dados e execute a seguinte consulta:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Definir o ajuste automático como AUTO aplicará os Padrões do Azure. Definindo-o como INHERIT, a configuração de ajuste automático será herdada do servidor pai. Se escolher CUSTOM, você precisará configurar manualmente o ajuste automático.

Para configurar opções de ajuste automático individuais por meio do T-SQL, conecte-se ao banco de dados e execute a consulta de modo similar a esta:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = ON, DROP_INDEX = OFF)
```

Definir a opção de ajuste individual como ON substituirá qualquer configuração que o banco de dados herdou e habilitará a opção de ajuste. Configurá-lo como OFF substituirá também qualquer configuração herdada pelo banco de dados e desabilitará a opção de ajuste. A opção de ajuste automático, para a qual o padrão é especificado, herdará a configuração de ajuste automático das configurações de nível de servidor.  

> [!IMPORTANT]
> No caso da [replicação geográfica ativa](auto-failover-group-overview.md), o ajuste automático precisa ser configurado somente no banco de dados primário. Automaticamente as ações de ajuste aplicadas, como por exemplo, criar ou excluir índice serão replicadas automaticamente para o secundário somente leitura. A tentativa de habilitar o Ajuste automático por meio do T-SQL no secundário somente leitura resultará em uma falha, pois não há suporte para uma configuração de ajuste diferente no secundário somente leitura.
>

Para obter mais opções do T-SQL está confinado no para configurar o ajuste automático, consulte [Opções ALTER DATABASE SET (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true).

## <a name="troubleshooting"></a>Solução de problemas

### <a name="automated-recommendation-management-is-disabled"></a>O gerenciamento de recomendação automatizado está desabilitado

No caso de mensagens de erro que o gerenciamento de recomendação automatizado foi desabilitado ou simplesmente desabilitado pelo sistema, as causas mais comuns são:
- O Repositório de Consultas não está habilitado ou
- Repositório de Consultas está no modo somente leitura para um banco de dados especificado ou
- Repositório de Consultas parou de ser executada porque usou o espaço de armazenamento alocado.

As etapas a seguir podem ser consideradas para corrigir esse problema:
- Limpe o Repositório de Consultas ou modifique o período de retenção de dados para "auto" usando o T-SQL. Consulte como [Configurar a política de retenção e captura recomendada para repositório de consultas](/azure/azure-sql/database/query-performance-insight-use#recommended-retention-and-capture-policy).
- Use o SQL Server Management Studio (SSMS) e siga estas etapas:
  - Conectar-se ao banco de dados SQL do Azure
  - Clique com o botão direito do mouse no banco de dados
  - Vá para propriedades e clique em Repositório de Consultas
  - Altere o modo de operação para Read-Write
  - Alterar o modo de captura de armazenamento para automático
  - Alterar o modo de limpeza baseado em tamanho para automático

### <a name="permissions"></a>Permissões

Como o ajuste automático é um recurso do Azure, para usá-lo, será necessário usar as funções internas do Azure. Usar a autenticação do SQL somente não será suficiente para usar o recurso do portal do Azure.

Para usar o ajuste automático, a permissão mínima necessária para conceder ao usuário é a função [colaborador do banco de dados SQL](../../role-based-access-control/built-in-roles.md#sql-db-contributor) interna do Azure. Você também pode considerar o uso de funções de privilégio mais altas, como colaborador de SQL Server, SQL Instância Gerenciada colaborador, colaborador e proprietário.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Configurar o ajuste automático das notificações por email

Para receber notificações de email automatizadas sobre recomendações feitas pelo ajuste automático, consulte o guia de [notificações por email de ajuste automático](automatic-tuning-email-notifications-configure.md) .

## <a name="next-steps"></a>Próximas etapas

- Leia o [artigo Ajuste automático](automatic-tuning-overview.md) para saber mais sobre o ajuste automático e como ele pode ajudar você a melhorar o desempenho.
- Consulte [Recomendações de desempenho](database-advisor-implement-performance-recommendations.md) para obter uma visão geral das recomendações de desempenho do Banco de Dados SQL do Azure.
- Consulte [Análise de desempenho de consultas](query-performance-insight-use.md) para saber mais sobre como visualizar o impacto no desempenho de suas principais consultas.
