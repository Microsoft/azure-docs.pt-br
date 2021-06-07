---
title: Criar e criptografar uma VM do Linux com o portal do Azure
description: Neste início rápido, você aprende a usar o portal do Azure para criar e criptografar uma máquina virtual Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 188ba72f4da4e5a24554f895473f1c74e48d50fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558348"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Início Rápido: Criar e criptografar uma máquina virtual com o portal do Azure

As máquinas virtuais (VM) do Azure podem ser criadas por meio do Portal do Azure. O portal do Azure é uma interface de usuário baseada em navegador para criar as VMS e seus recursos relacionados. Neste início rápido, você usará o portal do Azure para implantar uma VM (máquina virtual) do Linux executando o Ubuntu 18.04 LTS, criar um cofre de chaves para o armazenamento de chaves de criptografia e criptografar a VM.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na página Novo, em Popular, selecione **Ubuntu Server 18.04 LTS**.
1. Na guia Informações Básicas, em Detalhes do projeto, confirme se a assinatura correta está selecionada.
1. Em "Grupo de Recursos", selecione **Criar**. Insira *myResourceGroup* como o nome e selecione **OK**.
1. Para **Nome da máquina virtual**, insira *MyVM*.
1. Em **Região**, selecione *EUA (Leste dos EUA)* .
1. Verifique se o **Tamanho** é *Standard D2s v3*.
1. Em **Conta de administrador**, selecione *Senha* como o **Tipo de autenticação**. Digite um nome de usuário e uma senha.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-linux-vm-creation.png" alt-text="Tela de criação de VM do Linux":::

    > [!WARNING]
    > A guia "Discos" apresenta o campo "Tipo de Criptografia" em **Opções de disco**. Esse campo é usado para especificar as opções de criptografia dos [Managed Disks](../managed-disks-overview.md) + CMK, **não** do Azure Disk Encryption.
    >
    > Para evitar confusões, sugerimos que você ignore completamente a guia *Discos* ao seguir este tutorial.

1. Selecione a guia "Gerenciamento" e verifique se você tem uma Conta de Armazenamento de Diagnóstico. Se você não tiver contas de armazenamento, selecione *Criar*, nomeie sua conta de armazenamento *myStorageAccount* e selecione "OK"

    :::image type="content" source="../media/disk-encryption/portal-quickstart-vm-creation-storage.png" alt-text="Tela de criação ResourceGroup":::

1. Clique em "Examinar + Criar".
1. Na página **Criar uma máquina virtual**, você pode ver os detalhes sobre a VM que você está prestes a criar. Quando estiver pronto, selecione **Criar**.

Levará alguns minutos para que sua VM seja implantada. Quando a implantação for concluída, vá para a próxima seção.

## <a name="encrypt-the-virtual-machine"></a>Criptografar a máquina virtual

1. Depois que a implantação da VM estiver concluída, selecione **Ir para o recurso**.
1. No lado esquerdo, selecione **Discos**.
1. Na barra superior, selecione **Configurações Adicionais**.
1. Em **Configurações de criptografia** > **Discos para criptografia**, selecione **Discos do sistema operacional e de dados**.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-disks-to-encryption.png" alt-text="Captura de tela que realça o sistema operacional e os discos de dados.":::

1. Em **Configurações de criptografia**, escolha **Selecionar um cofre de chaves e uma chave para criptografia**.
1. Na tela **Selecionar chave no Azure Key Vault**, selecione **Criar**.

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="Captura de tela que realça o botão Criar.":::

1. À esquerda de **Cofre de chaves e chave**, selecione **Clique aqui para selecionar uma chave**.
1. Em **Selecionar chave no Azure Key Vault**, no campo **Key Vault**, selecione **Criar**.
1. Na tela **Criar um cofre de chaves**, verifique se o Grupo de Recursos é *myResourceGroup* e dê um nome ao cofre de chaves.  Cada cofre de chaves no Azure deve ter um nome exclusivo.
1. Na guia **Políticas de Acesso**, marque a caixa **Azure Disk Encryption para a criptografia de volume**.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-keyvault-enable.png" alt-text="seleção de discos e criptografia":::

1. Selecione **Examinar + criar**.  
1. Depois que o cofre de chaves passar na validação, selecione **Criar**. Isso retornará você para a tela **Selecionar chave no Azure Key Vault**.
1. Deixe o campo **Chave** em branco e escolha **Selecionar**.
1. Na parte superior da tela de criptografia, clique em **Salvar**. Um pop-up avisará que a VM será reinicializada. Clique em **Sim**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos da máquina virtual, selecione Excluir, em seguida, confirme o nome do grupo de recursos a excluir.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault habilitado para chaves de criptografia, criou uma máquina virtual e habilitou-a para criptografia.  

> [!div class="nextstepaction"]
> [Visão geral do Azure Disk Encryption](disk-encryption-overview.md)