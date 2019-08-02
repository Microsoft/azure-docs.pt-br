---
title: Modelos do Azure Resource Manager para o Banco de Dados SQL | Microsoft Docs
description: Use modelos do Azure Resource Manager para criar e configurar um Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 81f3e4beb29f21f1b752a876827e9b35856c713a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566890"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Modelos do Azure Resource Manager para o Banco de Dados SQL do Azure

Os modelos do Azure Resource Manager permitem que você defina a infraestrutura como código e implante suas soluções na nuvem do Azure.

## <a name="single-database--elastic-pool"></a>Banco de dados individual e pool elástico

A tabela a seguir inclui links para modelos do Azure Resource Manager para o Banco de Dados SQL do Azure.

| |  |
|---|---|
| [Banco de dados individual](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Esse modelo do Azure Resource Manager cria um Banco de Dados SQL do Azure individual com um servidor lógico e configura as regras de firewall. |
| [Servidor lógico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Esse modelo do Azure Resource Manager cria um servidor lógico para o Banco de Dados SQL do Azure. |
| [Pool elástico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Esse modelo permite que você implante um novo pool elástico com seu novo SQL Server associado e novos Bancos de Dados SQL para atribuir a ele. |
| [Grupos de failover](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Esse modelo cria dois servidores lógicos do SQL do Azure, um banco de dados SQL e um grupo de failover.|
| [Detecção de Ameaças](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Esse modelo permite que você implante um servidor lógico do SQL do Azure e um conjunto de Bancos de Dados SQL do Azure com a Detecção de Ameaças habilitada, com um endereço de email para alertas para cada banco de dados. A Detecção de Ameaças faz parte da oferta ATP (Proteção Avançada contra Ameaças ao SQL) e fornece uma camada de segurança que responde às ameaças potenciais em servidores e bancos de dados SQL.|
| [Auditoria no Armazenamento de Blobs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Esse modelo permite que você implante um servidor lógico do SQL do Azure com a Auditoria habilitada para gravar logs de auditoria em um armazenamento de blobs. A auditoria do Banco de Dados SQL do Azure acompanha eventos do banco de dados e os grava em um log de auditoria que pode colocado em sua conta de armazenamento do Azure, no workspace do OMS ou nos Hubs de Eventos.|
| [Auditoria no Hub de Eventos do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Esse modelo permite que você implante um servidor do SQL do Azure com a Auditoria habilitada para gravar logs de auditoria em um Hub de Eventos existente. Para enviar eventos de auditoria para o Hub de Eventos, defina as configurações de auditoria com `Enabled` `State` e defina `IsAzureMonitorTargetEnabled` como `true`. Além disso, defina as Configurações de Diagnóstico com a categoria de logs de diagnóstico `SQLSecurityAuditEvents` no banco de dados `master` (para a auditoria no nível do servidor). A auditoria do Banco de Dados SQL do Azure e do SQL Data Warehouse acompanha eventos do banco de dados e os grava em um log de auditoria que pode ser colocado em sua conta de armazenamento do Azure, no workspace do OMS ou nos Hubs de Eventos.|
| [Aplicativo Web do Azure com o Banco de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Essa amostra cria um Aplicativo Web do Azure gratuito e um Banco de Dados SQL no nível de serviço "Básico".|
| [Aplicativo Web do Azure e Cache Redis com o Banco de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Esse modelo cria um Aplicativo Web, um Cache Redis e um Banco de Dados SQL no mesmo grupo de recursos, além de criar duas cadeias de conexão no Aplicativo Web para o Banco de Dados SQL e o Cache Redis.|
| [Importar dados do armazenamento de blobs usando o ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Esse modelo do Azure Resource Manager cria um Azure Data Factory V2 que copia dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL.|
| [Cluster HDInsight com um Banco de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Esse modelo permite que você crie um cluster HDInsight, um servidor do Banco de Dados SQL, um Banco de Dados SQL e duas tabelas. Esse modelo é usado pelo [artigo Usar o Sqoop com o Hadoop no HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Aplicativo Lógico do Azure que executa um procedimento armazenado SQL de acordo com um agendamento](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Esse modelo permite que você crie um Aplicativo Lógico que executará um procedimento armazenado SQL de acordo com um agendamento. Os argumentos para o procedimento podem ser colocados na seção do corpo do modelo.|

## <a name="managed-instance"></a>Instância Gerenciada

A tabela a seguir inclui links para modelos do Azure Resource Manager para a Instância Gerenciada do Banco de Dados SQL do Azure.

| |  |
|---|---|
| [Instância Gerenciada em uma nova VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Esse modelo do Azure Resource Manager cria uma VNet do Azure configurada e uma Instância Gerenciada na VNet. |
| [Ambiente de rede da Instância Gerenciada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Essa implantação criará uma Rede Virtual do Azure configurada com duas sub-redes – uma que será dedicada para as Instâncias Gerenciadas e a outra na qual você poderá colocar outros recursos (por exemplo, VMs, ambientes do Serviço de Aplicativo etc.). Esse modelo criará um ambiente de rede configurado de forma apropriada em que você poderá implantar as Instâncias Gerenciadas. |
| [Instância Gerenciada com conexão P2S](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Essa implantação criará uma Rede Virtual do Azure com duas sub-redes `ManagedInstance` e `GatewaySubnet`. A Instância Gerenciada será implantada na sub-rede da ManagedInstance. O gateway de rede virtual será criado na sub-rede `GatewaySubnet` e configurado para a conexão VPN ponto a site. |
| [Instância Gerenciada com uma Máquina Virtual](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Essa implantação criará uma Rede Virtual do Azure com duas sub-redes `ManagedInstance` e `Management`. A Instância Gerenciada será implantada na sub-rede `ManagedInstance`. A máquina virtual com a última versão do SSMS (SQL Server Management Studio) será implantada na sub-rede `Management`. |
