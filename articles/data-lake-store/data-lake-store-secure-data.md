---
title: Protegendo os dados armazenados no Armazenamento de Data Lake do Azure Gen1 | Microsoft Docs
description: Aprenda como proteger dados no Azure Data Lake Storage Gen1 usando grupos e listas de controle de acesso
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: ac7666f4c4e68d24499f9c097dc9bd021d270355
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370688"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Protegendo Dados Armazenados no Armazenamento de Data Lake do Azure Gen1
A proteção de dados no Azure Data Lake Storage Gen1 é uma abordagem em três etapas.  Tanto o controle de acesso baseado em função do Azure (RBAC do Azure) quanto as listas de controle de acesso (ACLs) devem ser definidos para habilitar totalmente o acesso aos dados para usuários e grupos de segurança.

1. Comece criando grupos de segurança no Azure Active Directory (Azure AD). Esses grupos de segurança são usados para implementar o controle de acesso baseado em função do Azure (RBAC do Azure) no portal do Azure. Para obter mais informações, consulte [RBAC do Azure](../role-based-access-control/role-assignments-portal.md).
2. Atribua os grupos de segurança do Azure AD à conta de Data Lake Storage Gen1. Isso controla o acesso à conta do Data Lake Storage Gen1 do portal e das operações de gerenciamento do portal ou das APIs.
3. Atribua os grupos de segurança do Azure AD como listas de controle de acesso (ACLs) no sistema de arquivos Data Lake Storage Gen1.
4. Além disso, você também pode definir um intervalo de endereços IP para clientes que possam acessar os dados no Data Lake Storage Gen1.

Este artigo fornece instruções sobre como usar o Portal do Azure para realizar as tarefas acima. Para obter informações detalhadas sobre como o Data Lake Storage Gen1 implementa a segurança no nível da conta e dos dados, consulte [Segurança no armazenamento de dados do Lake Azure no Gen1](data-lake-store-security-overview.md). Para obter informações detalhadas sobre como as ACLs são implementadas no Data Lake Storage Gen1, consulte [Visão geral do controle de acesso no Data Lake Storage Gen1](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Data Lake Storage Gen1**. Para obter instruções sobre como criar uma, consulte Introdução [ao Azure data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Criar grupos de segurança no Active Directory do Azure
Para obter instruções sobre como criar grupos de segurança do Azure AD e como adicionar usuários ao grupo, consulte [Managing Security Groups in Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Adicione usuários e outros grupos a um grupo no Azure AD usando o portal do Azure. No entanto, para adicionar uma entidade de serviço a um grupo, use um [módulo do PowerShell do Azure AD](../active-directory/enterprise-users/groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Atribuir usuários ou grupos de segurança a contas do Data Lake Storage Gen1
Quando você atribui usuários ou grupos de segurança a contas do Data Lake Storage Gen1, controla o acesso às operações de gerenciamento na conta usando o portal do Azure e as APIs do Azure Resource Manager. 

1. Abra uma conta do Data Lake Storage Gen1. No painel esquerdo, clique em **Todos os recursos**, e depois, na folha Todos os recursos, clique no nome da conta sob a qual você deseja criar um usuário ou grupo de segurança.

2. Na folha de conta do Data Lake Storage Gen1, clique em **Access Control (IAM)**. Por padrão, a folha lista os proprietários de assinatura como o proprietário.
   
    ![Atribuir grupo de segurança à conta de Azure Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Atribuir grupo de segurança à conta de Azure Data Lake Storage Gen1")

3. Na folha do **Controle de Acesso (IAM)**, clique em **Adicionar** para abrir a folha **Adicionar permissões**. Na folha **Adicionar permissões**, selecione um **Função** para o usuário/grupo. Procure o grupo de segurança criado anteriormente no Microsoft Azure Active Directory e selecione-o. Se houver muitos usuários e grupos a sua pesquisa, use a caixa de texto **Selecionar** na parte superior para filtrar pelo nome do grupo. 
   
    ![Adicionar uma função ao usuário](./media/data-lake-store-secure-data/adl.add.user.1.png "Adicionar uma função ao usuário")
   
    As funções **Proprietário** e **Colaborador** fornecem acesso a várias funções de administração da conta do Data Lake. Para usuários que irão interagir com dados no lago de dados, mas ainda precisa exibir informações de gerenciamento de conta, você pode adicioná-los à função do **Leitor**. O escopo dessas funções é limitado às operações de gerenciamento relacionadas à conta do Data Lake armazenamento Gen1.
   
    Para as operações de dados, as permissões do sistema de arquivos individual definem o que os usuários podem fazer. Portanto, um usuário com uma função de Leitor pode exibir somente as configurações administrativas associadas à conta, mas possivelmente pode ler e gravar dados com base nas permissões de sistema de arquivo atribuídas a ele. As permissões do sistema de arquivos Data Lake Storage Gen1 são descritas em [Atribuir grupo de segurança como ACLs ao sistema de arquivos do Azure Data Lake Storage Gen1](#filepermissions).

    > [!IMPORTANT]
    > Somente a função do **Proprietário** automaticamente habilita o acesso de sistema de arquivos. O **Colaborador**, **Leitor**, e todas as outras funções exigem ACLs para permitir qualquer nível de acesso a arquivos e pastas.  A função do **Proprietário** fornece arquivo de superusuário e permissões que não podem ser substituídas via ACLs. Para obter mais informações sobre como as políticas RBAC do Azure são mapeadas para acesso a dados, consulte [RBAC do Azure para gerenciamento de conta](data-lake-store-security-overview.md#azure-rbac-for-account-management).

4. Se você deseja adicionar um grupo/usuário não listado na folha **Adicionar permissões**, você pode convidá-los digitando seu endereço de email na caixa de texto **Selecionar**, e em seguida, selecione-os na lista.
   
    ![Adicionar um grupo de segurança](./media/data-lake-store-secure-data/adl.add.user.2.png "Adicionar um grupo de segurança")
   
5. Clique em **Save** (Salvar). O grupo de segurança deve ter sido adicionado, como exibido abaixo.
   
    ![Grupo de segurança adicionado](./media/data-lake-store-secure-data/adl.add.user.3.png "Grupo de segurança adicionado")

6. Seu grupo de segurança do usuário agora tem acesso à conta do Data Lake armazenamento Gen1. Se você quiser fornecer acesso a usuários específicos, adicione-os ao grupo de segurança. Da mesma forma, se você quiser revogar o acesso de um usuário, remova-o do grupo de segurança. Também é possível atribuir vários grupos de segurança a uma conta. 

## <a name="assign-users-or-security-groups-as-acls-to-the-data-lake-storage-gen1-file-system"></a><a name="filepermissions"></a> Atribuir usuários ou grupos de segurança como ACLs ao sistema de arquivos Data Lake Storage Gen1
Ao atribuir grupos de usuários / segurança ao sistema de arquivos do Data Lake Storage Gen1, você define o controle de acesso nos dados armazenados no Data Lake Storage Gen1.

1. Na folha de sua conta do Data Lake Storage Gen1, clique em **Data Explorer**.
   
    ![Exibir dados por meio de Data Explorer](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Exibir dados por meio de Data Explorer")
2. Na folha **Data Explorer**, clique na pasta para o qual você deseja configurar a ACL e, em seguida, clique em **Acessar**. Para atribuir as ACLs em um arquivo, primeiro você clique no arquivo para visualizá-lo e, em seguida, clique em **Acesso** da **folha Exibição de arquivo**.
   
    ![Definir ACLs no sistema de arquivos Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.acl.1.png "Definir ACLs no sistema de arquivos Data Lake Storage Gen1")
3. A folha de **Acesso** lista os proprietários e permissões já atribuído para a raiz. Clique no ícone **Adicionar** para adicionar ACLs de Acesso.
    > [!IMPORTANT]
    > Configuração das permissões de acesso para um único arquivo necessariamente não concede um acesso de usuário/grupo a esse arquivo. O caminho para o arquivo deve estar acessível para o usuário/grupo atribuído. Para obter mais informações e exemplos, consulte [Cenários comuns relacionados às permissões](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Listar acesso padrão e personalizado](./media/data-lake-store-secure-data/adl.acl.2.png "Listar acesso padrão e personalizado")
   
   * Os **Proprietários** e **Ouras pessoas** fornecem acesso ao estilo UNIX, no qual você especifica leitura, gravação e execução (rwx) a três classes de usuário distintas: proprietário, grupo e outros.
   * **Permissões atribuídas** correspondem às ACLs POSIX e permite a definição de permissões para usuários ou grupos nomeados específicos, além do proprietário do arquivo ou grupo. 
     
     Para saber mais, consulte [ACLs HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Para obter mais informações sobre como as ACLs são implementadas no Data Lake Storage Gen1, consulte [Access Control no Data Lake Storage Gen1](data-lake-store-access-control.md).
4. Clique no ícone **Adicionar** para abrir a folha **Atribuir permissões**. Nessa folha, clique em **Selecionar Usuário ou Grupo** e na folha **Selecionar Usuário ou Grupo**, procure o grupo de segurança que você criou anteriormente no Azure Active Directory. Se houver muitos grupos para sua pesquisa, use a caixa de texto na parte superior para filtrar pelo nome do grupo. Clique no grupo que você deseja adicionar e clique em **Selecionar**.
   
    ![Adicionar um grupo](./media/data-lake-store-secure-data/adl.acl.3.png "Adicionar um grupo")
5. Clique em **Selecionar Permissões**, selecione as permissões, todas as permissões devem ser aplicadas recursivamente, e sempre que você quiser atribuir as permissões como um ACL de acesso, ACL padrão ou ambos. Clique em **OK**.
   
    ![Captura de tela da folha atribuir permissões com a opção Selecionar permissões chamada out e a folha selecionar permissões com a opção Ok chamada out.](./media/data-lake-store-secure-data/adl.acl.4.png "Atribuir permissões ao grupo")
   
    Para obter mais informações sobre permissões no Data Lake armazenamento Gen1 e ACLs de acesso/padrão, consulte [controle de acesso no Data Lake armazenamento Gen1](data-lake-store-access-control.md).
6. Depois de clicar em **Ok** na folha **Selecionar permissões**, o grupo recém-adicionado e permissões associadas agora estarão listadas na folha **Acesso**.
   
    ![Captura de tela da folha de acesso com a opção de engenharia de dados chamada out.](./media/data-lake-store-secure-data/adl.acl.5.png "Atribuir permissões ao grupo")
   
   > [!IMPORTANT]
   > Na versão atual, você pode ter até 28 entradas em **Permissões atribuídas**. Se você quiser adicionar mais de 28 usuários, deverá criar grupos de segurança, adicionar usuários a grupos de segurança, adicionar acesso a esses grupos de segurança para a conta do Data Lake Storage Gen1.
   > 
   > 
7. Se for necessário, também é possível modificar as permissões de acesso depois de adicionar o grupo. Marque ou desmarque a caixa de seleção de cada tipo de permissão (Leitura, Gravação, Execução) com base em seu desejo de remover ou atribuir essa permissão ao grupo de segurança. Clique em **Salvar** para salvar as alterações ou em **Descartar** para desfazer as alterações.

## <a name="set-ip-address-range-for-data-access"></a>Definir o intervalo de endereços IP para acesso a dados
O Data Lake Storage Gen1 permite bloquear ainda mais o acesso ao seu armazenamento de dados no nível da rede. Você pode habilitar o firewall e definir um intervalo de endereços IP para seus clientes confiáveis. Uma vez habilitado, somente os clientes com os endereços IP no intervalo definido poderão conectar o repositório.

![Configurações do firewall e acesso IP](./media/data-lake-store-secure-data/firewall-ip-access.png "Configurações de firewall e endereço IP")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Remover grupos de segurança para uma conta do Data Lake Storage Gen1
Quando você remove grupos de segurança de contas do Data Lake Storage Gen1, você está apenas alterando o acesso às operações de gerenciamento na conta usando as APIs portal do Azure e Azure Resource Manager.  

O acesso a dados é alterado e ainda é gerenciado pelo acesso de ACLs.  A exceção a isso são os usuários/grupos na função Proprietários.  Usuários/grupos não são mais superusuários e seu acesso voltará para acessar configurações de ACL. 

1. Na folha de conta do Data Lake Storage Gen1, clique em **Access Control (IAM)**. 
   
    ![Atribuir grupo de segurança à conta de Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.select.user.icon.png "Atribuir grupo de segurança à conta de Data Lake Storage Gen1")
2. Na folha **Controle de Acesso (IAM)**, clique no grupo de segurança que você deseja remover. Clique em **Remover**.
   
    ![Grupo de segurança removido](./media/data-lake-store-secure-data/adl.remove.group.png "Grupo de segurança removido")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Remover as ACLs do grupo de segurança de um sistema de arquivos Data Lake Storage Gen1
Quando você remove as ACLs do grupo de segurança de um sistema de arquivos do Data Lake Storage Gen1, altera o acesso aos dados na conta do Data Lake Storage Gen1.

1. Na folha de sua conta do Data Lake Storage Gen1, clique em **Data Explorer**.
   
    ![Criar diretórios na conta de Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Criar diretórios na conta de Data Lake Storage Gen1")
2. Na folha **Data Explorer**, clique na pasta para o qual você deseja remover a ACL e, em seguida, clique em **Acessar**. Para remover as ACLs em um arquivo, primeiro você clique no arquivo para visualizá-lo e, em seguida, clique em **Acesso** da **folha Exibição de arquivo**. 
   
    ![Definir ACLs no sistema de arquivos Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.acl.1.png "Definir ACLs no sistema de arquivos Data Lake Storage Gen1")
3. Na folha **Acesso**, clique no grupo de segurança que você deseja remover. Na folha **Acessar detalhes**, clique em **Remover**.
   
    ![Captura de tela da folha de acesso com a opção de engenharia de dados chamada saída e a folha detalhes de acesso com a opção remover chamada out.](./media/data-lake-store-secure-data/adl.remove.acl.png "Atribuir permissões ao grupo")

## <a name="see-also"></a>Veja também
* [Visão Geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Copiar dados do Azure Storage Blobs para o Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Usar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Introdução ao Armazenamento do Data Lake Gen1 usando o PowerShell](data-lake-store-get-started-powershell.md)
* [Introdução ao Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
* [Acesse os logs de diagnóstico do Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md)
