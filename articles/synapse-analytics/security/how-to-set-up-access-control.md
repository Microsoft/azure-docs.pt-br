---
title: Como configurar o controle de acesso para seu espaço de trabalho Synapse
description: Este artigo ensinará como controlar o acesso a um espaço de trabalho do Synapse usando funções do Azure, funções do Synapse, permissões do SQL e permissões de git.
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/03/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 513d23de3d7f823ec934af32e5f3776e876f02d8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368992"
---
# <a name="how-to-set-up-access-control-for-your-synapse-workspace"></a>Como configurar o controle de acesso para seu espaço de trabalho Synapse 

Este artigo ensinará como controlar o acesso a um espaço de trabalho do Synapse usando funções do Azure, funções do Synapse, permissões do SQL e permissões de git.   

Neste guia, você configurará um espaço de trabalho e configurará um sistema de controle de acesso básico adequado para muitos projetos Synapse.  Em seguida, ele descreve opções mais avançadas para um controle mais refinado, caso você precise dela.  

O controle de acesso do Synapse pode ser simplificado usando grupos de segurança que estão alinhados com as funções e pessoalmente em sua organização.  Você só precisa adicionar e remover usuários de grupos de segurança para gerenciar o acesso.

Antes de iniciar este passo a passos, leia a [visão geral do controle de acesso do Synapse](./synapse-workspace-access-control-overview.md) para se familiarizar com os mecanismos de controle de acesso usados pelo Synapse.   

## <a name="access-control-mechanisms"></a>Mecanismos de controle de acesso

> [!NOTE]
> A abordagem adotada neste guia é criar vários grupos de segurança e, em seguida, atribuir funções a esses grupos. Depois que os grupos são configurados, você só precisa gerenciar a associação dentro dos grupos de segurança para controlar o acesso ao espaço de trabalho.

Para proteger um espaço de trabalho do Synapse, você seguirá um padrão de configuração dos seguintes itens:

- **Grupos de segurança**, para agrupar usuários com requisitos de acesso semelhantes.
- **Funções do Azure**, para controlar quem pode criar e gerenciar pools de SQL, pools de Apache Spark e tempos de execução de integração e acessar ADLS Gen2 armazenamento.
- **Synapse funções**, para controlar o acesso a artefatos de código publicados, o uso de Apache Spark recursos de computação e tempos de execução de integração 
- **Permissões SQL**, para controlar o acesso administrativo e de plano de dados a pools SQL. 
- **Permissões de git** para controlar quem pode acessar os artefatos de código no controle do código-fonte se você configurar o suporte do git para o espaço de trabalho 
 
## <a name="steps-to-secure-a-synapse-workspace"></a>Etapas para proteger um workspace do Synapse

Este documento usa nomes padrão para simplificar as instruções. Substitua-os por nomes de sua escolha.

|Setting | Nome padrão | Descrição |
| :------ | :-------------- | :---------- |
| **Workspace do Synapse** | `workspace1` |  O nome que o workspace do Synapse terá. |
| **Conta do ADLSGEN2** | `storage1` | A conta do ADLS a ser usada com seu workspace. |
| **Contêiner** | `container1` | O contêiner no STG1 que o workspace usará por padrão. |
| **Locatário do Active Directory** | `contoso` | o nome do locatário do Active Directory.|
||||

## <a name="step-1-set-up-security-groups"></a>ETAPA 1: Configurar grupos de segurança

>[!Note] 
>Durante a versão prévia, era recomendável criar grupos de segurança mapeados para o **administrador do SQL Synapse Synapse** e as funções de administrador do **Synapse Apache Spark** .  Com a introdução de novas funções e escopos de RBAC Synapse mais refinados, agora é recomendável que você use esses novos recursos para controlar o acesso ao seu espaço de trabalho.  Essas novas funções e escopos fornecem mais flexibilidade de configuração e reconhecem que os desenvolvedores geralmente usam uma combinação de SQL e Spark na criação de aplicativos de análise e podem precisar receber acesso a recursos específicos em vez de todo o espaço de trabalho. [Saiba mais](./synapse-workspace-synapse-rbac.md) sobre o RBAC Synapse.

Crie os seguintes grupos de segurança para seu espaço de trabalho:

- **`workspace1_SynapseAdministrators`**, para usuários que precisam de controle total sobre o espaço de trabalho.  Adicione-se a esse grupo de segurança, pelo menos inicialmente.
- **`workspace1_SynapseContributors`**, para os desenvolvedores que precisam desenvolver, depurar e publicar código para o serviço.   
- **`workspace1_SynapseComputeOperators`**, para os usuários que precisam gerenciar e monitorar pools de Apache Spark e tempos de execução de integração.
- **`workspace1_SynapseCredentialUsers`**, para usuários que precisam depurar e executar pipelines de orquestração usando a credencial MSI do espaço de trabalho (identidade de serviço gerenciado) e cancelar execuções de pipeline.   

Em breve, você atribuirá funções Synapse a esses grupos no escopo do espaço de trabalho.  

Crie também esse grupo de segurança: 
- **`workspace1_SQLAdmins`**, grupo para usuários que precisam de autoridade de administrador do SQL Active Directory em pools SQL no espaço de trabalho. 

O `workspace1_SQLAdmins` grupo será usado quando você configurar permissões do SQL em pools do SQL enquanto os cria. 

Para uma configuração básica, esses cinco grupos são suficientes. Posteriormente, você pode adicionar grupos de segurança para lidar com os usuários que precisam de acesso mais especializado ou para conceder aos usuários acesso apenas a recursos específicos.

> [!NOTE]
>- Saiba como criar um grupo de segurança de rede [neste artigo](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).
>- Saiba como adicionar um grupo de segurança com base em outro grupo de segurança [neste artigo](../../active-directory/fundamentals/active-directory-groups-membership-azure-portal.md).

>[!Tip]
>Os usuários individuais do Synapse podem usar Azure Active Directory no portal do Azure para exibir suas associações de grupo para determinar quais funções foram concedidas.

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>ETAPA 2: preparar sua conta de armazenamento ADLS Gen2

Um espaço de trabalho Synapse usa um contêiner de armazenamento padrão para:
  - Armazenar os arquivos de dados de backup para tabelas do Spark
  - Logs de execução para trabalhos do Spark
  - Gerenciando bibliotecas que você escolhe instalar

Identifique as seguintes informações sobre seu armazenamento:

- A conta de ADLS Gen2 a ser usada para seu espaço de trabalho. Este documento o chama `storage1` . `storage1` é considerado a conta de armazenamento "primária" para seu espaço de trabalho.
- O contêiner no `workspace1` seu espaço de trabalho do Synapse será usado por padrão. Este documento o chama `container1` . 

- Usando o portal do Azure, atribua as seguintes funções do Azure `container1` para os grupos de segurança 

  - Atribuir a função de **colaborador de dados do blob de armazenamento** a `workspace1_SynapseAdmins` 
  - Atribuir a função de **colaborador de dados do blob de armazenamento** a `workspace1_SynapseContributors`
  - Atribuir a função de **colaborador de dados do blob de armazenamento** a `workspace1_SynapseComputeOperators`

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>ETAPA 3: Criar e configurar o workspace do Synapse

No portal do Azure, crie um workspace do Synapse:

- Selecionar sua assinatura
- Selecione ou crie um grupo de recursos para o qual você tem a função **proprietário** do Azure.
- Nomear o espaço de trabalho `workspace1`
- Escolha `storage1` para a conta de armazenamento
- Escolha `container1` para o contêiner que está sendo usado como "FileSystem".
- Abra WS1 no Synapse Studio
- Navegue até **gerenciar**  >  o **controle de acesso** e atribua funções Synapse no escopo do espaço de *trabalho* aos grupos de segurança da seguinte maneira:
  - Atribuir a função de **administrador de Synapse** a `workspace1_SynapseAdministrators` 
  - Atribuir a função **colaborador de Synapse** a `workspace1_SynapseContributors` 
  - Atribuir a função de **operador de computação Synapse** para `workspace1_SynapseComputeOperators`

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>ETAPA 4: conceder o acesso de MSI do espaço de trabalho ao contêiner de armazenamento padrão

Para executar pipelines e executar tarefas do sistema, o Synapse requer que a MSI (identidade do serviço gerenciado) do espaço de trabalho precise acessar `container1` na conta de ADLS Gen2 padrão.

- Abrir o portal do Azure
- Localize a conta de armazenamento, `storage1` e, em seguida, `container1`
- Usando **iam (controle de acesso)**, verifique se a função **colaborador de dados do blob de armazenamento** está atribuída ao msi do espaço de trabalho
  - Se não estiver atribuído, atribua-o.
  - A MSI tem o mesmo nome que o workspace. Neste artigo, seria `workspace1` .

## <a name="step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>ETAPA 5: conceder aos administradores do Synapse a função de colaborador do Azure no espaço de trabalho 

Para criar pools de SQL, pools de Apache Spark e tempos de execução de integração, os usuários devem ter pelo menos acesso de colaborador do Azure ao espaço de trabalho. A função colaborador também permite que esses usuários gerenciem os recursos, incluindo pausa e dimensionamento.

- Abrir o portal do Azure
- Localize o espaço de trabalho, `workspace1`
- Atribua a função **colaborador do Azure** no `workspace1` `workspace1_SynapseAdministrators` . 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>ETAPA 6: atribuir função de administrador do SQL Active Directory

O criador do espaço de trabalho é configurado automaticamente como o administrador do SQL Active Directory para o espaço de trabalho.  Apenas um único usuário ou grupo pode receber essa função. Nesta etapa, você atribui o administrador do SQL Active Directory no espaço de trabalho ao `workspace1_SQLAdmins` grupo de segurança.  Atribuir essa função dá a esse grupo acesso de administrador altamente privilegiado a todos os pools e bancos de dados do SQL no espaço de trabalho.   

- Abrir o portal do Azure
- Navegue até `workspace1`
- Em **configurações**, selecione **administrador do SQL Active Directory**
- Selecione **definir administrador** e escolha **`workspace1_SQLAdmins`**

>[!Note]
>A etapa 6 é opcional.  Você pode optar por conceder ao `workspace1_SQLAdmins` grupo uma função menos privilegiada. Para atribuir `db_owner` ou outras funções SQL, você deve executar scripts em cada banco de dados SQL. 

## <a name="step-7-grant-access-to-sql-pools"></a>ETAPA 7: conceder acesso a pools SQL

Por padrão, todos os usuários atribuídos à função de administrador Synapse também recebem a `db_owner` função SQL no pool SQL sem servidor, ' built-in ' e em todos os seus bancos de dados.

O acesso a pools SQL para outros usuários e para o MSI do espaço de trabalho é controlado usando permissões SQL.  A atribuição de permissões SQL requer que os scripts SQL sejam executados em cada banco de dados SQL após a criação.  Há três casos que exigem a execução desses scripts:
1. Conceder a outros usuários acesso ao pool de SQL sem servidor, ' built-in ' e seus bancos de dados
2. Concedendo a qualquer usuário acesso a bancos de dados de pools dedicados
3. Conceder o acesso de MSI do espaço de trabalho a um banco de dados do pool SQL para habilitar pipelines que exigem o acesso do pool do SQL para serem executados com êxito.

Scripts SQL de exemplo estão incluídos abaixo.

Para conceder acesso a um banco de dados do pool SQL dedicado, os scripts podem ser executados pelo criador do espaço de trabalho ou por qualquer membro do `workspace1_SQLAdmins` grupo.  

Para conceder acesso ao pool do SQL sem servidor, ' built-in ', os scripts podem ser executados por qualquer membro do `workspace1_SQLAdmins` grupo ou do  `workspace1_SynapseAdministrators` grupo. 

> [!TIP]
> As etapas a seguir precisam ser executadas para **cada** pool do SQL para conceder acesso de usuário a todos os bancos de dados SQL, exceto na seção [permissão no escopo do espaço de trabalho](#workspace-scoped-permission) , na qual você pode atribuir um usuário a uma função sysadmin no nível do espaço de trabalho.

### <a name="step-71-serverless-sql-pool-built-in"></a>ETAPA 7,1: pool de SQL sem servidor, interno

Nesta seção, há exemplos de script que mostram como conceder a um usuário permissão para acessar um banco de dados específico ou todos os bancos de dado no pool SQL sem servidor, ' built-in '.

> [!NOTE]
> Nos exemplos de script, substitua *alias* pelo alias do usuário ou grupo que está recebendo acesso e *domínio* com o domínio da empresa que você está usando.

#### <a name="database-scoped-permission"></a>Permissão no escopo do banco de dados

Para conceder acesso a um usuário para um **único** banco de dados SQL sem servidor, siga as etapas neste exemplo:

1. Criar um LOGON

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Criar um USUÁRIO

    ```sql
    use yourdb -- Use your database name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. Adicionar o USUÁRIO aos membros da função especificada

    ```sql
    use yourdb -- Use your database name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

#### <a name="workspace-scoped-permission"></a>Permissão no escopo do espaço de trabalho

Para conceder acesso completo a **todos os** POOLs SQL sem servidor no espaço de trabalho, use o script neste exemplo:

```sql
use master
go
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE sysadmin ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>ETAPA 7,2: pools de SQL dedicados

Para conceder acesso a um **único** banco de dados do pool do SQL dedicado, siga estas etapas no editor de script do Synapse SQL:

1. Crie o usuário no banco de dados executando o seguinte comando no banco de dados de destino, selecionado usando a lista suspensa *conectar ao* :

    ```sql
    --Create user in the database
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Conceda ao usuário uma função para acessar o banco de dados:

    ```sql
    --Grant role to the user in the database
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* e *db_datawriter* podem funcionar para permissões de leitura/gravação se a concessão de *db_owner* permissão não for desejada.
> Para que um usuário do Spark Leia e grave diretamente do Spark no ou em um pool do SQL, *db_owner* permissão é necessária.

Depois de criar os usuários, execute as consultas para validar se o pool SQL sem servidor pode consultar a conta de armazenamento.

### <a name="step-73-sql-access-control-for-synapse-pipeline-runs"></a>ETAPA 7,3: controle de acesso do SQL para execuções de pipeline do Synapse

### <a name="workspace-managed-identity"></a>Identidade gerenciada do espaço de trabalho

> [!IMPORTANT]
> Para executar pipelines com êxito que incluem conjuntos de valores ou atividades que fazem referência a um pool SQL, a identidade do espaço de trabalho precisa receber acesso ao pool do SQL.

Execute os seguintes comandos em cada pool de SQL para permitir que a identidade do sistema gerenciada do espaço de trabalho execute pipelines nos bancos de dados do pool do SQL:  

>[!note]
>Nos scripts abaixo, para um banco de dados do pool SQL dedicado, DatabaseName é o mesmo que o nome do pool.  Para um banco de dados no pool SQL sem servidor ' built-in ', DatabaseName é o nome do banco de dados.

```sql
--Create a SQL user for the workspace MSI in database
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<databasename> TO <workspacename>;
```

Essa permissão pode ser removida pela execução do seguinte script no mesmo pool de SQL:

```sql
--Revoke permission granted to the workspace MSI
REVOKE CONTROL ON DATABASE::<databasename> TO <workspacename>;

--Delete the workspace MSI user in the database
DROP USER [<workspacename>];
```

## <a name="step-8-add-users-to-security-groups"></a>ETAPA 8: adicionar usuários a grupos de segurança

A configuração inicial do seu sistema de controle de acesso foi concluída.

Para gerenciar o acesso, você pode adicionar e remover usuários dos grupos de segurança que você configurou.  Embora você possa atribuir usuários manualmente a funções Synapse, se fizer isso, ele não configurará suas permissões de forma consistente. Em vez disso, apenas adicione usuários aos grupos de segurança ou remova-os de lá.

## <a name="step-9-network-security"></a>ETAPA 9: segurança de rede

Como etapa final para proteger seu espaço de trabalho, você deve proteger o acesso à rede, usando:
- [Firewall do espaço de trabalho](./synapse-workspace-ip-firewall.md)
- [Rede virtual gerenciada](./synapse-workspace-managed-vnet.md) 
- [Pontos de extremidade privados](./synapse-workspace-managed-private-endpoints.md)
- [Link privado](../../azure-sql/database/private-endpoint-overview.md)

## <a name="step-10-completion"></a>ETAPA 10: conclusão

Agora seu workspace está totalmente configurado e protegido.

## <a name="supporting-more-advanced-scenarios"></a>Dando suporte a cenários mais avançados

Este guia se concentrou na configuração de um sistema de controle de acesso básico. Você pode dar suporte a cenários mais avançados criando grupos de segurança adicionais e atribuindo esses grupos funções mais granulares em escopos mais específicos. Considere os seguintes casos:

**Habilite o suporte ao git** para o espaço de trabalho para cenários de desenvolvimento mais avançados, incluindo CI/CD.  No modo git, as permissões git determinarão se um usuário pode confirmar as alterações em seu Branch de trabalho.  A publicação no serviço só ocorre a partir da ramificação de colaboração.  Considere a criação de um grupo de segurança para desenvolvedores que precisam desenvolver e depurar atualizações em um Branch de trabalho, mas que não precisam publicar alterações no serviço ativo.

**Restringir o acesso do desenvolvedor** a recursos específicos.  Crie grupos de segurança mais refinados para os desenvolvedores que precisam de acesso apenas a recursos específicos.  Atribua a esses grupos funções Synapse apropriadas que estejam no escopo de pools Spark específicos, tempos de execução de integração ou credenciais.

**Restrinja que os operadores acessem artefatos de código**.  Crie grupos de segurança para operadores que precisam monitorar o status operacional de recursos de computação Synapse e exibir logs, mas que não precisam de acesso ao código ou para publicar atualizações no serviço. Atribua esses grupos a função de operador de computação com escopo para pools Spark específicos e tempos de execução de integração.  

## <a name="next-steps"></a>Próximas etapas

Saiba [como gerenciar atribuições de função do RBAC Synapse](./how-to-manage-synapse-rbac-role-assignments.md) criar um [espaço de trabalho do Synapse](../quickstart-create-workspace.md)
