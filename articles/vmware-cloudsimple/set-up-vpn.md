---
title: Solução do Azure VMware por CloudSimple-configure a VPN entre a nuvem local e privada
description: Descreve como configurar uma conexão VPN site a site ou ponto a site entre sua rede local e sua nuvem privada do CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b7a119b538ee07b961011bfa98d748f92cc76f0e
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899211"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>Configurar uma conexão VPN para sua nuvem privada do CloudSimple

Os gateways de VPN permitem que você se conecte à rede CloudSimple a partir de sua rede local e de um computador cliente remotamente.  Neste artigo, você pode encontrar informações sobre como configurar gateways de VPN no portal do CloudSimple.  Uma conexão VPN entre sua rede local e sua rede CloudSimple fornece acesso ao vCenter e às cargas de trabalho em sua nuvem privada. O CloudSimple dá suporte a VPN ponto a site e gateways de VPN site a site.

## <a name="vpn-gateway-types"></a>Tipos de gateway de VPN

* A conexão **VPN ponto a site** é a maneira mais simples de se conectar à sua nuvem privada do seu computador. Use a conectividade VPN ponto a site para conectar-se à nuvem privada remotamente.
* A conexão **VPN site a site** permite que você configure suas cargas de trabalho de nuvem privada para acessar os serviços locais. Você também pode usar Active Directory locais como uma fonte de identidade para autenticação em seu vCenter de nuvem privada.  Atualmente, há suporte para o tipo **de VPN baseado em políticas** .

Em uma região, você pode criar um gateway de VPN site a site e um gateway de VPN ponto a site.

## <a name="point-to-site-vpn"></a>VPN ponto a site

Para criar um gateway de VPN ponto a site, consulte [criar gateway de VPN ponto a site](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Conectar-se ao CloudSimple usando a VPN ponto a site

O cliente VPN é necessário para se conectar ao CloudSimple do seu computador.  Baixe o [openvpn client](https://openvpn.net/community-downloads/) para Windows ou [viscosity](https://www.sparklabs.com/viscosity/download/) para MacOS e os X.

1. Inicie o portal do CloudSimple e selecione **rede**.
2. Selecione **Gateway de VPN**.
3. Na lista de gateways de VPN, clique no gateway de VPN ponto a site.
4. Selecione **Usuários**.
5. Clique em **baixar minha configuração de VPN**

    ![Baixar configuração de VPN](media/download-p2s-vpn-configuration.png)

6. Importar a configuração em seu cliente VPN

    * Instruções para [importar a configuração no cliente Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruções para [importar a configuração no MacOS ou no os X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Conecte-se ao gateway de VPN CloudSimple.

O exemplo abaixo mostra a importação de conexão usando o **cliente viscosity**.

#### <a name="import-connection-on-viscosity-client"></a>Importar conexão no cliente viscosity

1. Extraia o conteúdo da configuração de VPN do arquivo. zip baixado.

2. Abra o viscosity no seu computador.

3. Clique no **+** ícone e selecione **importar conexão**  >  **do arquivo**.

    ![Importar configuração de VPN do arquivo](media/import-p2s-vpn-config.png)

4. Selecione o arquivo de configuração OpenVPN (. ovpn) para o protocolo que você deseja usar e clique em **abrir**.

    ![Captura de tela que realça os arquivos de configuração do OpenVPN que você pode selecionar.](media/import-p2s-vpn-config-choose-ovpn.png)

A conexão agora aparece no menu viscosity.

#### <a name="connect-to-the-vpn"></a>Conectar-se ao VPN

Para se conectar à VPN usando o cliente viscosity OpenVPN, selecione a conexão no menu. O ícone de menu é atualizado para indicar que a conexão foi estabelecida.

![Captura de tela que mostra o status de conectividade de VPN CloudSimple.](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Conectando-se a várias nuvens privadas

Uma conexão VPN ponto a site resolve os nomes DNS da primeira nuvem privada que você criar. Quando você quiser acessar outras nuvens privadas, deverá atualizar o servidor DNS em seu cliente VPN.

1. Inicie o [portal do CloudSimple](access-cloudsimple-portal.md).

2. Navegue até **recursos**  >  **nuvens privadas** e selecione a nuvem privada à qual você deseja se conectar.

3. Na página **Resumo** da nuvem privada, copie o endereço IP do servidor DNS da nuvem privada em **informações básicas**.

    ![Servidores DNS da nuvem privada](media/private-cloud-dns-server.png)

4. Clique com o botão direito do mouse no ícone de viscosity na bandeja do sistema do computador e selecione **preferências**.

    ![VPN](media/vis00.png)

5. Selecione a conexão VPN CloudSimple.

    ![Conexão VPN](media/viscosity-client.png)

6. Clique em **Editar** para alterar as propriedades de conexão.

    ![Editar conexão VPN](media/viscosity-edit-connection.png)

7. Clique na guia **rede** e insira os endereços IP do servidor DNS da nuvem privada separados por uma vírgula ou um espaço e o domínio como ```cloudsimple.io``` .  Selecione **ignorar configurações de DNS enviadas pelo servidor VPN**.

    ![Rede VPN](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Para se conectar à sua primeira nuvem privada, remova essas configurações e conecte-se ao servidor VPN.

## <a name="site-to-site-vpn"></a>VPN de site a site

Para criar um gateway de VPN site a site, consulte [criar gateway de VPN site a site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).  A conexão VPN site a site da sua rede local para sua nuvem privada fornece esses benefícios.  

* Acessibilidade de sua nuvem privada vCenter de qualquer estação de trabalho em sua rede local
* Uso do seu Active Directory local como uma fonte de identidade do vCenter
* Transferência conveniente de modelos de VM, ISOs e outros arquivos de seus recursos locais para seu vCenter de nuvem privada
* Acessibilidade de cargas de trabalho em execução em sua nuvem privada da sua rede local

Para configurar o gateway de VPN local no modo de alta disponibilidade, consulte [Configurar uma conexão VPN de alta disponibilidade](high-availability-vpn-connection.md).

> [!IMPORTANT]
>    1. Defina TCP MSS fixação MSS às 1200 em seu dispositivo VPN. Ou, se os dispositivos VPN não oferecerem suporte a MSS fixação MSS, você poderá definir, como alternativa, o MTU na interface de túnel como 1240 bytes.
> 2. Depois que a VPN site a site for configurada, encaminhe as solicitações DNS para *. cloudsimple.io para os servidores DNS da nuvem privada.  Siga as instruções em [configuração de DNS local](on-premises-dns-setup.md).
