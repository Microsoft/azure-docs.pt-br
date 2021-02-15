---
title: Adicionar marcas a um laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como criar marcas personalizadas em Azure DevTest Labs e usar marcas para categorizar os recursos. Você pode ver todos os recursos em sua assinatura que têm uma marca.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a4d2b328626f0d0f096082fa88bb170894ccf3e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88270726"
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

Cada marca é composta de um par **nome**- / **valor** . Por exemplo, você pode criar uma marca com o nome *costcenter* que tenha um valor de *34543*. Uma marca como essa pode ajudá-lo mais tarde a identificar os recursos de laboratório que são faturáveis para essa área específica da sua organização. Você pode escolher os nomes e valores que façam sentido para a maneira como você deseja organizar sua assinatura.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Etapas para gerenciar marcas em um laboratório existente

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **Todos os Serviços** e selecione **DevTest Labs** na lista. Seu laboratório já pode ter sido mostrado no painel em **todos os recursos**.
1. Na lista de laboratórios, selecione o laboratório no qual você deseja adicionar ou gerenciar marcas.
1. Na área **Visão geral** do laboratório, selecione **Configuração e políticas**.

    ![Botão Configuração e políticas](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. À esquerda, em **GERENCIAR**, selecione **Marcas**.
1. Para criar uma nova marca para este laboratório, insira um par **nome** / **valor** e selecione **salvar**. Você também pode selecionar uma marca existente na lista a fim de exibir ou gerenciar os recursos associados a essa marca.

    ![Gerenciar marcas](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> Marcas criadas no nível de laboratório fluem por todos os recursos faturáveis que o laboratório gira em sua assinatura. Por exemplo, as marcas de nível de laboratório fluem para as VMs de computação subjacentes de VMs de laboratório. você pode usar marcas no contexto do gerenciamento de custos. As marcas de nível de laboratório aparecem no filtro de marca para o gerenciamento de custos.

## <a name="understanding-limitations-to-tags"></a>Entendendo as limitações das marcas

As seguintes limitações se aplicam a marcas:

* Cada recurso ou grupo de recursos pode ter um máximo de 15 pares de nome/valor de marca. Essa limitação se aplica somente a marcas aplicadas diretamente ao grupo de recursos ou recurso. Um grupo de recursos pode conter muitos recursos que possuem 15 pares de nome/valor de marca.
* O nome da marca é limitado a 512 caracteres e o valor da marca é limitado a 256 caracteres. Para contas de armazenamento, o nome da marca é limitado a 128 caracteres e o valor da marca é limitado a 256 caracteres.
* Marcas aplicadas ao grupo de recursos não são herdadas pelos recursos desse grupo de recursos.

[Usar marcas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md) fornece mais detalhes sobre o uso de marcas no Azure, incluindo como gerenciar marcas usando o PowerShell ou a CLI do Azure.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
* É possível aplicar restrições e convenções em sua assinatura usando políticas personalizadas. Uma política que você definir pode exigir que todos os recursos tenham uma valor para uma marcação específica. Para obter mais informações, consulte [Definir políticas e agendamentos](devtest-lab-set-lab-policy.md).
* Explore a [Galeria de modelos do DevTest Labs Azure Resource Manager guia de início rápido](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
