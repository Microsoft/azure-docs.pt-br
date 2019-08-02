---
title: Implantar o SAP S/4HANA ou o BW/4HANA em uma VM do Azure | Microsoft Docs
description: Implantar o SAP S/4HANA ou o BW/4HANA em uma VM do Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 65643352a269796fc5353ff4cd0cb15d5f1502ec
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707486"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Implantar o SAP S/4HANA ou o BW/4HANA no Azure
Este artigo descreve como implantar o S/4HANA no Azure usando o SAP CAL (SAP Cloud Appliance Library) 3.0. Para implantar outras soluções baseadas em SAP HANA, como BW/4HANA, siga as mesmas etapas.

> [!NOTE]
> Para obter mais informações sobre a SAP CAL, consulte o site [SAP Cloud Appliance Library](https://cal.sap.com/). A SAP também tem um blog sobre a [SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> A partir de 29 de maio de 2017, você pode usar o modelo de implantação do Azure Resource Manager além do modelo de implantação clássico menos preferencial para implantar a SAP CAL. É recomendável que você use o novo modelo de implantação do Resource Manager e ignore o modelo de implantação clássico.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Processo passo a passo para implantar a solução

A sequência de capturas de tela a seguir mostra como implantar a S/4HANA no Azure usando a SAP CAL. O processo funciona da mesma maneira para outras soluções, como o BW/4HANA.

A página **Soluções** mostra algumas soluções baseadas no SAP CAL HANA disponíveis no Azure. **SAP S/4HANA 1610 FPS01, dispositivo completamente ativado** está na linha do meio:

![Soluções de SAP CAL](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Criar uma conta na SAP CAL
1. Para entrar na SAP CAL pela primeira vez, use o seu SAP S-User ou outro usuário registrado com a SAP. Em seguida, defina uma conta da SAP CAL que é usada pela SAP CAL para implantar dispositivos no Azure. Na definição de conta, você precisa:

    a. Selecionar o modelo de implantação no Azure (Resource Manager ou clássico).

    b. Inserir sua assinatura do Azure. Uma conta da SAP CAL pode ser atribuída a apenas uma assinatura. Se você precisar de mais de uma assinatura, precisará criar uma outra conta da SAP CAL.

    c. Conceder a permissão da SAP CAL para implantar em sua assinatura do Azure.

   > [!NOTE]
   >  As próximas etapas mostram como criar uma conta da SAP CAL para implantações do Resource Manager. Se você já tiver uma conta da SAP CAL que está vinculada ao modelo de implantação clássico, é *necessário* seguir estas etapas para criar uma nova conta da SAP CAL. A nova conta da SAP CAL precisa ser implantada no modelo do Resource Manager.

1. Crie uma nova conta do SAP CAL. A página **Contas** mostra três opções para o Azure: 

    a. **Microsoft Azure (clássico)** é o modelo de implantação clássico e não é mais preferencial.

    b. **Microsoft Azure** é o novo modelo de implantação do Resource Manager.

    c. **Windows Azure operado pela 21Vianet** é uma opção na China que usa o modelo de implantação clássico.

    Para implantar no modelo do Resource Manager, selecione **Microsoft Azure**.

    ![Detalhes da conta da SAP CAL](./media/cal-s4h/s4h-pic-2a.png)

1. Insira a **ID da assinatura** do Azure que pode ser encontrada no portal do Azure.

   ![Contas da SAP CAL](./media/cal-s4h/s4h-pic3c.png)

1. Para autorizar a SAP CAL para implantar na assinatura do Azure que você definiu, clique em **Autorizar**. A seguinte página será exibida na guia do navegador:

   ![Entrar nos serviços de nuvem do Internet Explorer](./media/cal-s4h/s4h-pic4c.png)

1. Se mais de um usuário estiver listado, escolha a conta da Microsoft que está vinculada para ser o coadministrador da assinatura do Azure que você selecionou. A seguinte página será exibida na guia do navegador:

   ![Confirmação dos serviços de nuvem do Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

1. Clique em **Aceitar**. Se a autorização for bem-sucedido, a definição de conta da SAP CAL será exibida novamente. Após um curto período de tempo, uma mensagem confirmará que o processo de autorização foi bem-sucedido.

1. Para atribuir a conta da CAL SAP recém-criada ao seu usuário, insira sua **ID de usuário** na caixa de texto à direita e clique em **Adicionar**.

   ![Conta a ser associada ao usuário](./media/cal-s4h/s4h-pic8a.png)

1. Para associar sua conta com o usuário que você usa para entrar na SAP CAL, clique em **Análise**. 
 
1. Para criar a associação entre o usuário e a conta da CAL SAP recém-criada, clique em **Criar**.

   ![Usuário para a associação de conta da SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Você criou com êxito uma conta da SAP CAL que é capaz de:

- Usar o Modelo de implantação do Resource Manager.
- Implantar os sistemas SAP na sua assinatura do Azure.

Agora você pode começar a implantar a 4HANA/S em sua assinatura do usuário no Azure.

> [!NOTE]
> Antes de continuar, determine se você tem cotas de vCPU do Azure para as VMs Série H do Azure. No momento, a SAP CAL usa VMs Série H do Azure para implantar algumas das soluções com base em SAP HANA. Sua assinatura do Azure talvez não tenha cotas de vCPU da Série H para a Série H. Nesse caso, talvez seja necessário entrar em contato com o suporte do Azure para obter uma cota de pelo menos 16 vCPUs da Série H.
> 
> [!NOTE]
> Quando você implanta uma solução no Azure com a SAP CAL, talvez você descubra que pode escolher apenas uma região do Azure. Para implantar em regiões do Azure diferentes daquele sugeridas pela SAP CAL, você precisa comprar uma assinatura da CAL da SAP. Você também precisará abrir uma mensagem com a SAP para ter sua conta da CAL habilitada para entregar em regiões do Azure diferentes daquelas inicialmente sugeridas.

### <a name="deploy-a-solution"></a>Implantar uma solução

Permite implantar uma solução a partir da página **Soluções** da SAP CAL. A SAP CAL tem duas sequências para implantação:

- Uma sequência básica que usa uma página para definir o sistema a ser implantado
- Uma sequência avançada que fornece certas opções sobre tamanhos de VM 

Demonstramos o caminho básico para implantação aqui.

1. Na página **Detalhes da conta**, você precisa:

    a. Selecionar uma conta da SAP CAL. (Use uma conta que está associada à implantação com o modelo de implantação do Resource Manager.)

    b. Inserir um **Nome** para a instância.

    c. Selecionar uma **Região** do Azure. A SAP CAL sugere uma região. Se precisar de outra região do Azure e não tiver uma assinatura da SAP CAL, você precisa solicitar uma assinatura da CAL com a SAP.

    d. Inserir uma **Senha** mestre de oito ou nove caracteres para a solução. A senha é usada para os administradores dos diferentes componentes.

   ![Modo Básico SAP CAL: Criar Instância](./media/cal-s4h/s4h-pic10a.png)

1. Clique em **Criar** e na caixa de mensagem que for exibida, clique em **OK**.

   ![Tamanhos de VM com suporte na SAP CAL](./media/cal-s4h/s4h-pic10b.png)

1. Na caixa de diálogo **Chave privada**, clique em **Armazenar** para armazenar a chave privada na SAP CAL. Para usar a proteção de senha para a chave privada, clique em **Baixar**. 

   ![Chave privada da SAP CAL](./media/cal-s4h/s4h-pic10c.png)

1. Leia a Mensagem de **aviso** da SAP CAL e, em seguida, clique em **OK**.

   ![Aviso da SAP CAL](./media/cal-s4h/s4h-pic10d.png)

    Agora a implantação ocorre. Após algum tempo, dependendo do tamanho e da complexidade da solução (uma estimativa é fornecida pela SAP CAL), o status é mostrado como ativo e pronto para uso.

1. Para localizar as máquinas virtuais coletadas com os outros recursos associados em um grupo de recursos, vá para o portal do Azure: 

   ![Objetos da SAP CAL implantados no novo portal](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. No portal da SAP CAL, o status é exibido como **Ativo**. Para conectar-se à solução, clique em **Conectar**. Opções diferentes para se conectar a diferentes componentes são implantadas dentro dessa solução.

   ![Instâncias da SAP CAL](./media/cal-s4h/active_solution.png)

1. Antes de você poder usar uma das opções para se conectar aos sistemas implantados, clique em **Guia de introdução**. 

   ![Conecte-se à instância](./media/cal-s4h/connect_to_solution.png)

    A documentação nomeia os usuários para cada um dos métodos de conectividade. As senhas para esses usuários são definidas como a senha mestra que você definiu no início do processo de implantação. Na documentação, outros usuários mais funcionais são listados com suas senhas, o que você pode usar para entrar no sistema implantado. 

    Por exemplo, se você usar a GUI da SAP que foi pré-instalada no computador com área de trabalho remota do Windows, o sistema S/4 pode estar assim:

   ![SM50 na GUI da SAP pré-instalada](./media/cal-s4h/gui_sm50.png)

    Ou, se você usar o DBACockpit, a instância poderia ser assim:

   ![SM50 na GUI da SAP DBACockpit](./media/cal-s4h/dbacockpit.png)

Em algumas horas, um dispositivo de SAP S/4 íntegro é implantado no Azure.

Se você comprou uma assinatura da CAL SAP, a SAP dará total suporte a implantações por meio da CAL SAP no Azure. A fila de suporte é BC-VCM-CAL.







