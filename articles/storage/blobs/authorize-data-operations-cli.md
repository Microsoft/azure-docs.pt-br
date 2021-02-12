---
title: Escolha como autorizar o acesso a dados de blob com CLI do Azure
titleSuffix: Azure Storage
description: Especifique como autorizar operações de dados em relação a dados de blob com o CLI do Azure. Você pode autorizar operações de dados usando as credenciais do Azure AD, com a chave de acesso da conta ou com um token de SAS (assinatura de acesso compartilhado).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 060bfb6c88bbed8ba12c5b5ebfd2e9617f5abfb2
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637288"
---
# <a name="choose-how-to-authorize-access-to-blob-data-with-azure-cli"></a>Escolha como autorizar o acesso a dados de blob com CLI do Azure

O armazenamento do Azure fornece extensões para CLI do Azure que permitem especificar como você deseja autorizar operações em dados de BLOB. Você pode autorizar operações de dados das seguintes maneiras:

- Com uma entidade de segurança do Azure Active Directory (AD do Azure). A Microsoft recomenda usar as credenciais do Azure AD para segurança superior e facilidade de uso.
- Com a chave de acesso da conta ou um token de assinatura de acesso compartilhado (SAS).

## <a name="specify-how-data-operations-are-authorized"></a>Especificar como as operações de dados são autorizadas

CLI do Azure comandos para ler e gravar dados de blob incluem o `--auth-mode` parâmetro opcional. Especifique esse parâmetro para indicar como uma operação de dados deve ser autorizada:

- Defina o `--auth-mode` parâmetro como `login` para entrar usando uma entidade de segurança do Azure AD (recomendado).
- Defina o `--auth-mode` parâmetro para o valor herdado `key` para tentar recuperar a chave de acesso da conta a ser usada para autorização. Se você omitir o `--auth-mode` parâmetro, o CLI do Azure também tentará recuperar a chave de acesso.

Para usar o `--auth-mode` parâmetro, certifique-se de ter instalado CLI do Azure versão 2.0.46 ou posterior. Execute `az --version` para verificar sua versão instalada.

> [!IMPORTANT]
> Se você omitir o `--auth-mode` parâmetro ou defini-lo como `key` , o CLI do Azure tentará usar a chave de acesso da conta para autorização. Nesse caso, a Microsoft recomenda que você forneça a chave de acesso no comando ou na variável de ambiente **AZURE_STORAGE_KEY** . Para obter mais informações sobre variáveis de ambiente, consulte a seção intitulada [definir variáveis de ambiente para parâmetros de autorização](#set-environment-variables-for-authorization-parameters).
>
> Se você não fornecer a chave de acesso, a CLI do Azure tentará chamar o provedor de recursos de armazenamento do Azure para recuperá-la para cada operação. Executar muitas operações de dados que exigem uma chamada para o provedor de recursos pode resultar em limitação. Para obter mais informações sobre os limites do provedor de recursos, consulte [escalabilidade e metas de desempenho para o provedor de recursos de armazenamento do Azure](../common/scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Autorizar com as credenciais do Azure AD

Quando você entra no CLI do Azure com as credenciais do Azure AD, um token de acesso OAuth 2,0 é retornado. Esse token é usado automaticamente pelo CLI do Azure para autorizar operações de dados subsequentes no armazenamento de BLOBs ou filas. Para operações com suporte, você não precisa passar uma chave de conta ou token SAS com o comando.

Você pode atribuir permissões a dados de blob a uma entidade de segurança do Azure AD por meio do controle de acesso baseado em função do Azure (RBAC do Azure). Para obter mais informações sobre as funções do Azure no armazenamento do Azure, consulte [gerenciar direitos de acesso aos dados do armazenamento do Azure com o Azure RBAC](../common/storage-auth-aad-rbac-portal.md).

### <a name="permissions-for-calling-data-operations"></a>Permissões para operações de dados de chamada

As extensões de armazenamento do Azure têm suporte para operações em dados de BLOB. As operações que você pode chamar dependem das permissões concedidas à entidade de segurança do Azure AD com a qual você entra no CLI do Azure. As permissões para contêineres de armazenamento do Azure são atribuídas por meio do RBAC do Azure. Por exemplo, se você tiver atribuído a função de **leitor de dados de blob de armazenamento** , poderá executar comandos de script que lêem dados de um contêiner. Se você tiver atribuído a função **colaborador de dados do blob de armazenamento** , poderá executar comandos de script que lêem, gravam ou excluem um contêiner ou os dados que ele contém.

Para obter detalhes sobre as permissões necessárias para cada operação de armazenamento do Azure em um contêiner, consulte [chamar operações de armazenamento com tokens OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Exemplo: autorizar uma operação para criar um contêiner com as credenciais do Azure AD

O exemplo a seguir mostra como criar um contêiner a partir de CLI do Azure usando suas credenciais do Azure AD. Para criar o contêiner, você precisará fazer logon no CLI do Azure, e precisará de um grupo de recursos e uma conta de armazenamento. Para saber como criar esses recursos, consulte [início rápido: criar, baixar e listar BLOBs com CLI do Azure](../blobs/storage-quickstart-blobs-cli.md).

1. Antes de criar o contêiner, atribua a função [Colaborador de Dados do Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a si mesmo. Embora você seja o proprietário da conta, você precisa de permissões explícitas para executar operações de dados na conta de armazenamento. Para obter mais informações sobre como atribuir funções do Azure, consulte [usar o portal do Azure para atribuir uma função do Azure para acesso aos dados de BLOB e de fila](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > As atribuições de função do Azure podem levar alguns minutos para serem propagadas.

1. Chame o comando [AZ Storage container Create](/cli/azure/storage/container#az-storage-container-create) com o `--auth-mode` parâmetro definido como `login` para criar o contêiner usando suas credenciais do Azure AD. Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autorizar com a chave de acesso da conta

Se você possuir a chave de conta, poderá chamar qualquer operação de dados do armazenamento do Azure. Em geral, o uso da chave de conta é menos seguro. Se a chave de conta for comprometida, todos os dados em sua conta poderão ser comprometidos.

O exemplo a seguir mostra como criar um contêiner usando a chave de acesso da conta. Especifique a chave de conta e forneça o `--auth-mode` parâmetro com o `key` valor:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autorizar com um token SAS

Se você possuir um token SAS, poderá chamar operações de dados que são permitidas pela SAS. O exemplo a seguir mostra como criar um contêiner usando um token SAS:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Definir variáveis de ambiente para parâmetros de autorização

Você pode especificar parâmetros de autorização em variáveis de ambiente para evitar incluí-los em cada chamada para uma operação de dados do armazenamento do Azure. A tabela a seguir descreve as variáveis de ambiente disponíveis.

| Variável de ambiente                  | Descrição                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    O nome da conta de armazenamento. Essa variável deve ser usada em conjunto com a chave da conta de armazenamento ou com um token SAS. Se nenhum estiver presente, o CLI do Azure tentará recuperar a chave de acesso da conta de armazenamento usando a conta autenticada do Azure AD. Se um grande número de comandos for executado ao mesmo tempo, o limite de limitação do provedor de recursos do armazenamento do Azure poderá ser atingido. Para obter mais informações sobre os limites do provedor de recursos, consulte [escalabilidade e metas de desempenho para o provedor de recursos de armazenamento do Azure](../common/scalability-targets-resource-provider.md).             |
|    AZURE_STORAGE_KEY                  |    A chave da conta de armazenamento. Essa variável deve ser usada em conjunto com o nome da conta de armazenamento.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Uma cadeia de conexão que inclui a chave da conta de armazenamento ou um token SAS. Essa variável deve ser usada em conjunto com o nome da conta de armazenamento.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Um token de SAS (assinatura de acesso compartilhado). Essa variável deve ser usada em conjunto com o nome da conta de armazenamento.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    O modo de autorização com o qual executar o comando. Os valores permitidos são `login` (recomendado) ou `key` . Se você especificar `login` , o CLI do Azure usará suas credenciais do Azure ad para autorizar a operação de dados. Se você especificar o modo herdado `key` , o CLI do Azure tentará consultar a chave de acesso da conta e autorizar o comando com a chave.    |

## <a name="next-steps"></a>Próximas etapas

- [Use CLI do Azure para atribuir uma função do Azure para acesso aos dados de BLOB e de fila](../common/storage-auth-aad-rbac-cli.md)
- [Autorizar o acesso a dados de BLOB e de fila com identidades gerenciadas para recursos do Azure](../common/storage-auth-aad-msi.md)