---
title: Escopos de criptografia para armazenamento de BLOBs
description: Os escopos de criptografia fornecem a capacidade de gerenciar a criptografia no nível do contêiner ou de um blob individual. Você pode usar escopos de criptografia para criar limites seguros entre os dados que residem na mesma conta de armazenamento, mas que pertencem a clientes diferentes.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 16a600d7caf65f880ffb5c2a2abfe5a9774a7795
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640466"
---
# <a name="encryption-scopes-for-blob-storage"></a>Escopos de criptografia para armazenamento de BLOBs

Os escopos de criptografia permitem que você gerencie a criptografia com uma chave que tem como escopo um contêiner ou um blob individual. Você pode usar escopos de criptografia para criar limites seguros entre os dados que residem na mesma conta de armazenamento, mas que pertencem a clientes diferentes.

Para obter mais informações sobre como trabalhar com escopos de criptografia, consulte [criar e gerenciar escopos de criptografia](encryption-scope-manage.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-encryption-scopes-work"></a>Como funcionam os escopos de criptografia

Por padrão, uma conta de armazenamento é criptografada com uma chave que tem o escopo de toda a conta de armazenamento. Ao definir um escopo de criptografia, você especifica uma chave que pode ter como escopo um contêiner ou um blob individual. Quando o escopo de criptografia é aplicado a um blob, o blob é criptografado com essa chave. Quando o escopo de criptografia é aplicado a um contêiner, ele serve como o escopo padrão para BLOBs nesse contêiner, para que todos os blobs que são carregados nesse contêiner possam ser criptografados com a mesma chave. O contêiner pode ser configurado para impor o escopo de criptografia padrão para todos os BLOBs no contêiner ou para permitir que um blob individual seja carregado no contêiner com um escopo de criptografia diferente do padrão.

As operações de leitura em um blob criado com um escopo de criptografia ocorrem de forma transparente, desde que o escopo de criptografia não esteja desabilitado.

### <a name="key-management"></a>Gerenciamento de chaves

Ao definir um escopo de criptografia, você pode especificar se o escopo é protegido com uma chave gerenciada pela Microsoft ou com uma chave gerenciada pelo cliente que é armazenada em Azure Key Vault. Escopos de criptografia diferentes na mesma conta de armazenamento podem usar chaves gerenciadas pela Microsoft ou gerenciadas pelo cliente. Você também pode alternar o tipo de chave usado para proteger um escopo de criptografia de uma chave gerenciada pelo cliente para uma chave gerenciada pela Microsoft ou vice-versa, a qualquer momento. Para obter mais informações sobre chaves gerenciadas pelo cliente, consulte [chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure](../common/customer-managed-keys-overview.md). Para obter mais informações sobre chaves gerenciadas pela Microsoft, consulte [sobre gerenciamento de chaves de criptografia](../common/storage-service-encryption.md#about-encryption-key-management).

Se você definir um escopo de criptografia com uma chave gerenciada pelo cliente, poderá optar por atualizar a versão da chave automaticamente ou manualmente. Se você optar por atualizar automaticamente a versão de chave, o armazenamento do Azure verificará o cofre de chaves ou o HSM gerenciado diariamente para obter uma nova versão da chave gerenciada pelo cliente e atualizará automaticamente a chave para a versão mais recente. Para obter mais informações sobre como atualizar a versão de chave para uma chave gerenciada pelo cliente, consulte [atualizar a versão da chave](../common/customer-managed-keys-overview.md#update-the-key-version).

Uma conta de armazenamento pode ter até 10.000 escopos de criptografia protegidos com chaves gerenciadas pelo cliente para as quais a versão de chave é atualizada automaticamente. Se sua conta de armazenamento já tiver escopos de criptografia de 10.000 protegidos com chaves gerenciadas pelo cliente que estão sendo atualizadas automaticamente, a versão de chave deverá ser atualizada manualmente para todos os escopos de criptografia adicionais protegidos por chaves gerenciadas pelo cliente.  

### <a name="encryption-scopes-for-containers-and-blobs"></a>Escopos de criptografia para contêineres e blobs

Ao criar um contêiner, você pode especificar um escopo de criptografia padrão para os blobs que são carregados posteriormente nesse contêiner. Ao especificar um escopo de criptografia padrão para um contêiner, você pode decidir como o escopo de criptografia padrão é imposto:

- Você pode exigir que todos os BLOBs carregados no contêiner usem o escopo de criptografia padrão. Nesse caso, cada blob no contêiner é criptografado com a mesma chave.
- Você pode permitir que um cliente substitua o escopo de criptografia padrão para o contêiner, para que um blob possa ser carregado com um escopo de criptografia diferente do escopo padrão. Nesse caso, os BLOBs no contêiner podem ser criptografados com chaves diferentes.

A tabela a seguir resume o comportamento de uma operação de upload de BLOB, dependendo de como o escopo de criptografia padrão é configurado para o contêiner:

| O escopo de criptografia definido no contêiner é... | Carregando um blob com o escopo de criptografia padrão... | Carregando um blob com um escopo de criptografia diferente do escopo padrão... |
|--|--|--|
| Um escopo de criptografia padrão com substituições permitidas | For bem-sucedida | For bem-sucedida |
| Um escopo de criptografia padrão com substituições proibidas | For bem-sucedida | Falhas |

Um escopo de criptografia padrão deve ser especificado para um contêiner no momento em que o contêiner é criado.

Se nenhum escopo de criptografia padrão for especificado para o contêiner, você poderá carregar um BLOB usando qualquer escopo de criptografia que tenha definido para a conta de armazenamento. O escopo de criptografia deve ser especificado no momento em que o blob é carregado.

## <a name="disabling-an-encryption-scope"></a>Desabilitando um escopo de criptografia

Quando você desabilita um escopo de criptografia, todas as operações de leitura ou gravação subsequentes feitas com o escopo de criptografia falharão com o código de erro HTTP 403 (proibido). Se você reabilitar o escopo de criptografia, as operações de leitura e gravação continuarão normalmente novamente.

Quando um escopo de criptografia estiver desabilitado, você não será mais cobrado por ele. Desabilite os escopos de criptografia que não são necessários para evitar encargos desnecessários.

Se o escopo de criptografia estiver protegido com uma chave gerenciada pelo cliente e você excluir a chave no cofre de chaves, os dados ficarão inacessíveis. Certifique-se de também desabilitar o escopo de criptografia para evitar ser cobrado por ele.

Tenha em mente que as chaves gerenciadas pelo cliente são protegidas pela exclusão reversível e pela proteção de limpeza no cofre de chaves, e uma chave excluída está sujeita ao comportamento definido por essas propriedades. Para obter mais informações, consulte um dos seguintes tópicos na documentação do Azure Key Vault:

- [Como usar a exclusão reversível com o PowerShell](../../key-vault/general/key-vault-recovery.md)
- [Como usar a exclusão temporária com a CLI](../../key-vault/general/key-vault-recovery.md)

> [!IMPORTANT]
> Não é possível excluir um escopo de criptografia.

## <a name="next-steps"></a>Próximas etapas

- [Criptografia do Armazenamento do Azure para dados em repouso](../common/storage-service-encryption.md)
- [Criar e gerenciar escopos de criptografia](encryption-scope-manage.md)
- [Chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure](../common/customer-managed-keys-overview.md)
- [O que é o Cofre da Chave do Azure?](../../key-vault/general/overview.md)