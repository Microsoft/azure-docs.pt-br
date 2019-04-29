---
title: Usar o portal do Azure para começar a usar o Azure Data Lake Storage Gen1 | Microsoft Docs
description: Use o portal do Azure para criar uma conta do Azure Data Lake Storage Gen1 e executar operações básicas nessa conta.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: e021d8c056028c03ac71d2a27c9128272f374da6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60877845"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Introdução ao Azure Data Lake Store Gen1 usando o portal do Azure

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
>
> 

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Saiba como usar o portal do Azure para criar uma conta do Azure Data Lake Storage Gen1 e executar operações básicas, como criar pastas, carregar e baixar arquivos de dados, excluir sua conta etc. Para obter mais informações, consulte [Visão geral do Azure Data Lake Store Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter os seguintes itens:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1

1. Inscreva-se no novo [portal do Azure](https://portal.azure.com).
2. Clique em **Criar um recurso > Armazenamento > Data Lake Storage Gen1**.
3. Na folha **Novo Data Lake Storage Gen1**, forneça os valores conforme mostrado na captura de tela a seguir:
   
    ![Criar uma conta do Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Criar uma conta do Data Lake Storage Gen1")
   
   * **Nome**. Insira um nome exclusivo para a conta do Data Lake Storage Gen1.
   * **Assinatura**. Selecione a assinatura na qual você deseja criar uma conta do Azure Data Lake Storage Gen1.
   * **Grupo de Recursos**. Selecione um grupo de recursos existente ou selecione a opção **Criar novo** para criar um. Um grupo de recursos é um contêiner que mantém os recursos relacionados para um aplicativo. Para saber mais, consulte [Grupos de Recursos no Azure](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Localização**: selecione uma localização na qual deseja criar a conta do Data Lake Storage Gen1.
   * **Configurações de Criptografia**. Há três opções:
     
     * **Não habilite a criptografia**.
     * **Usar chaves gerenciadas pelo Data Lake Storage Gen1**, se você quiser que o Data Lake Storage Gen1 gerencie suas chaves de criptografia.
     * **Utilize as chaves do seu próprio Key Vault**. Você pode selecionar um Azure Key Vault existente ou criar um novo Key Vault. Para usar as chaves de um Key Vault, você precisa atribuir permissões à conta do Data Lake Storage Gen1 para acessar o Azure Key Vault. Para obter instruções, consulte [Atribuir permissões ao Azure Key Vault](#assign-permissions-to-azure-key-vault).
       
        ![Criptografia do Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Criptografia do Data Lake Storage Gen1")
       
        Clique em **OK** na folha **Configurações de Criptografia**.

        Para obter mais informações, confira [Criptografia de dados no Azure Data Lake Storage Gen1](./data-lake-store-encryption.md).

4. Clique em **Criar**. Ao escolher fixar a conta no painel, você retornará ao painel e poderá ver o progresso do provisionamento da conta do Data Lake Storage Gen1. Após o provisionamento da conta do Data Lake Storage Gen1, a folha da conta será exibida.

## <a name="assign-permissions-to-azure-key-vault"></a>Atribuir permissões ao Azure Key Vault
Se você tiver usado as chaves de um Azure Key Vault para configurar a criptografia na conta do Data Lake Storage Gen1, configure o acesso entre a conta do Data Lake Storage Gen1 e a conta do Azure Key Vault. Execute as seguintes etapas para fazê-lo.

1. Se você tiver usado as chaves do Azure Key Vault, a folha da conta do Data Lake Storage Gen1 exibirá um aviso na parte superior. Clique no aviso para abrir **Criptografia**.
   
    ![Criptografia do Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Criptografia do Data Lake Storage Gen1")
2. A folha mostra duas opções para configurar o acesso.

    ![Criptografia do Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Criptografia do Data Lake Storage Gen1")
   
   * Na primeira opção, clique em **Conceder Permissões** para configurar o acesso. A primeira opção só será habilitada quando o usuário que criou a conta do Data Lake Storage Gen1 também for administrador do Azure Key Vault.
   * A outra opção é executar o cmdlet do PowerShell exibido na folha. Você precisa ser o proprietário do Cofre de Chaves do Azure ou ter a capacidade de conceder permissões no Cofre de Chaves do Azure. Depois de executar o cmdlet, volte para a folha e clique em **Habilitar** para configurar o acesso.

> [!NOTE]
> Você também pode criar uma conta do Data Lake Storage Gen1 usando modelos do Azure Resource Manager. Esses modelos são acessíveis nos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
> - Sem criptografia de dados: [Implantar a conta do Azure Data Lake Storage Gen1 sem criptografia de dados](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> - Com criptografia de dados usando o Data Lake Storage Gen1: [Implantar a conta do Data Lake Storage Gen1 com criptografia (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> - Com criptografia de dados usando o Azure Key Vault: [Implantar a conta do Data Lake Storage Gen1 com criptografia (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
> 
> 



## <a name="createfolder"></a>Criar pastas em uma conta do Data Lake Storage Gen1
Você pode criar pastas em sua conta do Data Lake Storage Gen1 para gerenciar e armazenar dados.

1. Abra a conta do Data Lake Storage Gen1 que você criou. No painel esquerdo, clique em **Todos os recursos**, e depois, na folha Todos os recursos, clique no nome da conta sob a qual você deseja criar as pastas. Se você tiver fixado a conta no quadro inicial, clique no bloco da conta.
2. Na folha de sua conta do Data Lake Storage Gen1, clique em **Data Explorer**.
   
    ![Criar pastas em uma conta do Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Criar pastas em uma conta do Data Lake Storage Gen1")
3. Na folha Data Explorer, clique em **Nova Pasta**, insira um nome para a nova pasta e clique em **OK**.
   
    ![Criar pastas em uma conta do Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Criar pastas em uma conta do Data Lake Storage Gen1")
   
    A pasta recém-criada é listada na folha **Data Explorer**. Você pode criar pastas aninhadas em qualquer nível.
   
    ![Criar pastas em uma conta do Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Criar pastas em uma conta do Data Lake")

## <a name="uploaddata"></a>Carregar dados em uma conta do Data Lake Storage Gen1
É possível carregar dados em uma conta do Data Lake Storage Gen1 diretamente no nível raiz ou em uma pasta já criada na conta. 

1. Na folha **Data Explorer**, clique em **Upload**. 
2. Na folha **Carregar arquivos**, navegue até os arquivos que você deseja carregar e, em seguida, clique em **Adicionar arquivos selecionados**. Você também pode selecionar mais de um arquivo para upload.

    ![Carregar dados](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Carregar dados")

Se estiver procurando alguns dados de exemplo para carregar, é possível obter a pasta **Dados da Ambulância** no [Repositório Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="properties"></a>Ações disponíveis para os dados armazenados
Clique no ícone de reticências em um arquivo e, no menu pop-up, clique na ação que você deseja executar nos dados.

![Propriedades nos dados](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Propriedades nos dados") 

## <a name="secure-your-data"></a>Proteja seus dados
Você pode proteger os dados armazenados em sua conta do Data Lake Storage Gen1 usando o Azure Active Directory e as ACLs (controle de acesso). Para obter instruções de como fazer isso, confira [Protegendo dados no Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="delete-a-data-lake-storage-gen1-account"></a>Excluir uma conta do Data Lake Storage Gen1
Para excluir uma conta do Data Lake Storage Gen1, na folha dele, clique em **Excluir**. Para confirmar a ação, você receberá uma solicitação para inserir o nome da conta que você deseja excluir. Insira o nome da conta e clique em **Excluir**.

![Excluir conta do Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Excluir conta do Data Lake")

## <a name="next-steps"></a>Próximas etapas
* [Use o Armazenamento de Data Lake do Azure Gen1 para requisitos de big data](data-lake-store-data-scenarios.md) 
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)
* [Usar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

