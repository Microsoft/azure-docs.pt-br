---
title: 'Gateway de VPN do Azure: Sobre perfis de cliente VPN P2S'
description: Use este artigo para encontrar as informações necessárias para um perfil de cliente VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: 9bb66363d525648df08f32451842402ad1d0d93b
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979056"
---
# <a name="working-with-p2s-vpn-client-profile-files"></a>Trabalhando com arquivos de perfil de cliente VPN P2S

Os arquivos de perfil contêm informações necessárias para configurar uma conexão VPN. Este artigo ajudará você a entender as informações necessárias para um perfil de cliente VPN.

## <a name="generate-and-download-profile"></a>Gerar e baixar o perfil

É possível gerar arquivos de configuração do cliente usando o PowerShell ou usando o Portal do Azure. O método retorna o mesmo arquivo zip.

### <a name="portal"></a>Portal

1. No Portal do Azure, navegue até o gateway de rede virtual para a rede virtual à qual você deseja se conectar.
1. Na página gateway de rede virtual, selecione **configuração ponto a site**.
1. Na parte superior da página configuração ponto a site, selecione **baixar cliente VPN**. Levará alguns minutos para o pacote de configuração do cliente ser gerado.
1. Seu navegador indica que um arquivo zip de configuração do cliente está disponível. Ele terá o mesmo nome do seu gateway. Descompacte o arquivo para exibir as pastas.

### <a name="powershell"></a>PowerShell

Para gerar usando o PowerShell, você pode usar o seguinte exemplo:

1. Ao gerar arquivos de configuração de cliente VPN, o valor de '-AuthenticationMethod' é 'EapTls'. Gere os arquivos de configuração de cliente de VPN usando o seguinte comando:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Copie a URL para o seu navegador para baixar o arquivo zip. Em seguida, descompacte o arquivo para exibir as pastas.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A pasta **OpenVPN** contém o perfil *ovpn* que precisa ser modificado para incluir a chave e o certificado. Para saber mais, confira [Configurar os clientes OpenVPN para o gateway de VPN do Azure](vpn-gateway-howto-openvpn-clients.md#windows). Se a autenticação do Azure AD estiver selecionada no gateway de VPN, essa pasta não estará presente no arquivo zip. Em vez disso, navegue até a pasta AzureVPN e localize o arquivo azurevpnconfig.xml.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre conexões ponto a site, confira [Sobre ponto a site](point-to-site-about.md).
