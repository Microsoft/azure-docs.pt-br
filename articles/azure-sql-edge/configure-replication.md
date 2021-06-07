---
title: Configurar a replicação para o Azure SQL Edge
description: Saiba como configurar a replicação para o Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b424ece9207328d87068160f78ebc78a3bd1a8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395216"
---
# <a name="configure-replication-to-azure-sql-edge"></a>Configurar a replicação para o Azure SQL Edge 

Você pode configurar uma instância do Azure SQL Edge como o Assinante de push para replicação transacional unidirecional ou replicação de instantâneo. Esta instância não pode atuar como o Publicador ou o distribuidor para uma configuração de replicação transacional. Observe que o Azure SQL Edge não dá suporte à replicação de mesclagem, replicação ponto a ponto ou publicação Oracle.

## <a name="supported-configurations"></a>Configurações com suporte
  
- A instância do Azure SQL Edge deve ser um assinante de push para um Publicador.
- O Publicador e o distribuidor podem ser:
   - Uma instância do SQL Server em execução no local ou uma instância do SQL Server em execução em uma máquina virtual do Azure. Para obter mais informações, veja [SQL Server na visão geral de Máquinas Virtuais do Azure](../azure-sql/virtual-machines/index.yml). SQL Server instâncias devem estar usando uma versão posterior à SQL Server 2016.
   - Uma instância do Azure SQL Instância Gerenciada. O SQL Instância Gerenciada pode hospedar bancos de dados de Publicador, distribuidor e Assinante. Para obter mais informações, veja [Replicação com a Instância Gerenciada do Banco de Dados SQL](/azure/sql-database/replication-with-sql-database-managed-instance/).

- O banco de dados de distribuição e os agentes de replicação não podem ser colocados em uma instância do Azure SQL Edge.  

> [!NOTE]
> Se você tentar configurar a replicação usando uma versão sem suporte, poderá receber os dois erros a seguir: MSSQL_REPL20084 ("o processo não pôde se conectar ao Assinante.") e MSSQL_REPL40532 ("não é possível abrir o servidor \<name> solicitado pelo logon. Falha no logon. ").  

## <a name="remarks"></a>Comentários

Os seguintes requisitos e práticas recomendadas são importantes para entender conforme você configura a replicação:

- Você pode configurar a replicação usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Você também pode fazer isso executando instruções Transact-SQL no Publicador, usando SQL Server Management Studio ou [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).
- Para replicar para uma instância do Azure SQL Edge, você deve usar SQL Server autenticação para entrar.
- Tabelas replicadas devem ter uma chave primária.
- Uma única publicação no SQL Server pode dar suporte a assinantes do SQL do Azure no Edge e do SQL Server (local e SQL Server em uma máquina virtual do Azure).  
- O gerenciamento de replicação, o monitoramento e a solução de problemas devem ser executados na instância do SQL Server.  
- Há suporte apenas para assinaturas push no SQL do Azure no Edge.  
- Somente tem `@subscriber_type = 0` suporte no procedimento armazenado `sp_addsubscription` para o Azure SQL Edge.  
- O Azure SQL Edge não dá suporte à replicação bidirecional, imediata, atualizável ou ponto a ponto.
- O Azure SQL Edge só dá suporte a um subconjunto de recursos disponíveis em SQL Server ou Instância Gerenciada SQL. Se você tentar replicar um banco de dados (ou objetos dentro do banco de dados) que contém um ou mais recursos sem suporte, a tentativa falhará. Por exemplo, se você tentar replicar um banco de dados que contém objetos com tipos de dado espaciais, você receberá um erro. Para obter mais informações, consulte [recursos com suporte do Azure SQL Edge](features.md).

## <a name="initialize-reference-data-on-an-instance-of-azure-sql-edge"></a>Inicializar dados de referência em uma instância do Azure SQL Edge

Talvez você queira inicializar sua instância com dados de referência que mudam ao longo do tempo. Por exemplo, talvez você queira atualizar modelos de aprendizado de máquina em sua instância do Azure SQL Edge, depois que eles tiverem sido treinados em uma instância de SQL Server. Veja como inicializar sua instância em um cenário como esse:

1. Crie uma publicação de replicação transacional em um banco de dados SQL Server.  
2. Na instância de SQL Server, use o **Assistente para nova assinatura** ou instruções TRANSACT-SQL para criar um push para assinatura para o Azure SQL Edge.  
3. Você pode inicializar o banco de dados replicado no Azure SQL Edge usando um instantâneo gerado pelo Snapshot Agent e distribuído e entregue pelo Distribution Agent. Como alternativa, você pode inicializar usando um backup do banco de dados do Publicador. Lembre-se de que, se o backup do banco de dados contiver objetos ou recursos sem suporte do Azure SQL Edge, a operação de restauração falhará.

## <a name="limitations"></a>Limitações

As seguintes opções não têm suporte para as assinaturas do Azure SQL Edge:

- Copiar associação de grupos de arquivos  
- Copiar esquemas de particionamento de tabela  
- Copiar esquemas de particionamento de índice  
- Copiar estatísticas definidas pelo usuário  
- Copiar associações padrão  
- Copiar associações de regra  
- Copiar índices de texto completo  
- Copiar XML XSD  
- Copiar índices XML  
- Permissões de cópia  
- Copiar índices espaciais  
- Copiar índices filtrados  
- Copiar atributo de compactação de dados  
- Copiar atributo de coluna esparsa  
- Copiar tipos de dados de FileStream, `hierarchyid` , ou espaciais
- Converter `hierarchyid` em tipos de dados Max  
- Converter espacial em tipos de dados MAX  
- Copiar propriedades estendidas  
- Permissões de cópia  

## <a name="examples"></a>Exemplos

Crie uma publicação e uma assinatura push. Para obter mais informações, consulte:
  
- [Criar uma publicação](/sql/relational-databases/replication/publish/create-a-publication)
- [Crie uma assinatura push](/sql/relational-databases/replication/create-a-push-subscription/) usando o nome do servidor do Azure SQL Edge e o IP como o assinante (por exemplo, **myEdgeinstance, 1433**) e um nome de banco de dados na instância do Azure SQL Edge como o banco de dados de destino (por exemplo, **AdventureWorks**).  

## <a name="next-steps"></a>Próximas etapas  

- [Criar uma publicação](/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma assinatura push](/sql/relational-databases/replication/create-a-push-subscription/)
- [Tipos de replicação](/sql/relational-databases/replication/types-of-replication)
- [Monitoramento (replicação)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma assinatura](/sql/relational-databases/replication/initialize-a-subscription)