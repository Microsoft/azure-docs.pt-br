---
title: Interoperabilidade de recursos com grupos de disponibilidade e ouvinte de DNN
description: 'Saiba mais sobre as considerações adicionais ao trabalhar com determinados recursos de SQL Server e um ouvinte DNN (nome de rede distribuída) com um grupo de disponibilidade Always On em SQL Server em VMs do Azure. '
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
ms.date: 10/08/2020
ms.author: mathoma
ms.openlocfilehash: 19b4b7407468b19419e2f85193b1f8fb6ace39c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359397"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>Interoperabilidade de recursos com o AG e o ouvinte DNN 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Há determinados recursos de SQL Server que dependem de um VNN (nome de rede virtual) embutido em código. Dessa forma, ao usar o ouvinte DNN (nome de rede distribuída) com seu grupo de disponibilidade Always On e SQL Server em VMs do Azure, pode haver algumas considerações adicionais. 

Este artigo detalha SQL Server recursos e a interoperabilidade com o ouvinte de DNN do grupo de disponibilidade. 


## <a name="client-drivers"></a>Drivers de cliente

Para drivers ODBC, OLEDB, ADO.NET, JDBC, PHP e Node.js, os usuários precisam especificar explicitamente o nome do ouvinte e a porta do DNN como o nome do servidor na cadeia de conexão. Para garantir a conectividade rápida no failover, adicione `MultiSubnetFailover=True` à cadeia de conexão se o cliente SQL oferecer suporte a ela. 

## <a name="tools"></a>Ferramentas

Os usuários de [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)e [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) precisam especificar explicitamente o nome do ouvinte DNN e a porta como o nome do servidor na cadeia de conexão para se conectar ao ouvinte. 

No momento, não há suporte para a criação do ouvinte DNN por meio da GUI do SSMS (SQL Server Management Studio). 


## <a name="availability-groups-and-fci"></a>Grupos de disponibilidade e FCI

Você pode configurar um grupo de disponibilidade Always On usando uma FCI (instância de cluster de failover) como uma das réplicas. Para que essa configuração funcione com o ouvinte DNN, a [instância de cluster de failover também deve usar o DNN](failover-cluster-instance-distributed-network-name-dnn-configure.md) , pois não há como colocar o endereço IP virtual FCI na lista de IPS do AG DNN. 

Nessa configuração, a URL do ponto de extremidade de espelhamento para a réplica FCI precisa usar o DNN FCI. Da mesma forma, se o FCI for usado como uma réplica somente leitura, o roteamento somente leitura para a réplica FCI precisará usar o FCI DNN. 

O formato para o ponto de extremidade de espelhamento é: `ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'` . 

Por exemplo, se o nome DNS FCI DNN for `dnnlsnr` e `5022` for a porta do ponto de extremidade de espelhamento da FCI, o trecho de código TRANSACT-SQL (T-SQL) para criar a URL do ponto de extremidade será semelhante a: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Da mesma forma, o formato da URL de roteamento somente leitura é: `TCP://<FCI DNN DNS name>:<SQL Server instance port>` . 

Por exemplo, se o nome DNS do DNN for `dnnlsnr` , e `1444` for a porta usada pelo destino somente leitura SQL Server FCI, o trecho de código T-SQL para criar a URL de roteamento somente leitura terá a seguinte aparência: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

Você pode omitir a porta na URL se ela for a porta padrão 1433. Para uma instância nomeada, configure uma porta estática para a instância nomeada e especifique-a na URL de roteamento somente leitura.  

## <a name="distributed-availability-group"></a>Grupo de disponibilidade distribuída

Atualmente, não há suporte para grupos de disponibilidade distribuída com o ouvinte DNN. 

## <a name="replication"></a>Replicação

Replicação transacional, de mesclagem e de instantâneos, todos dão suporte à substituição do ouvinte VNN com a porta e o ouvinte DNN em objetos de replicação que se conectam ao ouvinte. 

Para obter mais informações sobre como usar a replicação com grupos de disponibilidade, consulte [Publisher e AG](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server), [subscriber e AG](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server), e [distribuidor e AG](/sql/relational-databases/replication/configure-distribution-availability-group).

## <a name="msdtc"></a>MSDTC

O MSDTC local e o de cluster são suportados, mas o MSDTC usa uma porta dinâmica, que requer um Azure Load Balancer padrão para configurar a porta de alta disponibilidade. Assim, a VM deve usar uma reserva de IP padrão ou não pode ser exposta à Internet. 

Defina duas regras, uma para a porta 135 do mapeador de ponto de extremidade RPC e outra para a porta real do MSDTC. Após o failover, modifique a regra de LB para a nova porta MSDTC depois que ela for alterada no novo nó. 

Se o MSDTC for local, certifique-se de permitir a comunicação de saída. 

## <a name="distributed-query"></a>Consulta distribuída 

A consulta distribuída depende de um servidor vinculado, que pode ser configurado usando o ouvinte e a porta do AG DNN. Se a porta não for 1433, escolha a opção **usar outra fonte de dados** no SQL Server Management Studio (SSMS) ao configurar o servidor vinculado. 

## <a name="filestream"></a>FileStream

FileStream tem suporte, mas não para cenários em que os usuários acessam o compartilhamento de arquivos com escopo usando a API de arquivo do Windows. 

## <a name="filetable"></a>FileTable

A filetable tem suporte, mas não para cenários em que os usuários acessam o compartilhamento de arquivos com escopo usando a API de arquivo do Windows. 

## <a name="linked-servers"></a>Servidores vinculados

Configure o servidor vinculado usando o nome e a porta do ouvinte AG DNN. Se a porta não for 1433, escolha a opção **usar outra fonte de dados** no SQL Server Management Studio (SSMS) ao configurar o servidor vinculado. 


## <a name="frequently-asked-questions"></a>Perguntas frequentes


- Qual versão SQL Server traz suporte a ouvinte AG DNN? 

   SQL Server 2019 CU8 e posterior.

- Qual é o tempo de failover esperado quando o ouvinte de DNN é usado?

   Para o ouvinte DNN, o tempo de failover será apenas o tempo de failover do AG, sem qualquer hora adicional (como o tempo de teste quando você estiver usando Azure Load Balancer).

- Há algum requisito de versão para clientes SQL para dar suporte a DNN com OLEDB e ODBC?

   Recomendamos o `MultiSubnetFailover=True` suporte de cadeia de conexão para o ouvinte DNN. Ele está disponível a partir do SQL Server 2012 (11. x).

- Há algum SQL Server alterações de configuração necessárias para usar o ouvinte DNN? 

   SQL Server não requer nenhuma alteração de configuração para usar DNN, mas alguns recursos de SQL Server podem exigir mais consideração. 

- O DNN dá suporte a clusters de várias sub-redes?

   Sim. O cluster associa o DNN no DNS com os endereços IP físicos de todas as réplicas na disponibilidade, independentemente da sub-rede. O cliente SQL tenta todos os endereços IP do nome DNS, independentemente da sub-rede. 



## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte: 

- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Grupo de disponibilidade AlwaysOn](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)

