---
title: Gerenciar chaves em um HSM Gerenciado – Azure Key Vault | Microsoft Docs
description: Use esse artigo para gerenciar as chaves em um HSM Gerenciado
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 3f054638e09061c652946c9c2db1a32db73c23d9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521026"
---
# <a name="manage-a-managed-hsm-using-the-azure-cli"></a>Gerenciar um HSM Gerenciado usando a CLI do Azure

> [!NOTE]
> O Key Vault é compatível com dois tipos de recurso: cofres e HSMs Gerenciados. Este artigo é sobre o **HSM Gerenciado** . Se você quiser aprender como gerenciar um cofre, confira [Gerenciar o Key Vault usando a CLI do Azure](../general/manage-with-cli2.md).

Para obter uma visão geral do HSM Gerenciado, confira [O que é o HSM Gerenciado?](overview.md)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste artigo, você precisará ter os seguintes itens:

* Uma assinatura do Microsoft Azure. Se você não tiver uma, pode se inscrever e fazer uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* A CLI do Azure versão 2.12.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
* Um HSM gerenciado na sua assinatura. Confira [Início Rápido: Provisione e ative um HSM gerenciado usando a CLI do Azure](quick-create-cli.md) para provisionar e ativar um HSM gerenciado.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Para entrar no Azure usando a CLI, você pode digitar:

```azurecli
az login
```

Para obter mais informações sobre as opções de logon por meio da CLI, confira [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

> [!NOTE]
> Todos os comandos a seguir mostram dois métodos de uso. Um que usa os parâmetros **--hsm-name** e **--name** (para nome da chave) e outro que usa o parâmetro **--id** , em que você pode especificar a URL inteira, inclusive o nome da chave, quando apropriado. O último método é útil quando o chamador (seja um usuário ou um aplicativo) não tem acesso de leitura ao plano de controle e somente acesso restrito ao plano de dados.

## <a name="create-an-hsm-key"></a>Criar uma chave HSM

Use o comando `az keyvault key create` para criar uma chave.

### <a name="create-an-rsa-key"></a>Criar uma chave RSA

O exemplo a seguir mostra como criar uma chave **RSA** de 3.072 bits que será usada somente para as operações **wrapKey e unwrapKey** (--ops). 


```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072

## OR
# Note the key name (myrsakey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072
```

Observe que a operação `get` retorna apenas a chave pública e os atributos de chave. Ela não retorna a chave privada (em caso de chave assimétrica) ou o material da chave (em caso de chave simétrica).

### <a name="create-an-ec-key"></a>Criar uma chave do EC

O exemplo a seguir mostra como criar uma chave do **EC** com curva P-256 que será usada somente para **assinar e verificar** operações (--ops) e que tem duas marcações, **usage** e **appname** . As marcações ajudam na inclusão de metadados adicionais à chave para fins de acompanhamento e gerenciamento.

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256

## OR
# Note the key name (myec256key) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

### <a name="create-a-256-bit-symmetric-key"></a>Criar uma chave simétrica de 256 bits

O exemplo a seguir mostra como criar uma chave **simétrica** de 256 bits que será usada somente para as operações **encrypt e decrypt** (--ops).

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myaeskey --ops encrypt decrypt  --tags --kty oct-HSM --size 256

## OR
# Note the key name (myaeskey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myaeskey --ops encrypt decrypt  --tags ‘usage=signing] appname=myapp’ --kty oct-HSM --size 256
```

## <a name="view-key-attributes-and-tags"></a>Visualizar atributos de chave e marcações

Use o comando `az keyvault key show` para visualizar os atributos, as versões e as marcações de uma chave.

```azurecli-interactive
az keyvault key show --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key show --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey

```

## <a name="list-keys"></a>Listar chaves

Use o comando `az keyvault key list` para listar todas as chaves dentro de um HSM Gerenciado.

```azurecli-interactive
az keyvault key list --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list --id https://ContosoMHSM.managedhsm.azure.net/

```

## <a name="delete-a-key"></a>Excluir uma chave

Use o comando `az keyvault key delete` para excluir uma chave de um HSM Gerenciado. Observe que a exclusão reversível está sempre ativada. Portanto, uma chave excluída permanecerá no estado de exclusão e poderá ser recuperada até expirar o número de dias de retenção, quando a chave será limpa (excluída permanentemente) e não haverá mais a possibilidade de recuperação.

```azurecli-interactive
az keyvault key delete --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key delete --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey
```

## <a name="list-deleted-keys"></a>Listar chaves excluídas

Use o comando `az keyvault key list-deleted` para listar todas as chaves em estado de exclusão do seu HSM Gerenciado.

```azurecli-interactive
az keyvault key list-deleted --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list-deleted --id https://ContosoMHSM.managedhsm.azure.net/
```

## <a name="recover-undelete-a-deleted-key"></a>Recuperar (restaurar) uma chave excluída

Use o comando `az keyvault key list-deleted` para listar todas as chaves em estado de exclusão do seu HSM Gerenciado. Se você precisar recuperar (restaurar) uma chave usando o parâmetro --id nessa operação, será necessário observar o valor `recoveryId` da chave excluída, obtido por meio do comando `az keyvault key list-deleted`.

```azurecli-interactive
az keyvault key recover --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey
```

## <a name="purge-permanently-delete-a-key"></a>Limpar (excluir permanentemente) uma chave

Use o comando `az keyvault key purge` para limpar (excluir permanentemente) uma chave.

> [!NOTE]
> Se o HSM Gerenciado tiver a proteção de limpeza habilitada, a operação de limpeza não será permitida. A chave será limpa automaticamente ao expirar o período de retenção.

```azurecli-interactive
az keyvault key purge --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey

```

## <a name="create-a-single-key-backup"></a>Criar um backup de chave individual

Use `az keyvault key backup` para criar um backup de chave. O arquivo de backup é um blob criptografado ligado criptograficamente ao Domínio de Segurança do HSM de origem. Ele só pode ser restaurado em HSMs que compartilham o mesmo Domínio de Segurança. Leia mais sobre [Domínios de Segurança](security-domain.md).

```azurecli-interactive
az keyvault key backup --hsm-name ContosoHSM --name myrsakey --file myrsakey.backup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key backup --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey  --file myrsakey.backup

```

## <a name="restore-a-single-key-from-backup"></a>Restaurar uma chave individual do backup

Use `az keyvault key restore` para restaurar uma chave individual. O HSM de origem em que o backup foi criado deve compartilhar o mesmo Domínio de Segurança que o HSM de destino em que a chave está sendo restaurada.

> [!NOTE]
> A restauração não será bem sucedida se já houver uma chave com o mesmo nome em estado ativo ou excluído.

```azurecli-interactive
az keyvault key restore --hsm-name ContosoHSM --name myrsakey --file myrsakey.bakup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --file myrsakey.bakup

```

## <a name="import-a-key-from-a-file"></a>Importar uma chave de um arquivo

Use o comando `az keyvault key import` para importar uma chave (somente RSA e EC) de um arquivo. O arquivo de certificado deve ter uma chave privada e deve usar a codificação PEM (conforme definido nas RFCs [1421](https://tools.ietf.org/html/rfc1421), [1422](https://tools.ietf.org/html/rfc1422), [1423](https://tools.ietf.org/html/rfc1423) e [1424](https://tools.ietf.org/html/rfc1424)).

```azurecli-interactive
az keyvault key import --hsm-name ContosoHSM --name myrsakey --pem-file mycert.key --password 'mypassword'

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --pem-file mycert.key --password 'mypassword'
```

Para importar uma chave do HSM local ao HSM Gerenciado, confira [Importar chaves protegidas por HSM para o HSM Gerenciado (BYOK)](hsm-protected-keys-byok.md)

## <a name="next-steps"></a>Próximas etapas

- Para obter a referência completa da CLI do Azure de comandos do cofre de chaves, consulte [referência da CLI do Key Vault](/cli/azure/keyvault).
- Para referências de programação, veja o [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Saiba mais sobre o [gerenciamento de funções do HSM Gerenciado](role-management.md)
- Saiba mais sobre as [melhores práticas do HSM Gerenciado](best-practices.md)
