## <a name="public-ip-address"></a>Endereço IP público
Um recurso de endereço IP público fornece um endereço IP para Internet dinâmico ou reservado. Embora você possa criar um endereço IP público como um objeto autônomo, você precisa associá-la a outro objeto para usar o endereço em si. Você pode associar um endereço IP público a um balanceador de carga, Application Gateway ou uma NIC para fornecer acesso à Internet a esses recursos.  

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **publicIPAllocationMethod** |Define se o endereço IP é *estático* ou *dinâmico*. |estático, dinâmico |
| **idleTimeoutInMinutes** |Define o tempo limite ocioso, com um valor padrão de 4 minutos. Se nenhum ouro pacote de uma determinada sessão for recebido nesse período, a sessão será encerrada. |qualquer valor entre 4 e 30 |
| **ipAddress** |Endereço IP atribuído a um objeto. Essa é uma propriedade somente leitura. |104.42.233.77 |

### <a name="dns-settings"></a>Configurações DNS
Endereços IP públicos têm um objeto filho chamado **dnsSettings** que contém as seguintes propriedades:

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **domainNameLabel** |Host nomeado usado para resolução de nomes. |www, ftp, vm1 |
| **fqdn** |Nome totalmente qualificado para o IP público. |www.westus.cloudapp.azure.com |
| **reverseFqdn** |Nome de domínio totalmente qualificado que resolve o endereço IP e está registrado no DNS como um registro PTR. |www.contoso.com. |

Endereço IP de público exemplo, no formato JSON:

    {
       "name": "PIP01",
       "location": "North US",
       "tags": { "key": "value" },
       "properties": {
          "publicIPAllocationMethod": "Static",
          "idleTimeoutInMinutes": 4,
          "ipAddress": "104.42.233.77",
          "dnsSettings": {
             "domainNameLabel": "mylabel",
             "fqdn": "mylabel.westus.cloudapp.azure.com",
             "reverseFqdn": "contoso.com."
          }
       }
    } 

### <a name="additional-resources"></a>Recursos adicionais
* Obtenha mais informações sobre [endereços IP públicos](../articles/virtual-network/virtual-networks-reserved-public-ip.md).
* Saiba mais sobre [endereços IP públicos em nível de instância](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).
* Leia a [documentação de referência da API REST](https://msdn.microsoft.com/library/azure/mt163638.aspx) para endereços IP públicos.

