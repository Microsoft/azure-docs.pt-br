---
title: Habilitar a criptografia dupla em discos gerenciados por portal do Azure em repouso
description: Habilite a criptografia dupla em repouso para os dados do disco gerenciado usando o portal do Azure.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b5f58b549fc9d879f6c2e5515c679d82c3ff44e4
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560354"
---
# <a name="use-the-azure-portal-to-enable-double-encryption-at-rest-for-managed-disks"></a>Usar o portal do Azure para habilitar a criptografia dupla em repouso para discos gerenciados

O Armazenamento em Disco do Azure dá suporte à criptografia dupla em repouso para discos gerenciados. Para obter informações conceituais sobre a criptografia dupla em repouso, bem como outros tipos de criptografia de disco gerenciado, consulte a seção [criptografia dupla em repouso](disk-encryption.md#double-encryption-at-rest) do nosso artigo sobre criptografia de disco.

## <a name="getting-started"></a>Introdução

1. Entre no [portal do Azure](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > Você deve usar o [link fornecido](https://aka.ms/diskencryptionupdates) para acessar o portal do Azure. A criptografia dupla em repouso não está visível no momento no portal do Azure público sem usar o link.

1. Procure e selecione **conjuntos de criptografia de disco**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Captura de tela da portal do Azure principal, os conjuntos de criptografia de disco são realçados na barra de pesquisa.":::

1. Selecione **+ Adicionar**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="Captura de tela da folha do conjunto de criptografia de disco, + adicionar é realçada.":::

1. Selecione uma das regiões com suporte.
1. Para **tipo de criptografia**, selecione **criptografia dupla com chaves** gerenciadas por plataforma e gerenciadas pelo cliente.

    > [!NOTE]
    > Depois de criar um conjunto de criptografia de disco com um tipo de criptografia específico, ele não pode ser alterado. Se você quiser usar um tipo de criptografia diferente, deverá criar um novo conjunto de criptografia de disco.

1. Preencha as informações restantes.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="A captura de tela da folha de criação do conjunto de criptografia de disco, as regiões e a criptografia dupla com chaves gerenciadas por plataforma e gerenciadas pelo cliente são realçadas.":::

1. Selecione um Azure Key Vault e uma chave ou crie um novo, se necessário.

    > [!NOTE]
    > Se você criar uma instância de Key Vault, deverá habilitar a exclusão reversível e limpar a proteção. Essas configurações são obrigatórias ao usar um Key Vault para criptografar discos gerenciados e protegê-lo contra a perda de dados devido à exclusão acidental.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Captura de tela da folha de criação de Key Vault.":::

1. Selecione **Criar**.
1. Navegue até o conjunto de criptografia de disco que você criou e selecione o erro que é exibido. Isso irá configurar o conjunto de criptografia de disco para funcionar.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="Captura de tela do erro de conjunto de criptografia de disco exibido; o texto de erro é: para associar um disco, uma imagem ou um instantâneo com esse conjunto de criptografia de disco, você deve conceder permissões ao cofre de chaves.":::

    Uma notificação deve aparecer e ter sucesso. Isso permitirá que você use o conjunto de criptografia de disco com o cofre de chaves.
    
    ![Captura de tela de permissão e atribuição de função bem-sucedidas para o cofre de chaves.](media/virtual-machines-disks-double-encryption-at-rest-portal/disk-encryption-notification-success.png)

1. Navegue até o disco.
1. Selecione **Criptografia**.
1. Para **tipo de criptografia**, selecione **criptografia dupla com chaves** gerenciadas por plataforma e gerenciadas pelo cliente.
1. Selecione o conjunto de criptografia de disco.
1. Selecione **Salvar**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Captura de tela da folha criptografia do disco gerenciado, o tipo de criptografia mencionado anteriormente é realçado.":::

Agora você habilitou a criptografia dupla em repouso no disco gerenciado.


## <a name="next-steps"></a>Próximas etapas

- [Azure PowerShell-habilitar chaves gerenciadas pelo cliente com discos gerenciados por criptografia do lado do servidor](./windows/disks-enable-customer-managed-keys-powershell.md)
- [Exemplos de modelo do Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Habilitar chaves gerenciadas pelo cliente com criptografia do lado do servidor-exemplos](./linux/disks-enable-customer-managed-keys-cli.md#examples)