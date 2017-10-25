O Azure oferece os seguintes SKUs de gateway de VPN:

|**SKU**   | **S2S/VNet para VNet<br>Túneis** | **Conexões<br>P2S** | **Parâmetro de comparação<br>de taxa de transferência total** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Máx. 30                         | Máx. 128               | 650 Mbps                    |
|**VpnGw2**| Máx. 30                         | Máx. 128               | 1 Gbps                      |
|**VpnGw3**| Máx. 30                         | Máx. 128               | 1,25 Gbps                   |
|**Básico** | Máx. 10                         | Máx. 128               | 100 Mbps                    | 
|          |                                 |                        |                             | 

- O parâmetro de comparação da taxa de transferência total se baseia nas medidas de vários túneis agregados por meio de um único gateway. Não é uma taxa de transferência garantida devido às condições de tráfego de Internet e seus comportamentos de aplicativo.

- Encontre informações sobre preços na página [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway) .

- As informações de SLA (contrato de nível de serviço) podem ser encontradas na página [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).