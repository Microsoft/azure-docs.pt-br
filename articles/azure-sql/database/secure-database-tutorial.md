---
title: Proteger um banco de dados
description: Este tutorial ensina você sobre as técnicas e os recursos para proteger um Banco de Dados SQL do Azure, esteja ele isolado ou em pool de bancos de dados.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 09/21/2020
ms.custom: seoapril2019 sqldbrb=1
ms.openlocfilehash: 8cdf95dd3c0e801896328136b15e4bd4efe53005
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563805"
---
# <a name="tutorial-secure-a-database-in-azure-sql-database"></a>Tutorial: Proteger um banco de dados no Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> - Criar regras de firewall no nível do servidor e do banco de dados
> - Configurar um administrador do Azure AD (Azure Active Directory)
> - Gerenciar o acesso do usuário com a autenticação do SQL, a autenticação do Azure AD e cadeias de conexão seguras
> - Habilitar recursos de segurança, como o Azure defender para SQL, auditoria, mascaramento de dados e criptografia

O Banco de Dados SQL do Azure protege os dados permitindo que você:

- Limite o acesso usando regras de firewall
- Use mecanismos de autenticação que exigem a identidade
- Use a autorização com associações e permissões baseadas em função
- Habilitar recursos de segurança

> [!NOTE]
> A Instância Gerenciada de SQL do Azure é protegida usando regras de segurança de rede e pontos de extremidade privados, conforme descrito em [Instância Gerenciada do SQL do Azure](../managed-instance/sql-managed-instance-paas-overview.md) e [Arquitetura de conectividade](../managed-instance/connectivity-architecture-overview.md).

Para obter mais informações, confira os artigos [Visão geral](./security-overview.md) e [Recursos de segurança do Banco de Dados SQL do Azure](security-overview.md).

> [!TIP]
> O módulo do Microsoft Learn a seguir ajuda você a aprender gratuitamente sobre como [Proteger seu banco de dados no Banco de Dados SQL do Azure](/learn/modules/secure-your-azure-sql-database/).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, verifique se você tem os seguintes pré-requisitos:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Um [Servidor](logical-servers.md) e um banco de dados individual
  - Crie-os com o [portal do Azure](single-database-create-quickstart.md), a [CLI](az-cli-script-samples-content-guide.md) ou o [PowerShell](powershell-script-content-guide.md)

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Para obter todas as etapas do tutorial, entre no [portal do Azure](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Criar regras de firewall

Os bancos de dados no Banco de Dados SQL são protegidos por firewalls no Azure. Por padrão, todas as conexões ao servidor e aos bancos de dados são rejeitadas. Para saber mais, confira [Regras de firewall no nível do servidor e do banco de dados](firewall-configure.md).

Defina **Permitir acesso aos serviços do Azure** como **DESATIVADO** para a configuração mais segura. Em seguida, crie um [IP reservado (implantação clássica)](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) para o recurso que precisa se conectar, como uma VM do Azure ou um serviço de nuvem, e só permita a esse endereço IP o acesso por meio do firewall. Se você estiver usando o modelo de implantação do [Resource Manager](../../virtual-network/public-ip-addresses.md), um endereço IP público dedicado será necessário para cada recurso.

> [!NOTE]
> O Banco de Dados SQL se comunica pela porta 1433. Se você estiver tentando conectar-se a partir de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall de sua rede. Se isso acontecer, você não poderá se conectar ao servidor, a menos que o administrador abra a porta 1433.

### <a name="set-up-server-level-firewall-rules"></a>Configurar regras de firewall no nível do servidor

As regras de firewall de IP no nível do servidor se aplicam a todos os bancos de dados dentro do mesmo servidor do Banco de Dados SQL.

Para configurar uma regra de firewall no nível do servidor:

1. No portal do Azure, selecione **Bancos de dados SQL** no menu à esquerda e selecione seu banco de dados na página **Bancos de dados SQL**.

    ![regra de firewall do servidor](./media/secure-database-tutorial/server-name.png)

    > [!NOTE]
    > Copie o nome do servidor totalmente qualificado (como *yourserver.database.windows.net*) para uso posterior no tutorial.

1. Na página **Visão geral**, selecione **Definir firewall do servidor**. A página **Configurações do firewall** do servidor será aberta.

   1. Selecione **Adicionar IP do cliente** na barra de ferramentas para adicionar seu endereço IP atual a uma nova regra de firewall. A regra pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP. Clique em **Salvar**.

      ![definir regra de firewall do servidor](./media/secure-database-tutorial/server-firewall-rule2.png)

   1. Selecione **OK** e feche a página **Configurações do firewall**.

Agora você pode se conectar a qualquer banco de dados do servidor com o endereço IP ou o intervalo de endereços IP especificado.

### <a name="setup-database-firewall-rules"></a>Configurar regras de firewall do banco de dados

As regras de firewall no nível do banco de dados se aplicam somente a bancos de dados individuais. O banco de dados manterá essas regras durante um failover de servidor. As regras de firewall no nível do banco de dados só poderão ser configuradas usando instruções T-SQL (Transact-SQL) e somente depois que você configurar uma regra de firewall no nível do servidor.

Para configurar uma regra de firewall de nível de banco de dados:

1. Conecte-se ao banco de dados, por exemplo, usando o [SQL Server Management Studio](connect-query-ssms.md).

1. No **Pesquisador de Objetos**, clique com o botão direito do mouse no banco de dados e selecione **Nova Consulta**.

1. Na janela de consulta, adicione esta instrução e modifique o endereço IP de seu endereço IP público:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Na barra de ferramentas, selecione **Executar** para criar a regra de firewall.

> [!NOTE]
> Crie também uma regra de firewall no nível do servidor no SSMS usando o comando [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current), embora você precise estar conectado ao banco de dados *mestre*.

## <a name="create-an-azure-ad-admin"></a>Criar um administrador do Azure AD

Verifique se você está usando o domínio gerenciado do Azure AD (Active Directory) apropriado. Para selecionar o domínio do AD, use o canto superior direito do portal do Azure. Esse processo confirma se a mesma assinatura é usada para o Azure AD e o SQL Server lógico que hospeda o banco de dados ou o data warehouse.

   ![choose-ad](./media/secure-database-tutorial/8choose-ad.png)

Para definir o administrador do Azure AD:

1. No portal do Azure, na página **SQL Server**, selecione **Administrador do Active Directory**. Em seguida, selecione **Definir administrador**.

    ![selecionar active directory](./media/secure-database-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > Você precisa ser "Administrador global" para executar essa tarefa.

1. Na página **Adicionar administrador**, pesquise e selecione o usuário ou o grupo do AD e escolha **Selecionar**. Todos os membros e grupos do Active Directory serão listados, e não há suporte para entradas esmaecidas como administradores do Azure AD. Confira [Recursos e limitações do Azure AD](authentication-aad-overview.md#azure-ad-features-and-limitations).

    ![selecionar administrador](./media/secure-database-tutorial/admin-select.png)

    > [!IMPORTANT]
    > O Azure RBAC (controle de acesso baseado em função do Azure) se aplica somente ao portal e não é propagado para o SQL Server.

1. Na parte superior da página **Administrador do Active Directory**, selecione **Salvar**.

    O processo de alteração de um administrador poderá levar vários minutos. O novo administrador será exibido na caixa **Administrador do Active Directory**.

> [!NOTE]
> Ao definir um administrador do Azure AD, o novo nome do administrador (usuário ou grupo) não poderá existir como logon ou usuário do SQL Server no banco de dados *mestre*. Se ele estiver presente, a instalação falhará e reverterá as alterações, indicando que um nome de administrador como esse já existe. Como o logon ou usuário do SQL Server não faz parte do Azure AD, qualquer esforço para conectar o usuário usando a autenticação do Azure AD falhará.

Para obter informações sobre como configurar o Azure AD, confira:

- [Integrar suas identidades locais ao Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Adicionar seu próprio nome de domínio ao Azure AD](../../active-directory/fundamentals/add-custom-domain.md)
- [O Microsoft Azure agora dá suporte à federação com o Windows Server AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Administrar seu diretório do Azure AD](../../active-directory/fundamentals/active-directory-whatis.md)
- [Gerenciar o Azure AD usando o PowerShell](/powershell/azure/)
- [Portas e protocolos necessários para a identidade híbrida](../../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Gerenciar o acesso ao banco de dados

Gerencie o acesso ao banco de dados adicionando usuários ao banco de dados ou permitindo o acesso do usuário com cadeias de conexão seguras. As cadeias de conexão são úteis para aplicativos externos. Para saber mais, consulte [Gerenciar logons e contas de usuário](logins-create-manage.md) e [Autenticação do AD](authentication-aad-overview.md).

Para adicionar usuários, escolha o tipo de autenticação de banco de dados:

- **Autenticação do SQL**: usa um nome de usuário e uma senha para logons, que só são válidos no contexto de um banco de dados específico no servidor

- **Autenticação do Azure AD**: usa identidades gerenciadas pelo Azure AD

### <a name="sql-authentication"></a>Autenticação SQL

Para adicionar um usuário com a autenticação do SQL:

1. Conecte-se ao banco de dados, por exemplo, usando o [SQL Server Management Studio](connect-query-ssms.md).

1. No **Pesquisador de Objetos**, clique com o botão direito do mouse no banco de dados e escolha **Nova Consulta**.

1. Na janela de consulta, insira o seguinte comando:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Na barra de ferramentas, selecione **Executar** para criar o usuário.

1. Por padrão, o usuário pode se conectar ao banco de dados, mas não tem permissões para ler nem gravar dados. Para conceder essas permissões, execute os seguintes comandos em uma nova janela de consulta:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Crie contas não administrador no nível do banco de dados, a menos que ele precise executar tarefas de administrador, como a criação de usuários.

### <a name="azure-ad-authentication"></a>Autenticação do Azure AD

A autenticação do Azure Active Directory exige que os usuários de banco de dados sejam criados como independentes. Um usuário de banco de dados independente é mapeado para uma identidade no diretório do Azure AD associada ao banco de dados e não tem logon no banco de dados *mestre*. A identidade do Azure AD pode se destinar a um usuário individual ou um grupo. Para obter mais informações, confira [Usuários de banco de dados independente – tornar o banco de dados portátil](/sql/relational-databases/security/contained-database-users-making-your-database-portable) e examine o [tutorial do Azure AD](authentication-aad-configure.md) sobre como fazer a autenticação usando o Azure AD.

> [!NOTE]
> Os usuários de banco de dados (excluindo os administradores) não podem ser criados usando o portal do Azure. As funções do Azure não são propagadas para servidores, bancos de dados nem data warehouses SQL. Elas só são usadas para gerenciar recursos do Azure e não se aplicam às permissões de banco de dados.
>
> Por exemplo, a função *Colaborador do SQL Server* não permite acesso para conexão com um banco de dados ou um data warehouse. Essa permissão precisa ser concedida no banco de dados usando instruções T-SQL.

> [!IMPORTANT]
> Não há suporte para caracteres especiais, como dois-pontos `:` ou E comercial `&`, em nomes de usuário nas instruções T-SQL `CREATE LOGIN` e `CREATE USER`.

Para adicionar um usuário com a autenticação do Azure AD:

1. Conecte-se ao servidor no Azure usando uma conta do Azure AD com, pelo menos, a permissão *ALTER ANY USER*.

1. No **Pesquisador de Objetos**, clique com o botão direito do mouse no banco de dados e selecione **Nova Consulta**.

1. Na janela de consulta, insira o seguinte comando e modifique `<Azure_AD_principal_name>` para o nome da entidade de segurança do usuário do Azure AD ou o nome de exibição do grupo do Azure AD:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Os usuários do Azure AD são marcados nos metadados do banco de dados com os tipos `E (EXTERNAL_USER)` e `X (EXTERNAL_GROUPS)` para grupos. Para obter mais informações, consulte [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Proteger cadeias de conexão

Para garantir uma conexão segura e criptografada entre o aplicativo cliente e o banco de dados SQL, uma cadeia de conexão precisa ser configurada para:

- Solicitar uma conexão criptografada
- Não confiar no certificado do servidor

A conexão é estabelecida usando o protocolo TLS e reduz o risco de um ataque man-in-the-middle. As cadeias de conexão estão disponíveis por banco de dados e são pré-configuradas para dar suporte a drivers de cliente, como ADO.NET, JDBC, ODBC e PHP. Para obter informações sobre TLS e conectividade, consulte [Considerações sobre TLS](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Para copiar uma cadeia de conexão segura:

1. No portal do Azure, selecione **Bancos de dados SQL** no menu à esquerda e selecione seu banco de dados na página **Bancos de dados SQL**.

1. Na página **Visão geral**, selecione **Mostrar cadeias de conexão de banco de dados**.

1. Selecione uma guia do driver e copie a cadeia de conexão completa.

    ![Cadeia de conexão do ADO.NET](./media/secure-database-tutorial/connection.png)

## <a name="enable-security-features"></a>Habilitar recursos de segurança

O Banco de Dados SQL do Azure fornece recursos de segurança que são acessados usando o portal do Azure. Esses recursos estão disponíveis para o banco de dados e o servidor, exceto a máscara de dados, que só está disponível no banco de dados. Para saber mais, confira [Azure Defender para SQL](azure-defender-for-sql.md), [Auditoria](../../azure-sql/database/auditing-overview.md), [Máscara dinâmica de dados](dynamic-data-masking-overview.md) e [Transparent data encryption](transparent-data-encryption-tde-overview.md).

### <a name="azure-defender-for-sql"></a>Azure Defender para SQL

O recurso Azure Defender para SQL detecta ameaças potenciais conforme elas ocorrem e fornece alertas de segurança sobre atividades anormais. Os usuários podem explorar esses eventos suspeitos usando o recurso de auditoria e determinar se o evento tinha o objetivo de acessar, violar ou explorar os dados no banco de dados. Os usuários também obtêm uma visão geral de segurança que inclui uma avaliação de vulnerabilidade e a ferramenta de descoberta e classificação de dados.

> [!NOTE]
> Uma ameaça de exemplo é a injeção de SQL, um processo em que os invasores injetam um SQL mal-intencionado em entradas de aplicativo. Em seguida, um aplicativo poderá inadvertidamente executar o SQL mal-intencionado e permitir que os invasores obtenham acesso para violar ou modificar dados no banco de dados.

Para habilitar o Azure Defender para SQL:

1. No portal do Azure, selecione **Bancos de dados SQL** no menu à esquerda e selecione seu banco de dados na página **Bancos de dados SQL**.

1. Na página **Visão geral**, selecione o link **Nome do servidor**. A página do servidor será aberta.

1. Na página **SQL Server**, encontre a seção **Segurança** e selecione **Central de segurança**.

   1. Selecione **LIGADO** em **Azure Defender para SQL** para habilitar o recurso. Escolha uma conta de armazenamento para salvar os resultados da avaliação de vulnerabilidade. Em seguida, selecione **Salvar**.

      ![Painel de navegação](./media/secure-database-tutorial/threat-settings.png)

      Configure também emails para receber alertas de segurança, detalhes de armazenamento e tipos de detecção de ameaças.

1. Retorne à página **Bancos de dados SQL** de seu banco de dados e selecione **Central de segurança** na seção **Segurança**. Aqui você encontrará vários indicadores de segurança disponíveis para o banco de dados.

    ![Status da ameaça](./media/secure-database-tutorial/threat-status.png)

Se forem detectadas atividades anormais, você receberá um email com informações sobre o evento. Isso inclui a natureza da atividade, o banco de dados, o servidor, a hora do evento, possíveis causas e ações recomendadas para investigar e atenuar a ameaça potencial. Se um email desse tipo for recebido, selecione o link **Log de Auditoria do SQL do Azure** para iniciar o portal do Azure e mostrar os registros de auditoria relevantes para a hora do evento.

   ![Email de detecção de ameaças](./media/secure-database-tutorial/threat-email.png)

### <a name="auditing"></a>Auditoria

O recurso de auditoria rastreia eventos de banco de dados, gravando-os em um log de auditoria no Armazenamento do Azure, no Azure Monitor ou em um hub de eventos. A auditoria ajuda a manter a conformidade regulatória, entender a atividade do banco de dados e obter insights sobre discrepâncias e anomalias que podem indicar possíveis violações de segurança.

Para habilitar a auditoria:

1. No portal do Azure, selecione **Bancos de dados SQL** no menu à esquerda e selecione seu banco de dados na página **Bancos de dados SQL**.

1. Na seção **Segurança**, selecione **Auditoria**.

1. Nas configurações de **Auditoria**, defina os seguintes valores:

   1. Defina **Auditoria** como **ATIVADO**.

   1. Selecione **Destino do log de auditoria** como um dos seguintes:

       - **Armazenamento**, uma conta de armazenamento do Azure na qual os logs de eventos são salvos e podem ser baixados como arquivos *.xel*

          > [!TIP]
          > Use a mesma conta de armazenamento para todos os bancos de dados auditados para aproveitar ao máximo os modelos de relatório de auditoria.

       - **Log Analytics**, que armazena automaticamente os eventos para consulta ou análise adicional

           > [!NOTE]
           > Um **workspace do Log Analytics** é necessário para dar suporte a recursos avançados, como análise, regras de alerta personalizadas e exportações do Excel ou do Power BI. Sem um workspace, apenas o editor de consultas fica disponível.

       - **Hub de Eventos**, que permite que os eventos sejam encaminhados para uso em outros aplicativos

   1. Clique em **Salvar**.

      ![Configurações de auditoria](./media/secure-database-tutorial/audit-settings.png)

1. Agora você pode selecionar **Exibir logs de auditoria** para exibir dados de eventos do banco de dados.

    ![Registros de auditoria](./media/secure-database-tutorial/audit-records.png)

> [!IMPORTANT]
> Confira [Auditoria do Banco de Dados SQL](../../azure-sql/database/auditing-overview.md) para saber como personalizar ainda mais os eventos de auditoria usando o PowerShell ou a API REST.

### <a name="dynamic-data-masking"></a>Mascaramento de dados dinâmicos

O recurso de máscara de dados ocultará automaticamente dados confidenciais no banco de dados.

Para habilitar a máscara de dados:

1. No portal do Azure, selecione **Bancos de dados SQL** no menu à esquerda e selecione seu banco de dados na página **Bancos de dados SQL**.

1. Na seção **Segurança**, selecione **Máscara de Dados Dinâmicos**.

1. Nas configurações de **Máscara de Dados Dinâmicos**, selecione **Adicionar máscara** para adicionar uma regra de máscara. O Azure preencherá automaticamente os esquemas, as tabelas e as colunas de banco de dados disponíveis para escolha.

    ![Configurações de máscara](./media/secure-database-tutorial/mask-settings.png)

1. Clique em **Salvar**. As informações selecionadas agora estão mascaradas para privacidade.

    ![Exemplo de máscara](./media/secure-database-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Transparent Data Encryption

O recurso de criptografia criptografa automaticamente os dados em repouso e não exige alterações em aplicativos que acessam o banco de dados criptografado. Para novos bancos de dados, a criptografia está ativada por padrão. Criptografe também os dados usando o SSMS e o recurso [Always Encrypted](always-encrypted-certificate-store-configure.md).

Para habilitar ou verificar a criptografia:

1. No portal do Azure, selecione **Bancos de dados SQL** no menu à esquerda e selecione seu banco de dados na página **Bancos de dados SQL**.

1. Na seção **Segurança**, selecione **Transparent Data Encryption**.

1. Se necessário, defina **Criptografia de dados** como **ATIVADO**. Clique em **Salvar**.

    ![Criptografia de Dados Transparente](./media/secure-database-tutorial/encryption-settings.png)

> [!NOTE]
> Para exibir o status da criptografia, conecte-se ao banco de dados usando o [SSMS](connect-query-ssms.md) e consulte a coluna `encryption_state` da exibição [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql). Um estado igual a `3` indica que o banco de dados está criptografado.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a aprimorar a segurança de seu banco de dados com apenas algumas etapas simples. Você aprendeu a:

> [!div class="checklist"]
>
> - Criar regras de firewall no nível do servidor e do banco de dados
> - Configurar um administrador do Azure AD (Active Directory)
> - Gerenciar o acesso do usuário com a autenticação do SQL, a autenticação do Azure AD e cadeias de conexão seguras
> - Habilitar recursos de segurança, como o Azure defender para SQL, auditoria, mascaramento de dados e criptografia

Avance para o próximo tutorial para saber como implementar a distribuição geográfica.

> [!div class="nextstepaction"]
>[Implementar um banco de dados distribuído geograficamente](geo-distributed-application-configure-tutorial.md)