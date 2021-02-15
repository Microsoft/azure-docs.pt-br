---
title: Filtros IP do Hub IoT do Azure | Microsoft Docs
description: Como usar a filtragem de IP para permitir conexões de endereços IP específicos para o Hub IoT do Azure.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: jlian
ms.openlocfilehash: c6544e8ac00744602476207a89567aea5afe5b1d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632335"
---
# <a name="use-ip-filters"></a>Usar filtros IP

A segurança é um aspecto importante de qualquer solução de IoT com base no Hub IoT do Azure. Às vezes você precisa especificar explicitamente os endereços IP dos quais o dispositivo pode se conectar como parte da sua configuração de segurança. O recurso *filtro IP* permite que você configure regras para rejeitar ou aceitar tráfego de endereços IPv4 específicos.

## <a name="when-to-use"></a>Quando usar

Use o filtro IP para receber tráfego somente de um intervalo especificado de endereços IP e rejeitar todo o resto. Por exemplo, você está usando o Hub IoT com o [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) para criar conexões privadas entre um hub IOT e sua infraestrutura local.

## <a name="default-setting"></a>Configuração padrão

Para acessar a página Configurações de filtro de IP, selecione **Rede**, **Acesso público**, em seguida escolha **Intervalos de IP selecionados**:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="Configurações de filtro de IP padrão do Hub IoT":::

Por padrão, a grade **Filtro IP** no portal de Hub IoT fica vazia. Essa configuração padrão significa que o Hub bloqueia as conexões de todos os endereços IP. Essa configuração padrão é equivalente a uma regra que bloqueia o `0.0.0.0/0` intervalo de endereços IP.

## <a name="add-or-edit-an-ip-filter-rule"></a>Adicionar ou editar uma regra de filtro IP

Para adicionar uma regra de filtro de IP, selecione **+ Adicionar regra de filtro de IP**.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Adicionar uma regra de filtro de IP a um hub IoT":::

Após selecionar **Adicionar regra de filtro de IP**, preencha os campos.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Após selecionar Adicionar uma regra de filtro de IP":::

* Forneça um **nome** para a regra de filtro de IP. Esse nome deve ser uma cadeia de caracteres alfanumérica, não diferencia maiúsculas de minúsculas, de até 128 caracteres de comprimento. Somente são aceitos caracteres alfanuméricos ASCII de 7 bits mais `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Forneça um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, uma notação CIDR 192.168.100.0/22 representa os 1024 endereços IPv4 de 192.168.100.0 a 192.168.103.255.

Depois de preencher os campos, selecione **Salvar** para salvar a regra. Você verá um alerta informando que a atualização está em andamento.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Notificação de salvamento uma regra de filtro de IP":::

A opção **Adicionar** é desabilitada quando você atinge o máximo de dez regras de filtro IP.

Para editar uma regra existente, selecione os dados que você deseja alterar, faça a alteração e, em seguida, selecione **Salvar** para salvar a alteração.

## <a name="delete-an-ip-filter-rule"></a>Excluir uma regra de filtro IP

Para excluir uma regra de filtro de IP, selecione o ícone de lixeira na respectiva linha e, em seguida, selecione **Salvar**. Isso removerá a regra e salvará a alteração.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Excluir uma regra de filtro de IP do Hub IoT":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>Aplicar regras de filtro IP ao ponto de extremidade compatível com o Hub de eventos interno

Para aplicar as regras de filtro IP ao ponto de extremidade compatível com o Hub de eventos interno, marque a caixa ao lado de **aplicar filtros IP ao ponto de extremidade interno?** e, em seguida, selecione **salvar**.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="Imagem mostrando a alternância para o ponto de extremidade interno e salvar":::

> [!NOTE]
> Essa opção não está disponível para hubs de IoT gratuitos (F1). Para aplicar regras de filtro IP ao ponto de extremidade interno, use um hub IoT pago.

Ao habilitar essa opção, suas regras de filtro IP são replicadas para o ponto de extremidade interno, de modo que somente os intervalos de IP confiáveis podem acessá-la.

Se você desabilitar essa opção, o ponto de extremidade interno poderá ser acessado por todos os endereços IP. Esse comportamento pode ser útil se você quiser ler a partir do ponto de extremidade com serviços com a alteração de endereços IP, como Azure Stream Analytics. 

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro IP são aplicadas no nível do serviço Hub IoT. Portanto, as regras de filtro de IP se aplicam a todas as conexões de dispositivos e aplicativos de back-end que usam qualquer protocolo com suporte. Além disso, você pode escolher se o [ponto de extremidade compatível com o Hub de eventos interno](iot-hub-devguide-messages-read-builtin.md) (não por meio da cadeia de conexão do Hub IOT) está associado a essas regras. 

Qualquer tentativa de conexão de um endereço IP que não tenha permissão explícita recebe um código de status 401 não autorizado e uma descrição. A mensagem de resposta não menciona a regra IP. Rejeitar endereços IP pode impedir que outros serviços do Azure como Stream Analytics do Azure, Máquinas Virtuais do Azure ou o Gerenciador de Dispositivos no portal do Azure interajam com o hub IoT.

> [!NOTE]
> Se você precisar usar Azure Stream Analytics (ASA) para ler mensagens de um hub IoT com o filtro IP habilitado, **desabilite** a opção **aplicar filtros IP ao ponto de extremidade interno** e use o nome compatível com o Hub de eventos e o ponto de extremidade do Hub IOT para adicionar manualmente uma [entrada de fluxo dos hubs de eventos](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) no asa.

### <a name="ordering"></a>Ordenando

As regras de filtro IP são regras de *permissão* e aplicadas sem ordenação. Somente endereços IP que você adiciona têm permissão para se conectar ao Hub IoT. 

Por exemplo, se você quiser aceitar endereços no intervalo `192.168.100.0/22` e rejeitar todo o resto, precisará apenas adicionar uma regra na grade com o intervalo de endereços `192.168.100.0/22` .

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Recuperar e atualizar os filtros IP usando a CLI do Azure

Os filtros de IP do seu Hub IoT Podem ser recuperados e atualizados por meio da [CLI do Azure](/cli/azure/).

Para recuperar os filtros IP atuais do Hub IoT, execute:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Isso retornará um objeto JSON onde seus filtros IP existentes são listados na chave `properties.networkRuleSets`:

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

Para adicionar um novo filtro IP para o Hub IoT, execute:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

Para remover um filtro IP existente em seu Hub IoT, execute:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

Aqui, `<ipFilterIndexToRemove>` deve corresponder à ordenação de filtros IP em seu hub IOT `properties.networkRuleSets.ipRules` .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Recuperar e atualizar os filtros IP usando o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os filtros de IP do seu Hub IoT podem ser recuperados e configurados através do [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Atualizar as regras do filtro de IP usando REST


Você também pode recuperar e modificar um filtro de IP do seu Hub IoT usando o ponto de extremidade REST do provedor de recursos do Azure. Veja `properties.networkRuleSets` no [método createorupdate](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-classic-retirement"></a>Desativação do filtro IP (clássico)

O filtro de IP clássico foi desativado. Para saber mais, consulte [filtro IP clássico do Hub IOT e como atualizar](iot-hub-ip-filter-classic.md).

## <a name="next-steps"></a>Próximas etapas

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Métricas do IoT Hub](iot-hub-metrics.md)
* [Suporte do Hub IoT para redes virtuais com Link Privado e Identidade Gerenciada](virtual-network-support.md)
* [Gerenciando o acesso à rede pública para o Hub IoT](iot-hub-public-network-access.md)
* [Monitorar o Hub IoT](monitor-iot-hub.md)
