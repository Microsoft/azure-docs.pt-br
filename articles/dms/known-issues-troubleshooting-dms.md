---
title: Artigo sobre solução de problemas conhecidos/erros comuns associados ao uso do serviço de migração de banco de dados do Azure | Microsoft Docs
description: Saiba mais sobre como solucionar problemas comuns/erros conhecidos associados ao uso do serviço de migração de banco de dados do Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/18/2019
ms.openlocfilehash: e33f195ea821b34147c748e9c0aa64cb63b58fdc
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249990"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Solucionar problemas e erros comuns do serviço de migração de banco de dados do Azure

Este artigo descreve alguns problemas comuns e erros que os usuários do serviço de migração de banco de dados do Azure podem percorrer. O artigo também inclui informações sobre como resolver esses problemas e erros.

## <a name="migration-activity-in-queued-state"></a>Atividade de migração no estado enfileirado

Quando você cria novas atividades em um projeto de serviço de migração de banco de dados do Azure, as atividades permanecem em um estado enfileirado.

| Causa         | Resolução |
| ------------- | ------------- |
| Esse problema ocorre quando a instância do serviço de migração de banco de dados do Azure atingiu a capacidade máxima para tarefas em andamento que são executadas simultaneamente. Todas as novas atividades são enfileiradas até que a capacidade se torne disponível. | Valide se a instância do serviço de migração de dados está executando atividades em projetos. Você pode continuar a criar novas atividades que são adicionadas automaticamente à fila para execução. Assim que qualquer uma das atividades existentes em execução for concluída, a próxima atividade enfileirada começará a ser executada e o status será alterado para executando o estado automaticamente. Você não precisa realizar nenhuma ação adicional para iniciar a migração da atividade enfileirada.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Número máximo de bancos de dados selecionados para migração

O seguinte erro ocorre ao criar uma atividade para um projeto de migração de banco de dados para mover para o banco de dados SQL do Azure ou uma instância gerenciada do banco de dados SQL do Azure:

* **Erro**: Erro de validação das configurações de migração "," errorDetail ":" mais do que o número máximo de objetos ' 4 ' de ' bancos de dados ' foi selecionado para migração. "

| Causa         | Resolução |
| ------------- | ------------- |
| Esse erro é exibido quando você seleciona mais de quatro bancos de dados para uma única atividade de migração. No momento, cada atividade de migração é limitada a quatro bancos de dados. | Selecione quatro ou menos bancos de dados por atividade de migração. Se você precisar migrar mais de quatro bancos de dados em paralelo, provisione outra instância do serviço de migração de banco de dados do Azure. Atualmente, cada assinatura dá suporte a até duas instâncias do serviço de migração de banco de dados do Azure.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Erros de migração do MySQL para o Azure MySQL com falhas de recuperação

Quando você migra do MySQL para o banco de dados do Azure para MySQL usando o serviço de migração de banco de dados do Azure, a atividade de migração falha com o seguinte erro:

* **Erro**: Erro de migração de banco de dados-a tarefa ' TaskId ' foi suspensa devido a [n] falhas de recuperação sucessivas.

| Causa         | Resolução |
| ------------- | ------------- |
| Esse erro pode ocorrer quando o usuário que está fazendo a migração não tem a função ReplicationAdmin e/ou privilégios de cliente de replicação, réplica de replicação e SUPER (versões anteriores ao MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Verifique se os [privilégios de pré-requisito](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) para a conta de usuário estão configurados com precisão na instância do banco de dados do Azure para MySQL. Por exemplo, as etapas a seguir podem ser seguidas para criar um usuário chamado ' Migrateuser ' com os privilégios necessários:<br>1. CRIAR usuário Migrateuser @ '% ' identificado por ' Secret '; <br>2. Conceder todos os privilégios em db_name. * para ' Migrateuser ' @ '% ' identificado por ' Secret '; Repita esta etapa para conceder acesso a mais bancos de dados <br>3. Conceda a replicação subordinada em *.* para ' Migrateuser ' @ '% ' identificado por ' Secret ';<br>4. Conceder cliente de replicação em *.* para ' Migrateuser ' @ '% ' identificado por ' Secret ';<br>5. Liberar privilégios; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Erro ao tentar parar o serviço de migração de banco de dados do Azure

Você recebe o seguinte erro ao interromper a instância do serviço de migração de banco de dados do Azure:

* **Erro**: Falha ao parar o serviço. Erro: {' erro ': {' código ': ' InvalidRequest ', ' mensagem ': ' uma ou mais atividades estão em execução no momento. Para interromper o serviço, aguarde até que as atividades sejam concluídas ou interrompa as atividades manualmente e tente novamente. '}}

| Causa         | Resolução |
| ------------- | ------------- |
| Esse erro é exibido quando a instância de serviço que você está tentando parar inclui atividades que ainda estão em execução ou estão presentes em projetos de migração. <br><br><br><br><br><br> | Verifique se não há atividades em execução na instância do serviço de migração de banco de dados do Azure que você está tentando parar. Você também pode excluir as atividades ou projetos antes de tentar interromper o serviço. As etapas a seguir ilustram como remover projetos para limpar a instância do serviço de migração excluindo todas as tarefas em execução:<br>1. Install-Module-Name AzureRM. datamigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription-subscriptionname "\<subname >" <br> 4. Remove-AzureRmDataMigrationProject-name \<projectName >-ResourceGroupName \< \<rgName >-ServiceName ServiceName >-DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Erro ao tentar iniciar o serviço de migração de banco de dados do Azure

Você recebe o seguinte erro ao iniciar a instância do serviço de migração de banco de dados do Azure:

* **Erro**: Falha ao iniciar o serviço. Erro: {' errorDetail ': ' falha ao iniciar o serviço, contate o suporte da Microsoft '}

| Causa         | Resolução |
| ------------- | ------------- |
| Esse erro exibe quando a instância anterior falhou internamente. Esse erro ocorre raramente e a equipe de engenharia está ciente dela. <br> | Exclua a instância do serviço que você não pode iniciar e provisione uma nova para substituí-la. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Erro ao restaurar o banco de dados ao migrar SQL para instância gerenciada do BD SQL do Azure

Quando você executa uma migração online do SQL Server para uma instância gerenciada do banco de dados SQL do Azure, a transferência falha com o seguinte erro:

* **Erro**: Falha na operação de restauração da ID da operação ' operationId '. O código ' AuthorizationFailed ', mensagem ' o cliente ' clientId ' com a ID de objeto ' objectId ' não tem autorização para executar a ação ' Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/Read ' sobre o escopo '/subscriptions/ SubscriptionId '. '.

| Causa         | Resolução    |
| ------------- | ------------- |
| Esse erro indica que a entidade de segurança de aplicativo que está sendo usada para migração online do SQL Server para uma instância gerenciada do banco de dados SQL do Azure não tem permissão de colaboração na assinatura. Determinadas chamadas à API com Instância Gerenciada no momento exigem essa permissão na assinatura para a operação de restauração. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Use o `Get-AzureADServicePrincipal` cmdlet do PowerShell `-ObjectId` com disponível na mensagem de erro para listar o nome de exibição da ID do aplicativo que está sendo usada.<br><br> Valide as permissões para esse aplicativo e verifique se ele tem a [função de colaborador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) no nível da assinatura. <br><br> A equipe de engenharia do serviço de migração de banco de dados do Azure está trabalhando para restringir o acesso necessário da função atual do Contribute na assinatura. Se você tiver um requisito de negócios que não permita o uso da função do Contribute, entre em contato com o suporte do Azure para obter ajuda adicional. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Erro ao excluir a NIC associada ao serviço de migração de banco de dados do Azure

Quando você tenta excluir uma placa de interface de rede associada ao serviço de migração de banco de dados do Azure, a tentativa de exclusão falha com esse erro:

* **Erro**: Não é possível excluir a NIC associada ao serviço de migração de banco de dados do Azure devido ao serviço DMS utilizando a NIC

| Causa         | Resolução    |
| ------------- | ------------- |
| Esse problema ocorre quando a instância do serviço de migração de banco de dados do Azure ainda pode estar presente e consumindo a NIC. <br><br><br><br><br><br><br><br> | Para excluir essa NIC, exclua a instância do serviço DMS que exclui automaticamente a NIC usada pelo serviço.<br><br> **Importante**: Verifique se a instância do serviço de migração de banco de dados do Azure sendo excluída não tem atividades em execução.<br><br> Depois que todos os projetos e atividades associados à instância do serviço de migração de banco de dados do Azure forem excluídos, você poderá excluir a instância de serviço. A NIC usada pela instância de serviço é automaticamente limpa como parte da exclusão do serviço. |

## <a name="connection-error-when-using-expressroute"></a>Erro de conexão ao usar o ExpressRoute

Quando você tenta se conectar à fonte no assistente de projeto de serviço de Migração de Banco de dados do Azure, a conexão falha após o tempo limite prolongado se a fonte estiver usando o ExpressRoute para conectividade.

| Causa         | Resolução    |
| ------------- | ------------- |
| Ao usar o [ExpressRoute](https://azure.microsoft.com/services/expressroute/), o serviço de migração de banco de dados do Azure [requer](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) o provisionamento de três pontos de extremidade de serviço na sub-rede da rede virtual associada ao serviço:<br> --Ponto de extremidade do barramento de serviço<br> --Ponto de extremidade de armazenamento<br> --Ponto de extremidade do banco de dados de destino (por exemplo, ponto Cosmos DB de extremidade SQL, ponto de extremidade<br><br><br><br><br> | [Habilite](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) os pontos de extremidade de serviço necessários para conectividade de ExpressRoute entre o serviço de migração de banco de dados de origem e do Azure. <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-mysql"></a>Erro de tempo limite ao migrar um banco de dados MySQL para o MySQL do Azure

Ao migrar um banco de dados MySQL para uma instância do banco de dados do Azure para MySQL por meio do serviço de migração de banco de dados do Azure, a migração falhará com o seguinte erro

* **Erro**: Erro de migração de banco de dados-falha ao carregar o arquivo-falha ao iniciar o processo de carregamento para o arquivo ' n' RetCode: SQL_ERROR SqlState: NativeError HY000: 1205 mensagem: [MySQL] [driver ODBC] [mysqld] tempo limite de espera de bloqueio excedido; Tente reiniciar a transação

| Causa         | Resolução    |
| ------------- | ------------- |
| Esse erro ocorre quando a migração falha devido ao tempo limite de espera de bloqueio durante a migração. | Considere aumentar o valor do parâmetro de servidor **' innodb_lock_wait_timeout '** . O maior valor permitido é 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Erro ao conectar-se ao SQL Server de origem ao usar a porta dinâmica ou a instância nomeada

Quando você tenta conectar o serviço de migração de banco de dados do Azure para SQL Server fonte que é executada na instância nomeada ou em uma porta dinâmica, a conexão falha com esse erro:

* **Erro**:-1-falha na conexão SQL. Ocorreu um erro relacionado à rede ou específico da instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível. Verifique se o nome da instância está correto e se SQL Server está configurado para permitir conexões remotas. (provedor: Adaptadores de Rede do SQL, erro: 26-erro ao localizar o servidor/instância especificada)

| Causa         | Resolução    |
| ------------- | ------------- |
| Esse problema ocorre quando a instância de SQL Server de origem para a qual o serviço de migração de banco de dados do Azure tenta se conectar tem uma porta dinâmica ou está usando uma instância nomeada. O serviço de SQL Server Browser escuta a porta UDP 1434 para conexões de entrada para uma instância nomeada ou ao usar uma porta dinâmica. A porta dinâmica pode mudar sempre que SQL Server serviço for reiniciado. Você pode verificar a porta dinâmica atribuída a uma instância por meio da configuração de rede no SQL Server Configuration Manager.<br><br><br> |Verifique se o serviço de migração de banco de dados do Azure pode se conectar ao serviço de SQL Server Browser de origem na porta UDP 1434 e a instância de SQL Server por meio da porta TCP atribuída dinamicamente, conforme aplicável. |

## <a name="additional-known-issues"></a>Problemas conhecidos adicionais

* [Problemas conhecidos/limitações de migração com migrações online para o banco de dados SQL do Azure](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Problemas conhecidos/limitações de migração com migrações online para o banco de dados do Azure para MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Problemas conhecidos/limitações de migração com migrações online para o banco de dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Próximas etapas

* Exiba o artigo [PowerShell do serviço de migração de banco de dados do Azure](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Exiba o artigo [como configurar parâmetros de servidor no banco de dados do Azure para MySQL usando o portal do Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Veja o artigo [visão geral dos pré-requisitos para usar o serviço de migração de banco de dados do Azure](https://docs.microsoft.com/azure/dms/pre-reqs).
* Consulte as [perguntas frequentes sobre como usar o serviço migração de banco de dados do Azure](https://docs.microsoft.com/azure/dms/faq).
