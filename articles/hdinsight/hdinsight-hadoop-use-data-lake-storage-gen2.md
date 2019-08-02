---
title: Usar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight
description: Saiba como usar o armazenamento do Azure Data Lake Gen2 com clusters de HDInsight do Azure.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: hrasheed
ms.openlocfilehash: dd639ae7e05309ab4528eb460ce38550db4cffe1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670771"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Usar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight

Gen2 de armazenamento do Azure Data Lake é um serviço de armazenamento de nuvem dedicado para análise de big data, criado no armazenamento de BLOBs do Azure. Data Lake armazenamento Gen2 combina os recursos de armazenamento de BLOBs do Azure e o Azure Data Lake armazenamento Gen1. O serviço resultante oferece recursos do Azure Data Lake armazenamento Gen1, como escalabilidade, juntamente com o armazenamento hierárquico de baixo custo, alta disponibilidade e recursos de recuperação de desastre, segurança em nível de arquivo e de nível de diretório e semântica do sistema de arquivos do armazenamento de BLOBs do Azure.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilidade o Data Lake Storage Gen2

Data Lake armazenamento Gen2 está disponível como uma opção de armazenamento para quase todos os tipos de cluster de HDInsight do Azure como um padrão e uma conta de armazenamento adicional. No entanto, o HBase, pode ter apenas uma conta do Data Lake armazenamento Gen2.

> [!Note]  
> Depois de selecionar o Data Lake armazenamento Gen2 como seu **tipo de armazenamento primário**, não é possível selecionar uma conta do Data Lake armazenamento Gen1 como armazenamento adicional.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Criar um cluster com o Data Lake armazenamento Gen2 por meio do portal do Azure

Para criar um cluster HDInsight que usa o Data Lake armazenamento Gen2 para armazenamento, siga estas etapas para configurar uma conta do Data Lake armazenamento Gen2.

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

Crie uma identidade gerenciada atribuída pelo usuário, se ainda não tiver uma. Confira [Criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). Para obter mais informações sobre o trabalho de identidades como gerenciado no Azure HDInsight, consulte [gerenciados identidades no Azure HDInsight](hdinsight-managed-identities.md).

![Criar uma identidade gerenciada atribuída ao usuário](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Criar uma conta do Data Lake Storage Gen2

Criar uma conta de armazenamento do Azure Data Lake Storage Gen2. Certifique-se de que o **namespace hierárquico** opção está habilitada. Para obter mais informações, confira [Início Rápido: Criar uma conta de armazenamento do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Captura de tela mostrando a criação da conta de armazenamento no portal do Azure](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Configurar permissões para a identidade gerenciada na conta do Data Lake armazenamento Gen2

Atribuir a identidade gerenciada para o **proprietário de dados do armazenamento de Blob** função na conta de armazenamento. Para obter mais informações, consulte [Gerenciar direitos de acesso para dados de Blob e fila do Azure com RBAC (visualização)](../storage/common/storage-auth-aad-rbac.md).

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.
1. Selecione sua conta de armazenamento e, em seguida, selecione **controle de acesso (IAM)** para exibir as configurações de controle de acesso para a conta. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.
    
    ![Captura de tela mostrando as configurações de controle de acesso de armazenamento](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
1. Selecione o **+ Adicionar atribuição de função** botão para adicionar uma nova função.
1. No **Adicionar atribuição de função** janela, selecione a **proprietário de dados do armazenamento de Blob** função. Em seguida, selecione a assinatura que tem a conta de armazenamento e a identidade gerenciada. Em seguida, pesquise para localizar a identidade gerenciada atribuída pelo usuário que você criou anteriormente. Por fim, selecione a identidade gerenciada e ele será listado sob **membros selecionados**.
    
    ![Captura de tela que mostra como atribuir uma função do RBAC](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
1. Clique em **Salvar**. Agora, a identidade atribuída pelo usuário que você selecionou é listada sob a função selecionada.
1. Ao concluir a configuração inicial, você poderá criar um cluster usando o portal. O cluster deve estar na mesma região do Azure da conta de armazenamento. Na seção **Armazenamento** do menu de criação do cluster, selecione as seguintes opções:
        
    * Para **tipo de armazenamento primário**, selecione **armazenamento do Azure Data Lake Gen2**.
    * Sob **selecione uma conta de armazenamento**, procure e selecione a conta de armazenamento do Data Lake armazenamento Gen2 recém-criado.
        
        ![Configurações do armazenamento para usar o Data Lake Storage Gen2 com o Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * Sob **identidade**, selecione a assinatura correta e atribuído pelo usuário recém-criado identidade gerenciada.
        
        ![Configurações da identidade para usar o Data Lake Storage Gen2 com o Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> Para adicionar uma conta do Data Lake armazenamento Gen2 secundária, no nível da conta de armazenamento, basta atribua a identidade gerenciada criada anteriormente para a nova conta de armazenamento Gen2 de armazenamento do Data Lake que você deseja adicionar. Lembre-se de que a adição de uma conta do Data Lake armazenamento Gen2 secundária por meio da folha de "contas de armazenamento adicionais" no HDInsight não é suportado. 

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Criar um cluster com o Data Lake armazenamento Gen2 por meio da CLI do Azure

Você pode [baixar um arquivo de modelo de exemplo](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) e [baixar um arquivo de parâmetros de exemplo](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Antes de usar o modelo e o trecho de código da CLI do Azure abaixo, substitua os seguintes espaços reservados com seus valores corretos:

| Placeholder | DESCRIÇÃO |
|---|---|
| `<SUBSCRIPTION_ID>` | A ID da assinatura do Azure |
| `<RESOURCEGROUPNAME>` | O grupo de recursos onde você deseja que a nova conta de armazenamento e o cluster criada. |
| `<MANAGEDIDENTITYNAME>` | O nome da identidade gerenciada que receberão permissões em sua conta de armazenamento do Azure Data Lake Gen2. |
| `<STORAGEACCOUNTNAME>` | A nova conta de armazenamento do Azure Data Lake Gen2 que será criada. |
| `<CLUSTERNAME>` | O nome do seu cluster HDInsight. |
| `<PASSWORD>` | Sua senha escolhida para entrar no cluster usando SSH, bem como o painel do Ambari. |

O trecho de código a seguir faz as seguintes etapas iniciais:

1. Logs em sua conta do Azure.
1. Define a assinatura ativa em que as operações de criar serão feitas.
1. Cria um novo grupo de recursos para as novas atividades de implantação. 
1. Cria uma identidade atribuída pelo usuário gerenciada.
1. Adiciona uma extensão da CLI do Azure para usar os recursos para o Data Lake armazenamento Gen2.
1. Cria uma nova conta do Data Lake armazenamento Gen2 usando o `--hierarchical-namespace true` sinalizador. 

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Em seguida, entre no portal. Adicionar a nova identidade atribuída pelo usuário gerenciada para o **Colaborador de dados de Blob de armazenamento** função na conta de armazenamento, conforme descrito na etapa 3 abaixo [usando o portal do Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Depois de atribuir a função para a identidade atribuída pelo usuário gerenciada, implante o modelo usando o seguinte trecho de código.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Controle de acesso do Data Lake armazenamento Gen2 no HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>O Data Lake Storage Gen2 dá suporte a quais tipos de permissões?

Data Lake armazenamento Gen2 usa um modelo de controle de acesso que oferece suporte a controle de acesso baseado em função (RBAC) e listas de controle de acesso POSIX (ACLs). Dá suporte a armazenamento Gen1 do data Lake acessar listas de controle somente para controlar o acesso aos dados.

RBAC usa atribuições de função para aplicar efetivamente conjuntos de permissões a usuários, grupos e entidades de serviço para recursos do Azure. Normalmente, esses recursos do Azure são restritos a recursos de nível superior (por exemplo, contas de armazenamento do Azure). Para o armazenamento do Azure e também o Data Lake armazenamento Gen2, esse mecanismo foi estendido para o recurso do sistema de arquivos.

 Para obter mais informações sobre permissões de arquivo com o RBAC, consulte [controle de acesso baseado em função do Azure (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Para obter mais informações sobre permissões de arquivo com ACLs, consulte [listas de controle de acesso nos arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Como controlar o acesso aos meus dados no Data Lake armazenamento Gen2?

A capacidade do seu cluster HDInsight para acessar os arquivos no Data Lake armazenamento Gen2 é controlada por meio de identidades gerenciadas. Uma identidade gerenciada é uma identidade registrada no Azure Active Directory (Azure AD) cujas credenciais são gerenciadas pelo Azure. Com identidades gerenciadas, você não precisa registrar as entidades de serviço no Azure AD ou manter as credenciais, como certificados.

Serviços do Azure tem dois tipos de identidades gerenciadas: atribuído pelo sistema e atribuída pelo usuário. HDInsight usa identidades de gerenciado atribuída pelo usuário para acessar o Data Lake armazenamento Gen2. Uma identidade gerenciada atribuída pelo usuário é criada como um recurso autônomo do Azure. Por meio de um processo de criação, o Microsoft Azure cria uma identidade no locatário do Azure AD confiado pela assinatura em uso. Depois que a identidade é criada, ela pode ser atribuída a uma ou mais instâncias de serviço do Azure.

O ciclo de vida de uma identidade atribuída pelo usuário é gerenciado separadamente do ciclo de vida das instâncias de serviço do Azure a que ela é atribuída. Para obter mais informações sobre identidades gerenciadas, consulte [como fazer as identidades gerenciadas para o trabalho de recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Como definir permissões para usuários do AD do Azure para consultar dados no Data Lake armazenamento Gen2 usando o Hive ou outros serviços?

Para definir permissões para usuários para consultar dados, use grupos de segurança do Azure AD como o principal atribuído em ACLs. Diretamente não atribua permissões de acesso de arquivo para usuários individuais ou entidades de serviço. Quando você usa grupos de segurança do Azure AD para controlar o fluxo de permissões, você pode adicionar e remover usuários ou entidades de serviço sem reaplicar as ACLs para uma estrutura de diretório inteiro. Você só precisa adicionar ou remover os usuários do grupo de segurança do Azure AD pertinente. As ACLs não são herdadas, portanto reaplicar as ACLs, é necessário atualizar a ACL em cada arquivo e o subdiretório.

## <a name="next-steps"></a>Próximas etapas

* [Integração do Azure HDInsight com a versão prévia do Data Lake Storage Gen2 – ACL e atualização de segurança](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
