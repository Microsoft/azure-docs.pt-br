---
title: Especificar imagens do Marketplace para um laboratório no Azure Lab Services
description: Este artigo mostra como especificar as imagens do Marketplace que o criador de laboratório pode usar para criar laboratórios em uma conta de laboratório no Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5c81c8d7a15d67055729a29e98f5b7e30f3d0764
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434729"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Especificar imagens do Marketplace disponíveis para os criadores de laboratório
Como proprietário da conta de laboratório, você pode especificar as imagens do Marketplace que os criadores do laboratório podem usar para criar laboratórios na conta de laboratório. 

## <a name="select-images-available-for-labs"></a>Selecionar imagens disponíveis para laboratórios
Selecione **Imagens do Marketplace** no menu esquerdo. Por padrão, você deve ver a lista completa de imagens (habilitadas e desabilitadas). Você pode filtrar a lista para ver apenas as imagens habilitadas/desabilitadas selecionando apenas a opção **habilitado** somente / **desabilitado** na lista suspensa na parte superior. 
    
![Página Imagens do Marketplace](./media/tutorial-setup-lab-account/marketplace-images-page.png)

As imagens do Marketplace exibidas na lista são apenas aquelas que atendem as seguintes condições:
    
- Cria uma única VM
- Usa o Azure Resource Manager para provisionar VMs
- Não requer a compra de um plano de licenciamento extra

## <a name="disable-images-for-a-lab"></a>Desabilitar imagens de um laboratório 
Para desabilitar uma única imagem para um laboratório, selecione **... (reticências)** na última coluna, selecione **desabilitar imagem**. 

![Desabilitar uma imagem](./media/tutorial-setup-lab-account/disable-one-image.png) 

Como alternativa, marque a caixa de seleção antes do nome da imagem e selecione **desabilitar imagens selecionadas** na barra de ferramentas. 

Para desabilitar várias imagens ao mesmo tempo, selecione caixas de seleção antes dos nomes da imagem e selecione **desabilitar imagens selecionadas** na barra de ferramentas. 

![Desabilitar várias imagens](./media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Habilitar imagens para um laboratório
Para habilitar uma imagem desabilitada, selecione **... (reticências)** na última coluna, selecione **habilitar imagem**. Como alternativa, marque a caixa de seleção antes do nome da imagem e selecione **Habilitar imagens selecionadas** na barra de ferramentas. 

Para desabilitar várias imagens ao mesmo tempo, selecione caixas de seleção antes dos nomes da imagem e selecione **Habilitar imagens selecionadas** na barra de ferramentas. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>Habilitar imagens no momento da criação do laboratório
Você pode habilitar mais imagens no momento da criação do laboratório: 

1. Entre no site do [Azure Lab Services](https://labs.azure.com) usando as credenciais do **proprietário da conta do laboratório**
2. Selecione a imagem de máquina virtual padrão ou a seta para baixo. 
3. Selecione **habilitar mais opções de imagem**. 

    ![Habilitar mais opções de imagem](./media/specify-marketplace-images/enable-more-images-menu.png)
4. Siga as instruções da seção anterior para habilitar as imagens selecionadas. 
5. Talvez seja necessário fechar a nova janela do **laboratório** e reabri-la para ver as imagens selecionadas na etapa anterior. 



## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como usuário do laboratório, laboratórios de acesso](how-to-use-classroom-lab.md)
