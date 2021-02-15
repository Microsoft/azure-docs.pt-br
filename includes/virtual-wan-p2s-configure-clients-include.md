---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9caf63fc90be7bae0461ddc24c94594a32199765
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812636"
---
#### <a name="microsoft-windows"></a>Microsoft Windows

##### <a name="openvpn"></a>OpenVPN

1. Faça o download e instale o cliente OpenVPN do site oficial.
1. Faça o download do perfil VPN para o gateway. Isso pode ser feito pela guia Configurações de VPN do usuário no Portal do Azure ou em New-AzureRmVpnClientConfiguration no PowerShell.
1. Descompacte o perfil. Abra o arquivo de configuração vpnconfig.ovpn na pasta OpenVPN no bloco de notas.
1. Preencha a seção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Em um certificado formatado em PEM, abra o arquivo .cer e copie a chave base64 entre os cabeçalhos de certificado. Para ver as etapas, confira [Como exportar um certificado para obter a chave pública codificada.](../articles/virtual-wan/certificates-point-to-site.md)
1. Preencha a seção de chave privada com a chave privada do certificado de cliente P2S em base64. Para ver as etapas, confira [Como extrair a chave privada](../articles/virtual-wan/howto-openvpn-clients.md#windows).
1. Não altere os outros campos. Use a configuração preenchida da entrada do cliente para se conectar à VPN.
1. Copie o arquivo vpnconfig.ovpn para a pasta C:\Arquivos de Programas\OpenVPN\config.
1. Clique com o botão direito do mouse no ícone OpenVPN na bandeja do sistema e selecione **conectar**.

##### <a name="ikev2"></a>IKEv2

1. Selecione os arquivos de configuração de cliente VPN que correspondem à arquitetura do computador com Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador 'VpnClientSetupAmd64'. Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador 'VpnClientSetupX86'.
1. Clique duas vezes no pacote para instalá-lo. Se vir um pop-up do SmartScreen, selecione **Mais informações** e **Executar mesmo assim**.
1. No computador cliente, navegue até **Configurações de Rede** e selecione **VPN**. A conexão VPN mostra o nome da rede virtual a que ele se conecta.
1. Antes de tentar se conectar, verifique se você instalou um certificado do cliente no computador cliente. Um certificado do cliente é necessário para autenticação ao usar o tipo de autenticação de certificado do Azure nativo. Para obter mais informações sobre como gerar certificados, consulte [Gerar Certificados](../articles/virtual-wan/certificates-point-to-site.md). Para obter mais informações sobre como instalar um certificado do cliente, consulte [Instalar um certificado do cliente](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).
