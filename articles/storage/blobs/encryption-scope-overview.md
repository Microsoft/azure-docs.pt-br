---
title: Escopos de criptografia para armazenamento de BLOBs (visualização)
description: Os escopos de criptografia fornecem a capacidade de gerenciar a criptografia no nível do contêiner ou de um blob individual. Você pode usar escopos de criptografia para criar limites seguros entre os dados que residem na mesma conta de armazenamento, mas que pertencem a clientes diferentes.
services: storage
author: tamram
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 35395a30f7d58b9edb3aa7622a35e8c4a62dc76f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211355"
---
# <a name="encryption-scopes-for-blob-storage-preview"></a>Escopos de criptografia para armazenamento de BLOBs (visualização)

Os escopos de criptografia fornecem a capacidade de gerenciar a criptografia no nível do contêiner ou de um blob individual. Você pode usar escopos de criptografia para criar limites seguros entre os dados que residem na mesma conta de armazenamento, mas que pertencem a clientes diferentes.

Por padrão, uma conta de armazenamento é criptografada com uma chave que tem o escopo de toda a conta de armazenamento. Com um escopo de criptografia, você pode especificar que um ou mais contêineres são criptografados com uma chave que tem o escopo definido apenas para esses contêineres.

Você pode optar por usar chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente armazenadas em Azure Key Vault para proteger e controlar o acesso à chave que criptografa os dados. Escopos de criptografia diferentes na mesma conta de armazenamento podem usar chaves gerenciadas pela Microsoft ou gerenciadas pelo cliente.

Depois de criar um escopo de criptografia, você pode especificar esse escopo de criptografia em uma solicitação para criar um contêiner ou um blob. Para obter mais informações sobre como criar um escopo de criptografia, consulte [criar e gerenciar escopos de criptografia (versão prévia)](encryption-scope-manage.md).

> [!IMPORTANT]
> No momento, os escopos de criptografia estão em versão **prévia**. Consulte os [termos de uso complementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.
>
> Para evitar custos inesperados, certifique-se de desabilitar os escopos de criptografia que não são necessários no momento.
>
> Não há suporte para escopos de criptografia com o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) ou contas de armazenamento com redundância de acesso de leitura (RA-GZRS) durante a visualização.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Criar um contêiner ou BLOB com um escopo de criptografia

Os BLOBs criados sob um escopo de criptografia são criptografados com a chave especificada para esse escopo. Você pode especificar um escopo de criptografia para um blob individual ao criar o BLOB ou pode especificar um escopo de criptografia padrão ao criar um contêiner. Quando um escopo de criptografia padrão é especificado no nível de um contêiner, todos os BLOBs nesse contêiner são criptografados com a chave associada ao escopo padrão.

Quando você cria um blob em um contêiner que tem um escopo de criptografia padrão, você pode especificar um escopo de criptografia que substitui o escopo de criptografia padrão se o contêiner estiver configurado para permitir substituições do escopo de criptografia padrão. Para evitar substituições do escopo de criptografia padrão, configure o contêiner para negar substituições para um blob individual.

As operações de leitura em um blob que pertence a um escopo de criptografia ocorrem de forma transparente, desde que o escopo de criptografia não esteja desabilitado.

## <a name="disable-an-encryption-scope"></a>Desabilitar um escopo de criptografia

Quando você desabilita um escopo de criptografia, todas as operações de leitura ou gravação subsequentes feitas com o escopo de criptografia falharão com o código de erro HTTP 403 (proibido). Se você reabilitar o escopo de criptografia, as operações de leitura e gravação continuarão normalmente novamente.

Quando um escopo de criptografia estiver desabilitado, você não será mais cobrado por ele. Desabilite os escopos de criptografia que não são necessários para evitar encargos desnecessários.

Se o escopo de criptografia estiver protegido com chaves gerenciadas pelo cliente, você também poderá excluir a chave associada no cofre de chaves para desabilitar o escopo de criptografia. Tenha em mente que as chaves gerenciadas pelo cliente são protegidas pela exclusão reversível e pela proteção de limpeza no cofre de chaves, e uma chave excluída está sujeita ao comportamento definido por essas propriedades. Para obter mais informações, consulte um dos seguintes tópicos na documentação do Azure Key Vault:

- [Como usar a exclusão reversível com o PowerShell](../../key-vault/general/key-vault-recovery.md)
- [Como usar a exclusão temporária com a CLI](../../key-vault/general/key-vault-recovery.md)

> [!NOTE]
> Não é possível excluir um escopo de criptografia.

## <a name="next-steps"></a>Próximas etapas

- [Criptografia do Armazenamento do Azure para dados em repouso](../common/storage-service-encryption.md)
- [Criar e gerenciar escopos de criptografia (visualização)](encryption-scope-manage.md)
- [Chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure](../common/customer-managed-keys-overview.md)
- [O que é o Cofre da Chave do Azure?](../../key-vault/general/overview.md)