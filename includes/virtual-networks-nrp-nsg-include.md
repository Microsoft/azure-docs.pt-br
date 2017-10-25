## <a name="network-security-group"></a>Grupo de Segurança de Rede
Um recurso NSG habilita a criação de limite de segurança para cargas de trabalho, por meio de regras de permissão e recusa. Essas regras podem ser aplicadas a uma VM, NIC ou sub-rede.

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **sub-redes** |Lista de IDS de sub-rede às quais o NSG é aplicado. |/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd |
| **securityRules** |Lista de regras de segurança que compõem o NSG |Veja [Regra de segurança](#Security-rule) abaixo |
| **defaultSecurityRules** |Lista de regras de segurança padrão presentes em cada NSG |Veja [Regras de segurança padrão](#Default-security-rules) abaixo |

* **Regra de segurança** - um NSG um pode ter várias regras de segurança definidas. Cada regra pode permitir ou negar diferentes tipos de tráfego.

### <a name="security-rule"></a>Regra de segurança
Uma regra de segurança é um recurso filho de um NSG que contém as propriedades abaixo.

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **description** |Descrição da regra |Permitir tráfego de entrada para todas as VMs na sub-rede X |
| **protocol** |Protocolo para fazer a correspondência da regra |TCP, UDP ou * |
| **sourcePortRange** |Intervalo de portas de origem para fazer a correspondência da regra |80, 100-200, * |
| **destinationPortRange** |Intervalo de portas de destino para fazer a correspondência da regra |80, 100-200, * |
| **sourceAddressPrefix** |Prefixo de endereço de origem para fazer a correspondência da regra |10.10.10.1, 10.10.10.0/24, Rede Virtual |
| **destinationAddressPrefix** |Prefixo de endereço de destino para fazer a correspondência da regra |10.10.10.1, 10.10.10.0/24, Rede Virtual |
| **direction** |Direção do tráfego para fazer a correspondência da regra |entrada ou saída |
| **prioridade** |Prioridade da regra. As regras são verificadas em ordem de prioridade, e depois que uma regra é aplicada, nenhuma outra regra é testada quanto à correspondência. |10, 100, 65000 |
| **access** |Tipo de acesso a ser aplicado se a regra for correspondente |permitir ou negar |

Exemplo de NSG no formato JSON:

    {
        "name": "NSG-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkSecurityGroups",
        "location": "westus",
        "tags": {
            "displayName": "NSG - Front End"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "securityRules": [
                {
                    "name": "rdp-rule",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/rdp-rule",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "description": "Allow RDP",
                        "protocol": "Tcp",
                        "sourcePortRange": "*",
                        "destinationPortRange": "3389",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "access": "Allow",
                        "priority": 100,
                        "direction": "Inbound"
                    }
                }
            ],
            "defaultSecurityRules": [
                { [...],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                }
            ]
        }
    }

### <a name="default-security-rules"></a>Regras de segurança padrão

As regras de segurança padrão têm as mesmas propriedades disponíveis nas regras de segurança. Elas existem para fornecer a conectividade básica entre os recursos com NSGs aplicados. Certifique-se de que você sabe quais [regras de segurança padrão](../articles/virtual-network/virtual-networks-nsg.md#default-rules) existem.

### <a name="additional-resources"></a>Recursos adicionais
* Obtenha mais informações sobre [NSGs](../articles/virtual-network/virtual-networks-nsg.md).
* Leia a [documentação de referência da API REST](https://msdn.microsoft.com/library/azure/mt163615.aspx) para obter NSGs.
* Leia a [documentação de referência da API REST](https://msdn.microsoft.com/library/azure/mt163580.aspx) para obter regras de segurança.
