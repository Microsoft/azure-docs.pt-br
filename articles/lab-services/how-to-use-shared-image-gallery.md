---
title: Use uma galeria de imagens compartilhadas no Azure Lab Services | Microsoft Docs
description: Saiba como configurar uma conta de laboratório para usar uma galeria de imagens compartilhadas para que um usuário possa compartilhar uma imagem com outra e outro usuário possa usar a imagem para criar uma VM modelo no laboratório.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: 984cb4c47099928ffab327895a728cbe8a8f9604
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791383"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Usar uma galeria de imagens compartilhadas no Azure Lab Services
Este artigo mostra como educadores/administradores de laboratório podem salvar um modelo de imagem de máquina virtual em uma [galeria de imagens compartilhada](../virtual-machines/shared-image-galleries.md) para que ela possa ser usada por outras pessoas para criar laboratórios. 

> [!IMPORTANT]
> Ao usar uma galeria de imagens compartilhadas, o Azure Lab Services dá suporte apenas a imagens com menos de 128 GB de espaço em disco do sistema operacional. Imagens com mais de 128 GB de espaço em disco ou vários discos não serão mostradas na lista de imagens de máquina virtual durante a criação do laboratório.

## <a name="scenarios"></a>Cenários
Aqui estão alguns dos cenários aos quais esse recurso dá suporte: 

- Um administrador de conta de laboratório anexa uma galeria de imagens compartilhadas à conta de laboratório e carrega uma imagem para a galeria de imagens compartilhadas fora do contexto de um laboratório. Em seguida, os criadores de laboratório podem usar essa imagem da galeria de imagens compartilhadas para criar laboratórios. 
- Um administrador de conta de laboratório anexa uma galeria de imagens compartilhadas à conta de laboratório. Um criador de laboratório (instrutor) salva a imagem personalizada do laboratório na galeria de imagens compartilhadas. Em seguida, outros criadores de laboratório podem selecionar essa imagem na galeria de imagens compartilhadas para criar um modelo para os laboratórios deles. 

    Quando uma imagem é salva em uma galeria de imagens compartilhadas, o Azure Lab Services replica a imagem salva em outras regiões disponíveis na mesma [geografia](https://azure.microsoft.com/global-infrastructure/geographies/). Isso verifica se a imagem está disponível para laboratórios criados em outras regiões na mesma geografia. Salvar imagens em uma galeria de imagens compartilhadas gera um custo adicional, que inclui o custo de todas as imagens replicadas. Esse custo é separado do custo de uso do Azure Lab Services. Para obter mais informações sobre o preço da Galeria de Imagens Compartilhadas, confira [Galeria de Imagens Compartilhadas – cobrança]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).
    
## <a name="prerequisites"></a>Pré-requisitos
- Crie uma galeria de imagens compartilhada usando o [Azure PowerShell](../virtual-machines/shared-images-powershell.md) ou o [CLI do Azure](../virtual-machines/shared-images-cli.md).
- Você anexou uma galeria de imagens compartilhadas à conta de laboratório. Para obter instruções passo a passo, consulte [Como anexar ou desanexar a galeria de imagens compartilhadas](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Salvar uma imagem na galeria de imagens compartilhadas
Depois que uma galeria de imagens compartilhadas é anexada, um administrador de conta de laboratório ou um educador pode salvar uma imagem na galeria de imagens compartilhadas para que ela possa ser reutilizada por outros educadores. 

1. Na página **Modelo** do laboratório, selecione **Exportar para a Galeria de Imagens Compartilhadas** na barra de ferramentas.

    ![Botão Salvar Imagem](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Na caixa de diálogo **Exportar para a Galeria de Imagens Compartilhadas**, insira um **nome para a imagem** e, em seguida, selecione **Exportar**. 

    ![Caixa de diálogo Exportar para Galeria de Imagens Compartilhadas](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Você pode ver o progresso dessa operação na página do **Modelo**. Esta operação pode levar algum tempo. 

    ![Exportação em andamento](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Quando a operação de exportação for bem-sucedida, você verá a seguinte mensagem:

    ![Exportação concluída](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    Depois de salvar a imagem na galeria de imagens compartilhadas, você poderá usar essa imagem na galeria para criar outro laboratório. Você também pode carregar uma imagem na galeria de imagens compartilhadas fora do contexto de um laboratório. Para obter mais informações, consulte [Visão geral da galeria de imagens compartilhadas](../virtual-machines/shared-images-powershell.md). 

    > [!IMPORTANT]
    > Quando você [salva uma imagem de modelo de um laboratório](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) no Azure Lab Services em uma galeria de imagens compartilhada, a imagem é carregada na galeria como uma **imagem especializada**. [Imagens especializadas](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) mantêm informações específicas do computador e perfis de usuário. Você ainda pode carregar diretamente uma imagem generalizada para a Galeria fora do Azure Lab Services.    

## <a name="use-an-image-from-the-shared-image-gallery"></a>Salvar uma imagem da galeria de imagens compartilhadas
Um educador pode escolher uma imagem personalizada disponível na galeria de imagens compartilhadas para usar como modelo durante a criação do novo laboratório.

![Use uma imagem da máquina virtual na galeria](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> Você pode criar uma VM de modelo com base em imagens **generalizadas** e **especializadas** em Azure Lab Services. 


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre galerias de imagens compartilhadas, consulte [galeria de imagens compartilhadas](../virtual-machines/shared-image-galleries.md).