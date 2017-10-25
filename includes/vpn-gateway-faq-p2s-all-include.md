### <a name="supportedclientos"></a>Quais sistemas operacionais de cliente posso usar com ponto a site?

Há suporte para os seguintes sistemas operacionais de cliente:

* Windows 7 (32 bits e 64 bits)
* Windows Server 2008 R2 (somente 64 bits)
* Windows 8 (32 bits e 64 bits)
* Windows 8.1 (32 bits e 64 bits)
* Windows Server 2012 (somente 64 bits)
* Windows Server 2012 R2 (somente 64 bits)
* Windows 10
* OSX versão 10.11 para Mac (El Capitan)
* macOS versão 10.12 para Mac (Serra)

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Quantos pontos de extremidade de cliente VPN posso ter em minha configuração ponto a site?

Damos suporte para até 128 clientes VPN para poderem se conectar a uma rede virtual ao mesmo tempo.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Posso atravessar proxies e firewalls usando o recurso de ponto a site?

O Azure oferece suporte a dois tipos de opções de VPN Ponto a Site:

* SSTP (Secure Socket Tunneling Protocol). SSTP é uma solução baseada em SSL da Microsoft que pode invadir firewalls, já que a maioria dos firewalls abre a porta TCP, que usa SSL 443.

* VPN IKEv2. A VPN IKEv2 é uma solução de VPN IPsec baseada em padrões que usa as portas UDP 500 e 4500 e o protocolo IP número 50. Nem sempre os firewalls abrem essas portas, por isso, há uma possibilidade de a VPN IKEv2 não conseguir atravessar proxies e firewalls.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se eu reiniciar um computador cliente configurado para ponto a site, a VPN se reconectará automaticamente?

Por padrão, o computador cliente não restabelecerá a conexão VPN automaticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>A opção de ponto para site dá suporte a reconexão automática e DDNS nos clientes de VPN?

A reconexão automática e o DDNS atualmente não têm suporte em VPNs ponto a site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Posso ter configurações site a site e ponto a site que coexistam para a mesma rede virtual?

Sim. Para o modelo de implantação do Gerenciador de Recursos, você deve ter um tipo de VPN RouteBased para o gateway. Para o modelo de implantação clássica, você precisará de um gateway dinâmico. Não damos suporte a Ponto a Site para o roteamento estático de gateways de VPN ou gateways de VPN PolicyBased.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Posso configurar um cliente de ponto a site para se conectar a várias redes virtuais ao mesmo tempo?

Não. Um cliente de Ponto a Site só pode se conectar aos recursos na rede virtual na qual o gateway de rede virtual reside.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Quanta taxa de transferência posso esperar por meio de conexões site a site ou ponto a site?

É difícil manter a taxa de transferência exata dos túneis de VPN. IPsec e SSTP são protocolos VPN de criptografia pesada. A taxa de transferência também é limitada pela latência e pela largura de banda entre seus locais e na Internet. Para um Gateway de VPN apenas com conexões de VPN de Ponto a Site IKEv2, a taxa de transferência total que você pode esperar depende do SKU do Gateway. Para saber mais sobre taxa de transferência, confira [SKUs de gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Posso usar qualquer cliente de VPN de software para Ponto a Site que dê suporte a SSTP e/ou IKEv2?

Não. Você só pode usar o cliente VPN nativo no Windows para SSTP, e o cliente VPN nativo no Mac para IKEv2. Consulte a lista dos sistemas operacionais de cliente com suporte.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>O Azure oferece suporte à VPN IKEv2 com o Windows?

Os usuários podem se conectar ao Azure utilizando o cliente VPN do Windows incorporado, o qual fornece suporta o IKEv2. Porém, as conexões IKEv2 de um dispositivo Windows não funcionarão no seguinte cenário:

  Quando o dispositivo do usuário contiver um grande número de certificados raiz confiáveis, o tamanho do conteúdo da mensagem durante a troca de IKE será grande e causará a fragmentação da camada IP. Os fragmentos serão rejeitados no end do Azure, o que resultará na falha na conexão. É difícil estimar a contagem exata de certificados em que esse problema ocorre. Como resultado, não será garantido que as conexões IKEv2 de dispositivos Windows funcionem. Ao configurar tanto o SSTP como o IKEv2 em um ambiente misto (composto por dispositivos Windows e Mac), o perfil da VPN do Windows sempre tentará primeiro o túnel IKEv2. Se falhar devido ao problema aqui descrito, retornará para o SSTP.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>Além do Windows e Mac, quais outras plataformas possuem suporte Azure para VPN P2S?

O Azure fornece suporta apenas para VPN P2S do Windows e Mac.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enabled-radius-andor-ikev2-vpn-on-it"></a>Eu já tenho um Gateway de VPN do Azure implantado. É possível habilitar o RADIUS e/ou IKEv2 VPN nele?

Sim, você pode habilitar esses novos recursos em gateways já implantados, tanto no Powershell quanto no Portal do Azure.
