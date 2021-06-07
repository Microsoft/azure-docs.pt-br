---
title: Configurar o tempo limite de ociosidade e redefinição de TCP do balanceador de carga
titleSuffix: Azure Load Balancer
description: Neste artigo, saiba como configurar Azure Load Balancer tempo limite de ociosidade TCP e redefinir.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2020
ms.author: allensu
ms.openlocfilehash: 8a6be588544883b77c3ff115c9dba5e6ecd5fbd7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92747227"
---
# <a name="configure-tcp-reset-and-idle-timeout-for-azure-load-balancer"></a>Configurar a redefinição de TCP e o tempo limite de ociosidade para Azure Load Balancer

Azure Load Balancer tem o seguinte intervalo de tempo limite ocioso:

* 4 minutos a 100 minutos para regras de saída
* 4 minutos a 30 minutos para regras de Load Balancer e regras NAT de entrada

Por padrão, ele é definido como 4 minutos. Se um período de inatividade for maior do que o valor de tempo limite, não haverá garantia de que a sessão TCP ou HTTP seja mantida entre o cliente e o serviço. 

As seções a seguir descrevem como alterar o tempo limite de ociosidade e as configurações de redefinição de TCP para recursos do balanceador de carga.

## <a name="set-tcp-reset-and-idle-timeout"></a>Definir a redefinição de TCP e o tempo limite de ociosidade
---
# <a name="portal"></a>[**Portal**](#tab/tcp-reset-idle-portal)

Para definir o tempo limite de ociosidade e a redefinição de TCP para um balanceador de carga, edite a regra de balanceamento de carga. 

1. Entre no [portal do Azure](https://portal.azure.com).

2. No menu à esquerda, selecione grupos de **recursos**.

3. Selecione o grupo de recursos para o balanceador de carga. Neste exemplo, o grupo de recursos é denominado **MyResource** Group.

4. Selecione seu balanceador de carga. Neste exemplo, o balanceador de carga é denominado **myLoadBalancer**.

5. Em **configurações**, selecione **regras de balanceamento de carga**.

     :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules.png" alt-text="Edite as regras do balanceador de carga." border="true":::

6. Selecione sua regra de balanceamento de carga. Neste exemplo, a regra de balanceamento de carga é denominada **myLBrule**.

7. Na regra de balanceamento de carga, mova o controle deslizante no **tempo limite de ociosidade (minutos)** para o valor de tempo limite.  

8. Em **redefinição de TCP**, selecione **habilitado**.

   :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules-tcp-reset.png" alt-text="Defina o tempo limite de ociosidade e a redefinição de TCP." border="true":::

9. Clique em **Salvar**.

# <a name="powershell"></a>[**PowerShell**](#tab/tcp-reset-idle-powershell)

Para definir o tempo limite de ociosidade e a redefinição de TCP, defina os valores nos seguintes parâmetros de regra de balanceamento de carga com [set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* **IdleTimeoutInMinutes**
* **EnableTcpReset**

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 5.4.1 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

Substitua os seguintes exemplos pelos valores de seus recursos:

* **myResourceGroup**
* **myLoadBalancer**

```azurepowershell
$lb = Get-AzLoadBalancer -Name "myLoadBalancer" -ResourceGroup "myResourceGroup"
$lb.LoadBalancingRules[0].IdleTimeoutInMinutes = '15'
$lb.LoadBalancingRules[0].EnableTcpReset = 'true'
Set-AzLoadBalancer -LoadBalancer $lb
```

# <a name="azure-cli"></a>[**CLI do Azure**](#tab/tcp-reset-idle-cli)

Para definir o tempo limite de ociosidade e a redefinição de TCP, use os seguintes parâmetros para [atualização de regra AZ Network lb](/cli/azure/network/lb/rule?az_network_lb_rule_update):

* **--tempo limite de ociosidade**
* **--Habilitar-TCP-redefinir**

Valide seu ambiente antes de começar:

* Entre no portal do Azure e verifique se a sua assinatura está ativa executando `az login`.
* Verifique sua versão da CLI do Azure em uma janela Comando ou de terminal executando `az --version`. Para obter a última versão, confira as [notas sobre a versão mais recente](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Caso não tenha a última versão, atualize a instalação seguindo o [guia de instalação para seu sistema operacional ou sua plataforma](/cli/azure/install-azure-cli).

Substitua os seguintes exemplos pelos valores de seus recursos:

* **myResourceGroup**
* **myLoadBalancer**
* **myLBrule**


```azurecli
az network lb rule update \
    --resource-group myResourceGroup \
    --name myLBrule \
    --lb-name myLoadBalancer \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
---
## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o tempo limite de ociosidade e a redefinição de TCP, consulte [Load BALANCER TCP Reset e tempo limite de ociosidade](load-balancer-tcp-reset.md)

Para obter mais informações sobre como configurar o modo de distribuição do balanceador de carga, consulte [configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md).
