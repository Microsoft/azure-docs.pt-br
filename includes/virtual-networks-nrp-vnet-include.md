## <a name="virtual-network"></a>Rede Virtual
Os recursos de VNETs (Redes Virtuais) e de sub-redes ajudam a definir um limite de segurança para cargas de trabalho em execução no Azure. Uma VNet é caracterizada por uma coleção de espaços de endereços, definidos como blocos CIDR. 

> [!NOTE]
> Os administradores de rede estão familiarizados com a notação CIDR. Se não estiver familiarizado com o CIDR, [saiba mais sobre ele](http://whatismyipaddress.com/cidr).
> 
> 

![VNet com várias sub-redes](./media/resource-groups-networking/Figure4.png)

As VNets contêm as propriedades a seguir.

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **addressSpace** |Coleção de prefixos de endereços que compõem a notação CIDR da VNet |192.168.0.0/16 |
| **sub-redes** |Coleção de sub-redes que compõem a VNet |veja [sub-redes](#Subnets) abaixo. |
| **ipAddress** |Endereço IP atribuído a um objeto. Essa é uma propriedade somente leitura. |104.42.233.77 |

### <a name="subnets"></a>Sub-redes
Uma sub-rede é um recurso filho de uma VNet e ajuda a definir segmentos de espaços de endereços em um bloco CIDR, usando prefixos de endereços IP. As NICs podem ser adicionadas a sub-redes e conectadas a VMs, fornecendo conectividade para diversas cargas de trabalho.

As sub-redes contêm as propriedades a seguir. 

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **addressPrefix** |Prefixo de endereço único que compõe a sub-rede na notação CIDR |192.168.1.0/24 |
| **networkSecurityGroup** |NSG aplicado à sub-rede |veja [NSGs](#Network-Security-Group) |
| **routeTable** |Tabela de rotas aplicada à sub-rede |veja [UDR](#Route-table) |
| **ipConfigurations** |Coleção de objetos de configuração IP usada pelas NICs conectadas à sub-rede |veja [UDR](#Route-table) |

Exemplo de VNet no formato JSON:

    {
        "name": "TestVNet",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/virtualNetworks",
        "location": "westus",
        "tags": {
            "displayName": "VNet"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "addressSpace": {
                "addressPrefixes": [
                    "192.168.0.0/16"
                ]
            },
            "subnets": [
                {
                    "name": "FrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "networkSecurityGroup": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "routeTable": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                        },
                        "ipConfigurations": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                            },
                            ...]
                    }
                },
                ...]
        }
    }

### <a name="additional-resources"></a>Recursos adicionais
* Obtenha mais informações sobre a [VNet](../articles/virtual-network/virtual-networks-overview.md).
* Leia a [documentação de referência da API REST](https://msdn.microsoft.com/library/azure/mt163650.aspx) para obter VNets.
* Leia a [documentação de referência da API REST](https://msdn.microsoft.com/library/azure/mt163618.aspx) para obter Sub-redes.

