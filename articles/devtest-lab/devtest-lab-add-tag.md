---
title: "Adicionar marcas a um laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como adicionar uma marca a um laboratório no Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: tarcher
ms.openlocfilehash: c268c1a10e4a313d0520f96e452ef978b6201115
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Adicionar marcas a um laboratório no Azure DevTest Labs

Você pode criar marcas personalizadas e aplicá-las aos seus recursos do DevTest Labs para categorizá-los de forma lógica. Posteriormente, você pode ver rapidamente e facilmente todos os recursos em sua assinatura que tenham essa marca. As marcas são úteis quando você precisa organizar os recursos para gerenciamento ou cobrança.

Recursos que são compatíveis com marcas incluem

* VMs de Computação
* NICs
* Endereços IP
* Balanceadores de carga
* Contas de armazenamento
* Discos gerenciados

Você pode aplicar marcas ao [Criar um laboratório](devtest-lab-create-lab.md) e, posteriormente, gerenciá-las por meio da folha Marcas em Configurações e definições.

Cada marca é composta de um par **nome**/**valor**. Por exemplo, você pode criar uma marca com o nome *costcenter* que tenha um valor de *34543*. Uma marca como essa pode ajudá-lo mais tarde a identificar os recursos de laboratório que são faturáveis para essa área específica da sua organização. Você pode escolher os nomes e valores que façam sentido para a maneira como você deseja organizar sua assinatura.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Etapas para gerenciar marcas em um laboratório existente

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **Mais Serviços** e, em seguida, selecione **DevTest Labs** na lista. O laboratório já pode ser exibido no painel, em **Todos os Recursos**.
1. Na lista de laboratórios, selecione o laboratório no qual você deseja adicionar ou gerenciar marcas.  
1. Na área **Visão geral** do laboratório, selecione **Configuração e políticas**.  

    ![Botão Configuração e políticas](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. À esquerda, em **GERENCIAR**, selecione **Marcas**.
1. Para criar uma nova marca para este laboratório, insira um par **nome**/**valor** e selecione **Salvar**. Você também pode selecionar uma marca existente na lista a fim de exibir ou gerenciar os recursos associados a essa marca.

    ![Gerenciar marcas](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

## <a name="understanding-limitations-to-tags"></a>Entendendo as limitações das marcas

As seguintes limitações se aplicam a marcas:

* Cada recurso ou grupo de recursos pode ter um máximo de 15 pares de nome/valor de marca. Essa limitação se aplica somente a marcas aplicadas diretamente ao grupo de recursos ou recurso. Um grupo de recursos pode conter muitos recursos que possuem 15 pares de nome/valor de marca. 
* O nome da marca é limitado a 512 caracteres e o valor da marca é limitado a 256 caracteres. Para contas de armazenamento, o nome da marca é limitado a 128 caracteres e o valor da marca é limitado a 256 caracteres.
* Marcas aplicadas ao grupo de recursos não são herdadas pelos recursos desse grupo de recursos.

[Usar marcas para organizar os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) fornece mais detalhes sobre o uso de marcas no Azure, incluindo como gerenciar marcas usando o PowerShell ou a CLI do Azure.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
* É possível aplicar restrições e convenções em sua assinatura usando políticas personalizadas. Uma política que você definir pode exigir que todos os recursos tenham uma valor para uma marcação específica. Para obter mais informações, consulte [Definir políticas e agendamentos](devtest-lab-set-lab-policy.md).
* Explore a [galeria de modelos de Início Rápido do Azure Resource Manager do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
