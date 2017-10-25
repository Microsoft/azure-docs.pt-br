# Visão geral
## [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md)
## [Perguntas frequentes de gateway de VPN](vpn-gateway-vpn-faq.md)
## [Limites de serviço e assinatura](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# Introdução
## [Planejamento e design do Gateway de VPN](vpn-gateway-plan-design.md)
## [Sobre as configurações do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md)
## [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md)
## [Sobre os requisitos de criptografia](vpn-gateway-about-compliance-crypto.md)
## [Sobre BGP e gateway de VPN](vpn-gateway-bgp-overview.md)
## [Sobre a conectividade altamente disponível](vpn-gateway-highlyavailable.md)
## [Sobre conexões Ponto a Site](point-to-site-about.md)

# Como
## Configurar conexões site a site
### [Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [PowerShell do Azure](vpn-gateway-create-site-to-site-rm-powershell.md)
### [CLI do Azure](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [Portal do Azure (clássico)](vpn-gateway-howto-site-to-site-classic-portal.md)

## Configurar conexões ponto a site - autenticação de certificado do Azure nativo
### Configurar um VPN P2S
#### [Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [PowerShell do Azure](vpn-gateway-howto-point-to-site-rm-ps.md)
#### [Portal do Azure (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### Gerar certificados autoassinados
#### [PowerShell do Azure](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [Criar e instalar arquivos de configuração de cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md)
### [Instalar certificados do cliente](point-to-site-how-to-vpn-client-install-azure-cert.md)

## Configurar conexões Ponto a Site - autenticação RADIUS
### Configurar um VPN P2S
#### [PowerShell do Azure](point-to-site-how-to-radius-ps.md)
### [Criar e instalar arquivos de configuração de cliente VPN](point-to-site-vpn-client-configuration-radius.md)

## Configurar conexões de Rede Virtual a Rede Virtual
### [Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [PowerShell do Azure](vpn-gateway-vnet-vnet-rm-ps.md)
### [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
### [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## Configurar uma conexão de rede virtual a rede virtual entre os modelo de implantação
### [Portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
### [PowerShell do Azure](vpn-gateway-connect-different-deployment-models-powershell.md)
## Configurar conexões coexistentes Site a Site e do ExpressRoute
### [PowerShell do Azure](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## Configurar múltiplas conexões Site a Site
### [Portal do Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [Azure PowerShell (clássico)](vpn-gateway-multi-site.md)
## Conectar vários dispositivos VPN baseados em políticas
### [PowerShell do Azure](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## Configurar políticas de IPsec/IKE nas conexões
### [PowerShell do Azure](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## Configurar conexões de ativo-ativo altamente disponíveis
### [PowerShell do Azure](vpn-gateway-activeactive-rm-powershell.md)
## Configurar o BGP para um gateway de VPN
### [PowerShell do Azure](vpn-gateway-bgp-resource-manager-ps.md)
### [CLI do Azure](bgp-how-to-cli.md)
## Configurar o túnel forçado
### [PowerShell do Azure](vpn-gateway-forced-tunneling-rm.md)
### [Azure PowerShell (clássico)](vpn-gateway-about-forced-tunneling.md)
## Modificar configurações de gateway de rede locais
### [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
### [PowerShell do Azure](vpn-gateway-modify-local-network-gateway.md)
### [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
## [Verificar uma conexão de gateway de VPN](vpn-gateway-verify-connection-resource-manager.md)
## [Redefinir um gateway de VPN](vpn-gateway-resetgw-classic.md)
## Excluir um gateway de VPN
### [Portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
### [PowerShell do Azure](vpn-gateway-delete-vnet-gateway-powershell.md)
### [Azure PowerShell (clássico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [Configurar um gateway de VPN (clássico)](vpn-gateway-configure-vpn-gateway-mp.md)
## [SKUs de gateway (herdado)](vpn-gateway-about-skus-legacy.md)
## Configurar dispositivos VPN de terceiros
### [Configuração de visão geral e do Azure](vpn-gateway-3rdparty-device-config-overview.md)
### [Exemplo: dispositivo Cisco ASA (IKEv2/no BGP)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## Solucionar problemas
### [Validar a taxa de transferência VPN em uma VNet](vpn-gateway-validate-throughput-to-vnet.md)
### [VPN sugerida pela comunidade ou configurações do dispositivo de firewall](vpn-gateway-third-party-settings.md)
### [Problema de conexão Ponto a Site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
### [A conexão Site a Site desconecta-se intermitentemente](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)
### [Não é possível conectar a conexão Site a Site](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md) 
### [Configurar e validar as conexões de VNet ou VPN](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)

# Referência
## [PowerShell do Azure](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell (clássico)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST (clássico)](https://msdn.microsoft.com/library/jj154113)
## [CLI do Azure](/cli/azure/network/vnet-gateway)

# Relacionados
## [Rede Virtual](/azure/virtual-network/)
## [Application Gateway](/azure/application-gateway/)
## [DNS do Azure](/azure/dns/)
## [Gerenciador de Tráfego](/azure/traffic-manager/)
## [Balanceador de Carga](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# Recursos
## [Roteiro do Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Blog](https://azure.microsoft.com/blog/topics/networking)
## [Fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [Calculadora de preço](https://azure.microsoft.com/pricing/calculator/)
## [SLA](https://azure.microsoft.com/support/legal/sla)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
