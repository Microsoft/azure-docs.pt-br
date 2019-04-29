---
title: Dimensionar limites e cotas em seu laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como dimensionar um laboratório no Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: ec79f6a9b255d44e66b901a0aae263c8dbbf2863
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623437"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Dimensionar cotas e limites no DevTest Labs
Ao trabalhar em DevTest Labs, você observará que existem certos limites padrão a alguns recursos do Azure que podem afetar o serviço DevTest Labs. Esses limites são conhecidos como **cotas**.

> [!NOTE]
> O serviço DevTest Labs não impõe cotas. Qualquer cota que você encontre são restrições padrão da assinatura geral do Azure.

Você pode usar cada recurso do Azure até atingir sua cota. Cada assinatura tem cotas separadas e seu uso é controlado por assinatura.

Por exemplo, cada assinatura tem uma cota padrão de 20 núcleos. Portanto, se você estiver criando VMs em seu laboratório com quatro núcleos cada, você poderá criar apenas cinco VMs.

O item [Assinatura e Limites de Serviço do Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits) lista algumas das mais comuns cotas para recursos do Azure. Os recursos mais comumente usados em um laboratório e para os quais você pode encontrar cotas incluem núcleos de VM, endereços IP públicos, interface de rede, managed disks, atribuição de função RBAC e circuitos ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Exibir seu uso e cotas
Estas etapas mostram como visualizar as cotas atuais em sua assinatura para recursos específicos do Azure e ver o percentual de cada cota usada.

1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Mais Serviços** e, em seguida, selecione **Cobrança** na lista.
1. Selecione uma assinatura na folha de Cobrança.
4. Selecione **Uso + cotas**.

   ![Botão Uso e cotas](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   A folha Uso + cotas aparece, listando diferentes recursos disponíveis na assinatura e o percentual da cota que está sendo usada por recurso.

   ![Cotas e uso](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Solicitar mais recursos na sua assinatura
Se você atingir um limite de cota, o limite padrão de um recurso em uma assinatura poderá ser aumentado até o limite máximo, conforme descrito em [Assinatura e limites de serviço do Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits).

Estas etapas mostram como solicitar um aumento de cota por meio do [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Mais Serviços**, selecione **Cobrança** e, em seguida, selecione **Uso + cotas**.
1. Na folha Uso + cotas, selecione o botão **Solicitar aumento**.

   ![Botão Solicitar aumento](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Para concluir e enviar a solicitação, preencha as informações necessárias em todas as três guias do formulário **Nova solicitação de suporte**.

   ![Formulário de solicitação de aumento](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Entendendo os limites e aumentos do Azure](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) apresenta mais informações sobre como contatar o suporte do Azure para solicitar um aumento de cota.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Próximas etapas
* Explore a [galeria de modelos de Início Rápido do Azure Resource Manager do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
