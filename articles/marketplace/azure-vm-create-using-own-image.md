---
title: Criar uma oferta de máquina virtual do Azure no Azure Marketplace usando sua própria imagem
description: Saiba como publicar uma oferta de máquina virtual no Azure Marketplace usando sua própria imagem.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 42022d1204c3b524ee2e9ef2770f616fba89dc8c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283556"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Como criar uma máquina virtual usando sua própria imagem

Este artigo descreve como criar e implantar uma imagem de VM (máquina virtual) fornecida pelo usuário.

> [!NOTE]
> Antes de iniciar este procedimento, examine os [requisitos técnicos](marketplace-virtual-machines.md#technical-requirements) das ofertas de VM do Azure, incluindo os requisitos de VHD (disco rígido virtual).

Para usar uma imagem de base aprovada em vez disso, siga as instruções em [criar uma imagem de VM de uma base aprovada](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>Configurar a VM

Esta seção descreve como dimensionar, atualizar e generalizar uma VM do Azure. Essas etapas são necessárias para preparar a VM para ser implantada no Azure Marketplace.

### <a name="size-the-vhds"></a>Dimensionar os VHDs

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Instalar as atualizações mais atuais

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Realizar verificações de segurança adicionais

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Executar configuração personalizada e tarefas agendadas

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

## <a name="upload-the-vhd-to-azure"></a>Carregar o VHD no Azure

Configure e prepare a VM a ser carregada conforme descrito em [preparar um VHD do Windows ou VHDX para carregar no Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) ou [criar e carregar um VHD do Linux](../virtual-machines/linux/create-upload-generic.md).

## <a name="extract-the-vhd-from-image-if-using-image-building-services"></a>Extrair o VHD da imagem (se estiver usando serviços de criação de imagens)

Se você estiver usando um serviço de criação de imagens, como o [Pack](https://www.packer.io/), talvez seja necessário extrair o VHD da imagem. Não há uma maneira direta de fazer isso. Você precisará criar uma VM e extrair o VHD do disco da VM.

### <a name="create-the-vm-on-the-azure-portal"></a>Criar a VM no portal do Azure

Siga estas etapas para criar a imagem de VM de base no [portal do Azure](https://ms.portal.azure.com/).

1. Entre no [portal do Azure](https://ms.portal.azure.com/).
2. Selecione **Máquinas virtuais**.
3. Selecione **+ Adicionar** para abrir a tela **criar uma máquina virtual** .
4. Selecione a imagem na lista suspensa ou selecione **procurar todas as imagens públicas e privadas** para pesquisar ou procurar todas as imagens de máquina virtual disponíveis.
5. Para criar uma VM de **Gen 2** , vá para a guia **avançado** e selecione a opção **Gen 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Selecione Gen 1 ou Gen 2.":::

6. Selecione o tamanho da VM a ser implantada.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Selecione Gen 1 ou Gen 2.":::

7. Forneça os outros detalhes necessários para criar a VM.
8. Selecione **Examinar + criar** para examinar suas opções. Quando a mensagem **validação aprovada** for exibida, selecione **criar**.

O Azure inicia o provisionamento da máquina virtual especificada. Acompanhe seu progresso selecionando a guia **máquinas virtuais** no menu à esquerda. Após a criação do status das alterações da máquina virtual para **execução**.

### <a name="connect-to-your-vm"></a>Conectar-se à sua VM

Consulte a documentação a seguir para se conectar à sua VM do [Windows](../virtual-machines/windows/connect-logon.md) ou [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) .

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Próximas etapas

- Próxima etapa recomendada: [teste sua imagem de VM](azure-vm-image-test.md) para garantir que ela atenda aos requisitos de publicação do Azure Marketplace. Isso é opcional.
- Se você não testar sua imagem de VM, continue com [gerar o URI de SAS](azure-vm-get-sas-uri.md).
- Se você encontrou dificuldades para criar seu novo VHD baseado no Azure, consulte [perguntas frequentes sobre a VM para o Azure Marketplace](azure-vm-create-faq.md).
