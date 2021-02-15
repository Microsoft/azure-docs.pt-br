---
title: Interoperabilidade de recursos com SQL Server FCI & DNN
description: 'Saiba mais sobre as considerações adicionais ao trabalhar com determinados recursos de SQL Server e um recurso de DNN (nome de rede distribuída) com uma instância de cluster de failover em SQL Server em VMs do Azure. '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 3c92aa3b35240831fad14919dc73609d803c610a
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358207"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>Interoperabilidade de recursos com SQL Server FCI & DNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Há determinados recursos de SQL Server que dependem de um VNN (nome de rede virtual) embutido em código. Assim, ao usar o recurso DNN (nome de rede distribuída) com sua instância de cluster de failover e SQL Server em VMs do Azure, há algumas considerações adicionais. 

Neste artigo, saiba como configurar o alias de rede ao usar o recurso DNN, bem como quais recursos de SQL Server exigem consideração adicional.

## <a name="create-network-alias-fci"></a>Criar alias de rede (FCI)

Alguns componentes do lado do servidor dependem de um valor de VNN embutido em código e exigem um alias de rede que mapeia o VNN para o nome DNS DNN para funcionar corretamente. Siga as etapas em [criar um alias de servidor](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) para criar um alias que mapeie o VNN para o nome DNS DNN. 

Para uma instância padrão, você pode mapear o VNN para o nome DNS DNN diretamente, de modo que VNN = DNN nome DNS.
Por exemplo, se VNN name for `FCI1` , o nome da instância é `MSSQLSERVER` e o DNN é `FCI1DNN` (os clientes conectados anteriormente ao `FCI` e agora se conectam `FCI1DNN` ) e, em seguida, mapeiam o VNN `FCI1` para o DNN `FCI1DNN` . 

Para uma instância nomeada, o mapeamento de alias de rede deve ser feito para a instância completa, de modo que `VNN\Instance`  =  `DNN\Instance` . Por exemplo, se VNN name for `FCI1` , o nome da instância é `instA` e o DNN é `FCI1DNN` (os clientes conectados anteriormente ao `FCI1\instA` e agora se conectam `FCI1DNN\instaA` ) e, em seguida, mapeiam o VNN `FCI1\instaA` para o DNN `FCI1DNN\instaA` . 



## <a name="client-drivers"></a>Drivers de cliente

Para drivers ODBC, OLEDB, ADO.NET, JDBC, PHP e Node.js, os usuários precisam especificar explicitamente o nome DNS DNN como o nome do servidor na cadeia de conexão. Para garantir a conectividade rápida no failover, adicione `MultiSubnetFailover=True` à cadeia de conexão se o cliente SQL oferecer suporte a ela. 

## <a name="tools"></a>Ferramentas

Os usuários de [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)e [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) precisam especificar explicitamente o nome DNS DNN como o nome do servidor na cadeia de conexão. 

## <a name="availability-groups-and-fci"></a>Grupos de disponibilidade e FCI

Você pode configurar um grupo de disponibilidade Always On usando uma instância de cluster de failover como uma das réplicas. Nessa configuração, a URL do ponto de extremidade de espelhamento para a réplica FCI precisa usar o DNN FCI. Da mesma forma, se o FCI for usado como uma réplica somente leitura, o roteamento somente leitura para a réplica FCI precisará usar o FCI DNN. 

O formato para o ponto de extremidade de espelhamento é: `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'` . 

Por exemplo, se o nome DNS do DNN for `dnnlsnr` , e `5022` for a porta do ponto de extremidade de espelhamento do FCI, o trecho de código TRANSACT-SQL (T-SQL) para criar a URL do ponto de extremidade será semelhante a: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Da mesma forma, o formato da URL de roteamento somente leitura é: `TCP://<DNN DNS name>:<SQL Server instance port>` . 

Por exemplo, se o nome DNS do DNN for `dnnlsnr` , e `1444` for a porta usada pelo destino somente leitura SQL Server FCI, o trecho de código T-SQL para criar a URL de roteamento somente leitura terá a seguinte aparência: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

Você pode omitir a porta na URL se ela for a porta padrão 1433. Para uma instância nomeada, configure uma porta estática para a instância nomeada e especifique-a na URL de roteamento somente leitura.  

## <a name="replication"></a>Replicação

A replicação tem três componentes: Publicador, distribuidor, Assinante. Qualquer um desses componentes pode ser uma instância de cluster de failover. Como o FCI VNN é muito usado na configuração de replicação, de forma explícita e implícita, um alias de rede que mapeia o VNN para o DNN pode ser necessário para que a replicação funcione. 

Continue usando o nome do VNN como o nome do FCI na replicação, mas crie um alias de rede nas seguintes situações remotas *antes de configurar a replicação*:

| **Componente de replicação (FCI com DNN)** | **Componente remoto** | **Mapa de alias de rede** | **Servidor com mapa de rede**| 
|---------|---------|---------|-------- | 
|Publicador | Distribuidor | VNN do Publicador para o Publicador DNN| Distribuidor| 
|Distribuidor|Subscriber |VNN do distribuidor para DNN do distribuidor| Subscriber | 
|Distribuidor|Publicador | VNN do distribuidor para DNN do distribuidor | Publicador| 
|Subscriber| Distribuidor| Assinante VNN para Assinante DNN | Distribuidor| 

Por exemplo, suponha que você tenha um Publicador configurado como um FCI usando DNN em uma topologia de replicação e o distribuidor seja remoto. Nesse caso, crie um alias de rede no servidor do distribuidor para mapear o VNN do Publicador para o DNN do Publicador: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="Configure o nome DNS DNN como o alias de rede usando SQL Server Configuration Manager." :::

Use o nome completo da instância para uma instância nomeada, como o exemplo de imagem a seguir: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="Use o nome de instância completo ao configurar um alias de rede para uma instância nomeada." :::

## <a name="database-mirroring"></a>Espelhamento de banco de dados

Você pode configurar o espelhamento de banco de dados com um FCI como parceiro de espelhamento de banco de dados. Configure-o usando [Transact-SQL (T-SQL)](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) em vez da GUI SQL Server Management Studio. Usar o T-SQL garantirá que o ponto de extremidade de espelhamento de banco de dados seja criado usando o DNN em vez do VNN. 

Por exemplo, se o nome DNS DNN for `dnnlsnr` e o ponto de extremidade de espelhamento de banco de dados for 7022, o seguinte trecho de código T-SQL configurará o parceiro de espelhamento de banco de dados: 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

Para acesso de cliente, a propriedade de **parceiro de failover** pode manipular o failover de espelhamento de banco de dados, mas não o failover de FCI. 

## <a name="msdtc"></a>MSDTC

O FCI pode participar de transações distribuídas coordenadas pelo Microsoft Coordenador de Transações Distribuídas (MSDTC). Embora o MSDTC clusterizado e o MSDTC local tenham suporte com o FCI DNN, no Azure, um balanceador de carga ainda é necessário para o MSDTC clusterizado. O DNN definido no FCI não substitui o requisito de Azure Load Balancer para o MSDTC clusterizado no Azure. 

## <a name="filestream"></a>FileStream

Embora o FileStream tenha suporte para um banco de dados em um FCI, não há suporte para o acesso a FileStream ou Filetable usando APIs de sistema de arquivos com DNN. 

## <a name="linked-servers"></a>Servidores vinculados

Há suporte para o uso de um servidor vinculado com um FCI DNN. Use o DNN diretamente para configurar um servidor vinculado ou use um alias de rede para mapear o VNN para o DNN. 


Por exemplo, para criar um servidor vinculado com o nome DNS DNN `dnnlsnr` para a instância nomeada `insta1` , use o seguinte comando TRANSACT-SQL (T-SQL):

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

Como alternativa, você pode criar o servidor vinculado usando o VNN (nome da rede virtual), mas, em vez disso, será necessário definir um alias de rede para mapear o VNN para o DNN. 

Por exemplo, para nome de instância `insta1` , nome de VNN `vnnname` e nome de DNN `dnnlsnr` , use o seguinte comando Transact-SQL (T-SQL) para criar um servidor vinculado usando o VNN:

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

Em seguida, crie um alias de rede para o qual Mapear `vnnname\insta1` `dnnlsnr\insta1` . 



## <a name="frequently-asked-questions"></a>Perguntas frequentes


- Qual versão do SQL Server oferece suporte a DNN? 

   SQL Server 2019 CU2 e posterior.

- Qual é o tempo de failover esperado quando DNN é usado?

   Para DNN, o tempo de failover será apenas o tempo de failover do FCI, sem nenhum tempo adicionado (como o tempo de investigação quando você estiver usando Azure Load Balancer).

- Há algum requisito de versão para clientes SQL para dar suporte a DNN com OLEDB e ODBC?

   Recomendamos o `MultiSubnetFailover=True` suporte de cadeia de conexão para DNN. Ele está disponível a partir do SQL Server 2012 (11. x).

- Há algum SQL Server alterações de configuração necessárias para usar o DNN? 

   SQL Server não requer nenhuma alteração de configuração para usar DNN, mas alguns recursos de SQL Server podem exigir mais consideração. 

- O DNN dá suporte a clusters de várias sub-redes?

   Sim. O cluster associa o DNN no DNS com os endereços IP físicos de todos os nós no cluster, independentemente da sub-rede. O cliente SQL tenta todos os endereços IP do nome DNS, independentemente da sub-rede. 



## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte: 

- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Instâncias de cluster de failover do SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

