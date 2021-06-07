---
title: Definir configurações de imagem do Azure Marketplace no Azure DevTest Labs
description: Configure quais imagens do Azure Marketplace podem ser usadas ao criar uma VM no Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5158ed33bf253db1dbe0eb3232bc43d27ce15e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86512428"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Definir configurações de imagem do Azure Marketplace no Azure DevTest Labs
O DevTest Labs dá suporte à criação de VMs com base em imagens do Azure Marketplace, dependendo de como você tiver configurado o uso de imagens do Azure Marketplace em seu laboratório. Este artigo mostra como especificar quais imagens (caso haja alguma) do Azure Marketplace podem ser usadas durante a criação de VMs em um laboratório. Ele garante que sua equipe tenha acesso apenas às imagens do Marketplace de que precisam. 

## <a name="specify-allowed-images-for-creating-vms"></a>Especificar imagens permitidas para a criação de VMs
Seguindo estas etapas para especificar quais imagens do Azure Marketplace são permitidas ao criar uma VM. 

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório. 
4. Na home page da página do laboratório, selecione **configuração e políticas**.
5. Na página **configuração e políticas** do laboratório em **bases da máquina virtual**, selecione **imagens do Marketplace**.
6. Especifique se você deseja que todas as imagens qualificadas do Azure Marketplace estejam disponíveis para uso como uma base de uma nova VM. Se você selecionar **Sim**, todas as imagens do Azure Marketplace que atenderem a todos os critérios a seguir serão permitidas no laboratório:
   
   * A imagem cria uma única VM **e**
   * A imagem usa o Azure Resource Manager para provisionar VMs **e**
   * A imagem não exige a compra de um plano de licenciamento extra
     
     Se você não quiser permitir qualquer imagem, ou se quiser especificar quais imagens poderão ser usadas, selecione **Não**.
     
     ![Opção para permitir que todas as imagens do Marketplace sejam usadas como imagens base para VMs](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Se você tiver selecionado **Não** na etapa anterior, a caixa de seleção **Imagens permitidas/Selecionar todas** será habilitada. 
   Você pode usar essa opção junto com a caixa de pesquisa para marcar ou desmarcar rapidamente todos os itens exibidos na lista.
   * Selecione as imagens do Azure Marketplace que você deseja permitir para a criação da VM individualmente, marcando a caixa de seleção correspondente de cada imagem.
   * Não selecione nada na lista se não quiser permitir que as imagens do Azure Marketplace sejam usadas no laboratório.
   
     ![Você pode especificar quais imagens do Marketplace podem ser usadas como imagens base para VMs](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)


## <a name="troubleshoot"></a>Solucionar problemas
Se você não conseguir encontrar uma imagem específica a ser habilitada para o laboratório, siga estas etapas: 

- Verifique se você pode exibir a imagem ao criar uma VM de computação.
- A imagem pode não estar disponível no tipo de assinatura que você usa. Verifique com o administrador da assinatura o tipo de assinatura (por exemplo: MSDN, gratuito, pré-pago e assim por diante). 
- O suporte para imagens de Gen 2 no DevTest Labs é limitado. Se as versões Gen 1 e Gen 2 estiverem disponíveis para uma imagem, o DevTest Labs mostrará apenas a versão Gen 1 da imagem ao criar uma VM. A solução alternativa é criar uma imagem de Gen 2 personalizada fora do laboratório e usá-la para criar uma VM. Se houver apenas a versão de Gen 2 da imagem disponível, ela terá suporte e será mostrada na lista por DevTest Labs. 
      


## <a name="next-steps"></a>Próximas etapas
Depois de configurar como as imagens do Azure Marketplace são permitidas ao criar uma VM, a próxima etapa é [Adicionar uma VM ao seu laboratório](devtest-lab-add-vm.md).

