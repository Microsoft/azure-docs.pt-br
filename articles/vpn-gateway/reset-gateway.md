---
title: Redefinir um gateway de VPN do Azure para restabelecer o túnel IPsec
description: Redefina o gateway de VPN do Azure para restabelecer túneis IPsec para gateways de VPN nos modelos de implantação clássico e do Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: cd25c7638bd7e178cdb963ba528cccefde6b9eca
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646467"
---
# <a name="reset-a-vpn-gateway"></a>Redefinir um Gateway de VPN

Redefinir um gateway de VPN do Azure é útil se você perde a conectividade VPN entre locais em um ou mais túneis de VPN site a site. Nessa situação, os dispositivos VPN locais estão funcionando corretamente, mas não são capazes de estabelecer túneis IPsec com os gateways de VPN do Azure. Este artigo ajuda-o a redefinir o gateway de VPN.

### <a name="what-happens-during-a-reset"></a>O que acontece durante uma redefinição?

Um gateway de VPN é composto de duas instâncias de VM em execução em uma configuração de modo em espera ativo. Quando você redefine o gateway, ele reinicializa o gateway e aplica novamente as configurações entre instalações a ele. O gateway mantém o endereço IP público que já tem. Isso significa que você não precisa atualizar a configuração do roteador VPN com um novo endereço IP público do gateway de VPN do Azure.

Ao emitir o comando para redefinir o gateway, a instância ativa atual do gateway de VPN do Azure é imediatamente reiniciada. Haverá um breve intervalo durante o failover da instância ativa (que está sendo reinicializada) à instância em espera. O intervalo deve ser menor que um minuto.

Se a conexão não é restaurada após a primeira reinicialização, execute o mesmo comando novamente para reiniciar a segunda instância VM (o novo gateway ativo). Se as duas reinicializações são solicitadas uma após a outra, haverá um período um pouco mais longo durante o qual as duas instâncias da VM (ativas e em espera) estão sendo reinicializadas. Isso causará uma lacuna mais longa na conectividade de VPN, até 30 a 45 minutos para que as VMs concluam as reinicializações.

Após duas reinicializações, se você ainda estiver tendo problemas de conectividade entre instalações, abra uma solicitação de suporte no portal do Azure.

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Antes de redefinir o gateway, verifique os principais itens listados abaixo para cada túnel VPN Site a Site (S2S) de IPsec. Qualquer incompatibilidade nos itens resultará na desconexão de túneis VPN S2S. Verificar e corrigir as configurações para seus gateways de VPN do Azure e locais faz com que você evite reinicializações desnecessárias e interrupções para as outras conexões de trabalho nos gateways.

Verifique os itens a seguir antes de redefinir seu gateway:

* Os VIPs (endereços IP da Internet) para o gateway de VPN do Azure e o gateway de VPN local estão configurados corretamente em ambas a políticas de VPN do Azure e de VPN local.
* A chave pré-compartilhada deve ser a mesma em ambos o gateway de VPN do Azure e o gateway de VPN local.
* Se você aplicar a configuração de IPsec/IKE específica, como criptografia, algoritmos de hash e PFS (Perfect Forward Secrecy), verifique se que o gateway de VPN do Azure e o gateway de VPN local têm as mesmas configurações.

## <a name="azure-portal"></a><a name="portal"></a>Portal do Azure

Você pode redefinir um gateway de VPN do Resource Manager usando o Portal do Azure. Se você quiser redefinir um gateway clássico, consulte as etapas do PowerShell para o [modelo de implantação clássico](#resetclassic).

### <a name="resource-manager-deployment-model"></a>Modelo de implantação do Gerenciador de Recursos

[!INCLUDE [portal steps](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="powershell"></a><a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Modelo de implantação do Gerenciador de Recursos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O cmdlet para redefinição de um gateway é **Reset-AzVirtualNetworkGateway**. Antes de executar uma redefinição, verifique se você tem a versão mais recente dos [cmdlets AZ do PowerShell](/powershell/module/az.network). O exemplo a seguir redefine um gateway de rede virtual nomeado VNet1GW no grupo de recursos TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Resultado:

Ao receber um resultado de retorno, você poderá assumir que a redefinição do gateway foi bem-sucedida. No entanto, não há nada no resultado de retorno que indica explicitamente que a redefinição foi bem-sucedida. Caso queira analisar detalhadamente o histórico para visualizar exatamente quando ocorreu a redefinição do gateway, é possível visualizar essas informações no [Portal do Azure](https://portal.azure.com). No portal, navegue até **'GatewayName' -> Resource Health**.

### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Modelo de implantação clássica

O cmdlet para redefinição de um gateway é **Reset-AzureVNetGateway**. Os cmdlets Azure PowerShell para o gerenciamento de serviços devem ser instalados localmente na sua área de trabalho. Você não pode usar Azure Cloud Shell. Antes de realizar uma redefinição, certifique-se de que possui a última versão dos [cmdlets do PowerShell do Gerenciamento de Serviços (SM) ](/powershell/azure/servicemanagement/install-azure-ps#azure-service-management-cmdlets). Ao usar esse comando, verifique se você está usando o nome completo da rede virtual. Os VNets clássicos que foram criados usando o portal têm um nome longo que é necessário para o PowerShell. Você pode exibir o nome longo usando ' Get-AzureVNetConfig-ExportToFile C:\Myfoldername\NetworkConfig.xml '.

O exemplo a seguir redefine o gateway para uma rede virtual chamada "Group TestRG1 TestVNet1" (que mostra simplesmente "TestVNet1" no Portal):

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
```

Resultado:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="azure-cli"></a><a name="cli"></a>Azure CLI

Para redefinir o gateway, utilize o comando [az network vnet-gateway reset](/cli/azure/network/vnet-gateway). O exemplo a seguir redefine um gateway de rede virtual nomeado VNet5GW no grupo de recursos TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Resultado:

Ao receber um resultado de retorno, você poderá assumir que a redefinição do gateway foi bem-sucedida. No entanto, não há nada no resultado de retorno que indica explicitamente que a redefinição foi bem-sucedida. Caso queira analisar detalhadamente o histórico para visualizar exatamente quando ocorreu a redefinição do gateway, é possível visualizar essas informações no [Portal do Azure](https://portal.azure.com). No portal, navegue até **'GatewayName' -> Resource Health**.