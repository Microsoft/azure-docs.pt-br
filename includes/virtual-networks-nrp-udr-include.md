## <a name="route-tables"></a>Tabelas de rotas
Os recursos de tabela de rotas contêm as rotas usadas para definir o fluxo de tráfego em sua infraestrutura do Azure. Você pode usar UDRs (rotas definidas pelo usuário) para enviar todo o tráfego de uma determinada sub-rede para um dispositivo virtual, como um firewall ou IDS (sistema de detecção de intrusões). É possível associar uma tabela de rotas a sub-redes. 

As tabelas de rotas contêm as propriedades a seguir.

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **routes** |Coleção de rotas definidas pelo usuário na tabela de rotas |veja [rotas definidas pelo usuário](#User-defined-routes) |
| **sub-redes** |Coleção de sub-redes às quais a tabela de rotas é aplicada |veja [sub-redes](#Subnets) |

### <a name="user-defined-routes"></a>rotas definidas pelo usuário
Você pode criar UDRs para especificar para onde o tráfego deve ser enviado, com base em seu endereço de destino. Pense em uma rota como a definição de gateway padrão baseado no endereço de destino de um pacote de rede.

As UDRs contêm as propriedades a seguir. 

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **addressPrefix** |Prefixo de endereço ou endereço IP completo para o destino |192.168.1.0/24, 192.168.1.101 |
| **nextHopType** |Tipo de dispositivo para o qual o tráfego será enviado |Dispositivo Virtual, Gateway de VPN, Internet |
| **nextHopIpAddress** |Endereço IP do próximo salto |192.168.1.4 |

Exemplo de tabela de rotas no formato JSON:

    {
        "name": "UDR-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/routeTables",
        "location": "westus",
        "properties": {
            "provisioningState": "Succeeded",
            "routes": [
                {
                    "name": "RouteToFrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd/routes/RouteToFrontEnd",
                    "etag": "W/\"v\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "nextHopType": "VirtualAppliance",
                        "nextHopIpAddress": "192.168.0.4"
                    }
                }
            ],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd"
                }
            ]
        }
    }

### <a name="additional-resources"></a>Recursos adicionais
* Obtenha mais informações sobre [UDRs](../articles/virtual-network/virtual-networks-udr-overview.md).
* Leia a [documentação de referência da API REST](https://msdn.microsoft.com/library/azure/mt502549.aspx) para obter tabelas de rotas.
* Leitura de [documentação de referência da API REST](https://msdn.microsoft.com/library/azure/mt502539.aspx) para obter UDRs (rotas definidas pelo usuário).

