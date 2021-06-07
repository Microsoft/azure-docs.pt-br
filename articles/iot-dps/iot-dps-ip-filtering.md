---
title: Filtros de conexão de IP do DPS em IoT do Azure | Microsoft Docs
description: Como usar a filtragem de IP para bloquear conexões de endereços IP específicos com sua instância do DPS em IoT do Azure. Você pode bloquear conexões de endereços IP individuais ou de intervalos de endereços IP.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: e1b175a176255da465433b2db45cb3cb67d360d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934492"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Usar filtros de conexão de IP do DPS em IoT do Azure

A segurança é um aspecto importante de qualquer solução de IoT. Às vezes você precisa especificar explicitamente os endereços IP dos quais o dispositivo pode se conectar como parte da sua configuração de segurança. O recurso *Filtro de IP* de um DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT do Azure permite que você configure regras para rejeitar ou aceitar o tráfego de endereços IPv4 específicos.

## <a name="when-to-use"></a>Quando usar

Há dois casos de uso específicos em que é útil bloquear as conexões com um ponto de extremidade de DPS de determinados endereços IP:

* Seu DPS deve receber tráfego somente de um intervalo especificado de endereços IP e rejeitar todos os outros. Por exemplo, você está usando o DPS com o [Azure ExpressRoute](../expressroute/expressroute-faqs.md#supported-services) para criar conexões privadas entre um DPS e seus dispositivos.

* Você precisa rejeitar o tráfego dos endereços IP que foram identificados como suspeitos pelo administrador do DPS.

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro de IP são aplicadas no nível da instância do DPS. Portanto, as regras de filtro IP se aplicam a todas as conexões de dispositivos e aplicativos de back-end que usam qualquer protocolo com suporte.

Todas as tentativas de conexão de um endereço IP que correspondem a uma regra de rejeição de IP na instância do DPS recebem um código de status 401 não autorizado e uma descrição. A mensagem de resposta não menciona a regra IP.

## <a name="default-setting"></a>Configuração padrão

Por padrão, a grade **Filtro de IP** no portal fica vazia para o DPS. Essa configuração padrão significa que o DPS aceita conexões de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

![Configurações de filtro de IP padrão do DPS em IoT](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Adicionar ou editar uma regra de filtro IP

Para adicionar uma regra de filtro de IP, selecione **+ Adicionar regra de filtro de IP**.

![Adicionar uma regra de filtro de IP a um DPS em IoT](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Após selecionar **Adicionar regra de filtro de IP**, preencha os campos.

![Após selecionar Adicionar uma regra de filtro de IP](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Forneça um **nome** para a regra de filtro de IP. O nome deve ser uma cadeia exclusiva de caracteres alfanuméricos, que não diferencia maiúsculas de minúsculas, de até 128 caracteres. Somente são aceitos caracteres alfanuméricos ASCII de 7 bits mais `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Forneça um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, uma notação CIDR 192.168.100.0/22 representa os 1024 endereços IPv4 de 192.168.100.0 a 192.168.103.255.

* Selecione **Permitir** ou **Bloquear** como a **ação** para a regra de filtro de IP.

Depois de preencher os campos, selecione **Salvar** para salvar a regra. Você verá um alerta informando que a atualização está em andamento.

![Notificação sobre como salvar uma regra de filtro IP](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

A opção **Adicionar** é desabilitada quando você atinge o máximo de 100 regras de filtro IP.

Para editar uma regra existente, selecione os dados que você deseja alterar, faça a alteração e, em seguida, selecione **Salvar** para salvar a alteração.

> [!NOTE]
> A rejeição de endereços IP pode impedir que outros Serviços do Azure interajam com a instância do DPS.

## <a name="delete-an-ip-filter-rule"></a>Excluir uma regra de filtro IP

Para excluir uma regra de filtro de IP, selecione o ícone de lixeira na respectiva linha e, em seguida, selecione **Salvar**. Isso removerá a regra e salvará a alteração.

![Excluir uma regra de filtro de IP do DPS em IoT](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Atualizar as regras de filtro de IP no código

Você pode recuperar e modificar o filtro de IP do DPS usando o ponto de extremidade REST do provedor de recursos do Azure. Veja `properties.ipFilterRules` no [método createorupdate](/rest/api/iot-dps/iotdpsresource/createorupdate).

No momento, não há suporte para a atualização das regras de filtro de IP do DPS na CLI do Azure nem no Azure PowerShell, mas ela pode ser realizada com os modelos do Azure Resource Manager. Confira [Modelos do Azure Resource Manager](../azure-resource-manager/templates/overview.md) para obter as diretrizes de como usar os modelos do Resource Manager. Os exemplos de modelo a seguir mostram como criar, editar e excluir regras de filtro de IP do DPS.

### <a name="add-an-ip-filter-rule"></a>Adicionar uma regra de filtro de IP

O exemplo de modelo a seguir cria uma regra de filtro de IP chamada "AllowAll" que aceita todo o tráfego.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Atualize os atributos da regra de filtro de IP do modelo com base nos seus requisitos.

| Atributo                | Descrição |
| ------------------------ | ----------- |
| **FilterName**           | Forneça um nome para a regra de filtro de IP. O nome deve ser uma cadeia exclusiva de caracteres alfanuméricos, que não diferencia maiúsculas de minúsculas, de até 128 caracteres. Somente os caracteres alfanuméricos ASCII de 7 bits mais  `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`   são aceitos. |
| **Ação**               | Os valores aceitos são **Accept** ou  **Reject** como a ação para a regra de filtro de IP. |
| **ipMask**               | Forneça um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, uma notação CIDR 192.168.100.0/22 representa os 1024 endereços IPv4 de 192.168.100.0 a 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Atualizar uma regra de filtro de IP

O exemplo de modelo a seguir atualiza a regra de filtro de IP chamada "AllowAll", já mostrada, para rejeitar todo o tráfego.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Excluir uma regra de filtro IP

O exemplo de modelo a seguir exclui todas as regras de filtro de IP da instância do DPS.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Por exemplo, se você quiser aceitar endereços no intervalo 192.168.100.0/22 e rejeitar todo o resto, a primeira regra na grade deverá aceitar o intervalo de endereços 192.168.100.0/22. A próxima regra deve rejeitar todos os endereços usando o intervalo 0.0.0.0/0.

Você pode alterar a ordem de suas regras de filtro IP na grade clicando nos três pontos verticais no início de uma linha e usando arrastar e soltar.

Para salvar a nova ordem das regras de filtro IP, clique em **Salvar**.

![Alterar a ordem das regras de filtro de IP do DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Próximas etapas

Para explorar melhor o DPS de gerenciamento, confira:

* [Noções básicas sobre os endereços IP do DPS em IoT](iot-dps-understand-ip-address.md)
* [Configurar o DPS usando o CLI do Azure](quick-setup-auto-provision-cli.md)
* [Controlar o acesso ao DPS](how-to-control-access.md)
