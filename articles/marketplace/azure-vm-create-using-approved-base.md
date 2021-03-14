---
title: Criar uma VM (oferta de máquina virtual) do Azure de uma base aprovada, Azure Marketplace
description: Saiba como criar uma oferta de VM (máquina virtual) de uma base aprovada.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 94d21cb82290e59ebb003969a566c1bfc877713e
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200439"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Como criar uma máquina virtual usando uma base aprovada

Este artigo descreve como usar o Azure para criar uma VM (máquina virtual) contendo um sistema operacional previamente configurado e endossado. Se isso não for compatível com sua solução, será possível [criar e configurar uma VM local](azure-vm-create-using-own-image.md) usando um sistema operacional aprovado.

> [!NOTE]
> Antes de iniciar este procedimento, examine os [requisitos técnicos](marketplace-virtual-machines.md#technical-requirements) das ofertas de VM do Azure, incluindo os requisitos de VHD (disco rígido virtual).

## <a name="select-an-approved-base-image"></a>Selecionar uma imagem de base aprovada

Selecione uma das seguintes imagens do Windows ou do Linux como sua base.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview), [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

O Azure oferece uma variedade de distribuições aprovadas do Linux. Para obter uma lista atual, consulte [Linux em distribuições endossadas pelo Azure](../virtual-machines/linux/endorsed-distros.md).

## <a name="create-vm-on-the-azure-portal"></a>Criar VM no portal do Azure

1. Entre no [portal do Azure](https://ms.portal.azure.com/).
2. Selecione **Máquinas virtuais**.
3. Selecione **+ Adicionar** para abrir a tela **criar uma máquina virtual** .
4. Selecione a imagem na lista suspensa ou selecione **procurar todas as imagens públicas e privadas** para pesquisar ou procurar todas as imagens de máquina virtual disponíveis.
5. Para criar uma VM de **Gen 2** , vá para a guia **avançado** e selecione a opção **Gen 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Selecione Gen 1 ou Gen 2.":::

6. Selecione o tamanho da VM a ser implantada.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Selecione um tamanho de VM recomendado para a imagem selecionada.":::

7. Forneça os outros detalhes necessários para criar a VM.
8. Selecione **Examinar + criar** para examinar suas opções. Quando a mensagem **validação aprovada** for exibida, selecione  **criar**.

O Azure inicia o provisionamento da máquina virtual especificada. Acompanhe seu progresso selecionando a guia **máquinas virtuais** no menu à esquerda. Depois de criado, o status da máquina virtual é alterado para **em execução**.

## <a name="configure-the-vm"></a>Configurar a VM

Esta seção descreve como dimensionar, atualizar e generalizar uma VM do Azure. Essas etapas são necessárias para preparar a VM para ser implantada no Azure Marketplace.

### <a name="connect-to-your-vm"></a>Conectar-se à sua VM

Consulte a documentação a seguir para se conectar à sua VM do [Windows](../virtual-machines/windows/connect-logon.md) ou [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) .

### <a name="install-the-most-current-updates"></a>Instalar as atualizações mais atuais

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Realizar verificações de segurança adicionais

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Executar configuração personalizada e tarefas agendadas

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Próximas etapas

- Próxima etapa recomendada: [teste sua imagem de VM](azure-vm-image-test.md) para garantir que ela atenda aos requisitos de publicação do Azure Marketplace. Isso é opcional.
- Se você não quiser testar sua imagem de VM, entre no [Partner Center](https://partner.microsoft.com/) para publicar sua imagem.
- Se você encontrou dificuldades para criar seu novo VHD baseado no Azure, consulte [perguntas frequentes sobre a VM para o Azure Marketplace](azure-vm-create-faq.md).
