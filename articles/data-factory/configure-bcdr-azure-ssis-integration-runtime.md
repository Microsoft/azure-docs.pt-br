---
title: Configurar o tempo de execução de integração do Azure-SSIS para continuidade dos negócios e recuperação de desastres (BCDR)
description: Este artigo descreve como configurar o tempo de execução de integração do Azure-SSIS no Azure Data Factory com o grupo de failover do banco de dados SQL/Instância Gerenciada do Azure para continuidade dos negócios e recuperação de desastres (BCDR).
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/05/2021
ms.openlocfilehash: a426ee39ba3c0f50b9a6c1fb9c7de1ef8e7291b2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566346"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>Configurar o tempo de execução de integração do Azure-SSIS para continuidade dos negócios e recuperação de desastres (BCDR) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

O banco de dados SQL do Azure/Instância Gerenciada e o SQL Server Integration Services (SSIS) no Azure Data Factory (ADF) podem ser combinados como a solução de PaaS (todas as plataformas como serviço) recomendada para a migração de SQL Server. Você pode implantar seus projetos do SSIS no SSISDB (banco de dados de catálogo do SSIS) hospedado pelo banco de dados SQL do Azure/Instância Gerenciada e executar seus pacotes SSIS no Azure SSIS Integration Runtime (IR) no ADF.

Para a continuidade dos negócios e a recuperação de desastres (BCDR), o banco de dados SQL/Instância Gerenciada do Azure pode ser configurado com um [grupo de replicação/failover geográfico](../azure-sql/database/auto-failover-group-overview.md), no qual o SSISDB em uma região primária do Azure com acesso de leitura/gravação (função primária) será replicado continuamente para uma região secundária com acesso somente leitura (função secundária). Quando ocorrer um desastre na região primária, um failover será disparado, em que o SSISDBs primário e o secundário trocarão funções.

Para BCDR, você também pode configurar um par de IR do Azure SSIS em espera duplo que funciona em sincronia com o grupo de failover do banco de dados SQL do Azure/Instância Gerenciada. Isso permite que você tenha um par de executar o IRs do Azure-SSIS que, a qualquer momento, apenas um pode acessar o SSISDB principal para buscar e executar pacotes, bem como gravar logs de execução de pacote (função primária), enquanto o outro só pode fazer o mesmo para pacotes implantados em outro lugar, por exemplo, em arquivos do Azure (função secundária). Quando ocorre o failover do SSISDB, o IRs do Azure-SSIS primário e o secundário também trocarão funções e, se ambos estiverem em execução, haverá um tempo de inatividade quase zero.

Este artigo descreve como configurar Azure-SSIS IR com o grupo de failover do banco de dados SQL do Azure/Instância Gerenciada para BCDR.

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>Configurar um par de Azure-SSIS IR em espera duplo com o grupo de failover do banco de dados SQL do Azure

Para configurar um par de Azure-SSIS IR em espera duplo que funciona em sincronia com o grupo de failover do banco de dados SQL do Azure, conclua as etapas a seguir.

1. Usando a interface do usuário do portal do Azure/ADF, você pode criar um novo Azure-SSIS IR com seu servidor de banco de dados SQL do Azure primário para hospedar o SSISDB na região primária. Se você tiver um Azure-SSIS IR existente que já esteja anexado ao banco SSIDB hospedado pelo seu servidor de banco de dados SQL principal do Azure e ainda estiver em execução, você precisará interrompê-lo primeiro para reconfigurá-lo. Este será o seu Azure-SSIS IR primário.

   Ao [selecionar para usar o SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) na página **configurações de implantação** do painel instalação do **Integration Runtime** , selecione também a caixa de seleção **usar par de Azure-SSIS Integration Runtime em espera dupla com failover do SSISDB** . Para **nome de par em espera duplo**, insira um nome para identificar seu par de IRS do Azure-SSIS primário e secundário. Quando você concluir a criação de sua Azure-SSIS IR primária, ela será iniciada e anexada a um SSISDB principal que será criado em seu nome com acesso de leitura/gravação. Se você acabou de reconfigurá-lo, será necessário reiniciá-lo.

1. Usando portal do Azure, você pode verificar se o SSISDB principal foi criado na página **visão geral** do seu servidor de banco de dados SQL do Azure primário. Depois de criado, você pode [criar um grupo de failover para seus servidores de banco de dados SQL do Azure primários e secundários e adicionar o SSISDB a ele](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#2---create-the-failover-group) na página **grupos de failover** . Depois que o grupo de failover for criado, você poderá verificar se o SSISDB principal foi replicado para um secundário com acesso somente leitura na página **visão geral** do seu servidor de banco de dados SQL do Azure secundário.

1. Usando a interface do usuário do portal do Azure/ADF, você pode criar outro Azure-SSIS IR com seu servidor de banco de dados SQL secundário do Azure para hospedar o SSISDB na região secundária. Este será o seu Azure-SSIS IR secundário. Para o BCDR completo, verifique se todos os recursos dos quais ele depende também são criados na região secundária, por exemplo, o armazenamento do Azure para armazenar arquivos/script de instalação personalizada, ADF para execuções de pacote de orquestração/agendamento, etc.

   Ao [selecionar para usar o SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) na página **configurações de implantação** do painel instalação do **Integration Runtime** , selecione também a caixa de seleção **usar par de Azure-SSIS Integration Runtime em espera dupla com failover do SSISDB** . Para **nome de par em espera duplo**, insira o mesmo nome para identificar seu par de IRS do Azure-SSIS primário e secundário. Quando você concluir a criação de sua Azure-SSIS IR secundária, ela será iniciada e anexada ao SSISDB secundário.

1. Se você quiser ter um tempo de inatividade quase zero quando ocorrer o failover do SSISDB, mantenha os dois IRs em execução do Azure-SSIS. Somente seu Azure-SSIS IR primário pode acessar o SSISDB principal para buscar e executar pacotes, bem como gravar logs de execução de pacote, enquanto o Azure-SSIS IR secundário só pode fazer o mesmo para pacotes implantados em outro lugar, por exemplo, em arquivos do Azure.

   Se você quiser minimizar o custo em execução, poderá parar o Azure-SSIS IR secundário depois que ele for criado. Quando ocorre o failover do SSISDB, seu IRs do Azure-SSIS primário e secundário trocará funções. Se o Azure-SSIS IR primário for interrompido, você precisará reiniciá-lo. Dependendo se ele é injetado em uma rede virtual e o método de injeção usado, ele levará em 5 minutos ou cerca de 20-30 minutos para ser executado.

1. Se você [usar o ADF para execuções de pacote de orquestração/agendamento](./how-to-invoke-ssis-package-ssis-activity.md), verifique se todos os pipelines do ADF relevantes com as atividades executar pacote SSIS e os gatilhos associados são copiados para o ADF secundário com os gatilhos inicialmente desabilitados. Quando ocorre o failover do SSISDB, você precisa habilitá-los.

1. Você pode [testar o grupo de failover do banco de dados SQL do Azure](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#3---test-failover) e verificar [Azure-SSIS ir página monitoramento no portal do ADF](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) , se o seu IRS principal e secundário do Azure-SSIS têm funções trocadas. 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>Configurar um par de Azure-SSIS IR em espera duplo com o grupo de failover do SQL Instância Gerenciada do Azure

Para configurar um par de Azure-SSIS IR em espera duplo que funciona em sincronia com o grupo de failover do SQL Instância Gerenciada do Azure, conclua as etapas a seguir.

1. Usando portal do Azure, você pode [criar um grupo de failover para suas instâncias gerenciadas do Azure SQL primárias e secundárias](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal) na página **grupos de failover** de seu instância gerenciada do SQL do Azure primário.

1. Usando a interface do usuário do portal do Azure/ADF, você pode criar um novo Azure-SSIS IR com seu Instância Gerenciada do Azure SQL primário para hospedar o SSISDB na região primária. Se você tiver um Azure-SSIS IR existente que já esteja anexado ao banco SSIDB hospedado por seu Instância Gerenciada SQL principal do Azure e ainda estiver em execução, você precisará interrompê-lo primeiro para reconfigurá-lo. Este será o seu Azure-SSIS IR primário.

   Ao [selecionar para usar o SSISDB](./create-azure-ssis-integration-runtime.md#creating-ssisdb) na página **configurações de implantação** do painel instalação do **Integration Runtime** , selecione também a caixa de seleção **usar par de Azure-SSIS Integration Runtime em espera dupla com failover do SSISDB** . Para **nome de par em espera duplo**, insira um nome para identificar seu par de IRS do Azure-SSIS primário e secundário. Quando você concluir a criação de sua Azure-SSIS IR primária, ela será iniciada e anexada a um SSISDB principal que será criado em seu nome com acesso de leitura/gravação. Se você acabou de reconfigurá-lo, será necessário reiniciá-lo. Você também pode verificar se o SSISDB principal foi replicado para um secundário com acesso somente leitura na página **visão geral** do seu instância gerenciada de SQL do Azure secundário.

1. Usando a interface do usuário do portal do Azure/ADF, você pode criar outro Azure-SSIS IR com seu Instância Gerenciada do Azure SQL secundário para hospedar o SSISDB na região secundária. Este será o seu Azure-SSIS IR secundário. Para o BCDR completo, verifique se todos os recursos dos quais ele depende também são criados na região secundária, por exemplo, o armazenamento do Azure para armazenar arquivos/script de instalação personalizada, ADF para execuções de pacote de orquestração/agendamento, etc.

   Ao [selecionar para usar o SSISDB](./create-azure-ssis-integration-runtime.md#creating-ssisdb) na página **configurações de implantação** do painel instalação do **Integration Runtime** , selecione também a caixa de seleção **usar par de Azure-SSIS Integration Runtime em espera dupla com failover do SSISDB** . Para **nome de par em espera duplo**, insira o mesmo nome para identificar seu par de IRS do Azure-SSIS primário e secundário. Quando você concluir a criação de sua Azure-SSIS IR secundária, ela será iniciada e anexada ao SSISDB secundário.

1. O Azure SQL Instância Gerenciada pode proteger dados confidenciais em bancos de dado, como o SSISDB, criptografando-os usando DMK (chave mestra de banco de dados). O próprio DMK, por sua vez, é criptografado usando a SMK (chave mestra de serviço) por padrão. No momento da gravação, o grupo de failover do Azure SQL Instância Gerenciada não Replica SMK do Instância Gerenciada do SQL do Azure primário, portanto, DMK e, por sua vez, o SSISDB não poderá ser descriptografado no Instância Gerenciada do SQL do Azure secundário após a ocorrência do failover. Para solucionar esse trabalho, você pode adicionar uma criptografia de senha para DMK a ser descriptografada no Instância Gerenciada de SQL do Azure secundário. Usando o SSMS, conclua as etapas a seguir.

   1. Execute o seguinte comando para o SSISDB em seu Instância Gerenciada de SQL do Azure primário para adicionar uma senha para criptografar DMK.

      ```sql
      ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'YourPassword'
      ```
   
   1. Execute o seguinte comando para o SSISDB nas suas instâncias gerenciadas primárias e secundárias do Azure SQL para adicionar a nova senha para descriptografar DMK.

      ```sql
      EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'YourPassword', @action = N'add'
      ```

1. Se você quiser ter um tempo de inatividade quase zero quando ocorrer o failover do SSISDB, mantenha os dois IRs em execução do Azure-SSIS. Somente seu Azure-SSIS IR primário pode acessar o SSISDB principal para buscar e executar pacotes, bem como gravar logs de execução de pacote, enquanto o Azure-SSIS IR secundário só pode fazer o mesmo para pacotes implantados em outro lugar, por exemplo, em arquivos do Azure.

   Se você quiser minimizar o custo em execução, poderá parar o Azure-SSIS IR secundário depois que ele for criado. Quando ocorre o failover do SSISDB, seu IRs do Azure-SSIS primário e secundário trocará funções. Se o Azure-SSIS IR primário for interrompido, você precisará reiniciá-lo. Dependendo se ele é injetado em uma rede virtual e o método de injeção usado, ele levará em 5 minutos ou cerca de 20-30 minutos para ser executado.

1. Se você [usar o agente do Azure SQL instância gerenciada para execuções de pacote de orquestração/agendamento](./how-to-invoke-ssis-package-managed-instance-agent.md), certifique-se de que todos os trabalhos relevantes do SSIS com suas etapas de trabalho e agendas associadas sejam copiados para seu instância gerenciada SQL secundário do Azure com as agendas inicialmente desabilitadas. Usando o SSMS, conclua as etapas a seguir.

   1. Para cada trabalho do SSIS, clique com o botão direito do mouse e selecione o **trabalho de script como**, **crie para** e novos itens de menu suspenso da **janela do editor de consultas** para gerar seu script.

      ![Gerar script de trabalho do SSIS](media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png)

   1. Para cada script de trabalho do SSIS gerado, localize o comando para executar o `sp_add_job` procedimento armazenado e modifique/remova a atribuição de valor para `@owner_login_name` argumento, conforme necessário.

   1. Para cada script de trabalho do SSIS atualizado, execute-o em seu Instância Gerenciada do Azure SQL secundário para copiar o trabalho com suas etapas de trabalho e agendas associadas.

   1. Usando o script a seguir, crie um novo trabalho do T-SQL para habilitar/desabilitar agendas de trabalho do SSIS com base na função SSISDB primária/secundária, respectivamente, nas instâncias gerenciadas primárias e secundárias do Azure SQL e execute-as regularmente. Quando ocorre o failover do SSISDB, as agendas de trabalho do SSIS que foram desabilitadas serão habilitadas e vice-versa.

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. Se você [usar o ADF para execuções de pacote de orquestração/agendamento](./how-to-invoke-ssis-package-ssis-activity.md), verifique se todos os pipelines do ADF relevantes com as atividades executar pacote SSIS e os gatilhos associados são copiados para o ADF secundário com os gatilhos inicialmente desabilitados. Quando ocorre o failover do SSISDB, você precisa habilitá-los.

1. Você pode [testar o grupo de failover do Azure SQL instância gerenciada](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal#test-failover) e verificar [Azure-SSIS ir página de monitoramento no portal do ADF](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) se o seu IRS principal e secundário do Azure-SSIS têm funções trocadas. 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>Anexar um novo Azure-SSIS IR ao SSISDB existente hospedado pelo banco de dados SQL/Instância Gerenciada do Azure

Se ocorrer um desastre e afetar o Azure-SSIS IR existente, mas não o banco de dados SQL do Azure/Instância Gerenciada na mesma região, você poderá substituí-lo por um novo em outra região. Para anexar o SSISDB existente hospedado pelo banco de dados SQL/Instância Gerenciada do Azure a um novo Azure-SSIS IR, conclua as etapas a seguir.

1. Se o Azure-SSIS IR existente ainda estiver em execução, você precisará interrompê-lo primeiro usando a interface do usuário do portal do Azure/ADF ou Azure PowerShell. Se o desastre também impactar o ADF na mesma região, você poderá ignorar esta etapa.

1. Usando o SSMS, execute o seguinte comando para o SSISDB em seu banco de dados SQL/Instância Gerenciada do Azure para atualizar os metadados que permitirão conexões do seu novo ADF/Azure-SSIS IR.

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. Usando a [interface do usuário do portal do Azure/ADF](./create-azure-ssis-integration-runtime.md#use-the-azure-portal-to-create-an-integration-runtime) ou [Azure PowerShell](./create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime), crie seu novo ADF/Azure-SSIS ir chamado *YourNewADF* / *YourNewAzureSSISIR*, respectivamente, em outra região. Se você usar a interface do usuário do portal do Azure/ADF, poderá ignorar o erro testar conexão na página **configurações de implantação** do painel **instalação do Integration Runtime** .

## <a name="next-steps"></a>Próximas etapas

Você pode considerar essas outras opções de configuração para seu Azure-SSIS IR:

- [Configurar repositórios de pacotes para seu Azure-SSIS IR](./create-azure-ssis-integration-runtime.md#creating-azure-ssis-ir-package-stores)

- [Configurar configurações personalizadas para seu Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [Configurar injeção de rede virtual para seu Azure-SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md)

- [Configurar o IR auto-hospedado como um proxy para seu Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)