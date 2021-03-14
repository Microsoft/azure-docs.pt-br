---
title: 'Gerenciador de Armazenamento: definir ACLs no Azure Data Lake Storage Gen2'
description: Use o Gerenciador de Armazenamento do Azure para gerenciar listas de controle de acesso (ACLs) em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3f5bd22619e49246583d8b9fc4e62ad8ab266993
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653986"
---
# <a name="use-azure-storage-explorer-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Usar Gerenciador de Armazenamento do Azure para gerenciar ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para gerenciar ACLs (listas de controle de acesso) em contas de armazenamento que têm o namespace HIERÁRQUICO (HNS) habilitado.

Você pode usar Gerenciador de Armazenamento para exibir e, em seguida, atualizar as ACLs de diretórios e arquivos. A herança de ACL já está disponível para novos itens filho que são criados em um diretório pai. Mas você também pode aplicar as configurações de ACL recursivamente nos itens filho existentes de um diretório pai sem precisar fazer essas alterações individualmente para cada item filho. 

Este artigo mostra como modificar a ACL do arquivo ou diretório e como aplicar as configurações de ACL recursivamente aos diretórios filho.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento precisa ter o HNS (namespace hierárquico) habilitado. Siga [estas](../common/storage-account-create.md) instruções para criar um.

- Gerenciador de Armazenamento do Azure instalado no computador local. Para instalar o Gerenciador de Armazenamento do Azure para Windows, Macintosh ou Linux, confira o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> Gerenciador de Armazenamento usa os [pontos de extremidade](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) de BLOB (blob) & data Lake Storage Gen2 (DFS) ao trabalhar com Azure data Lake Storage Gen2. Se o acesso ao Azure Data Lake Storage Gen2 for configurado usando pontos de extremidade privados, certifique-se de que dois pontos de extremidade privados sejam criados para a conta de armazenamento: um com o subrecurso de destino `blob` e o outro com o subrecurso de destino `dfs` .

## <a name="sign-in-to-storage-explorer"></a>Conectar-se ao Gerenciador de Armazenamento

Quando você iniciar o Gerenciador de Armazenamento pela primeira vez, a janela **Gerenciador de Armazenamento do Microsoft Azure - Conectar** será exibida. Embora o Gerenciador de Armazenamento forneça várias maneiras de se conectar às contas de armazenamento, apenas uma maneira tem suporte atualmente para gerenciar ACLs.

|Tarefa|Finalidade|
|---|---|
|Adicionar uma conta do Azure | Redireciona você para a página de entrada de sua organização para autenticá-lo no Azure. Atualmente, esse será o método de autenticação com suporte apenas se você quiser gerenciar e definir ACLs.|
|Usar uma cadeia de conexão ou um URI de assinatura de acesso compartilhado | Podem ser usados para acessar diretamente um contêiner ou uma conta de armazenamento com um token SAS ou uma cadeia de conexão compartilhada. |
|Usar um nome e uma chave da conta de armazenamento| Use o nome e a chave da sua conta de armazenamento para se conectar ao armazenamento do Azure.|

Selecione **Adicionar uma Conta do Azure** e clique em **Entrar...** . Siga os avisos da tela para entrar na sua conta do Azure.

![Captura de tela que mostra Gerenciador de Armazenamento do Microsoft Azure e realça a opção Adicionar uma conta do Azure e o botão entrar.](media/storage-quickstart-blobs-storage-explorer/connect.png)

Ao concluir a conexão, o Gerenciador de Armazenamento do Azure carrega exibindo a guia **Explorer**. Essa exibição lhe dá informações de todas suas contas de armazenamento do Azure e do armazenamento local, configuradas por meio do [emulador de armazenamento do Azurite](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), das contas do [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou dos ambientes do [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Gerenciador de Armazenamento do Microsoft Azure – Janela Conexão](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="manage-an-acl"></a>Gerenciar uma ACL

Clique com o botão direito do mouse no contêiner, em um diretório ou em um arquivo e clique em **gerenciar listas de controle de acesso**.  A captura de tela a seguir mostra o menu como ele aparece quando você clica com o botão direito do mouse em um diretório.

> [!div class="mx-imgBorder"]
> ![Clicando com o botão direito do mouse em um diretório no Gerenciador de Armazenamento do Azure](./media/data-lake-storage-explorer-acl/manage-access-control-list-option.png)

A caixa de diálogo **gerenciar acesso** permite que você gerencie permissões para o proprietário e o grupo proprietários. Ele também permite adicionar novos usuários e grupos à lista de controle de acesso para os quais você então pode gerenciar permissões.

> [!div class="mx-imgBorder"]
> ![Caixa de diálogo Gerenciar acesso](./media/data-lake-storage-explorer-acl/manage-access-dialog-box.png)

Para adicionar um novo usuário ou grupo à lista de controle de acesso, selecione o botão **Adicionar** . Em seguida, insira a entrada do Azure Active Directory (Azure AD) correspondente que você deseja adicionar à lista e, em seguida, selecione **Adicionar**.  O usuário ou grupo agora aparecerão no campo **Usuários e grupos:**, permitindo que você comece a gerenciar suas permissões.

> [!NOTE]
> É uma prática recomendada e é recomendada para criar um grupo de segurança no Azure AD e manter permissões no grupo em vez de usuários individuais. Para obter detalhes sobre essa recomendação, bem como outras práticas recomendadas, consulte [modelo de controle de acesso em Azure data Lake Storage Gen2](data-lake-storage-explorer-acl.md).

Use os controles da caixa de seleção para definir acesso e ACLs padrão. Para saber mais sobre a diferença entre esses tipos de ACLs, consulte [tipos de ACLs](data-lake-storage-access-control.md#types-of-acls).

## <a name="apply-acls-recursively"></a>Aplicar ACLs recursivamente

Você pode aplicar entradas de ACL recursivamente nos itens filho existentes de um diretório pai sem precisar fazer essas alterações individualmente para cada item filho.

Para aplicar entradas de ACL recursivamente, clique com o botão direito do mouse no contêiner ou em um diretório e clique em **propagar listas de controle de acesso**.  A captura de tela a seguir mostra o menu como ele aparece quando você clica com o botão direito do mouse em um diretório.

> [!div class="mx-imgBorder"]
> ![Clicando com o botão direito do mouse em um diretório e escolhendo a configuração de controle de acesso propagar](./media/data-lake-storage-explorer-acl/propagate-access-control-list-option.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o modelo de permissão Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Modelo de controle de acesso no Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md)
