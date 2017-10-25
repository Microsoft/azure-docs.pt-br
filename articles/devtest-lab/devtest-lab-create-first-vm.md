---
title: "Criar a primeira VM em um laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como criar a primeira máquina virtual em um laboratório no Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: fbc5a438-6e02-4952-b654-b8fa7322ae5f
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2017
ms.author: tarcher
ms.openlocfilehash: aa6b60b799e1e98815cf288d5612f98cd77cc00e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Criar a primeira VM no Azure DevTest Labs

Ao acessar inicialmente o DevTest Labs e desejar criar a primeira VM, você deverá fazer isso usando uma [imagem do Marketplace base](devtest-lab-configure-marketplace-images.md) pré-carregada. No futuro, você também poderá escolher uma imagem personalizada [ e uma fórmula](devtest-lab-add-vm.md) ao criar mais VMs. 

Este tutorial orienta você pelo Portal do Azure para adicionar uma VM a um laboratório no DevTest Labs.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Etapas para adicionar a primeira VM a um laboratório no Azure DevTest Labs
1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório no qual você deseja criar a VM.  
1. Na folha **Visão geral** do laboratório, selecione **+ Adicionar**.  

    ![Botão Adicionar VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Na folha **Escolher uma base**, selecione uma imagem do Marketplace para a VM.
1. Na folha **Máquina virtual**, insira um nome para a nova máquina virtual na caixa de texto **Nome da máquina virtual**.

    ![Folha VM de Laboratório](./media/devtest-lab-add-vm/devtestlab-lab-add-first-vm.png)

1. Insira um **Nome de Usuário** que receberá privilégios de administrador na máquina virtual.  
1. Digite uma senha no campo de texto identificado como **Digite um valor**.
1. O **tipo de disco de máquina virtual** determina que tipo de disco de armazenamento é permitido para as máquinas virtuais no laboratório.
1. Selecione **Tamanho da máquina virtual** e selecione um dos itens predefinidos que especificam os núcleos de processador, o tamanho da RAM e o tamanho do disco rígido da VM a ser criada.
1. Selecione **Artefatos** e, na lista de artefatos, selecione e configure os artefatos que você deseja adicionar à imagem base.
    **Observação:** se você for iniciante em Laboratórios de Desenvolvimento/Teste ou na configuração de artefatos, veja a seção [Adicionar um artefato existente a uma VM](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) e volte aqui quando terminar.
1. Selecione **Criar** para adicionar a VM especificada ao laboratório.

   A folha do laboratório exibe o status da criação da VM; primeiro como **Criando** e como **Executando** após a inicialização da VM.

## <a name="next-steps"></a>Próximas etapas
* Após a criação da VM, você poderá se conectar à VM selecionando **Conectar** na folha da VM.
* Marque [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md) para informações mais completas sobre como adicionar VMs subsequentes no laboratório.
* Explore a [galeria de modelos de Início Rápido do Azure Resource Manager do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
