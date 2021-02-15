---
title: Dimensionar limites e cotas em seu laboratório no Azure DevTest Labs | Microsoft Docs
description: Este artigo descreve como você pode dimensionar seu laboratório no Azure DevTest Labs. Exiba suas cotas e limites de uso e solicite um aumento.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2166eaab073e2abc24ca24494ae13eb876db1fcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533947"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Dimensionar cotas e limites no DevTest Labs
Ao trabalhar em DevTest Labs, você observará que existem certos limites padrão a alguns recursos do Azure que podem afetar o serviço DevTest Labs. Esses limites são conhecidos como **cotas**.

> [!NOTE]
> O serviço DevTest Labs não impõe cotas. Qualquer cota que você encontre são restrições padrão da assinatura geral do Azure.

Você pode usar cada recurso do Azure até atingir sua cota. Cada assinatura tem cotas separadas e seu uso é controlado por assinatura.

Por exemplo, cada assinatura tem uma cota padrão de 20 núcleos. Portanto, se você estiver criando VMs em seu laboratório com quatro núcleos cada, você poderá criar apenas cinco VMs.

O item [Assinatura e Limites de Serviço do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) lista algumas das mais comuns cotas para recursos do Azure. Os recursos mais comumente usados em um laboratório e para os quais você pode encontrar cotas, incluem núcleos de VM, endereços IP públicos, interface de rede, Managed disks, atribuição de função do Azure e circuitos de ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Exibir seu uso e cotas
Estas etapas mostram como visualizar as cotas atuais em sua assinatura para recursos específicos do Azure e ver o percentual de cada cota usada.

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Mais Serviços** e, em seguida, selecione **Cobrança** na lista.
1. Selecione uma assinatura na folha de Cobrança.
4. Selecione **uso + cotas**.

   ![Botão Uso e cotas](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas-new.png)

   A folha Uso + cotas aparece, listando diferentes recursos disponíveis na assinatura e o percentual da cota que está sendo usada por recurso.

   ![Cotas e uso](./media/devtest-lab-scale-lab/devtestlab-view-quotas-new.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Solicitar mais recursos na sua assinatura
Se você atingir um limite de cota, o limite padrão de um recurso em uma assinatura poderá ser aumentado até o limite máximo, conforme descrito em [Assinatura e limites de serviço do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

Estas etapas mostram como solicitar um aumento de cota por meio do [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Mais Serviços**, selecione **Cobrança** e, em seguida, selecione **Uso + cotas**.
1. Na folha Uso + cotas, selecione o botão **Solicitar aumento**.

   ![Botão Solicitar aumento](./media/devtest-lab-scale-lab/devtestlab-request-increase-new.png)

1. Para concluir e enviar a solicitação, preencha as informações necessárias em todas as três guias do formulário **Nova solicitação de suporte**.

   ![Formulário de solicitação de aumento](./media/devtest-lab-scale-lab/devtestlab-support-form-new.png)

[Entendendo os limites e aumentos do Azure](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) apresenta mais informações sobre como contatar o suporte do Azure para solicitar um aumento de cota.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Próximas etapas
* Explore a [Galeria de modelos do DevTest Labs Azure Resource Manager guia de início rápido](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
