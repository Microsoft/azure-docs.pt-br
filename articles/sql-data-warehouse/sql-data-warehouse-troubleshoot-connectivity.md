---
title: Solução de problemas do Azure SQL Data Warehouse | Microsoft Docs
description: Solução de problemas do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 2d7f56b65db09232af4fe7e198675ea0cfd64a25
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595485"
---
# <a name="troubleshooting-connectivity-issues"></a>Solucionar problemas de conectividade

Este artigo lista as técnicas de solução de problemas comuns em torno de conectar-se ao SQL Data Warehouse.
- [Verificar disponibilidade do serviço](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Verifique para operação de colocação em escala ou em pausa](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Verifique suas configurações de firewall](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Verifique suas configurações de ponto de extremidade de serviço/rede virtual](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Verificar os drivers mais recentes](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Verifique sua cadeia de conexão](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Problemas de conexão de intermitente](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Mensagens de erro comuns](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Verificar disponibilidade do serviço

Verifique se o serviço está disponível. No portal do Azure, vá para o SQL data warehouse, que você está tentando se conectar. No painel de Sumário à esquerda, clique em **diagnosticar e resolver problemas**.

![Selecione o Resource health](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

O status do seu SQL data warehouse será mostrado aqui. Se o serviço não estiver visível, como **disponível**, verifique mais etapas.

![Serviço disponível](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Se o Resource health mostra que o data warehouse está em pausa ou dimensionar, siga as orientações para retomar o data warehouse.

![Serviço pausado](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) informações adicionais sobre o Resource Health podem ser encontradas aqui.

## <a name="check-for-paused-or-scaling-operation"></a>Verifique para operação de colocação em escala ou em pausa

Verifique o portal para ver se o SQL data warehouse está em pausa ou dimensionar.

![Serviço pausado](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Se você vir que o serviço está em pausa ou dimensionar, verifico não é durante o agendamento de manutenção. No portal para o SQL data warehouse *visão geral*, você verá o agendamento de manutenção escolhido.

![Visão geral sobre o agendamento de manutenção](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Caso contrário, verifique com seu administrador de TI para verificar que a manutenção não é um evento agendado. Para retomar o SQL data warehouse, siga as etapas descritas [aqui](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Verifique suas configurações de firewall

O SQL Data Warehouse comunica-se pela porta 1433.   Se você estiver tentando se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 1433 talvez não seja permitido pelo firewall da rede. Nesse caso, você não pode se conectar ao seu servidor de banco de dados SQL, a menos que seu departamento de TI abra a porta 1433. Informações adicionais sobre configurações de firewall podem ser encontradas [aqui](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#manage-server-level-ip-firewall-rules-using-the-azure-portal).

## <a name="check-your-vnetservice-endpoint-settings"></a>Verifique suas configurações de ponto de extremidade de serviço/rede virtual

Se você estiver recebendo erros 40914 e 40615, consulte [descrição do erro e a resolução aqui](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Verificar os drivers mais recentes

### <a name="software"></a>Software

Verifique se que você estiver usando as ferramentas mais recentes para se conectar ao seu SQL data warehouse:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Drivers

Verifique se que você estiver usando as versões mais recentes do driver.  Usar uma versão mais antiga dos drivers pode resultar em comportamentos inesperados, pois os drivers mais antigos podem não dar suporte a novos recursos.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Verifique sua cadeia de conexão

Verifique se que as cadeias de conexão estão definidas corretamente.  Abaixo estão alguns exemplos.  Você pode encontrar informações adicionais em torno [cadeias de conexão aqui](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

Cadeia de conexão do ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Cadeia de caracteres de Conexão ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Cadeia de caracteres de Conexão PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Cadeia de conexão JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Problemas de conexão de intermitente

Verifique se você estiver tendo uma carga pesada no servidor com um grande número de solicitações em fila. Talvez você precise escalar verticalmente seu data warehouse para obter recursos adicionais.

## <a name="common-error-messages"></a>Mensagens de erro comuns

Erros 40914 e 40615, consulte o [descrição do erro e a resolução aqui](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Ainda está com problemas de conectividade?
Criar uma [tíquete de suporte](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) para que a equipe de engenharia pode dar suporte a você.
