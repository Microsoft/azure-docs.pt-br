---
title: Baixar scripts de configuração do dispositivo VPN para conexões VPN S2S
description: Este artigo mostra como baixar scripts de configuração de dispositivo VPN para conexões VPN S2S com gateways de VPN do Azure usando o Azure Resource Manager.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: d2593c656e17310c5afb4a897b94cc8c12a618f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879487"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Baixar scripts de configuração do dispositivo VPN para conexões VPN S2S

Este artigo mostra como baixar scripts de configuração de dispositivo VPN para conexões VPN S2S com gateways de VPN do Azure usando o Azure Resource Manager. O diagrama a seguir mostra o fluxo de trabalho de alto nível.

![download-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Os seguintes dispositivos têm scripts disponíveis:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>Sobre scripts de configuração do dispositivo VPN

Uma conexão de VPN entre locais consiste em um gateway de VPN do Azure, um dispositivo VPN local e um túnel IPsec S2S VPN conectando os dois. O fluxo de trabalho típico inclui as seguintes etapas:

1. Criar e configurar um gateway de VPN do Azure (gateway de rede virtual)
2. Criar e configurar um gateway de rede local do Azure que representa a sua rede local e o dispositivo de VPN
3. Criar e configurar uma conexão VPN do Azure entre o gateway de VPN do Azure e o gateway de rede local
4. Configurar o dispositivo VPN local representado pelo gateway de rede local para estabelecer o túnel VPN S2S real com o gateway de VPN do Azure

Você pode concluir as etapas 1 a 3 usando o [portal](./tutorial-site-to-site-portal.md), [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md), ou [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md) do Azure. A última etapa envolve a configuração de dispositivos VPN locais fora do Azure. Esse recurso permite que você baixe um script de configuração para seu dispositivo VPN com os valores correspondentes do seu gateway de VPN do Azure, rede virtual e prefixos de endereço de rede local e propriedades de conexão de VPN, etc. já preenchidos. Você pode usar o script como um ponto de partida ou aplicar o script diretamente para os dispositivos VPN local por meio do console de configuração.

> [!IMPORTANT]
> * A sintaxe para cada script de configuração de dispositivo VPN é diferente e depende fortemente dos modelos e das versões de firmware. Preste atenção especial às suas informações de modelo e a versão do dispositivo em relação aos modelos disponíveis.
> * Alguns valores de parâmetro devem ser exclusivos no dispositivo e não podem ser determinados sem acessar o dispositivo. Os scripts de configuração gerados pelo Azure preenchem previamente esses valores, mas você precisa garantir que os valores fornecidos são válidos em seu dispositivo. Por exemplo:
>    * Números de interface
>    * Números de lista de controle de acesso
>    * Nomes de política ou números, etc.
> * Procure a palavra-chave "**SUBSTITUIR**" inserida no script para localizar os parâmetros que você precisa verificar antes de aplicar o script.
> * Alguns modelos incluem uma "**LIMPEZA**" que você pode aplicar para remover as configurações. Por padrão, as seções de limpeza são comentadas.

## <a name="download-the-configuration-script-from-azure-portal"></a>Baixe o script de configuração do portal do Azure

Crie um gateway de VPN do Azure, o gateway de rede local e um recurso de conexão conectar os dois. A seguinte página orientará você durante as etapas:

* [Criar uma conexão site a site no Portal do Azure](./tutorial-site-to-site-portal.md)

Depois de criar o recurso de conexão, siga as instruções abaixo para baixar os scripts de configuração de dispositivo VPN:

1. Em um navegador, navegue até o [portal do Azure](https://portal.azure.com) e, se necessário, entre com sua conta do Azure
2. Vá para o recurso de conexão que você criou. Você pode encontrar a lista de todos os recursos de conexão clicando em "Todos os serviços" e "Rede" e "Conexões".

    ![connection-list](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Clique na conexão que você deseja configurar.

    ![connection-overview](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Clique no link "Configuração de Download" conforme realçado em vermelho na página de visão geral de Conexão; isso abre a página de "Configuração de Download".

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Selecione o família modelo e versão do firmware para o seu dispositivo VPN, clique no botão "Configuração de Download".

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Você precisará salvar o script baixado (um arquivo de texto) de seu navegador.
7. Depois que você baixou o script de configuração, abra-o com um editor de texto e procure a palavra-chave "SUBSTITUIR" para identificar e examinar os parâmetros que precisam ser substituídos.

    ![edit-script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Baixe o script de configuração do usando o Azure PowerShell



Você também pode baixar o script de configuração usando o Azure PowerShell, conforme mostrado no exemplo a seguir:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Aplique o script de configuração no seu dispositivo VPN

Depois que você tiver baixado e validado o script de configuração, a próxima etapa é aplicar o script ao seu dispositivo VPN. O procedimento real varia de acordo com suas formas e modelos de dispositivo VPN. Consulte os manuais de operação ou as páginas de instrução para seus dispositivos VPN.

## <a name="next-steps"></a>Próximas etapas

Continue configurando a [Conexão site a site](./tutorial-site-to-site-portal.md).