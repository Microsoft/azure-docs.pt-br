---
title: Capturar uma imagem de uma VM do Windows do Azure | Microsoft Docs
description: "Capture uma imagem de uma máquina virtual do Windows do Azure criada com o modelo de implantação clássico."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 6032263848c469ce2f416306e5c91c29f4cb30e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Capture uma imagem de uma máquina virtual do Windows do Azure criada com o modelo de implantação clássico.
> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para obter informações de modelo do Resource Manager, consulte [Capturar uma imagem gerenciada de uma VM generalizada no Azure](../capture-image-resource.md).

Esse artigo mostra como capturar uma máquina virtual do Azure executando Windows para que você a use como uma imagem para criar outras máquinas virtuais. Essa imagem inclui o disco do sistema operacional e discos de dados anexados à máquina virtual. Ele não inclui as configurações da rede; portanto, você precisará defini-las ao criar as outras máquinas virtuais que usam a imagem.

O Azure armazena a imagem em **Imagens de VM (clássico)**, um serviço de **Computação** listado quando todos os serviços do Azure são exibidos. Esse é o mesmo local em que as imagens que você carregou são armazenadas. Para obter detalhes sobre imagens, consulte [Sobre as imagens de máquinas virtuais](about-images.md?toc=%2fazure%2fvirtual-machines%2fWindows%2fclassic%2ftoc.json).

## <a name="before-you-begin"></a>Antes de começar
Essas etapas assumem que você já criou uma máquina virtual do Azure e já configurou o sistema operacional, incluindo os anexos de discos de dados. Se você ainda não fez isso, consulte os seguintes artigos para obter informações sobre como criar e preparar a máquina virtual:

* [Criar uma máquina virtual de uma imagem](createportal.md)
* [Como anexar um disco de dados à máquina virtual](attach-disk.md)
* Verifique se que as funções de servidor são compatíveis com Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!WARNING]
> Esse processo exclui a máquina virtual original depois de ela ser capturada.
>
>

Antes de capturar uma imagem de uma máquina virtual do Azure, é recomendável fazer backup da máquina virtual de destino. O backup das máquinas virtuais do Azure pode ser feito usando o Backup do Azure. Para obter detalhes, veja [Fazer backup de máquinas virtuais do Azure](../../../backup/backup-azure-vms.md). Existem outras soluções de parceiros certificados. Para descobrir o que está disponível no momento, pesquise no Azure Marketplace.

## <a name="capture-the-virtual-machine"></a>Capturar a máquina virtual
1. No [portal do Azure](http://portal.azure.com), **conecte-se** à máquina virtual. Para obter instruções, confira [Como entrar em uma máquina virtual que executa o Windows Server][How to sign in to a virtual machine running Windows Server].
2. Abra uma janela de Prompt de comando como administrador.
3. Altere o diretório para `%windir%\system32\sysprep`e execute sysprep.exe.
4. A caixa de diálogo **Ferramenta de Preparação do Sistema** é aberta. Faça o seguinte:

   * Em **Ação de Limpeza do Sistema**, selecione **Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)** e verifique se a opção **Generalizar** está marcada. Para obter mais informações sobre como usar o Sysprep, consulte [Como usar Sysprep: uma introdução][How to Use Sysprep: An Introduction].
   * Em **Opções de Desligamento**, selecione **Desligar**.
   * Clique em **OK**.

   ![Executar o Sysprep](./media/capture-image/SysprepGeneral.png)
5. O Sysprep desliga a máquina virtual, alterando o status dela no portal do Azure para **Parado**.
6. No portal do Azure, clique em **Máquinas Virtuais (clássico)** e selecione a máquina virtual que você deseja capturar. O grupo **Imagens de VM (clássico)** é listado em **Computação** ao exibir **Mais serviços**.

7. Na barra de comandos, clique em **Capturar**.

   ![Capturar a máquina virtual](./media/capture-image/CaptureVM.png)

   A caixa de diálogo **Capturar máquina virtual** é exibida.

8. Em **Nome da imagem**, digite um nome para a nova imagem. Em **Rótulo da imagem**, digite um rótulo para a nova imagem.

9. Clique em **Executei o Sysprep na máquina virtual**. Essa caixa de seleção se refere às ações com o Sysprep nas etapas 3 a 5. Uma imagem _deve_ ser generalizada com a execução do Sysprep antes que uma imagem do Windows Server seja adicionada ao conjunto de imagens personalizadas.

10. Após a conclusão da captura, a nova imagem fica disponível no **Marketplace**, em **Computação**, no contêiner **Imagens de VM (clássico)**.

    ![Captura de imagem bem-sucedida](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Próximas etapas
A imagem está pronta para ser usada para criar máquinas virtuais. Para fazer isso, você criará uma máquina virtual selecionando o item de menu **Mais serviços** na parte inferior do menu de serviços e, em seguida, **Imagens de VM (clássico)** no grupo **Computação**. Para obter instruções, consulte [Criar uma máquina virtual de uma imagem](createportal.md).

[How to sign in to a virtual machine running Windows Server]:connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
