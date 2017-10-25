Ao criar um gateway de rede virtual, você precisa especificar o SKU do gateway que você deseja usar. Selecione as SKUs que atendem às suas necessidades com base nos tipos de SLAs, taxas de transferência, recursos e cargas de trabalho.

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

###  <a name="workloads"></a>Produção *versus* Cargas de Trabalho de Desenvolvimento e Teste

Devido a diferenças nos SLAs e conjuntos de recursos, é recomendável usar as SKUs a seguir para produção *versus* desenvolvimento e teste:

| **Carga de trabalho**                       | **SKUs**               |
| ---                                | ---                    |
| **Produção, cargas de trabalho críticas** | VpnGw1, VpnGw2, VpnGw3 |
| **Teste de desenvolvimento ou prova de conceito**   | Basic                  |
|                                    |                        |

Se você estiver usando as SKUs antigas, as recomendações de SKU de produção serão Standard e Alto Desempenho. Para saber mais sobre os SKUs antigos, confira [SKUs de Gateway (SKUs herdados)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).

###  <a name="feature"></a>Conjuntos de recursos do SKU de gateway

As novas SKUs do gateway simplificam os conjuntos de recursos oferecidos nos gateways:

| **SKU**| **Recursos**|
| ---    | ---         |
|**Básico**   | **VPN baseado em rota**: 10 túneis com P2S; nenhuma autenticação RADIUS; nenhum IKEv2<br>**VPN baseada em políticas:** (IKEv1): 1 túnel; sem P2S|
| **VpnGw1, VpnGw2 e VpnGw3** | **VPN baseada em rota**: até 30 túneis (*), P2S, BGP, ativo-ativo, política IPsec/IKE personalizada, coexistência ExpressRoute/VPN |
|        |             |

(*) Você pode configurar "PolicyBasedTrafficSelectors" para se conectar a um gateway de VPN baseado em rota (VpnGw1, VpnGw2, VpnGw3) para vários dispositivos de firewall baseados em políticas. Confira [Conectar gateways de VPN a vários dispositivos VPN baseados em políticas usando o PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter detalhes.

###  <a name="resize"></a>Redimensionamento de SKUs de gateway

1. Você pode redimensionar entre as SKUs VpnGw1, VpnGw2 e VpnGw3.
2. Ao trabalhar com SKUs antigas, você ainda pode redimensionar entre SKUs Básicas, Standard e de Alto Desempenho.
2. Você **não pode** redimensionar as SKUs Basic/Standard/Alto Desempenho para as novas SKUs VpnGw1/VpnGw2/VpnGw3. Em vez disso, você deve [migrar](#migrate) para as novas SKUs.

###  <a name="migrate"></a>Migrando de SKUs antigas para novas SKUs

[!INCLUDE [Migrate SKU](./vpn-gateway-migrate-legacy-sku-include.md)]
