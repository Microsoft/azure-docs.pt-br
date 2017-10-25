---
title: Gerenciar bancos de dados no Azure SQL Data Warehouse | Microsoft Docs
description: "Visão geral do gerenciamento de bancos de dados do SQL Data Warehouse. Inclui ferramentas de gerenciamento, DWUs e escalar o desempenho horizontalmente, solucionar problemas de desempenho de consulta, estabelecer políticas de segurança e restaurar um banco de dados contra dados corrompidos ou de uma interrupção regional."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 8576fdb3-71fe-4b3b-a4e0-5e8a7f148acf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: b14d0aad5a1f50c225391dbab27ec6240423a65a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Gerenciar bancos de dados no SQL Data Warehouse do Azure
O SQL Data Warehouse automatiza muitos aspectos do gerenciamento de seus bancos de dados. Por exemplo, para aumentar o desempenho, basta ajustar para o nível certo de recursos de computação e pagar por eles, para então deixar o SQL Data Warehouse fazer todo o trabalho de escalar horizontalmente e de reverter essa escala.

Sem dúvida, você desejará monitorar sua carga de trabalho para identificar suas necessidades de desempenho, bem como solucionar problemas em consultas de execução longa. Você também precisará executar algumas tarefas de segurança para gerenciar permissões para usuários e logons.

Esta visão geral aborda esses aspectos do gerenciamento do SQL Data Warehouse.

* Ferramentas de gerenciamento
* Computação de escala
* Pausar e retomar
* Práticas recomendadas de desempenho
* Monitoramento de consulta
* Segurança
* Backup e restauração

## <a name="management-tools"></a>Ferramentas de gerenciamento
Você pode usar uma variedade de ferramentas para gerenciar bancos de dados no SQL Data Warehouse. Ao gerenciar bancos de dados, você desenvolverá preferências de ferramenta para cada tipo de tarefa, que você precisa executar.

### <a name="azure-portal"></a>Portal do Azure
O [Portal do Azure][Azure portal] é um portal baseado na Web, no qual você pode criar, atualizar e excluir bancos de dados e monitorar recursos do banco de dados. Essa ferramenta será excelente se você estiver começando a usar o Azure, se estiver gerenciando uma pequena quantidade de bancos de dados de data warehouse ou se precisar fazer alguma coisa rapidamente.

Para começar a usar o Portal do Azure, confira [Criar um SQL Data Warehouse (Portal do Azure)][Create a SQL Data Warehouse (Azure portal)].

### <a name="sql-server-data-tools-in-visual-studio"></a>SQL Server Data Tools no Visual Studio
O SSDT ([SQL Server Data Tools][SQL Server Data Tools]) no Visual Studio permite que você se conecte, gerencie e desenvolva seus bancos de dados. Se você for um desenvolvedor de aplicativos familiarizado com o Visual Studio ou com outros ambientes de desenvolvimento integrados (IDEs), tente usar o SSDT no Visual Studio.

O SSDT inclui o Pesquisador de Objetos do SQL Server, que permite a visualização, conexão e execução de scripts em bancos de dados do SQL Data Warehouse. Para conectar-se rapidamente ao SQL Data Warehouse, você pode simplesmente clicar no botão **Abrir no Visual Studio** na barra de comandos ao exibir os detalhes do banco de dados no Portal Clássico do Azure.  

Para uma introdução ao SSDT no Visual Studio, confira [Conectar-se ao SQL Data Warehouse do Azure com o Visual Studio][Query Azure SQL Data Warehouse with Visual Studio].

### <a name="command-line-tools"></a>Ferramentas da linha de comando
Ferramentas de linha de comando são ideais para automatizar suas cargas de trabalho.  PowerShell e sqlcmd são duas formas incríveis de automatizar os processos.  Recomendamos essas ferramentas para gerenciar uma grande quantidade de servidores lógicos e implantar alterações de recursos em um ambiente de produção, pois as tarefas necessárias podem ser incluídas em script e automatizadas.

### <a name="dynamic-management-views"></a>Exibições de gerenciamento dinâmico
DMVs são a base do gerenciamento de SQL Data Warehouse. Quase todas as informações que aparecem no portal dependem de DMVs. Para ver uma lista de DMVs do SQL Data Warehouse, confira [Exibições do sistema do SQL Data Warehouse][SQL Data Warehouse system views].

Para começar, confira [Conectar e consultar com sqlcmd][Connect and query with sqlcmd] e [Criar um banco de dados (PowerShell)][Create a database (PowerShell)].

## <a name="scale-compute"></a>Computação de escala
No SQL Data Warehouse você pode, com rapidez, escalar horizontalmente o desempenho ou reverter esse processo, aumentando ou diminuindo os recursos de computação de CPU, memória e largura de banda de E/S. Para dimensionar o desempenho, tudo o que você precisa fazer é ajustar o número de DWUs (unidades de data warehouse) que o SQL Data Warehouse aloca para seu banco de dados. O SQL Data Warehouse faz rapidamente a alteração e trata de todas as alterações subjacentes de hardware e software.

Para saber mais sobre o dimensionamento de DWUs, confira [Dimensionar o desempenho].

## <a name="pause-and-resume"></a>Pausar e retomar
Para economizar custos, é possível pausar e retomar os recursos de computação sob demanda. Por exemplo, se você não usar banco de dados durante a noite e nos finais de semana, você poderá pausá-lo durante esses períodos e retomá-lo durante o dia. Você não será cobrado por DWUs enquanto o banco de dados estiver em pausa.

Para saber mais, confira [Pausar computação][Pause compute] e [Retomar a computação][Resume compute].

## <a name="performance-best-practices"></a>Práticas recomendadas de desempenho
Ao ser apresentado a uma nova tecnologia, descobrir as dicas e truques que funcionam melhor desde o início pode economizar muito tempo.  Você encontrará as práticas recomendadas ao longo de muitos tópicos.

Para ver um resumo das considerações mais importantes no desenvolvimento de sua carga de trabalho, confira [Práticas recomendadas para o SQL Data Warehouse][SQL Data Warehouse Best Practices].

## <a name="query-monitoring"></a>Monitoramento de consulta
Às vezes, uma consulta está sendo executada por muito tempo, mas você não tem certeza de qual é o culpada. O SQL Data Warehouse tem DMVs (exibições de gerenciamento dinâmico) que você pode usar para descobrir qual consulta está demorando tempo demais.

Para encontrar consultas de longa execução, confira [Monitorar sua carga de trabalho usando DMVs][Monitor your workload using DMVs].

## <a name="security"></a>Segurança
Para manter um sistema seguro, você deve estar alerta e protegê-lo contra todo tipo de acesso não autorizado. Um sistema de segurança precisa verificar se as regras de firewall estão em vigor de modo que apenas endereços IP autorizados possam se conectar. Ele precisa de autenticação adequada das credenciais do usuário. Depois que um usuário se conectou ao banco de dados, o usuário só deve ter permissões para executar um número mínimo de ações. Para proteger dados, você pode usar criptografia. Também é importante ter auditoria e acompanhamento para que você possa retraçar eventos em caso de qualquer atividade suspeita.

Para saber sobre como gerenciar a segurança, vá até a [Visão geral de segurança][Security overview].

## <a name="backup-and-restore"></a>Backup e restauração
Ter backups confiáveis de seus dados é uma parte essencial de qualquer banco de dados de produção. O SQL Data Warehouse mantém seus dados seguros fazendo backup automaticamente de seus bancos de dados ativos em intervalos regulares. Esses backups permitem que você se recupere de cenários em que corromper ou descartar acidentalmente seus dados ou banco de dados.  Para agendamento de backup de dados, política de retenção e como restaurar um banco de dados, confira [Restaurar do instantâneo][Restore from snapshot].

## <a name="next-steps"></a>Próximas etapas
Usar bons princípios de design de banco de dados tornará mais fácil gerenciar seus bancos de dados no SQL Data Warehouse. Para saber mais, vá até a [Visão geral de desenvolvimento][Development overview].

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse (Azure Portal)]: sql-data-warehouse-get-started-provision.md
[Create a database (PowerShell)]: sql-data-warehouse-get-started-provision-powershell.md
[connection]: sql-data-warehouse-develop-connections.md
[Query Azure SQL Data Warehouse with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Connect and query with sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Development overview]: sql-data-warehouse-overview-develop.md
[Monitor your workload using DMVs]: sql-data-warehouse-manage-monitor.md
[Pause compute]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Restore from snapshot]: sql-data-warehouse-restore-database-overview.md
[Resume compute]: sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Dimensionar o desempenho]: sql-data-warehouse-manage-compute-overview.md#scale-compute
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SQL Data Warehouse Best Practices]: sql-data-warehouse-best-practices.md
[SQL Data Warehouse system views]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure portal]: http://portal.azure.com/
