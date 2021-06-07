---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a967777b65c06cf23239a47e8e691fb3a29231b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88815463"
---
A configuração de chaves gerenciadas pelo cliente para seus discos exigirá que você crie recursos em uma ordem específica, se estiver fazendo isso pela primeira vez. Primeiro, será necessário criar e configurar um Azure Key Vault.

## <a name="set-up-your-azure-key-vault"></a>Configure o seu Cofre da Chave do Azure

1. Faça logon no [Portal do Azure](https://aka.ms/diskencryptionupdates).
1. Procure e selecione **cofres de chaves**.

    [![Captura de tela da portal do Azure com a caixa de diálogo de pesquisa expandida.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Seu cofre de chaves do Azure, conjunto de criptografia de disco, VM, discos e instantâneos devem estar na mesma região e assinatura para que a implantação tenha sucesso.

1. Selecione **+ Adicionar** para criar um novo Key Vault.
1. Criar um grupo de recursos.
1. Insira um nome de Key Vault, selecione uma região e selecione um tipo de preço.

    > [!NOTE]
    > Ao criar a instância do Key Vault, habilite a exclusão temporária e a proteção de limpeza. A exclusão temporária garante que a Key Vault mantenha uma chave excluída para determinado período de retenção (padrão de 90 dias). A proteção de limpeza garante que uma chave excluída não seja excluída permanentemente até que o período de retenção termine. Essas configurações protegem você contra a perda de dados devido à exclusão acidental. Essas configurações são obrigatórias ao usar um Key Vault para criptografar discos gerenciados.

1. Selecione **revisão + criar**, verifique suas opções e, em seguida, selecione **criar**.

    ![Captura de tela da experiência de criação de Azure Key Vault. Mostrando os valores específicos que você criar](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. Depois que o cofre de chaves concluir a implantação, selecione-o.
1. Selecione **chaves** em **configurações**.
1. Selecione **Gerar/Importar**.

    ![Captura de tela do painel configurações de recurso Key Vault. Mostra o botão gerar/importar dentro das configurações.](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. Deixe o **tipo de chave** definido como **RSA** e o **tamanho da chave RSA** definido como **2048**.
1. Preencha as seleções restantes como desejar e, em seguida, selecione **criar**.

    ![Captura de tela da folha criar uma chave que aparece uma vez que o botão gerar/importar está selecionado](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

## <a name="set-up-your-disk-encryption-set"></a>Configurar o conjunto de criptografia de disco

1. Procure por **conjuntos de criptografia de disco** e selecione-o.
1. Na folha **conjuntos de criptografia de disco** , selecione **+ Adicionar**.

    ![Captura da tela principal do portal de criptografia de disco. Realçando o botão Adicionar](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. Selecione seu grupo de recursos, nomeie o conjunto de criptografia e selecione a mesma região que o cofre de chaves.
1. Para **tipo** de criptografia **, selecione criptografia em repouso com uma chave gerenciada pelo cliente**.

    > [!NOTE]
    > Depois de criar um conjunto de criptografia de disco com um tipo de criptografia específico, ele não pode ser alterado. Se você quiser usar um tipo de criptografia diferente, deverá criar um novo conjunto de criptografia de disco.

1. Selecione **clique para selecionar uma chave**.
1. Selecione o cofre de chaves e a chave que você criou anteriormente, bem como a versão.
1. Pressione **Selecionar**.
1. Selecione **Examinar + Criar** e **Criar**.

    ![Captura de tela da folha de criação de criptografia de disco. Mostrando a assinatura, o grupo de recursos, o nome do conjunto de criptografia de disco, a região e o Key Vault + seletor de chave.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png)

1. Abra o conjunto de criptografia de disco depois de concluir a criação e selecione o alerta que aparece.

    ![Captura de tela de alerta pop-up: ' para associar um disco, imagem ou instantâneo a um conjunto de criptografia de disco, você deve conceder permissões para o cofre de chaves '. Selecione este alerta para continuar](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-alert-fix.png)

    Duas notificações devem ser exibidas e bem sucedidos. Isso permite que você use o conjunto de criptografia de disco com o cofre de chaves.

    ![Captura de tela de permissão e atribuição de função bem-sucedidas para o cofre de chaves.](./media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)