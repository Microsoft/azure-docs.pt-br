---
title: Habilitar o autogerenci para máquinas virtuais por meio do Azure Policy
description: Saiba como habilitar o autogerenciamento do Azure para VMs por meio de um Azure Policy interno no portal do Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8f679626b69bd855e86b94cdde51955edd068e8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714900"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Habilitar o autogerenci para máquinas virtuais por meio do Azure Policy

Se você quiser habilitar o autogerenci para várias VMs, poderá fazer isso usando um [Azure Policy](..\governance\azure-management.md)interno. Este artigo guiará você pela localização da política certa e como atribuí-la para habilitar o autogerenciamento no portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar.

> [!NOTE]
> As contas de avaliação gratuita não têm acesso às máquinas virtuais usadas neste tutorial. Atualize para uma assinatura de Pagamento Conforme o Uso.

> [!IMPORTANT]
> A permissão do RBAC do Azure a seguir é necessária para habilitar autogerenci: função de **proprietário** ou **colaborador** junto com as funções de **administrador de acesso do usuário** .


## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com/).


## <a name="locate-and-assign-the-policy"></a>Localizar e atribuir a política

1. Navegue até a **política** no portal do Azure
1. Ir para o painel **definições**
1. Clique na lista suspensa **categorias** para ver as opções disponíveis
1. Selecione a opção **habilitar autogerenciar – práticas recomendadas da máquina virtual do Azure**
1. Agora a lista será atualizada para mostrar uma política interna com um nome que começa com *habilitar autogerenciar...*
1. Clique em *habilitar autogerenciar-nome da política de práticas recomendadas da máquina virtual do Azure*
1. Depois de clicar na política, agora você pode ver a guia **definição**

    > [!NOTE]
    > A definição de Azure Policy é usada para definir parâmetros de gerenciamento automática, como o perfil de configuração ou a conta. Ele também define filtros que garantem que a política se aplica apenas às VMs corretas.

1. Clique no botão **atribuir** para criar uma atribuição
1. Na guia **noções básicas** , preencha o **escopo** definindo a *assinatura* e o *grupo de recursos*

    > [!NOTE]
    > O escopo permite que você defina a quais VMs essa política se aplica. Você pode definir o aplicativo no nível da assinatura ou no nível do grupo de recursos. Se você definir um grupo de recursos, todas as VMs que estão atualmente nesse grupo de recursos ou em qualquer VM futura que adicionamos a ela terão o autogerenci habilitado automaticamente. 

1. Clique na guia **parâmetros** e defina a **conta de autogerenciamento** e o **perfil de configuração** desejado 
1. Na guia **revisar + criar** , examine as configurações
1. Aplicar a atribuição clicando em **criar**
1. Exiba suas atribuições na guia **atribuições** ao lado de **definição**

> [!NOTE]
> Levará algum tempo para que essa política comece a entrar em vigor nas VMs atualmente no grupo de recursos ou na assinatura.


## <a name="next-steps"></a>Próximas etapas 

Aprenda outra maneira de habilitar o autogerenciamento do Azure para máquinas virtuais por meio do portal do Azure. 

> [!div class="nextstepaction"]
> [Habilitar o autogerenci para máquinas virtuais no portal do Azure](quick-create-virtual-machines-portal.md)