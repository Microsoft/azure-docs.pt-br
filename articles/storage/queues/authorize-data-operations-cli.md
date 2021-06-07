---
title: Escolha como autorizar o acesso a dados de fila com a CLI do Azure
titleSuffix: Azure Storage
description: Especifique como autorizar operações de dados em dados de fila com a CLI do Azure. Você pode autorizar operações de dados usando as credenciais do Azure AD, com a chave de acesso da conta ou com um token SAS (Assinatura de Acesso Compartilhado).
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2f7092d8ce184d7021774814e96935e46d1ffb56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363161"
---
# <a name="choose-how-to-authorize-access-to-queue-data-with-azure-cli"></a>Escolha como autorizar o acesso a dados de fila com a CLI do Azure

O Armazenamento do Azure fornece extensões para a CLI do Azure que permitem especificar como você deseja autorizar as operações em dados de fila. Você pode autorizar operações de dados das seguintes maneiras:

- Com uma entidade de segurança do Azure AD (Azure Active Directory). A Microsoft recomenda usar as credenciais do Azure AD para maior segurança e facilidade de uso.
- Com a chave de acesso da conta ou um token SAS (Assinatura de Acesso Compartilhado).

## <a name="specify-how-data-operations-are-authorized"></a>Especificar como as operações de dados são autorizadas

Os comandos da CLI do Azure usados para ler e gravar os dados de fila incluem o parâmetro `--auth-mode` opcional. Especifique esse parâmetro para indicar como uma operação de dados deve ser autorizada:

- Defina o parâmetro `--auth-mode` como `login` para se conectar usando uma entidade de segurança do Azure AD (recomendado).
- Defina o parâmetro `--auth-mode` como o valor de `key` herdado para tentar recuperar a chave de acesso da conta a ser usada para autorização. Se você omitir o parâmetro `--auth-mode`, a CLI do Azure também tentará recuperar a chave de acesso.

Para usar o parâmetro `--auth-mode`, verifique se você instalou a CLI do Azure v2.0.46 ou posterior. Execute `az --version` para verificar sua versão instalada.

> [!NOTE]
> Quando uma conta de armazenamento é bloqueada com um bloqueio **ReadOnly** do Azure Resource Manager, a operação [Listar Chaves](/rest/api/storagerp/storageaccounts/listkeys) não é permitida nessa conta de armazenamento. **Listar Chaves** é uma operação POST, e todas as operações POST são impedidas quando um bloqueio **ReadOnly** é configurado para a conta. Por esse motivo, quando a conta é bloqueada com **ReadOnly**, os usuários que ainda não têm as chaves da conta precisam usar as credenciais do Azure AD para acessar os dados de fila.

> [!IMPORTANT]
> Se você omitir o parâmetro `--auth-mode` ou defini-lo como `key`, a CLI do Azure tentará usar a chave de acesso da conta para autorização. Nesse caso, a Microsoft recomenda que você forneça a chave de acesso no comando ou na variável de ambiente `AZURE_STORAGE_KEY`. Para obter mais informações sobre variáveis de ambiente, confira a seção intitulada [Definir variáveis de ambiente para parâmetros de autorização](#set-environment-variables-for-authorization-parameters).
>
> Se você não fornecer a chave de acesso, a CLI do Azure tentará chamar o provedor de recursos do Armazenamento do Azure para recuperá-la para cada operação. A execução de muitas operações de dados que exigem uma chamada ao provedor de recursos pode resultar em limitação. Para obter mais informações sobre os limites do provedor de recursos, confira [Metas de desempenho e escalabilidade do provedor de recursos do Armazenamento do Azure](../common/scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Autorização com as credenciais do Azure AD

Quando você entra na CLI do Azure com as credenciais do Azure AD, um token de acesso OAuth 2.0 é retornado. Esse token é usado automaticamente pela CLI do Azure para autorizar operações de dados posteriores no Armazenamento de Filas. Para operações com suporte, você não precisa passar uma chave de conta ou token SAS com o comando.

Você pode atribuir permissões em dados de fila a uma entidade de segurança do Azure AD por meio do RBAC do Azure (controle de acesso baseado em função do Azure). Para obter mais informações sobre as funções do Azure no Armazenamento do Azure, confira [Gerenciar direitos de acesso a dados do Armazenamento do Azure com o RBAC do Azure](../common/storage-auth-aad-rbac-portal.md).

### <a name="permissions-for-calling-data-operations"></a>Permissões para chamada de operações de dados

Há suporte para as extensões do Armazenamento do Azure nas operações em dados de fila. As operações que podem ser chamadas dependem das permissões concedidas à entidade de segurança do Azure AD com a qual você entra na CLI do Azure. As permissões em filas são atribuídas por meio do RBAC do Azure. Por exemplo, se a função **Leitor de Dados da Fila de Armazenamento** for atribuída a você, você poderá executar comandos de script que leem dados de uma fila. Se a função **Colaborador de Dados da Fila de Armazenamento** for atribuída a você, será possível executar comandos de script que leem, gravam ou excluem uma fila ou os dados que ela contém.

Para obter detalhes sobre as permissões necessárias para cada operação do Armazenamento do Microsoft Azure em uma fila, confira [Chamar operações de armazenamento com tokens OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-queue-with-azure-ad-credentials"></a>Exemplo: autorizar uma operação para criar uma fila com as credenciais do Azure AD

O exemplo a seguir mostra como criar uma fila por meio da CLI do Azure usando suas credenciais do Azure AD. Para criar a fila, você precisará fazer logon na CLI do Azure e ter um grupo de recursos e uma conta de armazenamento.

1. Antes de criar a fila, atribua a função [Colaborador de Dados da Fila de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) a si mesmo. Mesmo que você seja o proprietário da conta, precisará de permissões explícitas para executar operações de dados na conta de armazenamento. Para obter mais informações sobre como atribuir funções do Azure, confira [Usar o portal do Azure para atribuir uma função do Azure para acesso a dados de blob e de fila](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > As atribuições de função do Azure podem levar alguns minutos para serem propagadas.

1. Chame o comando [`az storage queue create`](/cli/azure/storage/queue#az-storage-queue-create) com o parâmetro `--auth-mode` definido como `login` para criar a fila usando suas credenciais do Azure AD. Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

    ```azurecli
    az storage queue create \
        --account-name <storage-account> \
        --name sample-queue \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autorização com a chave de acesso da conta

Se você tem a chave da conta, chame qualquer operação de dados do Armazenamento do Azure. Em geral, o uso da chave da conta é menos seguro. Se a chave de conta for comprometida, todos os dados da sua conta poderão ser comprometidos.

O exemplo a seguir mostra como criar uma fila usando a chave de acesso da conta. Especifique a chave de conta e forneça ao parâmetro `--auth-mode` o valor `key`:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autorização com um token SAS

Se você tem um token SAS, chame operações de dados que são permitidas pela SAS. O seguinte exemplo mostra como criar uma fila usando um token SAS:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Definir variáveis de ambiente para parâmetros de autorização

Você pode especificar parâmetros de autorização em variáveis de ambiente para evitar incluí-los em cada chamada a uma operação de dados do Armazenamento do Azure. A tabela a seguir descreve as variáveis de ambiente disponíveis.

| Variável de ambiente | Descrição |
|--|--|
| **AZURE_STORAGE_ACCOUNT** | O nome da conta de armazenamento. Essa variável deve ser usada em conjunto com a chave de conta de armazenamento ou um token SAS. Se nenhum deles estiver presente, a CLI do Azure tentará recuperar a chave de acesso da conta de armazenamento usando a conta autenticada do Azure AD. Se um número grande de comandos for executado ao mesmo tempo, a limitação do provedor de recursos do Armazenamento do Azure poderá ser atingida. Para obter mais informações sobre os limites do provedor de recursos, confira [Metas de desempenho e escalabilidade do provedor de recursos do Armazenamento do Azure](../common/scalability-targets-resource-provider.md). |
| **AZURE_STORAGE_KEY** | A chave da conta de armazenamento. Essa variável precisa ser usada em conjunto com o nome da conta de armazenamento. |
| **AZURE_STORAGE_CONNECTION_STRING** | Uma cadeia de conexão que inclui a chave de conta de armazenamento ou um token SAS. Essa variável precisa ser usada em conjunto com o nome da conta de armazenamento. |
| **AZURE_STORAGE_SAS_TOKEN** | Um token SAS (Assinatura de Acesso Compartilhado). Essa variável precisa ser usada em conjunto com o nome da conta de armazenamento. |
| **AZURE_STORAGE_AUTH_MODE** | O modo de autorização com o qual o comando deve ser executado. Os valores permitidos são `login` (recomendado) ou `key`. Se você especificar `login`, a CLI do Azure usará suas credenciais do Azure AD para autorizar a operação de dados. Se você especificar o modo `key` herdado, a CLI do Azure tentará consultar a chave de acesso da conta e autorizar o comando com a chave. |

## <a name="next-steps"></a>Próximas etapas

- [Usar a CLI do Azure para atribuir uma função do Azure para acesso a dados de blob e de fila](../common/storage-auth-aad-rbac-cli.md)
- [Autorizar o acesso a dados de blob e de fila com identidades gerenciadas para recursos do Azure](../common/storage-auth-aad-msi.md)
