---
title: Capturar uma imagem de uma VM do Linux | Microsoft Docs
description: "Saiba como capturar uma imagem de uma VM (máquina virtual) do Azure baseada em Linux criada com o modelo de implantação clássico."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
ms.openlocfilehash: ecde5dd3211bfbb290e6910d7d55136d079c6cf3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Como capturar uma máquina virtual clássica do Linux como uma imagem
> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Saiba como [executar estas etapas usando o modelo do Resource Manager](../capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Este artigo mostra como capturar uma VM (máquina virtual) do Azure clássica executando o Linux como uma imagem para criar outras máquinas virtuais. Esta imagem inclui o disco do sistema operacional e discos de dados anexados à VM. Ele não inclui a configuração de rede, então você precisará configurá-la quando criar as outras VMs por meio da imagem.

O Azure armazena a imagem em **Imagens**, juntamente com quaisquer imagens carregadas. Para saber mais sobre imagens, confira [Sobre imagens da Máquina Virtual no Azure][About Virtual Machine Images in Azure].

## <a name="before-you-begin"></a>Antes de começar
Essas etapas pressupõem que você já criou uma VM do Azure usando o modelo de implantação clássico e configurou o sistema operacional, incluindo a anexação dos discos de dados. Se você precisar criar uma VM, leia [Como criar uma máquina virtual do Linux][How to Create a Linux Virtual Machine].

## <a name="capture-the-virtual-machine"></a>Capturar a máquina virtual
1. [Conecte-se à VM](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) usando um cliente SSH de sua escolha.
2. Na janela SSH, digite o comando a seguir. A saída de `waagent` pode variar um pouco dependendo da versão do utilitário:

    ```bash
    sudo waagent -deprovision+user
    ```

    O comando anterior tenta limpar o sistema e torná-lo adequado para o reprovisionamento. Essa operação realiza as seguintes tarefas:

   * Remove as chaves de host SSH (se Provisioning.RegenerateSshHostKeyPair for 'y' no arquivo de configuração)
   * Limpa a configuração de servidor de nomes em /etc/resolv.conf
   * Remove a senha do usuário `root` de /etc/shadow (se Provisioning.DeleteRootPassword for 'y' no arquivo de configuração)
   * Remove concessões de cliente DHCP em cache
   * Reinicia o nome de host para localdomain.localdomain
   * Exclui a última conta de usuário provisionado (obtida em /var/lib/waagent) **e dados associados**.

     > [!NOTE]
     > O desprovisionamento exclui arquivos e dados para “generalizar” a imagem. Execute este comando apenas em uma VM que você deseja capturar como um novo modelo de imagem. Ele não garante que a imagem esteja sem nenhuma informação confidencial ou seja adequada para redistribuição a terceiros.

3. Digite **y** para continuar. Você pode adicionar o parâmetro `-force` para evitar esta etapa de confirmação.
4. Digite **Exit** para fechar o cliente SSH.

   > [!NOTE]
   > As etapas restantes presumem que você já [instalou a CLI do Azure](../../../cli-install-nodejs.md) no computador cliente. Todas as etapas a seguir também podem ser executadas no [Portal do Azure](http://portal.azure.com).

5. No computador cliente, abra a CLI do Azure e faça logon com sua assinatura do Azure. Para obter detalhes, leia [Conectar-se a uma assinatura do Azure da CLI do Azure](../../../xplat-cli-connect.md).

   > [!NOTE]
   > Faça logon no portal do Azure.

6. Verifique se você está no modo de Gerenciamento de Serviços:

    ```azurecli
    azure config mode asm
    ```

7. Desligar a VM já desprovisionada. O exemplo a seguir desliga a VM chamada `myVM`:

    ```azurecli
    azure vm shutdown myVM
    ```
   Se necessário, é possível exibir uma lista de todas as VMs criadas na sua assinatura usando `azure vm list`

   > [!NOTE]
   > Se você estiver usando o portal do Azure, selecione a VM e clique em **Parar** para desligar a VM.

8. Quando a VM é interrompida, capture a imagem. O exemplo a seguir captura a VM denominada `myVM` e cria uma imagem generalizada chamada `myNewVM`:

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    O subcomando `-t` exclui a máquina virtual original.

    > [!NOTE]
    > No portal do Azure, você pode capturar uma imagem selecionando **Imagem** no menu de hub. Você precisará fornecer as seguintes informações para a imagem: nome, grupo de recursos, local, tipo do sistema operacional e caminho de blob de armazenamento.

9. A nova imagem agora está disponível na lista de imagens que podem ser usadas para configurar qualquer nova VM. Você pode exibi-la com o comando:

   ```azurecli
   azure vm image list
   ```

   No [portal do Azure](http://portal.azure.com), a nova imagem é exibida nas **Imagens de VM (clássico)** que pertencem aos serviços de **Computação**. É possível acessar as **Imagens de VM (clássico)** clicando em _Mais serviços_ na parte inferior da lista de serviços do Azure e, em seguida, verificando os serviços de **Computação**.   

   ![Captura de imagem bem-sucedida](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Próximas etapas
A imagem está pronta para ser usada para criar VMs. É possível usar o comando `azure vm create` da CLI do Azure e fornecer o nome da imagem que você criou. Para obter mais informações, consulte [Usando a CLI do Azure com o modelo de implantação Clássico](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

Como alternativa, use o [Portal do Azure](http://portal.azure.com) para criar uma VM personalizada usando o método **Imagem** e selecionando a imagem que você criou. Para obter mais informações, consulte [Como criar uma VM personalizada][How to Create a Custom Virtual Machine].

**Consulte também:** [Guia do usuário do agente Linux para o Azure](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[About Virtual Machine Images in Azure]:../../virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]:create-custom.md
[How to Attach a Data Disk to a Virtual Machine]:attach-disk.md
[How to Create a Linux Virtual Machine]:create-custom.md
