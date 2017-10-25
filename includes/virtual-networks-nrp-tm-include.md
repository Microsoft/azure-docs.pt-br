## <a name="traffic-manager-profile"></a>Perfil de Gerenciador de Tráfego
O gerenciador de tráfego e seu recurso de ponto de extremidade filho habilitam o roteamento de DNS para pontos de extremidade no Azure e fora do Azure. Essa distribuição de tráfego é controlada por métodos de política de roteamento. O Gerenciador de Tráfego também permite que a integridade do ponto de extremidade seja monitorada e que o tráfego seja desviado adequadamente, com base na integridade de um ponto de extremidade. 

| Propriedade | Descrição |
| --- | --- |
| **trafficRoutingMethod** |os valores possíveis são *Desempenho*, *Ponderado* e *Prioridade* |
| **dnsConfig** |FQDN para o perfil |
| **Protocolo** |protocolo de monitoramento, os valores possíveis são *HTTP* e *HTTPS* |
| **Porta** |porta de monitoramento |
| **Caminho** |caminho de monitoramento |
| **Pontos de extremidade** |contêiner para recursos de ponto de extremidade |

### <a name="endpoint"></a>Ponto de extremidade
Um ponto de extremidade é um recurso de filho de um perfil de Gerenciador de Tráfego. Representa um serviço ou ponto de extremidade Web para o qual o tráfego de usuário é distribuído com base na política configurada no recurso de Perfil de Gerenciador de Tráfego. 

| Propriedade | Descrição |
| --- | --- |
| **Tipo** |o tipo do ponto de extremidade, os valores possíveis são *Ponto de Extremidade do Azure*, *Ponto de Extremidade Externo* e *Ponto de Extremidade Aninhado* |
| **targetResourceId** |endereço IP público de um ponto de extremidade de serviço ou da Web. Isso pode ser um ponto de extremidade do Azure ou externo. |
| **Peso** |peso de ponto de extremidade usado no gerenciamento de tráfego. |
| **Prioridade** |prioridade do ponto de extremidade, usada para definir uma ação de failover |

Exemplo do Gerenciador de Tráfego no formato Json: 

        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }


## <a name="additional-resources"></a>Recursos adicionais
Leia a [documentação da API REST para o Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/mt163664.aspx) para saber mais.

