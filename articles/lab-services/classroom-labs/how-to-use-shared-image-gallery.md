---
title: Use uma galeria de imagem compartilhada nos serviços de laboratório do Azure | Microsoft Docs
description: Saiba como configurar uma conta de laboratório para usar uma galeria de imagens compartilhadas para que um usuário pode compartilhar uma imagem com os outros e outro usuário pode usar a imagem para criar um modelo de VM no laboratório.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 93136c7d685bd9fc8ec4bcdea3a900b28029059b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695131"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Use uma galeria de imagem compartilhada no Azure Lab Services
Este artigo mostra como o administrador de professores/laboratório pode economizar uma imagem de máquina virtual de modelo para que ele seja reutilizado por outros. Essas imagens são salvas no Azure [Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-image-galleries.md). Como uma primeira etapa, o administrador de laboratório anexa uma galeria de imagem compartilhada existente para a conta de laboratório. Depois que a Galeria de imagens compartilhadas é anexada, laboratórios criados na conta de laboratório podem salvar imagens de galeria de imagens compartilhadas. Outros professores podem selecionar essa imagem da Galeria de imagens compartilhadas para criar um modelo para suas classes. 

## <a name="prerequisites"></a>Pré-requisitos
Criar uma galeria de imagem compartilhada usando um [Azure PowerShell](../../virtual-machines/windows/shared-images.md) ou [CLI do Azure](../../virtual-machines/linux/shared-images.md).

## <a name="attach-a-shared-image-gallery-to-a-lab-account"></a>Anexar uma galeria de imagens compartilhadas para uma conta de laboratório
O procedimento a seguir mostra como anexar uma galeria de imagens compartilhadas para uma conta de laboratório. 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo. Selecione **Serviços de Laboratório** na seção **DEVOPS**. Se você selecionar a estrela (`*`) ao lado de **Serviços de Laboratório**, ela será adicionada à seção **FAVORITOS** no menu esquerdo. Na próxima vez em diante, selecione **Serviços de Laboratório** em **FAVORITOS**.

    ![Todos os Serviços -> Serviços de Laboratório](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecione sua conta de laboratório para ver os **conta de laboratório** página. 
4. Selecione **Galeria de imagens compartilhadas** no menu à esquerda e selecione **Attach** na barra de ferramentas. 

    ![Compartilhado da Galeria de imagem - adicionar botão](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Sobre o **anexar uma galeria de imagens existente compartilhado** página, selecione sua galeria de imagens compartilhadas e selecione **Okey**.

    ![Selecione uma galeria existente](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Você verá a tela a seguir: 

    ![Minha galeria na lista](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Neste exemplo, ainda não há nenhuma imagem na Galeria de imagens compartilhadas.

Identidade do Azure Lab Services é adicionada como um colaborador para a Galeria de imagem compartilhada que é anexada ao laboratório. Ele permite aos professores / administrador de TI para salvar a máquina virtual de imagens na Galeria de imagem compartilhada. Todos os laboratórios criados nessa conta de laboratório tem acesso à Galeria de imagens compartilhadas anexado. 

Todas as imagens na Galeria de imagens compartilhadas anexados são habilitadas por padrão. Você pode habilitar ou desabilitar as imagens selecionadas selecionando-os na lista e usando o **habilitar imagens selecionadas** ou **desabilitar imagens selecionadas** botão. 

## <a name="detach-a-shared-image-gallery"></a>Desanexar uma galeria de imagem compartilhada
Apenas uma galeria de imagens compartilhadas pode ser anexada a um laboratório. Se você deseja anexar outro Galeria de imagens compartilhadas, Desanexe o ano atual antes de anexar um novo. Para desanexar uma galeria de imagem compartilhada do seu laboratório, selecione **desanexar** na barra de ferramentas e confirme a operação desanexar. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Salvar uma imagem na Galeria de imagem compartilhada
Depois que estiver anexada a uma galeria de imagem compartilhada, um professor pode salvar uma imagem de modelo para a Galeria de imagens compartilhadas para que ele possa ser reutilizado por outros professores.

![Salvar imagem de máquina virtual na Galeria](../media/how-to-use-shared-image-gallery/save-virtual-machine.png)

## <a name="use-an-image-from-the-shared-image-gallery"></a>Usar uma imagem da Galeria de imagens compartilhadas
Professor/professor pode escolher uma imagem personalizada disponível na Galeria de imagens de compartilhado para o modelo durante a criação do novo laboratório.

![Use a imagem de máquina virtual da Galeria](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre galerias de imagens compartilhadas, consulte [Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-image-galleries.md).
