---
title: Backup do Banco de Dados do Azure para PostgreSQL
description: Saiba mais sobre o backup do banco de dados do Azure para PostgreSQL com retenção de longo prazo (versão prévia)
ms.topic: conceptual
ms.date: 09/08/2020
ms.custom: references_regions
ms.openlocfilehash: 1e2d83d4a5e21ed747ec9d4dcf2fa03d1e3935cc
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737565"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Banco de dados do Azure para PostgreSQL backup com retenção de longo prazo (versão prévia)

O backup do Azure e os serviços de banco de dados do Azure vêm juntos para criar uma solução de backup de classe empresarial para servidores do banco de dados do Azure para PostgreSQL que retém backups por até 10 anos.

Além da retenção de longo prazo, a solução também tem muitos outros recursos, conforme listado abaixo:

- O Azure RBAC (controle de acesso baseado em função) para o banco de dados usando a autenticação Azure Active Directory e Identidade de Serviço Gerenciada (MSI).
- Backup sob demanda e agendado controlado pelo cliente no nível de banco de dados individual.
- Restaurações em nível de banco de dados para qualquer servidor postgres ou diretamente para o armazenamento de BLOBs.
- Retenção de longo prazo.
- Monitoramento central de todas as operações e trabalhos.
- Os backups são armazenados em domínios de falha e segurança separados. Portanto, mesmo que o servidor de origem fosse comprometido ou até mesmo ser eliminado, os backups permanecerão seguros no [cofre de backup](backup-vault-overview.md).
- O uso de **pg_dump** permite maior flexibilidade em restaurações, de modo que você possa restaurar nas versões do banco de dados ou até mesmo restaurar apenas uma parte do backup.

Você pode usar essa solução independentemente ou além da solução de backup nativo oferecida pelo Azure PostgreSQL que oferece retenção de até 35 dias. A solução nativa é adequada para recuperações operacionais, como quando você deseja recuperar dos backups mais recentes. A solução de backup do Azure ajuda você com suas necessidades de conformidade e backup e restauração mais granulares e flexíveis.

## <a name="support-matrix"></a>Matriz de suporte

|Suporte  |Detalhes  |
|---------|---------|
|Implantações com suporte   |  [Banco de Dados do Azure para PostgreSQL – Servidor Único](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|Regiões do Azure com suporte |  Leste dos EUA, leste dos EUA 2, EUA Central, Sul EUA Central, oeste dos EUA, oeste dos EUA 2, Oeste EUA Central, sul do Brasil, central do Canadá, Europa Setentrional, Europa Ocidental, Sul do Reino Unido, Oeste do Reino Unido, Centro-oeste da Alemanha, Norte da Suíça, Oeste da Suíça, Ásia Oriental, Sul Ásia Oriental, leste do Japão, oeste do Japão, centro da Coreia, sul da Coreia, Índia central, leste da Austrália, Austrália Central  |
|Versões do Azure PostgreSQL com suporte    |   9,5, 9,6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Considerações e limitações de recursos

- Todas as operações têm suporte somente do portal do Azure.
- O limite recomendado para o tamanho máximo do banco de dados é de 400 GB.
- Não há suporte para o backup entre regiões. Isso significa que você não pode fazer backup de um servidor PostgreSQL do Azure em um cofre em outra região. Da mesma forma, você só pode restaurar um backup para um servidor na mesma região que o cofre.
- Somente os dados são recuperados no momento da restauração. "Roles" não são restaurados.
- Em versão prévia, recomendamos que você execute a solução somente em seu ambiente de teste.

## <a name="backup-process"></a>Processo de backup

1. Essa solução usa **pg_dump** para fazer backups de seus bancos de dados PostgreSQL do Azure.

2. Depois de especificar os bancos de dados do PostgreSQL do Azure que você deseja fazer backup, o serviço será validado se ele tiver o conjunto correto de permissões e o acesso para executar a operação de backup no servidor e no banco de dados.

3. O backup do Azure gira uma função de trabalho com uma extensão de backup instalada nela. Essa extensão se comunica com o servidor Postgres.

4. Essa extensão consiste em um coordenador e um plug-in do postgres do Azure. Embora o coordenador seja responsável por disparar fluxos de trabalho para várias operações, como configurar backup, backup e restauração, o plug-in é responsável pelo fluxo de dados real.
  
5. Depois que você disparar configurar a proteção nos bancos de dados selecionados, o serviço de backup configurará o coordenador com os agendamentos de backup e outros detalhes da política.

6. No horário agendado, o coordenador se comunica com o plug-in e começa a transmitir os dados de backup do servidor postgres usando **pg_dump**.

7. O plug-in envia os dados diretamente para o cofre de backup, eliminando a necessidade de um local de preparo. Os dados são criptografados usando chaves gerenciadas pela Microsoft e armazenados pelo serviço de backup do Azure em contas de armazenamento.

8. Quando a transferência de dados é concluída, o coordenador confirma a confirmação com o serviço de backup.

    ![Processo de backup](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Configurar backup em bancos de dados PostgreSQL do Azure

Você pode configurar o backup em vários bancos de dados em vários servidores PostgreSQL do Azure. Verifique se as [permissões de pré-requisito](#prerequisite-permissions-for-configure-backup-and-restore) exigidas pelo serviço para fazer backup dos servidores postgres já estão configuradas.

As instruções a seguir são um guia passo a passo para configurar o backup nos bancos de dados do PostgreSQL do Azure usando o backup do Azure:

1. Há duas maneiras de iniciar o processo:

    1. Vá para o [centro de backup](backup-center-overview.md)  ->  **visão geral**  ->  **backup**.

        ![Ir para o centro de backup](./media/backup-azure-database-postgresql/backup-center.png)

        Em **Iniciar: configurar backup**, selecione o **tipo de DataSource** como **banco de dados do Azure para PostgreSQL**.

        ![Em Iniciar: configurar backup, selecione o tipo de fonte de tipos](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. Como alternativa, você pode ir diretamente para backup de [cofres de backup](backup-vault-overview.md)  ->  .

        ![Ir para cofres de backup](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Selecionar backup no cofre de backup](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. Em **Configurar backup**, selecione o **cofre de backup** para o qual você deseja fazer backup dos bancos de dados do Postgres. Essas informações serão preenchidas previamente se você já estiver no contexto do cofre.

    ![Selecione o cofre de backup em configurar backup](./media/backup-azure-database-postgresql/configure-backup.png)

1. Selecione ou crie uma **política de backup**.

    ![Escolher política de backup](./media/backup-azure-database-postgresql/backup-policy.png)

1. Selecione os recursos ou bancos de dados Postgres para fazer backup.

    ![Selecione os recursos para fazer backup](./media/backup-azure-database-postgresql/select-resources.png)

1. Você pode escolher na lista de todos os servidores do Azure PostgreSQL entre assinaturas se eles estiverem na mesma região que o cofre. Expanda a seta para ver a lista de bancos de dados em um servidor.

    ![Escolher servidores](./media/backup-azure-database-postgresql/choose-servers.png)

1. O serviço executa essas verificações nos bancos de dados selecionados para validar se o cofre tem permissões para fazer backup dos servidores e bancos de dados Postgres selecionados.
    1. A **prontidão de backup** para todos os bancos de dados deve ler **êxito** para continuar.
    1. Se houver um erro, **Corrija** o erro e **revalide** ou remova o banco de dados das seleções.

    ![Erros de validação para corrigir](./media/backup-azure-database-postgresql/validation-errors.png)

1. Confirme todos os detalhes em **revisar e configurar** e selecione **Configurar backup** para enviar a operação.

    ![Confirmar detalhes em revisão e configurar](./media/backup-azure-database-postgresql/review-and-configure.png)

1. Uma vez disparado, a operação **Configurar backup** criará uma instância de backup. Você pode acompanhar o status da operação no painel [instâncias de backup](backup-center-monitor-operate.md#backup-instances) na exibição do centro de backup ou do cofre.

    ![Instâncias de backup](./media/backup-azure-database-postgresql/backup-instances.png)

1. Os backups são disparados de acordo com o agendamento de backup definido na política. Os trabalhos podem ser acompanhados em [trabalhos de backup](backup-center-monitor-operate.md#backup-jobs). No momento, você pode exibir trabalhos para os últimos sete dias.

    ![Trabalhos de backup](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>Criar política de backup

1. Vá para o **centro de backup**  ->  **políticas de backup**  ->  **Adicionar**. Como alternativa, você pode acessar o **cofre de backup**  ->  **política de backup**  ->  **Adicionar**.

    ![Adicionar política de backup](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. Insira um **nome** para a nova política.

    ![Inserir o nome da política](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. Defina o agendamento de backup. Atualmente, há suporte para backup **semanal** . Você pode agendar os backups em um ou mais dias da semana.

    ![Definir o agendamento de backup](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. Defina as configurações de **retenção** . Você pode adicionar uma ou mais regras de retenção. Cada regra de retenção assume entradas para backups específicos e a duração da retenção e do armazenamento de dados para esses backups.

1. Você pode optar por armazenar seus backups em um dos dois repositórios de dados (ou camadas): **armazenamento de dados de backup** (camada Standard) ou armazenamento de dados de **arquivo morto** (em versão prévia). Você pode escolher entre **duas opções de camadas** para definir quando os backups são em camadas entre os dois repositórios de armazenamento:

    - Escolha copiar **imediatamente** se preferir ter uma cópia de backup em armazenamentos de dados de backup e de arquivo simultaneamente.
    - Opte por mover **-se durante a expiração** se preferir mover o backup para o armazenamento de dados de arquivamento após sua expiração no armazenamento de dados de backup.

1. A **regra de retenção padrão** é aplicada na ausência de qualquer outra regra de retenção e tem um valor padrão de três meses.

    - A duração da retenção varia de sete dias a 10 anos no **armazenamento de dados de backup**.
    - A duração da retenção varia de seis meses a 10 anos no **repositório de dados de arquivo morto**.

    ![Editar duração da retenção](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>As regras de retenção são avaliadas em uma ordem predeterminada de prioridade. A prioridade é a mais alta para a regra **anual** , seguida de **mensal** e, em seguida, a regra **semanal** . As configurações de retenção padrão são aplicadas quando nenhuma outra regra se qualificar. Por exemplo, o mesmo ponto de recuperação pode ser o primeiro backup bem-sucedido realizado todas as semanas, bem como o primeiro backup bem-sucedido realizado todos os meses. No entanto, como a prioridade de regra mensal é maior do que a da regra semanal, a retenção correspondente ao primeiro backup bem-sucedido feito todo mês se aplica.

## <a name="restore"></a>Restaurar

Você pode restaurar um banco de dados para qualquer servidor PostgreSQL do Azure dentro da mesma assinatura, se o serviço tiver o conjunto apropriado de permissões no servidor de destino. Verifique se as [permissões de pré-requisito](#prerequisite-permissions-for-configure-backup-and-restore) exigidas pelo serviço para fazer backup dos servidores postgres já estão configuradas.

Siga este guia passo a passo para disparar uma restauração:

1. Há duas maneiras de iniciar o processo de restauração:
    1. Vá para o [centro de backup](backup-center-overview.md)  ->  **visão geral**  ->  **restaurar**.

    ![Selecione restaurar no centro de backup](./media/backup-azure-database-postgresql/backup-center-restore.png)

    Em **Iniciar: restaurar**, selecione o **tipo DataSource** como **banco de dados do Azure para PostgreSQL**. Selecione a **instância de backup**.

    ![Selecione o tipo de fonte de origem em Iniciar: restaurar](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. Como alternativa, você pode acessar diretamente as instâncias de backup do **cofre de backup**  ->  . Selecione **instância de backup** correspondente ao banco de dados que você deseja restaurar.

    ![Instâncias de backup para restauração](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![Lista de instâncias de backup](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![Selecione Restaurar](./media/backup-azure-database-postgresql/select-restore.png)

1. **Selecione ponto de recuperação** na lista de todos os backups completos disponíveis para a instância de backup selecionada. Por padrão, o ponto de recuperação mais recente é selecionado.

    ![Selecionar ponto de recuperação](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![Lista de pontos de recuperação](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. **Parâmetros de restauração** de entrada. Neste ponto, você pode selecionar entre dois tipos de restaurações: **restaurar como banco de dados** e **restaurar como arquivos**.

1. **Restore as Database**: Restore The backup data para criar um novo banco de dados no servidor PostgreSQL de destino.

    - O servidor de destino pode ser o mesmo que o servidor de origem. No entanto, não há suporte para a substituição do banco de dados original.
    - Você pode escolher entre o servidor em todas as assinaturas, mas na mesma região que o cofre.
    - Selecione **revisão + restaurar**. Isso irá disparar a validação para verificar se o serviço tem as permissões de restauração apropriadas no servidor de destino.

    ![Restaurar como banco de dados](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **Restaurar como arquivos**: despeja os arquivos de backup para a conta de armazenamento de destino (BLOBs).

    - Você pode escolher entre as contas de armazenamento em todas as assinaturas, mas na mesma região que o cofre.
    - Selecione o contêiner de destino na lista de contêineres filtrada para a conta de armazenamento selecionada.
    - Selecione **revisão + restaurar**. Isso irá disparar a validação para verificar se o serviço tem as permissões de restauração apropriadas no servidor de destino.

    ![Restaurar como arquivos](./media/backup-azure-database-postgresql/restore-as-files.png)

1. Examine as informações e selecione **restaurar**. Isso irá disparar um trabalho de restauração correspondente que pode ser acompanhado em **trabalhos de backup**.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>Permissões de pré-requisito para configurar backup e restauração

O backup do Azure segue diretrizes de segurança estritas. Embora seja um serviço nativo do Azure, as permissões no recurso não são presumidas e precisam ser explicitamente concedidas pelo usuário.  Da mesma forma, as credenciais para se conectar ao banco de dados não são armazenadas. Isso é importante para proteger seus dados. Em vez disso, usamos Azure Active Directory autenticação.

[Baixe este documento](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) para obter um script automatizado e instruções relacionadas. Ele concederá um conjunto apropriado de permissões para um servidor PostgreSQL do Azure, para backup e restauração.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>Gerenciar os bancos de dados do Azure PostgreSQL com backup

Essas são as operações de gerenciamento que você pode executar nas **instâncias de backup**:

### <a name="on-demand-backup"></a>Backup sob demanda

Para disparar um backup que não está no agendamento especificado na política, acesse fazer backup das **instâncias de backup**  ->  **agora**.
Escolha na lista de regras de retenção que foram definidas na política de backup associada.

![Disparar o backup agora](./media/backup-azure-database-postgresql/backup-now.png)

![Escolher na lista de regras de retenção](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>Parar a proteção

Você pode interromper a proteção em um item de backup. Isso também excluirá os pontos de recuperação associados para esse item de backup. Ainda não oferecemos a opção de parar a proteção enquanto mantém os pontos de recuperação existentes.

![Parar a proteção](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>Alterar política

Você pode alterar a política associada com uma instância de backup.

1. Selecione a política de alteração de **instância de backup**  ->  .

    ![Alterar política](./media/backup-azure-database-postgresql/change-policy.png)

1. Selecione a nova política que você deseja aplicar ao banco de dados.

    ![Reatribuir política](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>Solução de problemas

Esta seção fornece informações de solução de problemas para fazer backup de bancos de dados do PostgreSQL do Azure com o backup do Azure.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Forneça acesso de **leitura** ao msi do cofre de backup no servidor PG do qual você deseja fazer backup ou restaurar:

Para estabelecer uma conexão segura com o banco de dados PostgreSQL, o backup do Azure usa o modelo de autenticação [identidade de serviço gerenciada (MSI)](../active-directory/managed-identities-azure-resources/overview.md) . Isso significa que o cofre de backup terá acesso apenas aos recursos que receberam permissão explícita pelo usuário.

Um sistema MSI é atribuído automaticamente ao cofre no momento da criação. Você precisa dar a esse cofre o MSI o acesso aos servidores PostgreSQL dos quais você pretende fazer backup dos bancos de dados.

Etapas:

1. No servidor Postgres, vá para o painel **controle de acesso (iam)** .

    ![Painel de controle de acesso](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Selecione **Adicionar uma atribuição de função**.

    ![Adicionar atribuição de função](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. No painel de contexto à direita que é aberto, insira o seguinte:<br>

    **Função:** Leitora<br>
    **Atribuir acesso a:** Escolher **cofre de backup**<br>
    Se você não encontrar a opção de **cofre de backup** na lista suspensa, escolha a **opção usuário, grupo ou entidade de serviço do Azure ad**<br>

    ![Selecione a função](./media/backup-azure-database-postgresql/select-role.png)

    **Selecione:** Insira o nome do cofre de backup para o qual você deseja fazer backup deste servidor e de seus bancos de dados.<br>

    ![Insira o nome do cofre de backup](./media/backup-azure-database-postgresql/enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Crie um usuário de backup de banco de dados que possa autenticar com Azure Active Directory:

Esse erro pode vir de uma ausência de um administrador de Azure Active Directory para o servidor PostgreSQL, ou na ausência de um usuário de backup que pode autenticar usando Azure Active Directory.

Etapas:

Adicione um administrador de Active Directory ao servidor OSS:

Essa etapa é necessária para se conectar ao banco de dados por meio de um usuário que pode autenticar com Azure Active Directory em vez de uma senha. O usuário administrador do Azure AD no banco de dados do Azure para PostgreSQL terá a função **azure_ad_admin**. Somente uma função **azure_ad_admin** pode criar novos usuários de banco de dados que podem ser autenticados com o Azure AD.

1. Vá para a guia Active Directory admin no painel de navegação à esquerda da exibição do servidor e adicione você mesmo (ou outra pessoa) como o administrador do Active Directory.

    ![Definir Active Directory administrador](./media/backup-azure-database-postgresql/set-admin.png)

1. Certifique-se de **salvar** a configuração de usuário administrador do AD.

    ![Salvar Active Directory configuração do usuário administrador](./media/backup-azure-database-postgresql/save-admin-setting.png)

Consulte [este documento](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) para obter a lista de etapas que você precisa executar para concluir as etapas de concessão de permissão.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Estabeleça a linha de visão da rede habilitando o sinalizador **permitir acesso aos serviços do Azure** na exibição do servidor. Na exibição do servidor, no painel **segurança da conexão** , defina o sinalizador **permitir acesso aos serviços do Azure** como **Sim**.

![Permitir acesso aos serviços do Azure](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Permissão para restaurar para um contêiner de conta de armazenamento ao restaurar como arquivos

1. Forneça ao cofre de backup o MSI a permissão para acessar os contêineres da conta de armazenamento usando o portal do Azure.
    1. Vá para controle de acesso da **conta de armazenamento**  ->    ->  **Adicionar atribuição de função**.
    1. Atribua a função de **colaborador de dados de blob de armazenamento** ao msi do cofre de backup.

    ![Atribuir função de colaborador de dados de blob de armazenamento](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. Como alternativa, forneça permissões granulares para o contêiner específico para o qual você está restaurando usando o comando CLI do Azure [AZ role Assignment Create](/cli/azure/role/assignment) .

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Substitua o parâmetro de atribuição com a **ID do aplicativo** do MSI do cofre e o parâmetro de escopo para se referir ao seu contêiner específico.
    1. Para obter a **ID do aplicativo** do MSI do cofre, selecione **todos os aplicativos** em **tipo de aplicativo**:

        ![Selecionar todos os aplicativos](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Procure o nome do cofre e copie a ID do aplicativo:

        ![Pesquisar nome do cofre](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos cofres de backup](backup-vault-overview.md)