---
title: Implantar SAP IDES EHP7 SP3 para SAP ERP 6.0 no Azure | Microsoft Docs
description: Implantar SAP IDES EHP7 SP3 para SAP ERP 6.0 no Azure
services: virtual-machines-windows
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 1b2b3d46d0352f72b1ffb513a96c1ab5dc25ad54
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707491"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Implantar SAP IDES EHP7 SP3 para SAP ERP 6.0 no Azure
Este artigo descreve como implantar o sistema SAP IDES em execução com o SQL Server e o sistema operacional Windows no Azure via SAP Cloud Appliance Library (SAP CAL) 3.0. As capturas de tela mostram o processo passo a passo. Para implantar uma solução diferente, siga as mesmas etapas.

Para iniciar com a SAP CAL, consulte o site [SAP Cloud Appliance Library](https://cal.sap.com/). A SAP também tem um blog sobre a nova [SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
> A partir de 29 de maio de 2017, você pode usar o modelo de implantação do Azure Resource Manager além do modelo de implantação clássico menos preferencial para implantar a SAP CAL. É recomendável que você use o novo modelo de implantação do Resource Manager e ignore o modelo de implantação clássico.

Se você já criou uma conta da SAP CAL que usa o modelo clássico, *é necessário criar outra conta da SAP CAL*. Essa conta precisa implantar exclusivamente no Azure usando o modelo do Gerenciador de recursos.

Depois de entrar na SAP CAL, a primeira página geralmente leva você para a página **Soluções**. As soluções oferecidas no SAP CAL continuamente estão aumentando, portanto, talvez seja necessário rolar um pouco para encontrar a solução que você deseja. A solução SAP IDES com base em Windows destacada está disponível exclusivamente no Azure e demonstra o processo de implantação:

![Soluções de SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Criar uma conta na SAP CAL
1. Para entrar na SAP CAL pela primeira vez, use o seu SAP S-User ou outro usuário registrado com a SAP. Em seguida, defina uma conta da SAP CAL que é usada pela SAP CAL para implantar dispositivos no Azure. Na definição de conta, você precisa:

    a. Selecionar o modelo de implantação no Azure (Resource Manager ou clássico).

    b. Inserir sua assinatura do Azure. Uma conta da SAP CAL pode ser atribuída a apenas uma assinatura. Se você precisar de mais de uma assinatura, precisará criar uma outra conta da SAP CAL.
    
    c. Conceder a permissão da SAP CAL para implantar em sua assinatura do Azure.

   > [!NOTE]
   >  As próximas etapas mostram como criar uma conta da SAP CAL para implantações do Resource Manager. Se você já tiver uma conta da SAP CAL que está vinculada ao modelo de implantação clássico, é *necessário* seguir estas etapas para criar uma nova conta da SAP CAL. A nova conta da SAP CAL precisa ser implantada no modelo do Resource Manager.

1. Para criar uma nova conta da SAP CAL, a página **Contas** mostra duas opções para o Azure: 

    a. **Microsoft Azure (clássico)** é o modelo de implantação clássico e não é mais preferencial.

    b. **Microsoft Azure** é o novo modelo de implantação do Resource Manager.

    ![Contas da SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Para implantar no modelo do Resource Manager, selecione **Microsoft Azure**.

    ![Contas da SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Insira a **ID da assinatura** do Azure que pode ser encontrada no portal do Azure. 

    ![ID da assinatura da SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Para autorizar a SAP CAL para implantar na assinatura do Azure que você definiu, clique em **Autorizar**. A seguinte página será exibida na guia do navegador:

    ![Entrar nos serviços de nuvem do Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Se mais de um usuário estiver listado, escolha a conta da Microsoft que está vinculada para ser o coadministrador da assinatura do Azure que você selecionou. A seguinte página será exibida na guia do navegador:

    ![Confirmação dos serviços de nuvem do Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Clique em **Aceitar**. Se a autorização for bem-sucedido, a definição de conta da SAP CAL será exibida novamente. Após um curto período de tempo, uma mensagem confirmará que o processo de autorização foi bem-sucedido.

1. Para atribuir a conta da CAL SAP recém-criada ao seu usuário, insira sua **ID de usuário** na caixa de texto à direita e clique em **Adicionar**. 

    ![Conta a ser associada ao usuário](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Para associar sua conta com o usuário que você usa para entrar na SAP CAL, clique em **Análise**. 

1. Para criar a associação entre o usuário e a conta da CAL SAP recém-criada, clique em **Criar**.

    ![Usuário para a associação de conta](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Você criou com êxito uma conta da SAP CAL que é capaz de:

- Usar o Modelo de implantação do Resource Manager.
- Implantar os sistemas SAP na sua assinatura do Azure.

> [!NOTE]
> Antes de implantar a solução SAP IDES com base no Windows e no SQL Server, você precisará inscrever-se para uma assinatura da SAP CAL. Caso contrário, a solução pode mostrar como **Bloqueado** na página da visão geral.

### <a name="deploy-a-solution"></a>Implantar uma solução
1. Depois de configurar uma conta da SAP CAL, selecione a solução **A solução SAP IDES no Windows e no SQL Server**. Clique em **Criar instância** e confirme as condições de uso e termos. 

1. No **Modo Básico: Criar Instância**, é necessário:

    a. Inserir um **Nome** para a instância.

    b. Selecionar uma **Região** do Azure. Talvez seja necessário uma assinatura da SAP CAL para obter várias regiões do Azure oferecidas.

    c.  Inserir a **Senha** mestre para a solução, conforme mostrado:

    ![Modo Básico SAP CAL: Criar Instância](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Clique em **Criar**. Após algum tempo, dependendo do tamanho e da complexidade da solução (uma estimativa é fornecida pela SAP CAL), o status é mostrado como ativo e pronto para uso: 

    ![Instâncias da SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Para localizar o grupo de recursos e todos os seus objetos que foram criados pela SAP CAL, acesse o portal do Azure. As máquinas virtuais podem ser encontradas começando com o mesmo nome de instância fornecido na SAP CAL.

    ![Objetos do grupo de recursos](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. No portal da SAP CAL, vá para as instâncias implantadas e clique em **Conectar**. A seguinte janela pop-up será exibida: 

    ![Conecte-se à instância](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Antes de você poder usar uma das opções para se conectar aos sistemas implantados, clique em **Guia de introdução**. A documentação nomeia os usuários para cada um dos métodos de conectividade. As senhas para esses usuários são definidas como a senha mestra que você definiu no início do processo de implantação. Na documentação, outros usuários mais funcionais são listados com suas senhas, o que você pode usar para entrar no sistema implantado.

    ![Documentação de boas-vindas da SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Em algumas horas, um sistema de SAP IDES íntegro é implantado no Azure.

Se você comprou uma assinatura da CAL SAP, a SAP dará total suporte a implantações por meio da CAL SAP no Azure. A fila de suporte é BC-VCM-CAL.

