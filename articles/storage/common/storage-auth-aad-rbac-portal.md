---
title: Usar o portal do Azure para atribuir uma função do Azure para acesso a dados
titleSuffix: Azure Storage
description: Saiba como usar o portal do Azure para atribuir permissões a uma entidade de segurança do Azure Active Directory com o Azure RBAC (Controle de Acesso Baseado em Função). O Armazenamento do Microsoft Azure dá suporte para funções personalizadas e internas do Azure para autenticação por meio do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d63a31eae57d09f1658f5f19c1518cb0648fa4e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373735"
---
# <a name="use-the-azure-portal-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Usar o portal do Azure para atribuir uma função do Azure para acesso aos dados de blob e de fila

O Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [Azure RBAC (Controle de Acesso Baseado em Função)](../../role-based-access-control/overview.md). O Armazenamento do Microsoft Azure define um conjunto de funções internas do Azure que abrangem conjuntos comuns de permissões usados para acessar blobs ou dados de fila.

Quando uma função do Azure é atribuída a uma entidade de segurança do Azure AD, o Azure permite o acesso a esses recursos para essa entidade de segurança. O escopo do acesso pode ser definido para o nível de assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Uma entidade de segurança do Azure AD pode ser um usuário, um grupo ou uma entidade de serviço de aplicativo ou uma [identidade gerenciada para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Este artigo descreve como usar o portal do Azure para atribuir funções do Azure. O portal do Azure oferece uma interface simples para atribuir funções do Azure e gerenciar o acesso a recursos de armazenamento. Você também pode atribuir funções do Azure para recursos de blob e de fila usando as ferramentas de linha de comando do Azure ou as APIs de gerenciamento de armazenamento do Azure. Veja mais informações sobre as funções do Azure para recursos de armazenamento em [Autenticar o acesso aos blobs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md).

## <a name="azure-roles-for-blobs-and-queues"></a>Funções do Azure para blobs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar o escopo do recurso

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-azure-roles-using-the-azure-portal"></a>Atribuir funções do Azure usando o portal do Azure

Depois de determinar o escopo apropriado para uma atribuição de função, navegue até esse recurso no portal do Azure. Exiba as configurações de **Controle de Acesso (IAM)** do recurso e siga estas instruções para gerenciar as atribuições de função:

1. Atribua a função do Azure apropriada do Armazenamento do Microsoft Azure para permitir o acesso a uma entidade de segurança do Azure AD.

1. Atribua a função [Leitor](../../role-based-access-control/built-in-roles.md#reader) do Azure Resource Manager a usuários que precisam acessar contêineres ou filas por meio do portal do Azure usando suas credenciais do Azure AD.

As seções a seguir descrevem cada uma dessas etapas mais detalhadamente.

> [!IMPORTANT]
> Quando cria uma conta do Armazenamento do Microsoft Azure, você não recebe permissões automaticamente para acessar dados por meio do Azure AD. Você deve atribuir explicitamente a si mesmo uma função do Azure para o Armazenamento do Microsoft Azure. Você pode atribuí-la no nível de assinatura, grupo de recursos, conta de armazenamento ou contêiner ou fila.
>
> Antes de atribuir a si mesmo uma função para acesso a dados, você poderá acessar dados em sua conta de armazenamento por meio do portal do Azure porque o portal também pode usar a chave de conta para acesso a dados. Para obter mais informações, confira [Escolher como autorizar o acesso a dados de blob no portal do Azure](../blobs/authorize-data-operations-portal.md).
>
> Se a conta de armazenamento estiver bloqueada com um bloqueio somente leitura do Azure Resource Manager, o bloqueio impedirá a atribuição de funções de RBAC do Azure que estão no escopo da conta de armazenamento ou em um contêiner de dados (contêiner de blob ou fila).

### <a name="assign-an-azure-built-in-role"></a>Atribuir uma função interna do Azure

Antes de atribuir uma função a uma entidade de segurança, considere o escopo das permissões que você está concedendo. Examine a seção [Determinar o escopo do recurso](#determine-resource-scope) para decidir o escopo apropriado.

O procedimento mostrado aqui atribui uma função com escopo a um contêiner, mas você pode seguir as mesmas etapas para atribuir uma função com escopo a uma fila:

1. No [portal do Azure](https://portal.azure.com), acesse sua conta de armazenamento e veja a **Visão geral** da conta.
1. Em Serviços, selecione **Blobs**.
1. Localize o contêiner para o qual você deseja atribuir uma função e exiba as configurações do contêiner.
1. Selecione **Controle de Acesso (IAM)** para exibir as configurações de controle de acesso do contêiner. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/portal-access-control-container.png" alt-text="Captura de tela mostrando as configurações de controle de acesso do contêiner":::

1. Clique no botão **Adicionar atribuição de função** para adicionar uma nova função.
1. Na janela **Adicionar atribuição de função**, selecione a função do Armazenamento do Microsoft Azure que você deseja atribuir. Em seguida, pesquise para localizar a entidade de segurança à qual você deseja atribuir essa função.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/add-rbac-role.png" alt-text="Captura de tela que mostra como atribuir uma função do Azure":::

1. Clique em **Salvar**. A identidade à qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que o usuário adicionado agora tem permissões de leitura para os dados no contêiner nomeado *sample-container*.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/container-scoped-role.png" alt-text="Captura de tela que mostra a lista de usuários atribuídos a uma função":::

Você pode seguir etapas semelhantes para atribuir uma função com escopo à conta de armazenamento, ao grupo de recursos ou à assinatura.

### <a name="assign-the-reader-role-for-portal-access"></a>Atribuir a função Leitor para acesso ao portal

Ao atribuir uma função interna ou personalizada do Armazenamento do Microsoft Azure para uma entidade de segurança, você está concedendo permissões a essa entidade de segurança para executar operações nos dados em sua conta de armazenamento. As funções internas de **Leitor de dados** concedem permissões de leitura para os dados em um contêiner ou fila, enquanto as funções de **Colaborador de dados** internas concedem permissões de leitura, gravação e exclusão em um contêiner ou fila. As permissões estão no escopo do recurso especificado.  
Por exemplo, se você atribuir a função de **Colaborador de dados de blob de armazenamento** à usuária Mary no nível de um contêiner denominado **sample-container**, Mary receberá acesso de leitura, gravação e exclusão em todos os BLOBs no contêiner.

No entanto, caso Mary queira ver um blob no portal do Azure, a função de **Colaborador de dados de blob de armazenamento** não dará permissões suficientes para navegar pelo portal até o blob para vê-lo. Permissões adicionais do Azure AD são necessárias para navegar pelo portal e exibir os outros recursos visíveis lá.

Se os usuários precisarem acessar blobs no portal do Azure, atribua a eles outra função do Azure, a função de [Leitor](../../role-based-access-control/built-in-roles.md#reader), no nível da conta de armazenamento ou acima. A função de **Leitor** é uma função do Azure Resource Manager que permite aos usuários ver os recursos da conta de armazenamento, mas não os modificar. Ela não dá permissões de leitura para dados no Armazenamento do Microsoft Azure, mas apenas para recursos de gerenciamento de conta.

Siga estas etapas para atribuir a função de **Leitor** para que um usuário possa acessar blobs no portal do Azure. Neste exemplo, a atribuição tem como escopo a conta de armazenamento:

1. No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento.
1. Selecione **Controle de Acesso (IAM)** para exibir as configurações de controle de acesso da conta de armazenamento. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.
1. Na janela **Adicionar atribuição de função**, selecione a função **Leitor**. 
1. No campo **Atribuir acesso a**, selecione **usuário, grupo ou entidade de serviço do Azure AD**.
1. Pesquise para localizar a entidade de segurança à qual você deseja atribuir a função.
1. Salve a atribuição de função.

A atribuição da função de **Leitor** é necessária apenas para os usuários que precisam acessar blobs ou filas usando o portal do Azure.

> [!IMPORTANT]
> A versão prévia do Gerenciador de Armazenamento no portal do Azure não dá suporte para o uso de credenciais do Azure AD para ver e modificar dados de blob ou de fila. O Gerenciador de Armazenamento no portal do Azure sempre usa as chaves de conta para acessar dados. Para usar o Gerenciador de Armazenamento no portal do Azure, é necessário ter uma função que inclua **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="next-steps"></a>Próximas etapas

- Veja mais informações sobre as funções do Azure para recursos de armazenamento em [Autenticar o acesso aos blobs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md). 
- Para saber mais sobre o RBAC do Azure, veja [O que é o RBAC do Azure (controle de acesso baseado em função do Azure)?](../../role-based-access-control/overview.md).
- Para saber como atribuir e gerenciar atribuições de função do Azure com Azure PowerShell, CLI do Azure ou API REST, consulte estes artigos:
    - [Adicionar ou remover atribuições de função do Azure usando o módulo Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Adicionar ou remover atribuições de função do Azure usando a CLI do Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Adicionar ou remover atribuições de função do Azure usando a API REST](../../role-based-access-control/role-assignments-rest.md)
- Para saber como autorizar o acesso aos contêineres e filas de seus aplicativos de armazenamento, consulte [Usar o Azure AD com aplicativos do Armazenamento do Microsoft Azure](storage-auth-aad-app.md).
