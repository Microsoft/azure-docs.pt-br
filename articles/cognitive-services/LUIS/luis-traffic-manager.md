---
title: Aumentar a cota do ponto de extremidade-LUIS
titleSuffix: Azure Cognitive Services
description: O LUIS (Reconhecimento vocal) oferece a capacidade de aumentar a cota de solicitação do ponto de extremidade além da cota de uma única chave. Isso é feito criando mais chaves para o LUIS e adicionando-as ao aplicativo LUIS na página **Publicar** na seção **Recursos e Chaves**.
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/08/2019
ms.author: diberry
ms.openlocfilehash: 10ddbed710d3055e66bd3cb0b06cfa7949a9a1c5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563370"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Usar o Gerenciador de Tráfego do Microsoft Azure para gerenciar a cota do ponto de extremidade entre chaves
O LUIS (Reconhecimento vocal) oferece a capacidade de aumentar a cota de solicitação do ponto de extremidade além da cota de uma única chave. Isso é feito criando mais chaves para o LUIS e adicionando-as ao aplicativo LUIS na página **Publicar** na seção **Recursos e Chaves**. 

O aplicativo cliente precisa gerenciar o tráfego entre as chaves. O LUIS não faz isso. 

Este artigo explica como gerenciar o tráfego entre chaves com o [Gerenciador de tráfego][traffic-manager-marketing]do Azure. Você precisa já ter treinado e publicado um aplicativo LUIS. Se não tiver um, siga o [início rápido](luis-get-started-create-app.md) do domínio predefinido. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Conectar-se ao PowerShell no portal do Azure
No portal [do Azure][azure-portal] , abra a janela do PowerShell. O ícone da janela do PowerShell é o **>_** na barra de navegação superior. Usando o PowerShell no portal, você obtém a versão mais recente do PowerShell e é autenticado. O PowerShell no portal requer uma conta de [Armazenamento do Azure](https://azure.microsoft.com/services/storage/). 

![Captura de tela do portal do Azure com a janela do PowerShell aberta](./media/traffic-manager/azure-portal-powershell.png)

As seções a seguir usam os [cmdlets do PowerShell do Gerenciador de Tráfego](https://docs.microsoft.com/powershell/module/az.trafficmanager/#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Criar grupo de recursos do Azure com o PowerShell
Antes de criar os recursos do Azure, crie um grupo de recursos para conter todos eles. Dê o nome `luis-traffic-manager` ao grupo de recursos e use a região `West US`. A região do grupo de recursos armazena metadados sobre o grupo. Ela não desacelerará seus recursos se eles estiverem em outra região. 

Criar grupo de recursos com o cmdlet **[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** :

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Criar chaves LUIS para aumentar a cota total do ponto de extremidade
1. No portal do Azure, crie duas chaves de **Reconhecimento vocal**, uma no `West US` e outra no `East US`. Use o grupo de recursos existente, criado na seção anterior, chamada `luis-traffic-manager`. 

    ![Captura de tela do portal do Azure com duas chaves LUIS no grupo de recursos luis-traffic-manager](./media/traffic-manager/luis-keys.png)

2. No site do [Luis][LUIS] , na seção **gerenciar** , na página **chaves e pontos de extremidade** , atribua chaves ao aplicativo e Republique o aplicativo selecionando o botão **publicar** no menu superior direito. 

    A URL de exemplo na coluna **ponto de extremidade** usa uma solicitação GET com a chave de ponto de extremidade como um parâmetro de consulta. Copie as URLs do ponto de extremidade das duas novas chaves. Elas são usadas como parte da configuração do Gerenciador de Tráfego posteriormente neste artigo.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Gerenciar solicitações de ponto de extremidade LUIS entre chaves com o Gerenciador de Tráfego
O Gerenciador de Tráfego cria um novo ponto de acesso do DNS para seus pontos de extremidade. Ele não funciona como gateway ou proxy, mas estritamente no nível do DNS. Esse exemplo não muda nenhum registro do DNS. Ele usa uma biblioteca do DNS para se comunicar com o Gerenciador de Tráfego para obter o ponto de extremidade correto para essa solicitação específica. _Cada_ solicitação destinada para o LUIS requer primeiro uma solicitação do Gerenciador de Tráfego para determinar qual ponto de extremidade LUIS usar. 

### <a name="polling-uses-luis-endpoint"></a>A sondagem usa o ponto de extremidade LUIS
O Gerenciador de Tráfego sonda os pontos de extremidade periodicamente para garantir que o ponto de extremidade ainda está disponível. A URL do Gerenciador de Tráfego sondado precisa estar acessível com uma solicitação GET e retornar um 200. A URL do ponto de extremidade na página **Publicar** faz isso. Como cada chave de ponto de extremidade tem um roteamento e parâmetros de cadeia de caracteres de consulta diferentes, cada chave de ponto de extremidade precisa de um caminho de pesquisa diferente. Cada vez que o Gerenciador de Tráfego faz uma sondagem, custa uma solicitação de cota. O parâmetro de cadeia de caracteres de consulta **q** do ponto de extremidade LUIS é a declaração enviada ao LUIS. Esse parâmetro, em vez de enviar uma declaração, é usado para adicionar a sondagem do Gerenciador de Tráfego ao log do ponto de extremidade LUIS como uma técnica de depuração ao configurar o Gerenciador de Tráfego.

Como cada ponto de extremidade LUIS precisa do seu próprio caminho, ele precisa do seu próprio perfil do Gerenciador de Tráfego. Para gerenciar entre perfis, crie uma arquitetura [_aninhada_ do Gerenciador de Tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles). Um perfil pai aponta para os perfis filho e gerencia o tráfego entre eles.

Depois que o Gerenciador de Tráfego for configurado, lembre-se de alterar o caminho para usar o parâmetro da cadeia de caracteres de consulta logging=false para seu log não ser preenchido com sondagem.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Configurar o Gerenciador de Tráfego com perfis aninhados
As seguintes seções criam dois perfis filho, um para a chave LUIS Leste e um para a chave LUIS Oeste. Em seguida, um perfil pai é criado e os dois perfis filho são adicionados ao perfil pai. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Criar o perfil do Gerenciador de Tráfego do Leste dos EUA com o PowerShell
Para criar o perfil do Gerenciador de Tráfego do Leste dos EUA, há várias etapas: criar perfil, adicionar o ponto de extremidade e definir o ponto de extremidade. Um perfil do Gerenciador de Tráfego pode ter muitos pontos de extremidade, mas cada ponto de extremidade tem o mesmo caminho de validação. Como as URLs do ponto de extremidade de LUIS para as assinaturas leste e oeste são diferentes devido à região e à chave de ponto de extremidade, cada ponto de extremidade de LUIS precisa ser um ponto de extremidade único no perfil. 

1. Criar perfil com o cmdlet **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)**

    Use o cmdlet a seguir para criar o perfil. Certifique-se de alterar o `appIdLuis` e o `subscriptionKeyLuis`. A subscriptionKey é para a chave LUIS Leste dos EUA. Se o caminho não estiver correto, incluindo a ID do aplicativo de LUIS e a chave de ponto de extremidade, a sondagem do Gerenciador de Tráfego será um status de `degraded` porque o Gerenciamento de Tráfego não poderá solicitar com êxito o ponto de extremidade de LUIS. Certifique-se de que o valor de `q` é `traffic-manager-east` para poder ver esse valor nos logs de ponto de extremidade LUIS.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    Esta tabela explica cada variável no cmdlet:
    
    |Parâmetro de configuração|Nome ou valor da variável|Finalidade|
    |--|--|--|
    |-Name|luis-profile-eastus|Nome do Gerenciador de Tráfego no portal do Azure|
    |-ResourceGroupName|luis-traffic-manager|Criado na seção anterior|
    |-TrafficRoutingMethod|Desempenho|Para obter mais informações, consulte [Métodos de roteamento do Gerenciador de Tráfego][routing-methods]. Se estiver usando o desempenho, a solicitação de URL ao Gerenciador de Tráfego deverá vir da região do usuário. Se estiver passando por um chatbot ou outro aplicativo, será responsabilidade do chatbot simular a região na chamada para o Gerenciador de Tráfego. |
    |-RelativeDnsName|luis-dns-eastus|Esse é o subdomínio do serviço: luis-dns-eastus.trafficmanager.net|
    |-Ttl|30|Intervalo de sondagem, 30 segundos|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|A porta e o protocolo do LUIS é HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Substitua `<appIdLuis>` e `<subscriptionKeyLuis>` pelos seus próprios valores.|
    
    Uma solicitação bem-sucedida não tem nenhuma resposta.

2. Adicionar ponto de extremidade leste dos EUA com o cmdlet **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome ou valor da variável|Finalidade|
    |--|--|--|
    |-EndpointName|luis-east-endpoint|Nome do ponto de extremidade exibido embaixo do perfil|
    |-TrafficManagerProfile|$eastprofile|Use o objeto de perfil criado na Etapa 1|
    |-Type|ExternalEndpoints|Para obter mais informações, consulte [ponto de extremidade do Traffic Manager][traffic-manager-endpoints] |
    |-Target|eastus.api.cognitive.microsoft.com|Este é o domínio do ponto de extremidade LUIS.|
    |-EndpointLocation|"eastus"|Região do ponto de extremidade|
    |-EndpointStatus|Enabled|Habilitar ponto de extremidade quando ele for criado|

    A resposta bem-sucedida tem a seguinte aparência:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. Definir ponto de extremidade leste dos EUA com o cmdlet **[set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Uma resposta bem-sucedida será a mesma resposta que a etapa 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Criar o perfil do Gerenciador de Tráfego do Oeste dos EUA com o PowerShell
Para criar o perfil do Gerenciador de Tráfego do Oeste dos EUA, siga as mesmas etapas: criar perfil, adicionar o ponto de extremidade e definir o ponto de extremidade.

1. Criar perfil com o cmdlet **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    Use o cmdlet a seguir para criar o perfil. Certifique-se de alterar o `appIdLuis` e o `subscriptionKeyLuis`. A subscriptionKey é para a chave LUIS Leste dos EUA. Se o caminho não estiver correto, incluindo a ID do aplicativo de LUIS e a chave de ponto de extremidade, a sondagem do Gerenciador de Tráfego será um status de `degraded` porque o Gerenciamento de Tráfego não poderá solicitar com êxito o ponto de extremidade de LUIS. Certifique-se de que o valor de `q` é `traffic-manager-west` para poder ver esse valor nos logs de ponto de extremidade LUIS.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    Esta tabela explica cada variável no cmdlet:
    
    |Parâmetro de configuração|Nome ou valor da variável|Finalidade|
    |--|--|--|
    |-Name|luis-profile-westus|Nome do Gerenciador de Tráfego no portal do Azure|
    |-ResourceGroupName|luis-traffic-manager|Criado na seção anterior|
    |-TrafficRoutingMethod|Desempenho|Para obter mais informações, consulte [Métodos de roteamento do Gerenciador de Tráfego][routing-methods]. Se estiver usando o desempenho, a solicitação de URL ao Gerenciador de Tráfego deverá vir da região do usuário. Se estiver passando por um chatbot ou outro aplicativo, será responsabilidade do chatbot simular a região na chamada para o Gerenciador de Tráfego. |
    |-RelativeDnsName|luis-dns-westus|Esse é o subdomínio do serviço: luis-dns-westus.trafficmanager.net|
    |-Ttl|30|Intervalo de sondagem, 30 segundos|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|A porta e o protocolo do LUIS é HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Substitua `<appId>` e `<subscriptionKey>` pelos seus próprios valores. Lembre-se de que essa chave de ponto de extremidade é diferente da chave de ponto de extremidade leste|
    
    Uma solicitação bem-sucedida não tem nenhuma resposta.

2. Adicionar ponto de extremidade oeste dos EUA com o cmdlet **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome ou valor da variável|Finalidade|
    |--|--|--|
    |-EndpointName|luis-west-endpoint|Nome do ponto de extremidade exibido embaixo do perfil|
    |-TrafficManagerProfile|$westprofile|Use o objeto de perfil criado na Etapa 1|
    |-Type|ExternalEndpoints|Para obter mais informações, consulte [ponto de extremidade do Traffic Manager][traffic-manager-endpoints] |
    |-Target|westus.api.cognitive.microsoft.com|Este é o domínio do ponto de extremidade LUIS.|
    |-EndpointLocation|"westus"|Região do ponto de extremidade|
    |-EndpointStatus|Enabled|Habilitar ponto de extremidade quando ele for criado|

    A resposta bem-sucedida tem a seguinte aparência:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. Definir ponto de extremidade oeste dos EUA com o cmdlet **[set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Uma resposta bem-sucedida é a mesma resposta que a etapa 2.

### <a name="create-parent-traffic-manager-profile"></a>Criar um perfil pai do Gerenciador de Tráfego
Crie o perfil pai do Gerenciador de Tráfego e vincule dois perfis filho do Gerenciador de Tráfego ao pai.

1. Criar perfil pai com cmdlet **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome ou valor da variável|Finalidade|
    |--|--|--|
    |-Name|luis-profile-parent|Nome do Gerenciador de Tráfego no portal do Azure|
    |-ResourceGroupName|luis-traffic-manager|Criado na seção anterior|
    |-TrafficRoutingMethod|Desempenho|Para obter mais informações, consulte [Métodos de roteamento do Gerenciador de Tráfego][routing-methods]. Se estiver usando o desempenho, a solicitação de URL ao Gerenciador de Tráfego deverá vir da região do usuário. Se estiver passando por um chatbot ou outro aplicativo, será responsabilidade do chatbot simular a região na chamada para o Gerenciador de Tráfego. |
    |-RelativeDnsName|luis-dns-parent|Esse é o subdomínio do serviço: luis-dns-parent.trafficmanager.net|
    |-Ttl|30|Intervalo de sondagem, 30 segundos|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|A porta e o protocolo do LUIS é HTTPS/443|
    |-MonitorPath|`/`|Esse caminho não importa, porque os caminhos de ponto de extremidade filho são usados.|

    Uma solicitação bem-sucedida não tem nenhuma resposta.

2. Adicionar perfil filho leste dos EUA ao pai com **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** e tipo **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome ou valor da variável|Finalidade|
    |--|--|--|
    |-EndpointName|child-endpoint-useast|Perfil do Leste|
    |-TrafficManagerProfile|$parentprofile|Perfil ao qual atribuir este ponto de extremidade|
    |-Type|NestedEndpoints|Para obter mais informações, consulte [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$eastprofile.Id|ID do perfil filho|
    |-EndpointStatus|Enabled|Status do ponto de extremidade após ser adicionado ao pai|
    |-EndpointLocation|"eastus"|[Nome da região do Azure](https://azure.microsoft.com/global-infrastructure/regions/) do recurso|
    |-MinChildEndpoints|1|Número mínimo de pontos de extremidade filho|

    A resposta bem-sucedida tem a seguinte aparência e inclui o novo ponto de extremidade `child-endpoint-useast`:    

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. Adicione o perfil filho do oeste dos EUA ao pai com o cmdlet **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** e o tipo **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Esta tabela explica cada variável no cmdlet:

    |Parâmetro de configuração|Nome ou valor da variável|Finalidade|
    |--|--|--|
    |-EndpointName|child-endpoint-uswest|Perfil do Oeste|
    |-TrafficManagerProfile|$parentprofile|Perfil ao qual atribuir este ponto de extremidade|
    |-Type|NestedEndpoints|Para obter mais informações, consulte [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$westprofile.Id|ID do perfil filho|
    |-EndpointStatus|Enabled|Status do ponto de extremidade após ser adicionado ao pai|
    |-EndpointLocation|"westus"|[Nome da região do Azure](https://azure.microsoft.com/global-infrastructure/regions/) do recurso|
    |-MinChildEndpoints|1|Número mínimo de pontos de extremidade filho|

    A resposta bem-sucedida tem a aparência do ponto de extremidade `child-endpoint-useast` anterior e do novo ponto de extremidade `child-endpoint-uswest` e os inclui:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. Definir pontos de extremidade com o cmdlet **[set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** 

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Uma resposta bem-sucedida é a mesma resposta que a etapa 3.

### <a name="powershell-variables"></a>Variáveis do PowerShell
Nas seções anteriores, três variáveis do PowerShell foram criadas: `$eastprofile`, `$westprofile`, `$parentprofile`. Essas variáveis são usadas no fim da configuração do Gerenciador de Tráfego. Se você optou por não criar as variáveis ou se esqueceu de, ou a janela do PowerShell atingir o tempo limite, poderá usar o cmdlet do PowerShell, **[Get-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** , para obter o perfil novamente e atribuí-lo a uma variável. 

Substitua os itens entre colchetes angulares, `<>`, pelos valores corretos para cada um dos três perfis de que você precisa. 

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Verificar como o Gerenciador de Tráfego funciona
Para verificar se os perfis do Gerenciador de Tráfego funcionam, os perfis precisam ter o status de `Online` Esse status é baseado no caminho de sondagem do ponto de extremidade. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Exibir novos perfis no portal do Azure
É possível verificar se todos os três perfis foram criados examinando os recursos no grupo de recursos `luis-traffic-manager`.

![Captura de tela do luis-traffic-manager do grupo de recursos do Azure](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Verifique se o status do perfil está Online
O Gerenciador de Tráfego pesquisa o caminho de cada ponto de extremidade para garantir que ele está online. Se ele estiver online, o status dos perfis filho serão `Online`. Isso é exibido na **Visão geral** de cada perfil. 

![Captura de tela da Visão geral do perfil do Gerenciador de Tráfego do Azure mostrando Monitorar status de online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Validar se a sondagem do Gerenciador de Tráfego funciona
Outra forma de validar a sondagem do Gerenciador de Tráfego é com os logs do ponto de extremidade LUIS. Na página de lista de aplicativos do site do [Luis][LUIS] , exporte o log do ponto de extremidade para o aplicativo. Como o Gerenciador de Tráfego pesquisa frequentemente os dois pontos de extremidade, haverá entradas nos logs mesmo se eles estiveram apenas por alguns minutos. Lembre-se de procurar entradas em que a consulta começa com `traffic-manager-`.

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Validar se a resposta DNS do Gerenciador de Tráfego funciona
Para validar se a resposta DNS retorna um ponto de extremidade LUIS, solicite o DNS do perfil pai do Gerenciador de Tráfego usando a biblioteca de clientes do DNS. O nome DNS do perfil pai é `luis-dns-parent.trafficmanager.net`.

O código Node.js a seguir cria uma solicitação para o perfil pai e retorna um ponto de extremidade LUIS:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

A resposta bem-sucedida com o ponto de extremidade LUIS é:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Usar o perfil pai do Gerenciador de Tráfego
Para gerenciar o tráfego entre pontos de extremidade, é necessário inserir uma chamada ao DNS do Gerenciador de Tráfego para localizar o ponto de extremidade LUIS. Essa chamada é feita para toda solicitação do ponto de extremidade LUIS e precisa simular a localização geográfica do usuário do aplicativo cliente LUIS. Adicione o código de resposta do DNS entre seu aplicativo cliente LUIS e a solicitação para o LUIS da previsão do ponto de extremidade. 

## <a name="resolving-a-degraded-state"></a>Resolvendo um estado degradado

Habilitar [logs de diagnóstico](../../traffic-manager/traffic-manager-diagnostic-logs.md) do Gerenciador de Tráfego para ver por que o status do ponto de extremidade está degradado.

## <a name="clean-up"></a>Limpar
Remova as duas chaves de ponto de extremidade de LUIS, os três perfis do Gerenciador de Tráfego e o grupo de recursos que continha esses cinco recursos. Isso é feito no portal do Azure. Exclua os cinco recursos da lista de recursos. Em seguida, exclua o grupo de recursos. 

## <a name="next-steps"></a>Próximas etapas

Examine as opções [middleware](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) no BotFramework v4 para entender como esse código de gerenciamento de tráfego pode ser adicionado a um bot do BotFramework. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
