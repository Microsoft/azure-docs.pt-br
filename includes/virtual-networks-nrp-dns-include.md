## <a name="azure-dns"></a>DNS do Azure
O DNS do Azure é um serviço de hospedagem para domínios DNS, fornecendo resolução de nomes usando a infraestrutura do Microsoft Azure.

| Propriedade | Descrição | Valor de exemplo |
| --- | --- | --- |
| **DNSzones** |Informações de zona de domínio para hospedar registros DNS de um domínio específico |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com" |

### <a name="dns-record-sets"></a>Conjuntos de registros DNS
As zonas DNS têm um objeto filho chamado conjunto de registros. Conjuntos de registros são uma coleção de registros de host por tipo para uma zona DNS. Os tipos de gravação são A, AAAA, CNAME, MX, NS, SOA, SRV e TXT.

| Propriedade | Descrição | Valor de exemplo |
| --- | --- | --- |
| Uma |Tipo de registro do IPv4 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/A/www |
| AAAA |Tipo de registro do IPv6 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/AAAA/hostrecord |
| CNAME |tipo de registro de nome canônico <sup>1</sup> |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/CNAME/www |
| MX |tipo de registro de email |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/MX/mail |
| NS |tipo de registro do servidor de nome |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/NS/ |
| SOA |Início do tipo de registro da Autoridade <sup>2</sup> |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SOA |
| SRV |tipo de registro do serviço |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SRV |

<sup>1</sup> permite apenas um valor por conjunto de registros.

<sup>2</sup> permite apenas um SOA de tipo de registro por zona DNS. 

Exemplo de zona DNS no formato JSON:

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "newZoneName": {
          "type": "String",
          "metadata": {
              "description": "The name of the DNS zone to be created."
          }
        },
        "newRecordName": {
          "type": "String",
          "defaultValue": "www",
          "metadata": {
              "description": "The name of the DNS record to be created.  The name is relative to the zone, not the FQDN."
          }
        }
      },
      "resources": 
      [
        {
          "type": "microsoft.network/dnszones",
          "name": "[parameters('newZoneName')]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": {
          }
        },
        {
          "type": "microsoft.network/dnszones/a",
          "name": "[concat(parameters('newZoneName'), concat('/', parameters('newRecordName')))]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": 
          {
            "TTL": 3600,
            "ARecords": 
            [
                {
                    "ipv4Address": "1.2.3.4"
                },
                {
                    "ipv4Address": "1.2.3.5"
                }
            ]
          },
          "dependsOn": [
            "[concat('Microsoft.Network/dnszones/', parameters('newZoneName'))]"
          ]
        }
          ]
    }

## <a name="additional-resources"></a>Recursos adicionais
Leia a [documentação da API REST para zonas DNS ](https://msdn.microsoft.com/library/azure/mt130626.aspx) para saber mais.

Leia a [documentação da API REST para conjuntos de registros DNS](https://msdn.microsoft.com/library/azure/mt130627.aspx) para saber mais.

