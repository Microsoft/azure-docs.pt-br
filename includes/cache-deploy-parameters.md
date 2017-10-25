
### <a name="cacheskuname"></a>cacheSKUName
A camada de preços do novo Cache Redis do Azure.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Redis Cache."
      }
    },

O modelo define os valores que são permitidos para esse parâmetro (Basic ou Standard) e atribui um valor padrão (Basic) se nenhum valor for especificado. Basic fornece um único nó com vários tamanhos disponíveis, até 53 GB.
Standard fornece Principal/Réplica de dois nós com vários tamanhos disponíveis, até 53 GB e SLA de 99,9%.

### <a name="cacheskufamily"></a>cacheSKUFamily
A família do SKU.

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
O tamanho da nova instância do Cache Redis do Azure. 

    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Redis Cache instance. "
      }
    }


O modelo define os valores permitidos para esse parâmetro (0, 1, 2, 3, 4, 5 ou 6) e atribui um valor padrão (1) se nenhum valor for especificado. Esses números correspondem aos seguintes tamanhos de cache: 0 = 250 MB, 1 = 1 GB, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

