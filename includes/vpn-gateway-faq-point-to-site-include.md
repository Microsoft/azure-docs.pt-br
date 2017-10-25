### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>Quais sistemas operacionais de cliente posso usar com ponto a site?

Há suporte para os seguintes sistemas operacionais de cliente:

* Windows 7 (32 bits e 64 bits)
* Windows Server 2008 R2 (somente 64 bits)
* Windows 8 (32 bits e 64 bits)
* Windows 8.1 (32 bits e 64 bits)
* Windows Server 2012 (somente 64 bits)
* Windows Server 2012 R2 (somente 64 bits)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>Posso usar qualquer cliente de VPN de software para ponto a site que dê suporte a SSTP?

Não. O suporte é limitado somente às versões do sistema operacional Windows listadas acima.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Quantos pontos de extremidade de cliente VPN posso ter em minha configuração ponto a site?

Damos suporte para até 128 clientes VPN para poderem se conectar a uma rede virtual ao mesmo tempo.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Posso usar minha própria CA de raiz de PKI interna para a conectividade ponto a site?

Sim. Anteriormente, somente os certificados raiz autoassinados podiam ser usados. Você ainda pode carregar 20 certificados raiz.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Posso atravessar proxies e firewalls usando o recurso de ponto a site?

Sim. Usamos o protocolo SSTP (Secure Socket Tunneling Protocol) para túnel através de firewalls. Esse túnel aparecerá como uma conexão HTTPs.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se eu reiniciar um computador cliente configurado para ponto a site, a VPN se reconectará automaticamente?

Por padrão, o computador cliente não restabelecerá a conexão VPN automaticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>A opção de ponto para site dá suporte a reconexão automática e DDNS nos clientes de VPN?

A reconexão automática e o DDNS atualmente não têm suporte em VPNs ponto a site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Posso ter configurações site a site e ponto a site que coexistam para a mesma rede virtual?

Sim. Essas duas soluções funcionarão se você tiver um tipo VPN Baseada em Rota para o gateway. Para o modelo de implantação clássica, você precisará de um gateway dinâmico. Não damos suporte a ponto a site para o roteamento estático de gateways VPN ou gateways usando o cmdlet `-VpnType PolicyBased`.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Posso configurar um cliente de ponto a site para se conectar a várias redes virtuais ao mesmo tempo?

Sim, isso é possível. Porém, as redes virtuais não podem ter prefixos IP sobrepostos, e os espaços de endereço de ponto para site não devem causar sobreposição entre as redes virtuais.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Quanta taxa de transferência posso esperar por meio de conexões site a site ou ponto a site?

É difícil manter a taxa de transferência exata dos túneis de VPN. IPsec e SSTP são protocolos VPN de criptografia pesada. A taxa de transferência também é limitada pela latência e pela largura de banda entre seus locais e na Internet.