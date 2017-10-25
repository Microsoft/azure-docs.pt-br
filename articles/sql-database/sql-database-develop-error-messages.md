---
title: "Códigos de erro de SQL – erro de conexão de banco de dados | Microsoft Docs"
description: "Saiba mais sobre os códigos de erro de SQL para aplicativos cliente do Banco de Dados SQL, como erros comuns de conexão de banco de dados, problemas de cópia de banco de dados e erros gerais. "
keywords: "código de erro de sql, acessar sql, erro de conexão de banco de dados, códigos de erro de sql"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 2a23e4ca-ea93-4990-855a-1f9f05548202
ms.service: sql-database
ms.custom: develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: sstein
ms.openlocfilehash: 9f9dfd039b76e4bedfb7f59d042123fa54d6b615
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>Códigos de erro de SQL em aplicativos cliente do Banco de Dados SQL: erros de conexão de banco de dados e outros problemas

Este artigo lista os códigos de erro de SQL para aplicativos cliente do Banco de Dados SQL, incluindo erros de conexão de banco de dados, erros transitórios (também chamados de falhas transitórias), erros de governança de recursos, problemas de cópia de banco de dados, pool elástico e outros erros. A maioria das categorias específicas do Banco de Dados SQL do Azure e não se aplicam ao Microsoft SQL Server.

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Erros de conexão de banco de dados, erros transitórios e outros erros temporários
A tabela a seguir aborda os códigos de erro de SQL para erros de perda de conexão e outros erros transitórios que podem ocorrer quando o aplicativo tenta acessar o Banco de Dados SQL. Para obter tutoriais de introdução sobre como se conectar ao Banco de Dados SQL do Azure, consulte [Conectar-se ao Banco de Dados SQL do Azure](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Erros de conexão de banco de dados mais comuns e erros de falhas transitórias mais comuns
O sistema Azure tem a capacidade de reconfigurar dinamicamente os servidores quando cargas de trabalho pesadas surgem no serviço do Banco de Dados SQL.  Este comportamento dinâmico pode fazer com que o programa cliente perca a conexão com o Banco de Dados SQL. Essa variante de condição de erro é chamada de uma *falha transitória*.

É altamente recomendável que seu programa cliente tem a lógica de repetição para que ele pôde restabelecer uma conexão após conceder, a hora de falhas transitórias para corrigir a mesmo.  É recomendável que você aguarde 5 segundos antes de sua primeira tentativa. Tentar novamente após um atraso inferior a 5 segundos poderá sobrecarregar o serviço de nuvem. Para cada tentativa subsequente, o atraso deverá aumentar exponencialmente, até um máximo de 60 segundos.

Erros de falhas transitórias normalmente se manifestam como uma das seguintes mensagens de erro de seus programas de cliente:

* O banco de dados &lt;nome_do_bd&gt; no servidor &lt;instância_do_Azure&gt; não está disponível no momento. Tente a conexão novamente mais tarde. Se o problema persistir, entre em contato com o atendimento ao cliente e forneça a ID de rastreamento da &lt;session_id&gt;
* O banco de dados &lt;nome_do_bd&gt; no servidor &lt;instância_do_Azure&gt; não está disponível no momento. Tente a conexão novamente mais tarde. Se o problema persistir, entre em contato com o atendimento ao cliente e forneça a ID de rastreamento da &lt;session_id&gt;. (Microsoft SQL Server, erro: 40613)
* Uma conexão existente foi fechada à força pelo host remoto.
* System.Data.Entity.Core.EntityCommandExecutionException: erro ao executar a definição de comando. Consulte a exceção interna para obter detalhes. ---> System.Data.SqlClient.SqlException: erro de nível de transporte ao receber os resultados do servidor. (provedor: Provedor de Sessão, erro: 19 - a conexão física não é utilizável)
* Uma tentativa de conexão para um banco de dados secundário falhou porque o banco de dados está em processo reconfguration e ele estiver ocupado aplicar novas páginas no meio de uma transação ativa no banco de dados primário. 

Para obter exemplos de código de lógica de repetição, consulte:

* [Bibliotecas de conexões para Banco de Dados SQL e SQL Server](sql-database-libraries.md) 
* [Ações para corrigir erros de conexão e erros transitórios no Banco de Dados SQL](sql-database-connectivity-issues.md)

Uma discussão sobre o *período de bloqueio* para clientes que usam o ADO.NET está disponível em [Pool de conexão do SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Códigos de erros de falha transitória
Os seguintes erros são transitórios e devem ser repetidos na lógica do aplicativo: 

| Código do erro | Severidade | Descrição |
| ---:| ---:|:--- |
| 4060 |16 |Não é possível abrir o banco de dados "%.&#x2a;ls" solicitado pelo logon. Houve falha no logon. |
| 40197 |17 |O serviço encontrou um erro ao processar sua solicitação. Tente novamente. Código de erro %d.<br/><br/>Você recebe este erro quando o serviço fica inativo devido a upgrades de software ou hardware, falhas de hardware ou outros problemas de failover. O código de erro (%d) inserido na mensagem de erro 40197 fornece informações adicionais sobre o tipo de falha ou failover que ocorreu. Alguns exemplos de códigos que são inseridos na mensagem de erro 40197 são 40020, 40143, 40166 e 40540.<br/><br/>A reconexão ao servidor do Banco de Dados SQL conecta você automaticamente a uma cópia íntegra do banco de dados. Seu aplicativo deve capturar o erro 40197, registrar o código de erro inserido (%d) na mensagem para solução do problema e tentar se reconectar ao Banco de Dados SQL até que os recursos estejam disponíveis e a conexão seja restabelecida. |
| 40501 |20 |O serviço está ocupado. Repita a solicitação depois de 10 segundos. ID do incidente: %ls. Código: %d.<br/><br/>Para obter mais informações, consulte:<br/>• [Limites de recursos do Banco de Dados SQL do Azure](sql-database-service-tiers.md). |
| 40613 |17 |O banco de dados “%.&#x2a;ls” no servidor “%.&#x2a;ls” não está disponível momento. Tente a conexão novamente mais tarde. Se o problema persistir, entre em contato com o atendimento ao cliente e forneça a ID de rastreamento da sessão “%.&#x2a;ls”. |
| 49918 |16 |Não é possível processar a solicitação. Não há recursos suficientes para processar a solicitação.<br/><br/>O serviço está ocupado. Tente fazer novamente a solicitação. |
| 49919 |16 |Não é possível criar o processo ou atualizar a solicitação. Muitas operações de criação ou atualização em andamento para a assinatura "%ld".<br/><br/>O serviço está ocupado processando várias solicitações de criação ou atualização para a assinatura ou o servidor. As solicitações estão bloqueadas no momento para a otimização de recursos. Consulte [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para obter as operações pendentes. Espere até que as solicitações pendentes de criação ou atualização sejam concluídas ou exclua uma das suas solicitações pendentes e tente a solicitação novamente mais tarde. |
| 49920 |16 |Não é possível processar a solicitação. Muitas operações em andamento para assinatura "% ld".<br/><br/>O serviço está ocupado processando várias solicitações para essa assinatura. As solicitações estão bloqueadas no momento para a otimização de recursos. Consulte [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para obter o status da operação. Espere até que as solicitações pendentes estejam concluídas ou exclua uma das suas solicitações pendentes e tente a solicitação novamente mais tarde. |
| 4221 |16 |Falha de logon secundário de leitura devido a longa espera em 'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING'. A réplica não está disponível para logon porque as versões de linha estão ausentes para as transações que estão em curso quando a réplica foi reciclada. O problema pode ser resolvido por reverter ou confirmar as transações ativas na réplica primária. Ocorrências dessa condição podem ser minimizadas, evitando transações de tempo de gravação no primário. |

## <a name="database-copy-errors"></a>Erros de cópia de banco de dados
Os erros a seguir podem ser encontrados durante a cópia de um banco de dados no Banco de Dados SQL do Azure. Para saber mais, confira [Copiar um Banco de Dados SQL do Azure](sql-database-copy.md).

| Código do erro | Severidade | Descrição |
| ---:| ---:|:--- |
| 40635 |16 |O cliente com endereço IP “%.&#x2a;ls” está desabilitado temporariamente. |
| 40637 |16 |A criação da cópia do banco de dados está desabilitada no momento. |
| 40561 |16 |Falha na cópia do banco de dados. O banco de dados de origem ou de destino não existe. |
| 40562 |16 |Falha na cópia do banco de dados. O banco de dados de origem foi descartado. |
| 40563 |16 |Falha na cópia do banco de dados. O banco de dados de destino foi descartado. |
| 40564 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente. |
| 40565 |16 |Falha na cópia do banco de dados. Não é permitida mais de uma cópia simultânea do banco de dados com a mesma origem. Remova o banco de dados de destino e tente novamente mais tarde. |
| 40566 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente. |
| 40567 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente. |
| 40568 |16 |Falha na cópia do banco de dados. O banco de dados de origem tornou-se indisponível. Remova o banco de dados de destino e tente novamente. |
| 40569 |16 |Falha na cópia do banco de dados. O banco de dados de destino tornou-se indisponível. Remova o banco de dados de destino e tente novamente. |
| 40570 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente mais tarde. |
| 40571 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente mais tarde. |

## <a name="resource-governance-errors"></a>Erros de governança de recursos
Os erros a seguir são causados pelo uso excessivo de recursos enquanto você trabalha com o Banco de Dados SQL do Azure. Por exemplo:

* Uma transação está aberta há muito tempo.
* Uma transação está mantendo bloqueios demais.
* Um aplicativo está consumindo muita memória.
* Um aplicativo está consumindo muito espaço de `TempDb` .

Tópicos relacionados:

* Informações mais detalhadas estão disponíveis aqui: [Limites de recursos do Banco de Dados SQL do Azure](sql-database-service-tiers.md).

| Código do erro | Severidade | Descrição |
| ---:| ---:|:--- |
| 10928 |20 |ID do recurso: %d. O limite de %s para o banco de dados é %d e foi atingido. Para saber mais, confira [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>A ID do recurso indica qual dos recursos atingiu o limite. Para threads de trabalho, a ID do recurso é igual a 1. Para sessões, a ID do recurso é igual a 2.<br/><br/>Para obter mais informações sobre esse erro e como resolvê-lo, consulte:<br/>• [Limites de recursos do Banco de Dados SQL do Azure](sql-database-service-tiers.md). |
| 10929 |20 |ID do recurso: %d. A garantia mínima de %s é %d, o limite máximo é %d e o uso atual do banco de dados é %d. No entanto, o servidor está muito ocupado para dar suporte a solicitações maiores que %d para este banco de dados. Para saber mais, confira [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Caso contrário, tente novamente mais tarde.<br/><br/>A ID do recurso indica qual dos recursos atingiu o limite. Para threads de trabalho, a ID do recurso é igual a 1. Para sessões, a ID do recurso é igual a 2.<br/><br/>Para obter mais informações sobre esse erro e como resolvê-lo, consulte:<br/>• [Limites de recursos do Banco de Dados SQL do Azure](sql-database-service-tiers.md). |
| 40544 |20 |O banco de dados atingiu sua cota de tamanho. Particione ou exclua dados, descarte índices ou consulte a documentação para conhecer as possíveis resoluções. |
| 40549 |16 |A sessão foi encerrada porque você tem uma transação de longa duração. Tente encurtar a transação. |
| 40550 |16 |A sessão foi encerrada porque adquiriu muitos bloqueios. Tente ler ou modificar menos linhas em uma única transação. |
| 40551 |16 |A sessão foi encerrada devido ao uso excessivo de `TEMPDB` . Tente modificar a consulta para reduzir o uso temporário de espaço da tabela.<br/><br/>Se você estiver usando objetos temporários, conserve espaço no banco de dados `TEMPDB` removendo objetos temporários depois que eles não forem mais necessários na sessão. |
| 40552 |16 |A sessão foi encerrada devido a uso excessivo do espaço de log de transação. Tente modificar menos linhas em uma única transação.<br/><br/>Se você executar inserções em massa usando o utilitário `bcp.exe` ou a classe `System.Data.SqlClient.SqlBulkCopy`, tente usar as opções `-b batchsize` ou `BatchSize` para limitar o número de linhas copiadas para o servidor em cada transação. Se você estiver recriando um índice com a instrução `ALTER INDEX`, tente usar a opção `REBUILD WITH ONLINE = ON`. |
| 40553 |16 |A sessão foi encerrada devido ao uso excessivo de memória. Tente modificar a consulta para processar menos linhas.<br/><br/>A redução do número de operações `ORDER BY` e `GROUP BY` no código Transact-SQL reduz os requisitos de memória da consulta. |

## <a name="elastic-pool-errors"></a>Erros de pool elástico
Os seguintes erros estão relacionados à criação e ao uso de pools elásticos:

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
|:--- |:--- |:--- |:--- |:--- |:--- |
| 1132 |EX_RESOURCE |O pool elástico atingiu seu limite de armazenamento. O uso do armazenamento do pool elástico não pode exceder (%d) MB. |O limite de espaço do pool elástico é medido em MB. |Tentando gravar dados em um banco de dados quando o limite de armazenamento do pool elástico foi atingido. |Considere a possibilidade de aumentar as DTUs do pool elástico, se possível, para aumentar seu limite de armazenamento, reduzir o armazenamento usado por bancos de dados individuais dentro do pool elástico ou remover bancos de dados do pool elástico. |
| 10929 |EX_USER |A garantia mínima de %s é %d, o limite máximo é %d e o uso atual do banco de dados é %d. No entanto, o servidor está muito ocupado para dar suporte a solicitações maiores que %d para este banco de dados. Consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) para obter assistência. Caso contrário, tente novamente mais tarde. |Mínimo de DTUs por banco de dados; máximo de DTUs por banco de dados |O número total de trabalhos simultâneos (solicitações) em todos os bancos de dados no pool elástico tentou exceder o limite do pool. |Considere a possibilidade de aumentar as DTUs do pool elástico, se possível, para aumentar o limite de funções de trabalho ou remover bancos de dados do pool elástico. |
| 40844 |EX_USER |O banco de dados '%ls' no servidor '%ls' é um banco de dados edição '%ls' em um pool elástico e não pode ter um relacionamento de cópia contínuo. |nome do banco de dados, edição do banco de dados, nome do servidor |Um comando StartDatabaseCopy é emitido para um banco de dados não premium em um pool elástico. |Em breve |
| 40857 |EX_USER |Pool elástico não encontrado para o servidor: '%ls', nome do pool elástico: '%ls'. |nome do servidor; nome do pool elástico |O pool elástico especificado não existe no servidor especificado. |Forneça um nome de pool elástico válido. |
| 40858 |EX_USER |O pool elástico '%ls' já existe no servidor: '%ls' |nome do pool elástico, nome do servidor |O pool elástico especificado já existe no servidor lógico especificado. |Forneça um novo nome de pool elástico. |
| 40859 |EX_USER |O pool elástico não dá suporte à camada de serviço '%ls'. |camada de serviço do pool elástico |A camada de serviço especificada não dá suporte ao provisionamento de pool elástico. |Forneça a edição correta ou deixe a camada de serviço em branco para o padrão. |
| 40860 |EX_USER |A combinação de pool elástico '%ls' e objetivo de serviço '%ls' é inválida. |nome do pool elástico; nome de objetivo de nível de serviço |O pool elástico e o objetivo de serviço podem ser especificados juntos somente se o objetivo de serviço for especificado como “ElasticPool”. |Especifique a combinação correta de pool elástico e objetivo de serviço. |
| 40861 |EX_USER |A edição do banco de dados '%.*ls' não pode ser diferente da camada de serviço do pool elástico, que é '%.*ls'. |edição do banco de dados, camada de serviço do pool elástico |A edição do banco de dados é diferente da camada de serviço do pool elástico. |Não especifique uma edição de banco de dados diferente da camada de serviço do pool elástico.  Observe que a edição do banco de dados não precisa ser especificada. |
| 40862 |EX_USER |O nome do pool elástico deverá ser especificado se o objetivo de serviço do pool elástico for especificado. |Nenhum |O objetivo de serviço do pool elástico não identifica exclusivamente um pool elástico. |Especifique o nome do pool elástico se você estiver usando o objetivo de serviço do pool elástico. |
| 40864 |EX_USER |A quantidade mínima de DTUs para o pool elástico deve ser de (%d) DTUs para a camada de serviço '%.*ls'. |DTUs para o pool elástico; camada de serviço do pool elástico. |Tentando definir uma quantidade de DTUs para o pool elástico abaixo do limite mínimo. |Tente configurar novamente as DTUs para o pool elástico de acordo com o limite mínimo. |
| 40865 |EX_USER |A quantidade máxima de DTUs para o pool elástico deve ser de (%d) DTUs para a camada de serviço '%.*ls'. |DTUs para o pool elástico; camada de serviço do pool elástico. |Tentando definir uma quantidade de DTUs para o pool elástico acima do limite máximo. |Tente configurar novamente as DTUs para o pool elástico de acordo com o limite máximo. |
| 40867 |EX_USER |O máximo de DTUs por banco de dados deve ser de pelo menos (%d) para a camada de serviço '%.*ls'. |máximo de DTUs por banco de dados, camada de serviço do pool elástico |Tentando definir o máximo de DTUs por banco de dados abaixo do limite permitido. | Considere a possibilidade de usar a camada de serviço do pool elástico que dá suporte à configuração desejada. |
| 40868 |EX_USER |O máximo de DTUs por banco de dados não deve exceder (%d) para a camada de serviço '%.*ls'. |máximo de DTUs por banco de dados, camada de serviço do pool elástico. |Tentando definir o máximo de DTUs por banco de dados acima do limite permitido. | Considere a possibilidade de usar a camada de serviço do pool elástico que dá suporte à configuração desejada. |
| 40870 |EX_USER |O mínimo de DTUs por banco de dados deve ser de, no máximo, (%d) para a camada de serviço '%.*ls'. |mínimo de DTUs por banco de dados, camada de serviço do pool elástico. |Tentando definir o mínimo de DTUs por banco de dados acima do limite permitido. | Considere a possibilidade de usar a camada de serviço do pool elástico que dá suporte à configuração desejada. |
| 40873 |EX_USER |O número de bancos de dados (%d) e o mínimo de DTUs por banco de dados (%d) não podem exceder as DTUs do pool elástico (%d). |Número de bancos de dados no pool elástico; mínimo de DTUs por banco de dados; DTUs do pool elástico. |Tentando especificar uma quantidade mínima de DTUs para bancos de dados no pool elástico que excede as DTUs do pool elástico. | Considere a possibilidade de aumentar as DTUs do pool elástico, diminuir o mínimo de DTUs por banco de dados ou diminuir o número de bancos de dados no pool elástico. |
| 40877 |EX_USER |Não é possível excluir um pool elástico, a menos que ele não contenha bancos de dados. |Nenhum |O pool elástico contém um ou mais bancos de dados e, portanto, não pode ser excluído. |Remova os bancos de dados do pool elástico para excluí-lo. |
| 40881 |EX_USER |O pool elástico '%.*ls' atingiu o limite de banco de dados.  O limite de bancos de dados para o pool elástico não pode exceder (%d) para um pool elástico com (%d) DTUs. |Nome do pool elástico; limite de contagem de bancos de dados do pool elástico; eDTUs para o pool de recursos. |Tentando criar ou adicionar um banco de dados ao pool elástico quando o limite de bancos de dados do pool elástico foi atingido. | Considere a possibilidade de aumentar as DTUs do pool elástico, se possível, para aumentar o limite de bancos de dados ou remover bancos de dados do pool elástico. |
| 40889 |EX_USER |O limite de DTUs ou de armazenamento para o pool elástico '%.*ls' não pode ser reduzido, pois não haveria espaço de armazenamento suficiente para seus bancos de dados. |Nome do pool elástico. |Tentando reduzir o limite de armazenamento do pool elástico abaixo do seu uso de armazenamento. | Considere a possibilidade de reduzir o uso do armazenamento de bancos de dados individuais no pool elástico ou remover bancos de dados do pool para reduzir seu limite de DTUs ou de armazenamento. |
| 40891 |EX_USER |O mínimo de DTUs por banco de dados (%d) não pode ser superior ao máximo de DTUs por banco de dados (%d). |Mínimo de DTUs por banco de dados; máximo de DTUs por banco de dados. |Tentando definir o mínimo de DTUs por banco de dados acima do máximo de DTUs por banco de dados. |Verifique se o mínimo de DTUs por bancos de dados não excede o máximo de DTUs por banco de dados. |
| TBD |EX_USER |O tamanho do armazenamento de um banco de dados individual em um pool elástico não pode exceder o tamanho máximo permitido pelo pool elástico da camada de serviço “%.*ls”. |camada de serviço do pool elástico |O tamanho máximo do banco de dados excede o tamanho máximo permitido pela camada de serviço do pool elástico. |Defina o tamanho máximo do banco de dados dentro dos limites do tamanho máximo permitido pela camada de serviço do pool elástico. |

Tópicos relacionados:

* [Criar um pool elástico (C#)](sql-database-elastic-pool-manage-csharp.md) 
* [Gerenciar um pool elástico (C#)](sql-database-elastic-pool-manage-csharp.md). 
* [Criar um pool elástico (PowerShell)](sql-database-elastic-pool-manage-powershell.md) 
* [Monitorar e gerenciar um pool elástico (PowerShell)](sql-database-elastic-pool-manage-powershell.md).

## <a name="general-errors"></a>Erros gerais
Os erros a seguir não se enquadram em nenhuma categoria anterior.

| Código do erro | Severidade | Descrição |
| ---:| ---:|:--- |
| 15006 |16 |(AdministratorLogin) não é um nome válido porque contém caracteres inválidos. |
| 18452 |14 |Falha no logon. O logon é de um domínio não confiável e não pode ser usado com a autenticação do Windows.%.&#x2a;ls (logons do Windows não têm suporte nesta versão do SQL Server.) |
| 18456 |14 |Falha no logon do usuário '%.&#x2a;ls'.%.&#x2a;ls%.&#x2a;ls(Falha de logon do usuário "%.&#x2a;ls". Falha na alteração da senha do usuário. A alteração de senha durante o logon não tem suporte nesta versão do SQL Server.) |
| 18470 |14 |Falha no logon do usuário '%.&#x2a;ls'. Motivo: A conta está desabilitada.%.&#x2a;ls |
| 40014 |16 |Não é possível usar vários bancos de dados na mesma transação. |
| 40054 |16 |Tabelas sem um índice clusterizado não têm suporte nesta versão do SQL Server. Criar um índice clusterizado e tente novamente. |
| 40133 |15 |Esta operação não tem suporte nesta versão do SQL Server. |
| 40506 |16 |O SID especificado é inválido para esta versão do SQL Server. |
| 40507 |16 |'%.&#x2a;ls' não pode ser invocado com parâmetros nesta versão do SQL Server. |
| 40508 |16 |Não há suporte para a instrução USE para alternar entre bancos de dados. Use uma nova conexão para se conectar a um banco de dados diferente. |
| 40510 |16 |A instrução '%.&#x2a;ls' não tem suporte nesta versão do SQL Server |
| 40511 |16 |A função incorporada '%.&#x2a;ls' não tem suporte nesta versão do SQL Server. |
| 40512 |16 |O recurso substituído '%ls' não tem suporte nesta versão do SQL Server. |
| 40513 |16 |A variável do servidor '%.&#x2a;ls' não tem suporte nesta versão do SQL Server. |
| 40514 |16 |'ls' não tem suporte nesta versão do SQL Server. |
| 40515 |16 |Referências ao nome do banco de dados e/ou servidor em '%.&#x2a;ls' não têm suporte nesta versão do SQL Server. |
| 40516 |16 |Objetos temporários globais não têm suporte nesta versão do SQL Server. |
| 40517 |16 |A opção de instrução ou palavra-chave '%.&#x2a;ls' não tem suporte nesta versão do SQL Server. |
| 40518 |16 |O comando DBCC '%.&#x2a;ls' não tem suporte nesta versão do SQL Server. |
| 40520 |16 |A classe protegível '%S_MSG' não tem suporte nesta versão do SQL Server. |
| 40521 |16 |A classe protegível '%S_MSG' não tem suporte no escopo do servidor nesta versão do SQL Server. |
| 40522 |16 |O banco de dados com tipo de entidade de segurança '%.&#x2a;ls' não tem suporte nesta versão do SQL Server. |
| 40523 |16 |A criação de usuário implícito '%.&#x2a;ls' não tem suporte nesta versão do SQL Server. Crie explicitamente o usuário antes de usá-lo. |
| 40524 |16 |O tipo de dados '%.&#x2a;ls' não tem suporte nesta versão do SQL Server. |
| 40525 |16 |WITH 'ls' não tem suporte nesta versão do SQL Server. |
| 40526 |16 |O provedor de conjunto de linhas '%.&#x2a;ls' não tem suporte nesta versão do SQL Server. |
| 40527 |16 |Os servidores vinculados não têm suporte nesta versão do SQL Server. |
| 40528 |16 |Usuários não podem ser mapeados para certificados, chaves assimétricas ou logons do Windows nesta versão do SQL Server. |
| 40529 |16 |A função incorporada '%.&#x2a;ls' no contexto de representação não tem suporte nesta versão do SQL Server. |
| 40532 |11 |Não é possível abrir o servidor "%.&#x2a;ls" solicitado pelo logon. Houve falha no logon. |
| 40553 |16 |A sessão foi encerrada devido ao uso excessivo de memória. Tente modificar a consulta para processar menos linhas.<br/><br/> A redução do número de operações `ORDER BY` e `GROUP BY` no código Transact-SQL ajuda a reduzir os requisitos de memória da consulta. |
| 40604 |16 |Não foi possível CRIAR/ALTERAR O BANCO DE DADOS pois isso excederia a cota do servidor. |
| 40606 |16 |Anexar bancos de dados não tem suporte nesta versão do SQL Server. |
| 40607 |16 |Logons do Windows não têm suporte nesta versão do SQL Server. |
| 40611 |16 |Os servidores podem ter no máximo 128 regras de firewall definidas. |
| 40614 |16 |O endereço IP inicial da regra de firewall não pode ultrapassar o endereço IP final. |
| 40615 |16 |Não é possível abrir o servidor '{0}' solicitado pelo logon. O cliente com o endereço IP '{1}' não tem permissão para acessar o servidor.<br /><br />Para habilitar o acesso, use o Portal do Banco de Dados SQL ou execute sp\_set\_firewall\_rule no banco de dados mestre para criar uma regra de firewall este endereço ou intervalo de endereços IP. Pode levar até cinco minutos para que essa alteração tenha efeito. |
| 40617 |16 |O nome da regra de firewall que começa com (nome da regra) é muito longo. O comprimento máximo é 128. |
| 40618 |16 |O nome da regra de firewall não pode estar vazio. |
| 40620 |16 |Falha no logon do usuário “%.&#x2a;ls”. Falha na alteração da senha do usuário. A alteração de senha durante o logon não tem suporte nesta versão do SQL Server. |
| 40627 |20 |A operação no servidor '{0}' e banco de dados '{1}' está em andamento. Aguarde alguns minutos antes de tentar novamente. |
| 40630 |16 |Falha na validação da senha. A senha não atende aos requisitos da política porque é muito curta. |
| 40631 |16 |A senha que você especificou é muito longa. A senha deve ter no máximo 128 caracteres. |
| 40632 |16 |Falha na validação da senha. A senha não atende aos requisitos da política porque não é complexa o suficiente. |
| 40636 |16 |Não é possível usar um nome de banco de dados reservado '%.&#x2a;ls' nesta operação. |
| 40638 |16 |ID de assinatura inválida (subscription-id). A assinatura não existe. |
| 40639 |16 |A solicitação não está em conformidade com o esquema: (erro de esquema). |
| 40640 |20 |O servidor encontrou uma exceção inesperada. |
| 40641 |16 |O local especificado é inválido. |
| 40642 |17 |O servidor está muito ocupado no momento. Tente novamente mais tarde. |
| 40643 |16 |O valor de cabeçalho x-ms-version especificado é inválido. |
| 40644 |14 |Falha ao autorizar o acesso à assinatura especificada. |
| 40645 |16 |Servername (servername) não pode ser vazio ou nulo. Ele pode ser composto apenas por letras minúsculas de “a” a “z”, números de 0 a 9 e o hífen. O hífen não pode ser o primeiro ou o último caractere do nome. |
| 40646 |16 |A ID da assinatura não pode ficar vazia. |
| 40647 |16 |A assinatura (subscription-id) não tem um servidor (servername). |
| 40648 |17 |Muitas solicitações foram executadas. Tente novamente mais tarde. |
| 40649 |16 |Tipo de conteúdo inválido especificado. Somente aplicativo/xml tem suporte. |
| 40650 |16 |A assinatura (subscription-id) não existe ou não está pronta para a operação. |
| 40651 |16 |Falha ao criar o servidor porque a assinatura (subscription-id) está desabilitada. |
| 40652 |16 |Não é possível mover ou criar o servidor. A assinatura (subscription-id) excederá a cota do servidor. |
| 40671 |17 |Falha de comunicação entre o gateway e o serviço de gerenciamento. Tente novamente mais tarde. |
| 40852 |16 |Não é possível abrir o banco de dados '%.\*ls' no servidor '%.\*ls' solicitado pelo logon. O acesso ao banco de dados é permitido apenas usando uma cadeia de conexão habilitada para segurança. Para acessar esse banco de dados, modifique as cadeias de conexão para conter “secure” no servidor FQDN – 'server name'.database.windows.net deve ser modificado para 'server name'.database`secure`.windows.net. |
| 40914 | 16 | Não é possível abrir o servidor '*[nome-do-servidor]*' solicitado pelo logon. O cliente não tem permissão para acessar o servidor.<br /><br />Para corrigir, considere adicionar uma [regra de rede virtual](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16 |O sistema do SQL Azure está sob carga e está estabelecendo um limite superior para operações CRUD de BD simultâneas para um único servidor (por exemplo, criar banco de dados). O servidor especificado na mensagem de erro ultrapassou o número máximo de conexões simultâneas. Tente novamente mais tarde. |
| 45169 |16 |O sistema SQL Azure está sob carga e está estabelecendo um limite superior para o número de operações CRUD de servidor simultâneas para uma única assinatura (por exemplo, criar servidor). A assinatura especificada na mensagem de erro ultrapassou o número máximo de conexões simultâneas e a solicitação foi negada. Tente novamente mais tarde. |

## <a name="next-steps"></a>Próximas etapas
* Leia mais sobre os [Recursos do Banco de Dados SQL do Azure](sql-database-features.md).
* Leia mais sobre as [Camadas de serviço](sql-database-service-tiers.md).

