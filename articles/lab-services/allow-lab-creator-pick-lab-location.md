---
title: Permitir que o criador de laboratório escolha o local no Azure Lab Services
description: Este artigo descreve como um administrador de conta de laboratório pode permitir que os criadores de laboratório escolham locais para seus laboratórios.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3b6c2d83414b1abc763755fbf15402b122c2186b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85444192"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Permitir que o criador de laboratório escolha o local do laboratório em Azure Lab Services
No Azure Lab Services, um proprietário de conta de laboratório pode permitir que os criadores de laboratório (educadores) escolham um local para o laboratório criado. Esse local pode ser diferente do local da conta do laboratório. Um local é um grupo de regiões do Azure. Por exemplo, Estados Unidos local é um grupo de regiões como leste dos EUA, oeste dos EUA e assim por diante. 

Você, como proprietário da conta do laboratório, pode selecionar a opção **permitir que o criador** do laboratório escolha o local do laboratório ao criar uma conta de laboratório e depois de criar a conta de laboratório (ou uma conta de laboratório existente). 

## <a name="at-the-time-of-lab-account-creation"></a>No momento da criação da conta do laboratório
Ao criar uma conta de laboratório, você verá essa opção na primeira tela (guia**noções básicas** ). 

![Habilitar a opção no momento da criação do laboratório](./media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Essa opção será desabilitada se você selecionar uma rede virtual par para sua conta de laboratório na guia **avançado** .  

![Quando a rede virtual par está habilitada](./media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>Depois que a conta do laboratório for criada
Depois de criar a conta de laboratório, você pode habilitar ou desabilitar essa opção seguindo estas etapas: 

1. Na página **conta do laboratório** , selecione **configurações de laboratório** no menu à esquerda.
2. Selecione a opção **permitir que o criador** do laboratório escolha o local do laboratório se desejar permitir que o criador do laboratório selecione um local para o laboratório. Se ele estiver desabilitado, os laboratórios serão criados automaticamente no mesmo local em que a conta de laboratório existe. 
    
    Esse campo é desabilitado quando você seleciona uma rede virtual para o campo **rede virtual par** . É porque os laboratórios na conta do laboratório devem estar na mesma região que a conta do laboratório para que eles acessem os recursos na rede virtual do par. 
1. Selecione **Salvar** na barra de ferramentas. 

    ![Configurações do laboratório](./media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Nenhuma seleção de rede virtual e local não é permitida
Nesse cenário, você não habilitou a opção **permitir que o criador do laboratório escolha o local do laboratório** . 

![Nenhum local de laboratório](./media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Em seguida, os criadores de laboratório (educadores) não veem uma opção para escolher um local para o laboratório. Eles verão o preço por hora para cada opção de tamanho disponível para eles. Ao criar um laboratório, ele será criado em uma região do Azure que está no mesmo local que a região do Azure em que sua conta de laboratório está. Por exemplo, se a conta de laboratório estiver no **oeste dos EUA**, o laboratório poderá ser criado no **EUA Central do Sul** , mas não seria criado no **leste do Canadá**. Não garantimos nada sobre a região que escolhemos de lado que está no local. Se um tamanho estiver restrito no momento, o criador do laboratório verá uma caixa de seleção em que é possível ver os tamanhos que normalmente suportam, mas que estão indisponíveis no momento. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>Na rede virtual e a seleção de local não é permitida
Nesse cenário, a opção **permitir que o criador do laboratório escolha o local do laboratório** está desabilitada porque você selecionou uma rede virtual par para a conta do laboratório. Em seguida, os criadores de laboratório verão a mesma tela que a opção anterior. Como todas as VMs precisam estar na mesma região do Azure que a rede virtual, o laboratório será criado na mesma região do Azure em que a rede virtual está. Se essa região específica for restrita para um tamanho, o tamanho será exibido como indisponível. 

## <a name="location-selection-is-enabled"></a>A seleção de local está habilitada
Quando você seleciona **permitir que o criador**do laboratório escolha o local do laboratório, os professores (educadores de laboratório) veem uma opção para selecionar um local ao criar um laboratório. 

![Escolha um local de laboratório](./media/allow-lab-creator-pick-lab-location/location-selection.png)

Os criadores de laboratório veem o intervalo de preços para todos os locais em que o tamanho está e pode escolher um local. O laboratório será criado em qualquer região do Azure que é mapeada para esse local.

Se um local for restrito, ele não será mostrado na lista por padrão. Expanda a lista suspensa e selecione **Mostrar locais não disponíveis para esse tamanho**. 

![Mostrar locais não disponíveis](./media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Cost
Anteriormente, o preço era baseado no tamanho da VM que você escolhe para o laboratório. Agora, o preço é baseado na combinação de sistema operacional (SO), tamanho e local. 

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Conectar a rede do seu laboratório a uma rede virtual par](how-to-connect-peer-virtual-network.md)
- [Anexar uma galeria de imagens compartilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicionar um usuário como um proprietário do laboratório](how-to-add-user-lab-owner.md)
- [Exibir configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Definir outras configurações para um laboratório](how-to-configure-lab-accounts.md)