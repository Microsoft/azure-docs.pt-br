---
title: Agente de sincronização de dados para Sincronização de Dados SQL
description: Saiba como instalar e executar o agente de sincronização de dados para Sincronização de Dados SQL no Azure a fim de sincronizar dados com bancos de SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: ed8d51adf5a93b470f287383a4d3eeb866b15236
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92791453"
---
# <a name="data-sync-agent-for-sql-data-sync"></a>Agente de sincronização de dados para Sincronização de Dados SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Sincronize os dados com os bancos de SQL Server Instalando e Configurando o agente de sincronização de dados para Sincronização de Dados SQL no Azure. Para obter mais informações sobre a Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> No momento, a Sincronização de Dados SQL **não** dá suporte à Instância Gerenciada de SQL do Azure.

## <a name="download-and-install"></a>Baixar e instalar

Para baixar o agente de sincronização de dados, vá para [sincronização de dados SQL agente](https://www.microsoft.com/download/details.aspx?id=27693). Para atualizar o agente de sincronização de dados, instale o agente no mesmo local que o agente antigo e ele substituirá o original.

### <a name="install-silently"></a>Instalar silenciosamente

Para instalar silenciosamente o Agente de Sincronização de Dados a partir do prompt de comando, digite um comando semelhante ao exemplo a seguir. Verifique o nome de arquivo do arquivo .msi baixado e forneça seus próprios valores para os argumentos **TARGETDIR** e **SERVICEACCOUNT**.

- Se você não fornecer um valor para **TARGETDIR**, o valor padrão será `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`.

- Se você fornecer `LocalSystem` como o valor de uma **conta**, use SQL Server autenticação ao configurar o agente para se conectar ao SQL Server.

- Se você fornecer uma conta de usuário de domínio ou uma conta de usuário local como o valor de **SERVICEACCOUNT**, você também precisará fornecer a senha com o argumento **SERVICEPASSWORD**. Por exemplo, `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-a-sql-server-database"></a>Sincronizar dados com um banco de dado SQL Server

Para configurar o agente de sincronização de dados para que você possa sincronizar dados com um ou mais bancos de SQL Server, consulte [Adicionar um banco de SQL Server](sql-data-sync-sql-server-configure.md#add-on-prem).

## <a name="data-sync-agent-faq"></a><a name="agent-faq"></a> Perguntas frequentes sobre o Agente de Sincronização de Dados

### <a name="why-do-i-need-a-client-agent"></a>Por que preciso um agente cliente

O serviço de Sincronização de Dados SQL se comunica com os bancos de dados do SQL Server através do agente do cliente. Esse recurso de segurança impede a comunicação direta com bancos de dados por trás de um firewall. Quando o serviço de Sincronização de Dados SQL se comunica com o agente, ele faz isso usando conexões criptografadas e um token exclusivo ou *chave do agente*. Os bancos de dados do SQL Server autenticam o agente usando a chave de agente e a cadeia de caracteres de conexão. Esse design fornece um alto nível de segurança para seus dados.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Quantas instâncias da interface do usuário do agente local podem ser executadas

Pode ser executada apenas uma instância da interface do usuário.

### <a name="how-can-i-change-my-service-account"></a>Como posso alterar minha conta de serviço

Depois de instalar um agente de cliente, a única maneira de alterar a conta de serviço é desinstalá-lo e instalar um novo agente de cliente com a nova conta de serviço.

### <a name="how-do-i-change-my-agent-key"></a>Como altero minha chave de agente

Uma chave de agente só pode ser usada uma vez por um agente. Ela não pode ser reutilizada quando você remove e reinstala um novo agente, nem pode ser usado por vários agentes. Se você precisar criar uma nova chave para um agente existente, você deve ter certeza de que a mesma chave está registrada com o agente do cliente e o serviço de Sincronização de Dados SQL.

### <a name="how-do-i-retire-a-client-agent"></a>Como fazer para desativar um agente cliente

Para invalidar ou desativar um agente imediatamente, regenere a chave no portal, mas não a envie na interface de usuário do agente. Gerar novamente uma chave invalida a chave anterior, independentemente se o agente correspondente estiver online ou offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Como faço para mover um agente cliente para outro computador

Se você deseja executar o agente local em um computador diferente que está atualmente no, siga estas etapas:

1. Instale o agente no computador desejado.
2. Faça logon no portal de Sincronização de Dados SQL e regenere uma chave de agente para o novo agente.
3. Use a interface de usuário do novo agente para enviar a nova chave de agente.
4. Aguarde enquanto o agente de cliente faz o download da lista de bancos de dados local que foram registrados anteriormente.
5. Forneça credenciais de banco de dados para todos os bancos de dados que são exibidos como inacessíveis. Esses bancos de dados devem ser acessíveis do novo computador no qual o agente está instalado.

## <a name="troubleshoot-data-sync-agent-issues"></a><a name="agent-tshoot"></a> Solucione os problemas do Agente de Sincronização de Dados

- [Falhas de instalação, desinstalação ou reparo de agente cliente](#agent-install)

- [O agente cliente não funciona depois de cancelar a desinstalação](#agent-uninstall)

- [Meu banco de dados não está listado na lista de agentes](#agent-list)

- [O agente cliente não é iniciado (erro 1069)](#agent-start)

- [Não consigo enviar a chave do agente](#agent-key)

- [O agente cliente não poderá ser excluído do portal se o banco de dados local associado estiver inacessível](#agent-delete)

- [O aplicativo Agente de Sincronização Local não pode se conectar ao serviço de sincronização local](#agent-connect)

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a><a name="agent-install"></a> A instalação, desinstalação ou reparo do agente cliente falha

- **Causa**. Muitos cenários podem causar a falha. Para determinar a causa específica dessa falha, você precisa examinar os logs.

- **Resolução**. Para encontrar a causa específica para a falha ocorrida, você precisa gerar e examinar os logs do Windows Installer. Ative o log no prompt de comando. Por exemplo, se o arquivo de instalação baixado for `SQLDataSyncAgent-2.0-x86-ENU.msi`, gere e examine os arquivos de log usando as seguintes linhas de comando:

  - Para instalações: `msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Para desinstalações: `msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Também habilite o log para todas as instalações executadas pelo Windows Installer. O artigo da Base de Dados de Conhecimento Microsoft [Como habilitar o log do Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fornece uma solução com um clique para ativar o log do Windows Installer. Ele também fornece o local desses logs.

### <a name="the-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a><a name="agent-uninstall"></a> O agente cliente não funciona depois de cancelar a desinstalação

O agente cliente não funciona mesmo após você cancelar a desinstalação.

- **Causa**. Esse problema ocorre porque o agente cliente da Sincronização de Dados SQL não armazena credenciais.

- **Resolução**. Você pode tentar estas duas soluções:

    -   Use services.msc para reinserir suas credenciais do agente cliente.
    -   Desinstale esse agente cliente e instale um novo. Baixe e instale o último agente cliente no [Centro de Download](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="my-database-isnt-listed-in-the-agent-list"></a><a name="agent-list"></a> Meu banco de dados não está listado na lista de agentes

Quando você tenta adicionar um banco de dados existente do SQL Server a um grupo de sincronização, o banco de dados não aparece na lista de agentes.

Estes cenários podem causar esse problema:

- **Causa**. O agente cliente e grupo de sincronização estão em diferentes data centers.

- **Resolução**. O agente cliente e o grupo de sincronização devem estar no mesmo data center. Para configurar isso, você tem duas opções:

    -   Crie um novo agente no data center no qual o grupo de sincronização está localizado. Em seguida, registre o banco de dados nesse agente.
    -   Exclua o grupo de sincronização atual. Em seguida, crie novamente o grupo de sincronização no data center no qual o agente está localizado.

- **Causa**. A lista de bancos de dados do agente cliente não é atual.

- **Resolução**. Pare e, em seguida, reinicie o serviço do agente cliente.

    O agente local baixa a lista de bancos de dados associados somente no primeiro envio da chave do agente. Ele não baixa a lista de bancos de dados associados em envios de chave do agente subsequentes. Os bancos de dados registrados durante uma movimentação do agente não aparecem na instância do agente original.

### <a name="client-agent-doesnt-start-error-1069"></a><a name="agent-start"></a> O agente cliente não é iniciado (erro 1069)

Você descobriu que o agente não está sendo executado em um computador que hospeda o SQL Server. Ao tentar iniciar o agente manualmente, você obtém uma caixa de diálogo de erro com a mensagem de erro “Erro 1069: o serviço não foi iniciado devido a uma falha de logon”.

![Caixa de diálogo do erro 1069 da Sincronização de Dados](./media/sql-data-sync-agent-overview/sync-error-1069.png)

- **Causa**. Uma causa provável desse erro é que a senha no servidor local foi alterada, já que você criou o agente e a senha do agente.

- **Resolução**. Atualize a senha do agente para a senha atual do servidor:

  1. Localize o serviço do agente do cliente da Sincronização de Dados SQL.  
    a. Selecione **Iniciar**.  
    b. Insira **services.msc** na caixa de pesquisa.  
    c. Nos resultados da pesquisa, selecione **Serviços**.  
    d. Na janela **Serviços**, role até a entrada de **Agente da Sincronização de Dados SQL**.  
  1. Clique com o botão direito do mouse em **Agente da Sincronização de Dados SQL** e, em seguida, selecione **Parar**.
  1. Clique com o botão direito do mouse em **Agente da Sincronização de Dados SQL** e, em seguida, selecione **Propriedades**.
  1. Em **Propriedades do Agente da Sincronização de Dados SQL**, selecione a guia **Fazer Logon**.
  1. Na caixa de **Senha**, insira sua senha.
  1. Na caixa de **Confirmar Senha**, insira sua senha novamente.
  1. Selecione **Aplicar** e, depois, **OK**.
  1. Na janela **Serviços**, clique com o botão direito do mouse no serviço **Agente da Sincronização de Dados SQL** e, em seguida, clique em **Iniciar**.
  1. Feche a janela **Serviços** .

### <a name="i-cant-submit-the-agent-key"></a><a name="agent-key"></a> Não consigo enviar a chave do agente

Depois de criar ou recriar uma chave para um agente, você tenta enviar essa chave pelo aplicativo SqlAzureDataSyncAgent. O envio não é concluído.

![Caixa de diálogo Erro de Sincronização – não é possível enviar a chave do agente](./media/sql-data-sync-agent-overview/sync-error-cant-submit-agent-key.png)

- **Pré-requisitos**. Antes de prosseguir, verifique os pré-requisitos a seguir:

  - O serviço Windows Sincronização de Dados SQL está em execução.

  - A conta de serviço do serviço Windows de versão prévia da Sincronização de Dados SQL tem acesso à rede.

  - A porta de saída 1433 está aberta na regra de firewall local.

  - O ip local é adicionado à regra de firewall do servidor ou banco de dados para o banco de dados de metadados de sincronização.

- **Causa**. A chave do agente identifica exclusivamente cada agente local. A chave deve atender às duas condições:

  -   A chave do agente cliente no servidor da Sincronização de Dados SQL e o computador local devem ser idênticos.
  -   A chave do agente cliente pode ser usada apenas uma vez.

- **Resolução**. Se o agente não estiver funcionando, isso indicará que uma ou ambas as condições não foram atendidas. Para fazer com que o agente funcione novamente:

  1. Gere uma nova chave.
  1. Aplique a nova chave ao agente.

  Para aplicar a nova chave ao agente:

  1. No Explorador de Arquivos, vá até o diretório de instalação do agente. O diretório de instalação padrão é C:\\Arquivos de Programa (x86)\\Sincronização de Dados SQL Microsoft.
  1. Clique duas vezes no subdiretório.
  1. Abra o aplicativo SqlAzureDataSyncAgent.
  1. Selecione **Enviar Chave do Agente**.
  1. Cole a chave da área de transferência no espaço fornecido.
  1. Selecione **OK**.
  1. Feche o programa.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a><a name="agent-delete"></a> O agente cliente não poderá ser excluído do portal se seu banco de dados local associado estiver inacessível

Se um ponto de extremidade local (ou seja, um banco de dados) registrado em um agente cliente da Sincronização de Dados SQL ficar inacessível, o agente cliente não poderá ser excluído.

- **Causa**. O agente local não pode ser excluído porque o banco de dados inacessível ainda está registrado no agente. Quando você tenta excluir o agente, o processo de exclusão tenta acessar o banco de dados, o que falha.

- **Resolução**. Use uma “exclusão forçada” para excluir o banco de dados inacessível.

> [!NOTE]
> Se as tabelas de metadados de sincronização permanecerem após uma "exclusão forçada", use `deprovisioningutil.exe` para limpá-las.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a><a name="agent-connect"></a> O aplicativo do agente de sincronização local não pode se conectar ao serviço de sincronização local

- **Resolução**. Experimente as etapas a seguir:

  1. Saia do aplicativo.  
  1. Abra o Painel de Serviços de Componentes.  
    a. Na caixa de pesquisa da barra de tarefas, digite **services.msc**.  
    b. Clique duas vezes em **Serviços** nos resultados da pesquisa.  
  1. Pare o serviço de **Sincronização de Dados SQL**.
  1. Reinicie o serviço **sincronização de dados SQL** .  
  1. Reabra o aplicativo.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Execute o Agente de Sincronização de Dados a partir do prompt de comando

Você pode executar os seguintes comandos do Agente de Sincronização de Dados a partir do prompt de comando:

### <a name="ping-the-service"></a>Execute ping no serviço

#### <a name="usage"></a>Uso

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Exemplo

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Exiba os bancos de dados registrados

#### <a name="usage"></a>Uso

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>Exemplo

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>Envie a chave do agente

#### <a name="usage"></a>Uso

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Exemplo

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Registre um banco de dados

#### <a name="usage"></a>Uso

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Exemplos

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Cancele o registro de um banco de dados

Quando você usa esse comando para cancelar o registro de um banco de dados, ele desprovisiona o banco de dados completamente. Se o banco de dados fizer parte de outros grupos de sincronização, essa operação interrompe os outros grupos de sincronização.

#### <a name="usage"></a>Uso

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>Exemplo

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>Atualizar credenciais

#### <a name="usage"></a>Uso

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Exemplos

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais informações sobre a Sincronização de Dados SQL, consulte os artigos a seguir:

-   Visão geral – [Sincronize dados em vários bancos de dados locais e na nuvem com a Sincronização de Dados SQL no Azure](sql-data-sync-data-sql-server-sql-database.md)
-   Configurar sincronização de dados
    - No portal- [tutorial: configurar o sincronização de dados SQL para sincronizar dados entre o Azure SQL Database e SQL Server](sql-data-sync-sql-server-configure.md)
    - Com o PowerShell
        -  [Usar o PowerShell para sincronizar entre vários bancos de dados no banco de dados SQL do Azure](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Usar o PowerShell para sincronizar entre um banco de dados no banco de dados SQL do Azure e um banco de dados em uma instância do SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Práticas recomendadas - [Práticas recomendadas para a Sincronização de Dados SQL do Azure](sql-data-sync-best-practices.md)
-   Monitor – [monitore a Sincronização de Dados SQL com logs do Azure Monitor](./monitor-tune-overview.md)
-   Solucionar problemas-[solucionar problemas com o Azure Sincronização de Dados SQL] SQL-Data-Sync-troubleshoot.md)
-   Atualizar o esquema de sincronização
    -   Com o Transact-SQL- [automatizar a replicação de alterações de esquema com o sincronização de dados SQL no Azure](sql-data-sync-update-sync-schema.md)
    -   Com o PowerShell - [usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/update-sync-schema-in-sync-group.md)