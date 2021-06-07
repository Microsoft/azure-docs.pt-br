---
title: Modelos do Azure Resource Manager – Banco de Dados SQL do Azure e Instância Gerenciada de SQL
description: Use modelos do Azure Resource Manager para criar e configurar um Banco de Dados SQL do Azure e uma Instância Gerenciada de SQL do Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 5202145af6f60f34bdce57aa94f4fec92a8b227f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92677259"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Modelos do Azure Resource Manager para o Banco de Dados SQL do Azure e a Instância Gerenciada de SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Os modelos do Azure Resource Manager permitem que você defina a infraestrutura como um código e implante suas soluções na nuvem do Azure para o Banco de Dados SQL do Azure e a Instância Gerenciada de SQL do Azure.

## <a name="azure-sql-database"></a>[Banco de Dados SQL do Azure](#tab/single-database)

A tabela a seguir inclui links para modelos do Azure Resource Manager para o Banco de Dados SQL do Azure.

|Link |Descrição|
|---|---|
| [Banco de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Esse modelo do Azure Resource Manager cria um banco de dados individual no Banco de Dados SQL do Azure e configura regras de firewall de IP no nível do servidor. |
| [Servidor](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Esse modelo do Azure Resource Manager cria um servidor para o Banco de Dados SQL do Azure. |
| [Pool elástico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Esse modelo permite que você implante um pool elástico e atribua bancos de dados a ele. |
| [Grupos de failover](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Esse modelo cria dois servidores, um banco de dados individual e um grupo de failover no Banco de Dados SQL do Azure.|
| [Detecção de Ameaças](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Esse modelo permite que você implante um servidor e um conjunto de bancos de dados com a Detecção de Ameaças habilitada, com um endereço de email para alertas para cada banco de dados. A Detecção de Ameaças faz parte da oferta ATP (Proteção Avançada contra Ameaças) ao SQL e fornece uma camada de segurança que responde às ameaças potenciais em servidores e bancos de dados.|
| [Auditoria no Armazenamento de blobs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Esse modelo permite que você implante um servidor com a auditoria habilitada para gravar logs de auditoria em um Armazenamento de blobs. A auditoria do Banco de Dados SQL do Azure acompanha eventos do banco de dados e os grava em um log de auditoria que pode colocado em sua conta de armazenamento do Azure, no workspace do OMS ou nos Hubs de Eventos.|
| [Auditoria no Hub de Eventos do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Esse modelo permite que você implante um servidor com a auditoria habilitada para gravar logs de auditoria em um hub de eventos existente. Para enviar eventos de auditoria para os Hubs de Eventos, defina as configurações de auditoria com `Enabled` `State` e defina `IsAzureMonitorTargetEnabled` como `true`. Além disso, defina as Configurações de Diagnóstico com a categoria de logs `SQLSecurityAuditEvents` no banco de dados `master` (para a auditoria no nível do servidor). A auditoria acompanha eventos do banco de dados e os grava em um log de auditoria que pode colocado na sua conta de armazenamento do Azure, no workspace do OMS ou nos Hubs de Eventos.|
| [Aplicativo Web do Azure com o Banco de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Esse exemplo cria um aplicativo Web do Azure gratuito e um banco de dados no Banco de Dados SQL do Azure o nível de serviço "Básico".|
| [Aplicativo Web do Azure e Cache Redis com o Banco de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Esse modelo cria um aplicativo Web, um Cache Redis e um banco de dados no mesmo grupo de recursos e duas cadeias de conexão no aplicativo Web para o banco de dados e o Cache Redis.|
| [Importar dados do Armazenamento de blobs usando o ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Esse modelo do Azure Resource Manager cria uma instância do Azure Data Factory V2 que copia dados do Armazenamento de blobs do Azure para o Banco de Dados SQL.|
| [Cluster HDInsight com um banco de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Esse modelo permite que você crie um cluster HDInsight, um servidor SQL lógico, um banco de dados e duas tabelas. Esse modelo é usado pelo [artigo Usar o Sqoop com o Hadoop no HDInsight](../../hdinsight/hadoop/hdinsight-use-sqoop.md). |
| [Aplicativo Lógico do Azure que executa um procedimento armazenado SQL de acordo com um agendamento](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Esse modelo permite que você crie um aplicativo lógico que executará um procedimento armazenado SQL de acordo com um agendamento. Os argumentos para o procedimento podem ser colocados na seção do corpo do modelo.|

## <a name="azure-sql-managed-instance"></a>[Instância Gerenciada do SQL do Azure](#tab/managed-instance)

A tabela a seguir inclui links para modelos do Azure Resource Manager da Instância Gerenciada de SQL do Azure.

|Link|Descrição|
|---|---|
| [Instância Gerenciada de SQL em uma nova VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Esse modelo do Azure Resource Manager cria uma rede virtual do Azure configurada e a instância gerenciada na rede virtual. |
| [Ambiente de rede da Instância Gerenciada de SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Essa implantação criará uma rede virtual do Azure configurada com duas sub-redes, uma que será dedicada para as instâncias gerenciadas e a outra na qual você poderá colocar outros recursos (por exemplo, VMs, ambientes do Serviço de Aplicativo etc.). Esse modelo criará um ambiente de rede configurado de maneira apropriada em que você poderá implantar as instâncias gerenciadas. |
| [Instância Gerenciada de SQL com a conexão P2S](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Essa implantação criará uma rede virtual do Azure com duas sub-redes, `ManagedInstance` e `GatewaySubnet`. A Instância Gerenciada de SQL será implantada na sub-rede ManagedInstance. Um gateway de rede virtual será criado na sub-rede `GatewaySubnet` e configurado para a conexão VPN ponto a site. |
| [Instância Gerenciada de SQL com uma máquina virtual](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Essa implantação criará uma rede virtual do Azure com duas sub-redes, `ManagedInstance` e `Management`. A Instância Gerenciada de SQL será implantada na sub-rede `ManagedInstance`. Uma máquina virtual com a última versão do SSMS (SQL Server Management Studio) será implantada na sub-rede `Management`. |

---