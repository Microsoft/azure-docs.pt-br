---
title: Azure App restrições de acesso de serviço
description: Saiba como proteger seu aplicativo no serviço Azure App configurando as restrições de acesso.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4b85397eeda651678fe66c6e78199dd25630dcc4
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889869"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Configurar Azure App restrições de acesso de serviço

Ao configurar as restrições de acesso, você pode definir uma lista de permissão/negação ordenada de prioridade que controla o acesso à rede para seu aplicativo. A lista pode incluir endereços IP ou sub-redes de rede virtual do Azure. Quando há uma ou mais entradas, um *negado* implícito existe no final da lista.

O recurso de restrição de acesso funciona com todas as Azure App cargas de trabalho hospedadas no serviço. As cargas de trabalho podem incluir aplicativos Web, aplicativos de API, aplicativos do Linux, aplicativos de contêiner do Linux e funções.

Quando uma solicitação é feita ao seu aplicativo, o endereço de é avaliado em relação às regras na sua lista de restrições de acesso. Se o endereço de estiver em uma sub-rede configurada com pontos de extremidade de serviço para Microsoft. Web, a sub-rede de origem será comparada com as regras de rede virtual na sua lista de restrições de acesso. Se o endereço não tiver permissão de acesso com base nas regras na lista, o serviço responderá com um código de status [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) .

O recurso de restrição de acesso é implementado nas funções de front-end do serviço de aplicativo, que são upstream dos hosts de trabalho em que seu código é executado. Portanto, as restrições de acesso são efetivamente as ACLs (listas de controle de acesso) de rede.

A capacidade de restringir o acesso ao seu aplicativo Web de uma rede virtual do Azure é habilitada pelos [pontos de extremidade de serviço][serviceendpoints]. Com os pontos de extremidade de serviço, você pode restringir o acesso a um serviço multilocatário de sub-redes selecionadas. Ele não funciona para restringir o tráfego para aplicativos hospedados em um Ambiente do Serviço de Aplicativo. Se você estiver em um Ambiente do Serviço de Aplicativo, poderá controlar o acesso ao seu aplicativo aplicando regras de endereço IP.

> [!NOTE]
> Os pontos de extremidade de serviço devem ser habilitados no lado da rede e para o serviço do Azure ao qual estão sendo habilitados. Para obter uma lista de serviços do Azure que dão suporte a pontos de extremidade de serviço, consulte [pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md).
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="Diagrama do fluxo de restrições de acesso.":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>Gerenciar regras de restrição de acesso no portal

Para adicionar uma regra de restrição de acesso ao seu aplicativo, faça o seguinte:

1. Entre no portal do Azure.

1. No painel esquerdo, selecione **rede**.

1. No painel **rede** , em **restrições de acesso**, selecione **Configurar restrições de acesso**.

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Captura de tela do painel opções de rede do serviço de aplicativo na portal do Azure.":::

1. Na página **restrições de acesso** , examine a lista de regras de restrição de acesso que são definidas para seu aplicativo.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="Captura de tela da página restrições de acesso na portal do Azure, mostrando a lista de regras de restrição de acesso definidas para o aplicativo selecionado.":::

   A lista exibe todas as restrições atuais que são aplicadas ao aplicativo. Se você tiver uma restrição de rede virtual em seu aplicativo, a tabela mostrará se os pontos de extremidade de serviço estão habilitados para Microsoft. Web. Se nenhuma restrição for definida em seu aplicativo, o aplicativo poderá ser acessado de qualquer lugar.

### <a name="add-an-access-restriction-rule"></a>Adicionar uma regra de restrição de acesso

Para adicionar uma regra de restrição de acesso ao seu aplicativo, no painel **restrições de acesso** , selecione **Adicionar regra**. Depois de adicionar uma regra, ela entra em vigor imediatamente. 

As regras são impostas em ordem de prioridade, a partir do número mais baixo na coluna **prioridade** . Uma *negação implícita tudo* estará em vigor depois que você adicionar até mesmo uma única regra.

No painel **Adicionar restrição de acesso** , ao criar uma regra, faça o seguinte:

1. Em **ação**, selecione **permitir** ou **negar**.  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="Captura de tela do painel ' Adicionar restrição de acesso '.":::

1. Opcionalmente, insira um nome e uma descrição da regra.
1. Na caixa **prioridade** , insira um valor de prioridade.
1. Na lista suspensa **tipo** , selecione o tipo de regra.

Os diferentes tipos de regras são descritos nas seções a seguir.

> [!NOTE]
> - Há um limite de 512 regras de restrição de acesso. Se você precisar de mais de 512 regras de restrição de acesso, sugerimos que considere a instalação de um produto de segurança autônomo, como a porta frontal do Azure, o gateway de Azure App ou um WAF alternativo.
>
#### <a name="set-an-ip-address-based-rule"></a>Definir uma regra baseada em endereço IP

Siga o procedimento conforme descrito na seção anterior, mas com a seguinte adição:
* Para a etapa 4, na lista suspensa **tipo** , selecione **IPv4** ou **IPv6**. 

Especifique o **bloco de endereço IP** na notação CIDR (roteamento sem classe Inter-Domain) para os endereços IPv4 e IPv6. Para especificar um endereço, você pode usar algo como *1.2.3.4/32*, em que os primeiros quatro octetos representam seu endereço IP e */32* é a máscara. A notação de CIDR de IPv4 para todos os endereços é 0.0.0.0/0. Para saber mais sobre a notação CIDR, consulte [Roteamento de Inter-Domain sem classe](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

#### <a name="set-a-service-endpoint-based-rule"></a>Definir uma regra com base no ponto de extremidade de serviço

* Para a etapa 4, na lista suspensa **tipo** , selecione **rede virtual**.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="Captura de tela do painel ' Adicionar restrição ' com o tipo de rede virtual selecionado.":::

Especifique as listas suspensas **assinatura**, **rede virtual** e **sub-rede** , correspondentes às quais você deseja restringir o acesso.

Usando pontos de extremidade de serviço, você pode restringir o acesso às sub-redes selecionadas da rede virtual do Azure. Se os pontos de extremidade de serviço ainda não estiverem habilitados com o Microsoft. Web para a sub-rede que você selecionou, eles serão habilitados automaticamente, a menos que você marque a caixa de seleção **ignorar pontos de extremidade do serviço Microsoft. Web ausentes** . O cenário em que você pode querer habilitar pontos de extremidade de serviço no aplicativo, mas não a sub-rede depende principalmente de você ter as permissões para habilitá-los na sub-rede. 

Se você precisar que outra pessoa habilite os pontos de extremidade de serviço na sub-rede, marque a caixa de seleção **ignorar pontos de extremidade do serviço Microsoft. Web ausentes** . Seu aplicativo será configurado para pontos de extremidade de serviço na antecipação de tê-los habilitados mais tarde na sub-rede. 

Você não pode usar pontos de extremidade de serviço para restringir o acesso a aplicativos que são executados em um Ambiente do Serviço de Aplicativo. Quando seu aplicativo estiver em um Ambiente do Serviço de Aplicativo, você poderá controlar o acesso a ele aplicando regras de acesso IP. 

Com os pontos de extremidade de serviço, você pode configurar seu aplicativo com gateways de aplicativo ou outros dispositivos WAF (firewall do aplicativo Web). Você também pode configurar aplicativos de várias camadas com back-ends seguros. Para obter mais informações, consulte [recursos de rede e](networking-features.md) integração do serviço de aplicativo e do [Gateway de aplicativo com pontos de extremidade de serviço](networking/app-gateway-with-service-endpoints.md).

> [!NOTE]
> - Atualmente, os pontos de extremidade de serviço não têm suporte para aplicativos Web que usam VIP (IP virtual) de IP protocolo SSL (SSL).
>
#### <a name="set-a-service-tag-based-rule"></a>Definir uma regra baseada em marca de serviço

* Para a etapa 4, na lista suspensa **tipo** , selecione marca de **serviço**.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png?v2" alt-text="Captura de tela do painel ' Adicionar restrição ' com o tipo de marca de serviço selecionado.":::

Cada marca de serviço representa uma lista de intervalos de IP dos serviços do Azure. Uma lista desses serviços e links para os intervalos específicos pode ser encontrada na documentação da [marca de serviço][servicetags].

Todas as marcas de serviço disponíveis têm suporte nas regras de restrição de acesso. Para simplificar, apenas uma lista das marcas mais comuns está disponível por meio do portal do Azure. Use Azure Resource Manager modelos ou scripts para configurar regras mais avançadas, como regras com escopo regional. Essas são as marcas disponíveis por meio de portal do Azure:

* ActionGroup
* ApplicationInsightsAvailability
* AzureCloud
* AzureCognitiveSearch
* AzureEventGrid
* AzureFrontDoor.Backend
* AzureMachineLearning
* AzureTrafficManager
* LogicApps

### <a name="edit-a-rule"></a>Editar uma regra

1. Para começar a editar uma regra de restrição de acesso existente, na página **restrições de acesso** , selecione a regra que você deseja editar.

1. No painel **Editar restrição de acesso** , faça as alterações e, em seguida, selecione **Atualizar regra**. As edições entram em vigor imediatamente, incluindo alterações na ordenação de prioridade.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="Captura de tela do painel ' Editar restrição de acesso ' na portal do Azure, mostrando os campos de uma regra de restrição de acesso existente.":::

   > [!NOTE]
   > Quando você edita uma regra, não pode alternar entre tipos de regra. 

### <a name="delete-a-rule"></a>Excluir uma regra

Para excluir uma regra, na página **restrições de acesso** , selecione as reticências (**...**) ao lado da regra que você deseja excluir e, em seguida, selecione **remover**.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="Captura de tela da página ' restrições de acesso ', mostrando as reticências ' remove ' ao lado da regra de restrição de acesso a ser excluída.":::

## <a name="access-restriction-advanced-scenarios"></a>Cenários avançados de restrição de acesso
As seções a seguir descrevem alguns cenários avançados usando restrições de acesso.

### <a name="filter-by-http-header"></a>Filtrar por cabeçalho http

Como parte de qualquer regra, você pode adicionar filtros de cabeçalho HTTP adicionais. Há suporte para os seguintes nomes de cabeçalho http:
* X-Forwarded-For
* X-Forwarded-Host
* X-Azure-FDID
* X-FD-HealthProbe

Para cada nome de cabeçalho, você pode adicionar até 8 valores separados por vírgula. Os filtros de cabeçalho HTTP são avaliados após a regra em si e as duas condições devem ser verdadeiras para que a regra seja aplicada.

### <a name="multi-source-rules"></a>Regras de várias fontes

As regras de várias fontes permitem combinar até 8 intervalos de IP ou 8 marcas de serviço em uma única regra. Você pode usar isso se tiver mais de 512 intervalos de IP ou se quiser criar regras lógicas em que vários intervalos de IP são combinados com um único filtro de cabeçalho http.

As regras de várias fontes são definidas da mesma maneira que você define regras de fonte única, mas com cada intervalo separado por vírgula.

Exemplo do PowerShell:

  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Multi-source rule" -IpAddress "192.168.1.0/24,192.168.10.0/24,192.168.100.0/24" `
    -Priority 100 -Action Allow
  ```

### <a name="block-a-single-ip-address"></a>Bloquear um único endereço IP

Quando você adiciona sua primeira regra de restrição de acesso, o serviço adiciona uma regra *negar tudo* explícita com uma prioridade de 2147483647. Na prática, a regra *negar tudo* explícita é a regra final a ser executada e bloqueia o acesso a qualquer endereço IP que não seja explicitamente permitido por uma regra de *permissão* .

Para um cenário em que você deseja bloquear explicitamente um único endereço IP ou um bloco de endereços IP, mas permitir o acesso a todos os outros, adicione uma regra *permitir tudo* explícito.

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="Captura de tela da página ' restrições de acesso ' na portal do Azure, mostrando um único endereço IP bloqueado.":::

### <a name="restrict-access-to-an-scm-site"></a>Restringir o acesso a um site do SCM 

Além de poder controlar o acesso ao seu aplicativo, você pode restringir o acesso ao site do SCM que é usado pelo seu aplicativo. O site do SCM é o ponto de extremidade de implantação da Web e o console do kudu. Você pode atribuir restrições de acesso ao site do SCM do aplicativo separadamente ou usar o mesmo conjunto de restrições para o aplicativo e o site do SCM. Quando você seleciona as **mesmas restrições que \<app name>** a caixa de seleção, tudo fica em branco. Se você desmarcar a caixa de seleção, as configurações do site SCM serão reaplicadas. 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="Captura de tela da página ' restrições de acesso ' na portal do Azure, mostrando que nenhuma restrição de acesso está definida para o site do SCM ou o aplicativo.":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance"></a>Restringir o acesso a uma instância específica da porta de recepção do Azure
O tráfego da porta frontal do Azure para seu aplicativo provém de um conjunto conhecido de intervalos de IP definido na marca de serviço AzureFrontDoor. backend. Usando uma regra de restrição de marca de serviço, você pode restringir o tráfego para ser originado apenas da porta de início do Azure. Para garantir que o tráfego seja originado apenas de sua instância específica, você precisará filtrar ainda mais as solicitações de entrada com base no cabeçalho http exclusivo que o Azure front door envia.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-frontdoor.png" alt-text="Captura de tela da página ' restrições de acesso ' na portal do Azure, mostrando como adicionar a restrição de porta frontal do Azure.":::

Exemplo do PowerShell:

  ```azurepowershell-interactive
  $afd = Get-AzFrontDoor -Name "MyFrontDoorInstanceName"
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
    -HttpHeader @{'x-azure-fdid' = $afd.FrontDoorId}
  ```

## <a name="manage-access-restriction-rules-programmatically"></a>Gerenciar regras de restrição de acesso programaticamente

Você pode adicionar restrições de acesso programaticamente seguindo um destes procedimentos: 

* Use [o CLI do Azure](/cli/azure/webapp/config/access-restriction). Por exemplo:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* Use [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule). Por exemplo:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```
   > [!NOTE]
   > Trabalhar com marcas de serviço, cabeçalhos HTTP ou regras de várias fontes requer pelo menos a versão 5.7.0. Você pode verificar a versão do módulo instalado com: **Get-InstalledModule-Name AZ**

Você também pode definir valores manualmente seguindo um destes procedimentos:

* Use uma operação Put da [API REST do Azure](/rest/api/azure/) na configuração do aplicativo no Azure Resource Manager. O local para essas informações no Azure Resource Manager é:

  management.azure.com/subscriptions/**ID da assinatura**/resourceGroups/**grupos de recursos**/Providers/Microsoft.Web/sites/nome do **aplicativo Web**/config/Web? API-Version = 2020-06-01

* Use um modelo do Resource Manager. Por exemplo, você pode usar resources.azure.com e editar o bloco ipSecurityRestrictions para adicionar o JSON necessário.

  A sintaxe JSON para o exemplo anterior é:

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```
  A sintaxe JSON para um exemplo avançado usando a marca de serviço e a restrição de cabeçalho HTTP é:
  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "AzureFrontDoor.Backend",
          "tag": "ServiceTag",
          "action": "Allow",
          "priority": 100,
          "name": "Azure Front Door example",
          "headers": {
            "x-azure-fdid": [
              "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
            ]
          }
        }
      ]
    }
  }
  ```
## <a name="set-up-azure-functions-access-restrictions"></a>Configurar restrições de acesso Azure Functions

As restrições de acesso também estão disponíveis para aplicativos de funções com a mesma funcionalidade que os planos do serviço de aplicativo. Ao habilitar as restrições de acesso, você também desabilita o editor de código portal do Azure para qualquer IPs não permitido.

## <a name="next-steps"></a>Próximas etapas
[Restrições de acesso para Azure Functions](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Integração do gateway de aplicativo com pontos de extremidade de serviço](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[servicetags]: ../virtual-network/service-tags-overview.md