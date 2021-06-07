---
title: Filtro IP clássico do Hub IoT do Azure (preterido) | Microsoft Docs
description: Como atualizar do filtro IP clássico e quais são os benefícios
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jlian
ms.openlocfilehash: 6f326bafb311acedc48c5a349c78f1cd6bcebc87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661147"
---
# <a name="iot-hub-classic-ip-filter-and-how-to-upgrade"></a>Filtro IP clássico do Hub IoT e como atualizar 

O filtro IP atualizado para o Hub IoT protege o ponto de extremidade interno e é seguro por padrão. Embora possamos nos esforçarmos para nunca fazer alterações significativas, o modelo de segurança aprimorado do filtro de IP atualizado é incompatível com o filtro de IP clássico e, portanto, anunciamos sua aposentadoria. Para saber mais sobre o novo filtro de IP atualizado, consulte [o que há de novo](#whats-new) e filtros de [IP do Hub IOT](iot-hub-ip-filtering.md).

Para evitar a interrupção do serviço, você deve executar a atualização guiada antes do prazo de migração. nesse ponto, a atualização será executada automaticamente. Para saber mais sobre a linha do tempo de migração, confira [Azure Update](https://aka.ms/ipfilterv2azupdate).

## <a name="how-to-upgrade"></a>Como atualizar

1.  Visite portal do Azure
2.  Navegue até o seu Hub IoT.
3.  Selecione **rede** no menu do lado esquerdo.
4.  Você deve ver uma faixa solicitando que você atualize seu filtro IP para o novo modelo. Clique em **Sim** para continuar.
    :::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-upgrade-banner.png" alt-text="Imagem mostrando o prompt de banner para atualizar do filtro IP clássico":::
5.  Como o novo filtro de IP bloqueia todos os IP por padrão, a atualização remove suas regras de negação individuais, mas lhe dá a oportunidade de examiná-las antes de salvar. Examine atentamente as regras para certificar-se de que elas funcionam para você.
6.  Siga os prompts para concluir a atualização.

## <a name="whats-new"></a>Novidades

### <a name="secure-by-default"></a>Segurança por padrão

O filtro de IP clássico permite implicitamente que todos os endereços IP se conectem ao Hub IoT por padrão, o que não se alinha bem com os cenários de segurança de rede mais comuns. Normalmente, você deseja que apenas endereços IP confiáveis sejam capazes de se conectar ao seu hub IoT e rejeitar todo o resto. Para atingir essa meta com o filtro de IP clássico, trata-se de um processo de várias etapas. Por exemplo, se você quiser aceitar apenas o tráfego do `192.168.100.0/22` , deverá

1. Configure uma única regra de *permissão* para `192.168.100.0/22` .
1. Configurar uma regra de *bloqueio* diferente para `0.0.0.0/0` (a regra "bloquear tudo")
1. Certifique-se de que as regras sejam ordenadas corretamente, com a regra de permissão ordenada acima da regra de bloqueio.

Na prática, esse processo de várias etapas causa confusão. Os usuários não configuraram a regra "bloquear tudo" ou não ordenaram as regras corretamente, resultando em uma exposição involuntária. 

O novo filtro de IP bloqueia todos os endereços IP por padrão. Somente os intervalos de IP que você adiciona explicitamente têm permissão para se conectar ao Hub IoT. No exemplo acima, as etapas 2 e 3 não são mais necessárias. Esse novo comportamento simplifica a configuração e segue o [princípio seguro por padrão](https://wikipedia.org/wiki/Secure_by_default).

### <a name="protect-the-built-in-event-hub-compatible-endpoint"></a>Proteger o ponto de extremidade compatível com o Hub de eventos interno

O filtro IP clássico não pode ser aplicado ao ponto de extremidade interno. Essa limitação significa que, se o evento com uma regra bloquear tudo (bloquear `0.0.0.0/0` ) estiver configurado, o ponto de extremidade interno ainda estará acessível a partir de qualquer endereço IP.

O novo filtro de IP fornece uma opção para aplicar regras ao ponto de extremidade interno, o que reduz a exposição a ameaças de segurança de rede.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-built-in-endpoint.png" alt-text="Imagem mostrando a alternância a ser aplicada ao ponto de extremidade interno ou não":::

> [!NOTE]
> Essa opção não está disponível para hubs de IoT gratuitos (F1). Para aplicar regras de filtro IP ao ponto de extremidade interno, use um hub IoT pago.

### <a name="api-impact"></a>Impacto da API

O filtro de IP atualizado está disponível na API de recurso do Hub IoT de `2020-08-31` (bem como `2020-08-31-preview` ) e em diante. O filtro de IP clássico ainda está disponível em todas as versões de API, mas será removido em uma versão de API futura próxima ao prazo de migração. Para saber mais sobre a linha do tempo de migração, confira [Azure Update](https://aka.ms/ipfilterv2azupdate).

## <a name="tip-try-the-changes-before-they-apply"></a>Dica: tente as alterações antes de aplicá-las

Como o novo filtro IP bloqueia todo o endereço IP por padrão, as regras de bloco individuais não são mais compatíveis. Portanto, o processo de atualização guiado remove essas regras de bloqueio individuais. 

Para tentar a alteração em com o filtro IP clássico:

1. Visite a guia **rede** em seu hub IOT
1. Anote sua configuração de filtro IP (clássico) existente, caso você queira reverter
1. Ao lado de regras com **bloco**, selecione o ícone de lixeira para removê-los
1. Adicione outra regra na parte inferior com `0.0.0.0/0` e escolha **Bloquear**
1. Selecione **Salvar**

Essa configuração imita como o novo filtro IP se comporta após a atualização do clássico. Uma exceção é a proteção de ponto de extremidade interna, que não é possível tentar usar o filtro de IP clássico. No entanto, esse recurso é opcional, portanto, você não precisa usá-lo se acreditar que ele pode quebrar algo.

## <a name="tip-check-diagnostic-logs-for-all-ip-connections-to-your-iot-hub"></a>Dica: Verifique os logs de diagnóstico para todas as conexões IP com o Hub IoT

Para garantir uma transição suave, verifique os logs de diagnóstico na categoria conexões. Procure a `maskedIpAddress` propriedade para ver se os intervalos são os esperados. Lembre-se: o novo filtro IP bloqueará todos os endereços IP que não foram explicitamente adicionados.

## <a name="iot-hub-classic-ip-filter-documentation-retired"></a>Documentação do filtro IP clássico do Hub IoT (desativada)

> [!IMPORTANT]
> Abaixo está a documentação original do filtro IP clássico, que está sendo desativada.

A segurança é um aspecto importante de qualquer solução de IoT com base no Hub IoT do Azure. Às vezes você precisa especificar explicitamente os endereços IP dos quais o dispositivo pode se conectar como parte da sua configuração de segurança. O recurso *filtro IP* permite que você configure regras para rejeitar ou aceitar tráfego de endereços IPv4 específicos.

### <a name="when-to-use"></a>Quando usar

Há dois casos de uso específicos em que é útil bloquear os pontos de extremidade do Hub IoT para determinados endereços IP:

* O Hub IoT deve receber tráfego somente de um intervalo específico de endereços IP e rejeitar todo o resto. Por exemplo, caso você esteja usando o Hub IoT com o [Azure ExpressRoute](../expressroute/expressroute-faqs.md#supported-services) para criar conexões privadas entre um Hub IoT e sua infraestrutura local.

* Você precisa rejeitar tráfego de endereços IP que foram identificados como suspeitos pelo administrador do Hub IoT.

### <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro IP são aplicadas no nível do serviço Hub IoT. Portanto, as regras de filtro de IP se aplicam a todas as conexões de dispositivos e aplicativos de back-end que usam qualquer protocolo com suporte. No entanto, os clientes que leem diretamente do [ponto de extremidade compatível com o Hub de Eventos interno](iot-hub-devguide-messages-read-builtin.md) (não por meio da cadeia de conexão do Hub IoT) não estão associados às regras de filtro de IP. 

Todas as tentativas de conexão de um endereço IP que corresponde a uma regra IP de rejeição em seu Hub IoT recebem um código de status 401 não autorizado e uma descrição. A mensagem de resposta não menciona a regra IP. Rejeitar endereços IP pode impedir que outros serviços do Azure como Stream Analytics do Azure, Máquinas Virtuais do Azure ou o Gerenciador de Dispositivos no portal do Azure interajam com o hub IoT.

> [!NOTE]
> Se você precisa usar o Azure Stream Analytics (ASA) para ler mensagens de um hub IoT com o filtro de IP habilitado, use o nome compatível com o Hub de Eventos e o ponto de extremidade do hub IoT para adicionar manualmente uma [entrada de fluxo dos Hubs de Eventos](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) no ASA.

### <a name="default-setting"></a>Configuração padrão

Por padrão, a grade **Filtro IP** no portal de Hub IoT fica vazia. Essa configuração padrão significa que seu hub aceita conexões de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

Para acessar a página Configurações de filtro de IP, selecione **Rede**, **Acesso público**, em seguida escolha **Intervalos de IP selecionados**:

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-default.png" alt-text="Configurações de filtro de IP padrão do Hub IoT":::

### <a name="add-or-edit-an-ip-filter-rule"></a>Adicionar ou editar uma regra de filtro IP

Para adicionar uma regra de filtro de IP, selecione **+ Adicionar regra de filtro de IP**.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-add-rule.png" alt-text="Adicionar uma regra de filtro de IP a um hub IoT":::

Após selecionar **Adicionar regra de filtro de IP**, preencha os campos.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-after-selecting-add.png" alt-text="Após selecionar Adicionar uma regra de filtro de IP":::

* Forneça um **nome** para a regra de filtro de IP. O nome deve ser uma cadeia exclusiva de caracteres alfanuméricos, que não diferencia maiúsculas de minúsculas, de até 128 caracteres. Somente são aceitos caracteres alfanuméricos ASCII de 7 bits mais `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Forneça um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, uma notação CIDR 192.168.100.0/22 representa os 1024 endereços IPv4 de 192.168.100.0 a 192.168.103.255.

* Selecione **Permitir** ou **Bloquear** como a **ação** para a regra de filtro de IP.

Depois de preencher os campos, selecione **Salvar** para salvar a regra. Você verá um alerta informando que a atualização está em andamento.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-save-new-rule.png" alt-text="Notificação de salvamento uma regra de filtro de IP":::

A opção **Adicionar** é desabilitada quando você atinge o máximo de dez regras de filtro IP.

Para editar uma regra existente, selecione os dados que você deseja alterar, faça a alteração e, em seguida, selecione **Salvar** para salvar a alteração.

### <a name="delete-an-ip-filter-rule"></a>Excluir uma regra de filtro IP

Para excluir uma regra de filtro de IP, selecione o ícone de lixeira na respectiva linha e, em seguida, selecione **Salvar**. Isso removerá a regra e salvará a alteração.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-delete-rule.png" alt-text="Excluir uma regra de filtro de IP do Hub IoT":::

### <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Recuperar e atualizar os filtros IP usando a CLI do Azure

Os filtros de IP do seu Hub IoT Podem ser recuperados e atualizados por meio da [CLI do Azure](/cli/azure/).

Para recuperar os filtros IP atuais do Hub IoT, execute:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Isso retornará um objeto JSON onde seus filtros IP existentes são listados na chave `properties.ipFilterRules`:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Para adicionar um novo filtro IP para o Hub IoT, execute:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Para remover um filtro IP existente em seu Hub IoT, execute:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Observe que `<ipFilterIndexToRemove>` deve corresponder à ordenação de filtros IP no `properties.ipFilterRules` do seu Hub IoT.

### <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Recuperar e atualizar os filtros IP usando o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os filtros de IP do seu Hub IoT podem ser recuperados e configurados através do [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

### <a name="update-ip-filter-rules-using-rest"></a>Atualizar as regras do filtro de IP usando REST

Você também pode recuperar e modificar um filtro de IP do seu Hub IoT usando o ponto de extremidade REST do provedor de recursos do Azure. Veja `properties.ipFilterRules` no [método createorupdate](/rest/api/iothub/iothubresource/createorupdate).

### <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Por exemplo, se você quiser aceitar endereços no intervalo 192.168.100.0/22 e rejeitar todo o resto, a primeira regra na grade deverá aceitar o intervalo de endereços 192.168.100.0/22. A próxima regra deve rejeitar todos os endereços usando o intervalo 0.0.0.0/0.

Você pode alterar a ordem de suas regras de filtro IP na grade clicando nos três pontos verticais no início de uma linha e usando arrastar e soltar.

Para salvar a nova ordem das regras de filtro IP, clique em **Salvar**.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-rule-order.png" alt-text="Alterar a ordem de suas regras de filtro IP do Hub IoT":::

## <a name="next-steps"></a>Próximas etapas

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Usar filtros IP](iot-hub-ip-filtering.md)