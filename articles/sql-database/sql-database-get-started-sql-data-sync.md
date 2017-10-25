---
title: "Introdução à Sincronização de Dados SQL do Azure (Visualização) | Microsoft Docs"
description: "Esse tutorial ajudará você a começar a Sincronização de Dados SQL do Azure (Versão prévia)."
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: a295a768-7ff2-4a86-a253-0090281c8efa
ms.service: sql-database
ms.custom: load & move data
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: douglasl
ms.openlocfilehash: 1943e87ffd991d099ab655af5a7d16d7f1608d22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-sql-data-sync-preview"></a>Introdução à Sincronização de Dados SQL do Azure (visualização)
Neste tutorial, você aprenderá a configurar a Sincronização de Dados SQL do Azure criando um grupo de sincronização híbrido que contém as instâncias de Banco de Dados SQL do Azure e do SQL Server. O novo grupo de sincronização ficará totalmente configurado e sincronizado no agendamento que você definir.

Este tutorial presume que você tem pelo menos alguma experiência anterior com o Banco de Dados SQL e o SQL Server. 

Para obter uma visão geral da Sincronização de Dados SQL, confira [Sincronizar dados](sql-database-sync-data.md).

Para obter exemplos completos do PowerShell que mostrem como configurar a Sincronização de Dados SQL, veja os seguintes artigos:
-   [Usar o PowerShell para sincronização entre vários banco de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
-   [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um banco de dados local do SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!NOTE]
> A documentação técnica completa para Sincronização de Dados SQL do Azure, localizada anteriormente no MSDN, está disponível como documento .PDF. Baixe [aqui](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true).

## <a name="step-1---create-sync-group"></a>Etapa 1: Criar grupo de sincronização

### <a name="locate-the-data-sync-settings"></a>Localizar as configurações de Sincronização de Dados

1.  Em seu navegador, navegue até o portal do Azure.

2.  No portal, localize os bancos de dados SQL no seu Painel ou no ícone Bancos de Dados SQL na barra de ferramentas.

    ![Lista de bancos de dados SQL do Azure](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  Na folha **bancos de dados SQL**, selecione o banco de dados SQL existente que você deseja usar como banco de dados de hub para a Sincronização de Dados. A folha do banco de dados SQL é aberta.

4.  Na folha do banco de dados SQL para o banco de dados selecionado, selecione **Sincronizar para outros bancos de dados**. A folha Sincronização de Dados é aberta.

    ![Opção Sincronizar para outros bancos de dados](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Criar um novo grupo de sincronização

1.  Na folha Sincronização de Dados, selecione **Novo Grupo de Sincronização**. A folha **Novo grupo de sincronização** é aberta com a Etapa 1, **Criar grupo de sincronização**, realçada. A folha **Criar Grupo de Sincronização de Dados** também é aberta.

2.  Na folha **Criar Grupo de Sincronização de Dados**, faça o seguinte:

    1.  No campo **Nome do Grupo de Sincronização**, digite um nome para o novo grupo de sincronização.

    2.  Na seção **Banco de Dados de Metadados de Sincronização**, escolha se deseja criar um novo banco de dados (recomendado) ou usar um banco de dados existente.

        > [!NOTE]
        > A Microsoft recomenda que você crie um novo banco de dados vazio para usar como o Banco de Dados de Metadados de Sincronização. A Sincronização de Dados cria tabelas nesse banco de dados e executa uma carga de trabalho frequente. Esse banco de dados é compartilhado automaticamente como o Banco de Dados de Metadados de Sincronização para todos os seus Grupos de sincronização na região selecionada. Você não pode alterar o Banco de Dados de Metadados de Sincronização ou seu nome sem removê-lo.

        Se você escolheu **Novo banco de dados**, selecione **Criar novo banco de dados.** A folha **Banco de Dados SQL** é aberta. Na folha **Banco de Dados SQL**, nomeie e configure o novo banco de dados. Depois, selecione **OK**.

        Se você escolheu **Usar banco de dados existente**, selecione o banco de dados na lista.

    3.  Na seção **Sincronização Automática**, primeiro selecione **Ativa** ou **Inativa**.

        Se você escolheu **Ativa**, na seção **Frequência de Sincronização**, insira um número e selecione segundos, minutos, horas ou dias.

        ![Especificar frequência de sincronização](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  Na seção **Resolução de Conflitos**, selecione "Hub ganha" ou "Membro ganha".

        ![Especificar como os conflitos são resolvidos](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  Selecione **OK** e aguarde até que o novo grupo de sincronização seja criado e implantado.

## <a name="step-2---add-sync-members"></a>Etapa 2: Adicionar membros de sincronização

Depois que o novo grupo de sincronização é criado e implantado, a Etapa 2, **Adicionar membros de sincronização**, fica realçada na folha **Novo grupo de sincronização**.

Na seção **Banco de Dados Hub**, insira as credenciais existentes para o servidor de Banco de Dados SQL em que o banco de dados hub está localizado. Não insira *novas* credenciais nesta seção.

![O banco de dados hub foi adicionado ao grupo de sincronização](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

## <a name="add-an-azure-sql-database"></a>Adicionar um Banco de Dados SQL do Azure

Na seção **Banco de Dados Membro**, opcionalmente, adicione um Banco de Dados SQL do Azure ao grupo de sincronização selecionando **Adicionar um Banco de Dados do Azure**. A folha **Configurar Banco de Dados do Azure** é aberta.

Na folha **Configurar Banco de Dados do Azure**, faça o seguinte:

1.  No campo **Nome de Membro de Sincronização**, forneça um nome para o novo membro de sincronização. Esse nome é diferente do nome do banco de dados.

2.  No campo **Assinatura**, selecione a assinatura associada do Azure para fins de cobrança.

3.  No **Azure SQL Server**, selecione o servidor de banco de dados SQL existente.

4.  No **Banco de Dados SQL do Azure**, selecione o banco de dados SQL existente.

5.  No campo **Direções de Sincronização**, selecione Sincronização Bidirecional, Para o Hub ou Do Hub.

    ![Adicionar um novo membro de sincronização do Banco de Dados SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  Nos campos **Nome de Usuário** e **Senha**, insira as credenciais existentes para o servidor de Banco de Dados SQL em que o banco de dados membro está localizado. Não insira *novas* credenciais nesta seção.

7.  Selecione **OK** e aguarde até que o novo membro de sincronização seja criado e implantado.

    ![Novo membro de sincronização do banco de dados SQL foi adicionado](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

## <a name="add-an-on-premises-sql-server-database"></a>Adicionar um Banco de dados do SQL Server local

Na seção **Banco de Dados Membro**, opcionalmente, adicione um SQL Server local ao grupo de sincronização selecionando **Adicionar um Banco de Dados Local**. A folha **Configurar Local** é aberta.

Na folha **Configurar Local**, faça o seguinte:

1.  Selecione **Escolher o Gateway de Agente de Sincronização**. A folha **Selecionar Agente de Sincronização** é aberta.

    ![Escolher o gateway de agente de sincronização](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  Na folha **Escolher o Gateway de Agente de Sincronização**, escolha se deseja usar um agente existente ou criar um novo agente.

    Se você escolheu **Agentes existentes**, selecione o agente existente na lista.

    Se você escolheu **Criar um novo agente**, faça o seguinte:

    1.  Baixe o software cliente do agente de sincronização no link fornecido e instale-o no computador em que o SQL Server está localizado.
 
        > [!IMPORTANT]
        > Você precisa abrir a porta de saída TCP 1433 no firewall para permitir que o agente do cliente se comunique com o servidor.


    2.  Insira um nome para o agente.

    3.  Selecione **Criar e Gerar Chave**.

    4.  Copie a chave do agente para a área de transferência.
        
        ![Criando um novo agente de sincronização](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  Selecione **OK** para fechar a folha **Selecionar o Agente de Sincronização**.

    6.  No computador do SQL Server, localize e execute o aplicativo Agente de Sincronização do Cliente.

        ![Os dados do aplicativo cliente do agente de sincronização de dados](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  No aplicativo do agente de sincronização, selecione **Enviar Chave do Agente**. A caixa de diálogo **Configuração de Banco de Dados de Metadados de Sincronização** é aberta.

    8.  Na caixa de diálogo **Configuração de Banco de Dados de Metadados de sincronização**, cole a chave do agente copiada do portal do Azure. Insira também as credenciais existentes para o servidor de Banco de Dados SQL do Azure em que o banco de dados de metadados está localizado. (Se você criou um novo banco de dados de metadados, esse banco de dados está no mesmo servidor do banco de dados hub.) Selecione **OK** e aguarde até que a configuração seja concluída.

        ![Insira as credenciais de chave e servidor do agente](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   Se você receber um erro de firewall nesta hora, precisará criar uma regra de firewall no Azure para permitir o tráfego de entrada do computador do SQL Server. Você pode criar a regra manualmente no portal, mas pode achar mais fácil criá-la no SSMS (SQL Server Management Studio). No SSMS, tente se conectar ao banco de dados hub no Azure. Digite o nome como \<hub_database_name\>.database.windows.net. Siga as etapas na caixa de diálogo para configurar a regra de firewall do Azure. Em seguida, retorne ao aplicativo Agente de Sincronização do Cliente.

    9.  No aplicativo Agente de Sincronização do Cliente, clique em **Registrar** para registrar um banco de dados do SQL Server no agente. A caixa de diálogo **Configuração do SQL Server** é aberta.

        ![Adicionar e configurar um banco de dados do SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. Na caixa de diálogo **Configuração do SQL Server**, escolha se deseja se conectar usando a autenticação do SQL Server ou a autenticação do Windows. Se você escolher a autenticação do SQL Server, insira as credenciais existentes. Forneça o nome do SQL Server e o nome do banco de dados que você deseja sincronizar. Selecione **Testar conexão** para testar suas configurações. Em seguida, selecione **Salvar**. O banco de dados registrado aparece na lista.

        ![O banco de dados do SQL Server agora está registrado](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. Agora você pode fechar o aplicativo Agente de Sincronização do Cliente.

    12. No portal, na folha **Configurar Local**, selecione **Selecionar o Banco de Dados.** A folha **Selecionar Banco de Dados** é aberta.

    13. Na folha **Selecionar Banco de Dados**, no campo **Nome de Membro de Sincronização**, forneça um nome para o novo membro de sincronização. Esse nome é diferente do nome do banco de dados. Selecione o banco de dados na lista. No campo **Direções de Sincronização**, selecione Sincronização Bidirecional, Para o Hub ou Do Hub.

        ![Selecione o banco de dados local](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. Selecione **OK** para fechar a folha **Selecionar o Banco de Dados**. Em seguida, selecione **OK** para fechar a folha **Configurar Local** e aguarde até o novo membro de sincronização ser criado e implantado. Por fim, clique em **OK** para fechar a folha **Selecionar membros de sincronização**.

        ![Banco de dados local adicionado ao grupo de sincronização](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  Para conectar-se à Sincronização de Dados SQL e ao agente local, adicione seu nome de usuário à função `DataSync_Executor`. A Sincronização de Dados cria essa função na instância do SQL Server.

## <a name="step-3---configure-sync-group"></a>Etapa 3: Configurar grupo de sincronização

Depois que os novos membros do grupo de sincronização são criados e implantados, a Etapa 3, **Configurar grupo de sincronização**, fica realçado na folha **Novo grupo de sincronização**.

1.  Na folha **Tabelas**, selecione um banco de dados da lista de sincronização de membros do grupo e selecione **Atualizar esquema**.

2.  Na lista de tabelas disponíveis, selecione as tabelas que você deseja sincronizar.

    ![Selecione as tabelas para sincronizar](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  Por padrão, todas as colunas na tabela são selecionadas. Se não quiser sincronizar todas as colunas, desmarque a caixa de seleção das colunas que você não deseja sincronizar. Deixe a coluna de chave primária selecionada.

    ![Selecionar os campos para sincronizar](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  Por fim, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas
Parabéns. Você criou um grupo de sincronização que inclui uma instância do Banco de Dados SQL e um banco de dados do SQL Server.

Para obter mais informações sobre o Banco de Dados SQL e a Sincronização de Dados SQL, consulte:

-   [Baixe a documentação técnica completa da Sincronização de Dados SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)
-   [Baixe a documentação da API REST de Sincronização de Dados SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)
-   [Visão geral do Banco de Dados SQL](sql-database-technical-overview.md)
-   [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
