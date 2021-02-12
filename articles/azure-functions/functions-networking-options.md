---
title: Opções de rede do Azure Functions
description: Uma visão geral de todas as opções de rede disponíveis no Azure Functions.
author: cachai2
ms.topic: conceptual
ms.date: 1/21/2021
ms.author: cachai
ms.openlocfilehash: 2c3f207e98f574bb6c43f87d34b0a404e263e83c
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806991"
---
# <a name="azure-functions-networking-options"></a>Opções de rede do Azure Functions

Este artigo descreve os recursos de rede disponíveis nas opções de hospedagem para o Azure Functions. Todas as opções de rede a seguir oferecem a você uma capacidade de acessar recursos sem usar endereços roteáveis pela Internet ou restringir o acesso à Internet a um aplicativo de funções.

Os modelos de hospedagem têm níveis diferentes de isolamento de rede disponíveis. Escolher o correto ajuda a atender aos seus requisitos de isolamento de rede.

Você pode hospedar aplicativos de funções de duas maneiras:

* Escolha entre opções de plano executadas em uma infraestrutura multilocatário, com vários níveis de conectividade de rede virtual e opções de dimensionamento:
    * O [plano de Consumo](consumption-plan.md) é dimensionado dinamicamente em resposta à carga e oferece opções de isolamento de rede mínimas.
    * O [Plano Premium](functions-premium-plan.md) também é dimensionado dinamicamente e oferece isolamento de rede mais abrangente.
    * O [plano do Serviço de Aplicativo](dedicated-plan.md) do Azure opera em uma escala fixa e oferece isolamento de rede semelhante ao plano Premium.
* Execute funções em um [Ambiente do Serviço de Aplicativo](../app-service/environment/intro.md). Esse método implanta sua função em sua rede virtual e oferece controle e isolamento de rede total.

## <a name="matrix-of-networking-features"></a>Matriz de recursos de rede

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-access-restrictions"></a>Restrições de acesso de entrada

Você pode usar as restrições de acesso para definir uma lista ordenada de prioridade de endereços IP que têm acesso permitido ou negado ao seu aplicativo. A lista pode incluir endereços IPv4 e IPv6 ou sub-redes de rede virtual específicas usando [pontos de extremidade de serviço](#use-service-endpoints). Quando há uma ou mais entradas, há uma negação implícita de tudo no final da lista. As restrições de IP funcionam com todas as opções de hospedagem de função.

As restrições de acesso estão disponíveis no serviço [Premium](functions-premium-plan.md), [consumo](consumption-plan.md)e [aplicativo](dedicated-plan.md).

> [!NOTE]
> Com as restrições de rede em vigor, você pode implantar somente de dentro de sua rede virtual ou quando você colocou o endereço IP do computador que está usando para acessar o portal do Azure na lista de destinatários seguros. No entanto, você ainda pode gerenciar a função usando o Portal.

Para saber mais, confira [Restrições de acesso ao serviço do Serviço de Aplicativo do Azure](../app-service/app-service-ip-restrictions.md).

### <a name="use-service-endpoints"></a>Usar pontos de extremidade de serviço

Usando pontos de extremidade de serviço, você pode restringir o acesso às sub-redes selecionadas da rede virtual do Azure. Para restringir o acesso a uma sub-rede específica, crie uma regra de restrição com um tipo de **rede virtual** . Você pode selecionar a assinatura, a rede virtual e a sub-rede às quais você deseja permitir ou negar acesso. 

Se os pontos de extremidade de serviço ainda não estiverem habilitados com o Microsoft. Web para a sub-rede que você selecionou, eles serão habilitados automaticamente, a menos que você marque a caixa de seleção **ignorar pontos de extremidade do serviço Microsoft. Web ausentes** . O cenário em que você pode querer habilitar pontos de extremidade de serviço no aplicativo, mas não a sub-rede depende principalmente de você ter as permissões para habilitá-los na sub-rede. 

Se você precisar que outra pessoa habilite os pontos de extremidade de serviço na sub-rede, marque a caixa de seleção **ignorar pontos de extremidade do serviço Microsoft. Web ausentes** . Seu aplicativo será configurado para pontos de extremidade de serviço na antecipação de tê-los habilitados mais tarde na sub-rede. 

![Captura de tela do painel "Adicionar restrição de IP" com o tipo de rede virtual selecionado.](../app-service/media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Você não pode usar pontos de extremidade de serviço para restringir o acesso a aplicativos que são executados em um Ambiente do Serviço de Aplicativo. Quando seu aplicativo estiver em um Ambiente do Serviço de Aplicativo, você poderá controlar o acesso a ele aplicando regras de acesso IP. 

Para saber como configurar pontos de extremidade de serviço, consulte [estabelecer Azure Functions acesso ao site privado](functions-create-private-site-access.md).

## <a name="private-endpoint-connections"></a>Conexões de ponto de extremidade privado

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

Para chamar outros serviços que têm uma conexão de ponto de extremidade privada, como armazenamento ou barramento de serviço, certifique-se de configurar seu aplicativo para fazer [chamadas de saída para pontos de extremidade privados](#private-endpoints).

## <a name="virtual-network-integration"></a>Integração de rede virtual

A integração de rede virtual permite que seu aplicativo de funções acesse recursos dentro de uma rede virtual.
O Azure Functions dá suporte a dois tipos de integração de rede virtual:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

A integração de rede virtual no Azure Functions usa a infraestrutura compartilhada com aplicativos Web do Serviço de Aplicativo. Para saber mais sobre os dois tipos de integração de rede virtual, confira:

* [Integração de rede virtual regional](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integração de rede virtual exigida por gateway](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Para saber como configurar a integração de rede virtual, confira [Integrar um aplicativo de funções com uma rede virtual do Azure](functions-create-vnet.md).

## <a name="regional-virtual-network-integration"></a>Integração de rede virtual regional

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Conectar-se a recursos protegidos do ponto de extremidade de serviço

Para fornecer um nível mais alto de segurança, você pode restringir vários serviços do Azure a uma rede virtual usando pontos de extremidade de serviço. Em seguida, você deve integrar seu aplicativo de funções com essa rede virtual para acessar o recurso. Essa configuração tem suporte em todos os planos que dão suporte à integração de rede virtual.

Para obter mais detalhes, confira [Pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Restringir a sua conta de armazenamento a uma rede virtual 

Quando você cria um aplicativo de funções, é necessário criar ou vincular uma conta de Armazenamento do Azure de uso geral que dá ao armazenamento de Tabelas, Blobs e Filas.  Você pode substituir essa conta de armazenamento por uma que seja protegida por pontos de extremidade de serviço ou ponto de extremidades particular.  Atualmente, esse recurso só funciona com planos do Windows Premium.  Para configurar uma função com uma conta de armazenamento restrita a uma rede privada:

1. Crie uma função com uma conta de armazenamento que não tenha pontos de extremidade de serviço habilitados.
1. Configure a função para se conectar à sua rede virtual.
1. Crie ou configure uma conta de armazenamento diferente.  Essa será a conta de armazenamento que protegemos com os pontos de extremidade de serviço e conectamos nossa função.
1. [Crie um compartilhamento de arquivos](../storage/files/storage-how-to-create-file-share.md#create-file-share) na conta de armazenamento protegida.
1. Habilite os pontos de extremidade de serviço ou o Endpoint particular para a conta de armazenamento.  
    * Se você estiver usando conexões de ponto de extremidade privado, a conta de armazenamento precisará de um ponto de extremidade privado para os `file` `blob` subrecursos e.  Se estiver usando determinados recursos como Durable Functions, você também precisará `queue` e poderá `table` ser acessado por meio de uma conexão de ponto de extremidade privada.
    * Se estiver usando pontos de extremidade de serviço, habilite a sub-rede dedicada para seus aplicativos de funções para contas de armazenamento.
1. Adicional Copie o conteúdo do arquivo e do blob da conta de armazenamento do aplicativo de funções para a conta de armazenamento protegida e o compartilhamento de arquivos.
1. Copie a cadeia de conexão para esta conta de armazenamento.
1. Atualize as **configurações do aplicativo** em **configuração** para o aplicativo de funções para o seguinte:
    - `AzureWebJobsStorage` para a cadeia de conexão da conta de armazenamento protegida.
    - `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` para a cadeia de conexão da conta de armazenamento protegida.
    - `WEBSITE_CONTENTSHARE` para o nome do compartilhamento de arquivos criado na conta de armazenamento protegido.
    - Crie uma nova configuração com o nome `WEBSITE_CONTENTOVERVNET` e o valor de `1` .
    - Se a conta de armazenamento estiver usando conexões de ponto de extremidade privadas, verifique ou adicione as seguintes configurações
        - `WEBSITE_VNET_ROUTE_ALL` com um valor de `1` .
        - `WEBSITE_DNS_SERVER` com um valor de `168.63.129.16` 
1. Salve as configurações do aplicativo.  

O aplicativo de funções será reiniciado e agora será conectado a uma conta de armazenamento protegida.

## <a name="use-key-vault-references"></a>Usar referências de Key Vault

Você pode usar referências do Azure Key Vault para usar segredos do Azure Key Vault no seu aplicativo Azure Functions sem exigir alterações de código. O Azure Key Vault é um serviço que fornece gerenciamento centralizado de segredos, com controle total sobre políticas de acesso e histórico de auditoria.

Atualmente, [referências do Key Vault](../app-service/app-service-key-vault-references.md) não funcionarão se o cofre de chaves estiver protegido com pontos de extremidade de serviço. Para se conectar a um cofre de chaves usando a integração de rede virtual, você precisará chamar o Key Vault no código do aplicativo.

## <a name="virtual-network-triggers-non-http"></a>Gatilhos de rede virtual (não HTTP)

No momento, você pode usar funções de gatilho não HTTP de dentro de uma rede virtual de duas maneiras:

+ Execute seu aplicativo de funções em um plano Premium e habilite o suporte ao gatilho de rede virtual.
+ Execute seu aplicativo de funções em um plano do Serviço de Aplicativo ou Ambiente de Serviço de Aplicativo.

### <a name="premium-plan-with-virtual-network-triggers"></a>Plano Premium com gatilhos de rede virtual

Ao executar um plano Premium, você poderá conectar funções de gatilho não HTTP a serviços que são executados dentro de uma rede virtual. Para fazer isso, você deverá habilitar o suporte ao gatilho de rede virtual para seu aplicativo de funções. A configuração **monitoramento de escala de tempo de execução** é encontrada no [portal do Azure](https://portal.azure.com) em configurações de tempo de execução da função de **configuração**  >  .

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

Você também pode habilitar gatilhos de rede virtual usando o seguinte comando de CLI do Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

> [!TIP]
> Habilitar gatilhos de rede virtual pode afetar o desempenho do seu aplicativo, pois as instâncias do plano do serviço de aplicativo precisarão monitorar seus gatilhos para determinar quando dimensionar. Esse impacto é provavelmente muito pequeno.

Os gatilhos de rede virtual têm suporte na versão 2.x e superior do tempo de execução do Functions. Há suporte para os seguintes tipos de gatilho não HTTP.

| Extensão | Versão mínima |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 ou superior |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 ou superior|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 ou superior|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 ou superior|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 ou superior|

> [!IMPORTANT]
> Quando você habilita o suporte ao gatilho de rede virtual, somente os tipos de gatilho mostrados na tabela anterior são dimensionados dinamicamente com seu aplicativo. Você ainda pode usar gatilhos que não estão na tabela, mas eles não são dimensionados além da contagem de instâncias previamente inicializadas. Para obter a lista completa de gatilhos, confira [Gatilhos e associações](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>Plano do Serviço de Aplicativo e Ambiente do Serviço de Aplicativo com gatilhos de rede virtual

Quando seu aplicativo de funções é executado em um plano do Serviço de Aplicativo ou em um Ambiente do Serviço de Aplicativo, você pode usar funções de gatilho não HTTP. Para que suas funções sejam disparadas corretamente, você deverá estar conectado a uma rede virtual com acesso ao recurso definido na conexão do gatilho.

Por exemplo, suponha que você deseja configurar o Azure Cosmos DB para aceitar o tráfego somente de uma rede virtual. Nesse caso, você deve implantar seu aplicativo de funções em um plano do Serviço de Aplicativo que fornece integração de rede virtual com essa rede virtual. A integração permite que uma função seja disparada por esse recurso do Azure Cosmos DB.

## <a name="hybrid-connections"></a>Conexões Híbridas

[Conexões Híbridas](../azure-relay/relay-hybrid-connections-protocol.md) é um recurso da Retransmissão do Azure que você pode usar para acessar recursos do aplicativo em outras redes. Ele fornece acesso de seu aplicativo para um ponto de extremidade do aplicativo. Não é possível usá-lo para acessar seu aplicativo. O recurso Conexões Híbridas está disponível para funções executadas no Windows em todos os planos, exceto no plano de Consumo.

Conforme usado no Azure Functions, cada conexão híbrida se correlaciona com uma única combinação de host e de porta TCP. Isso significa que o ponto de extremidade de conexões híbridas pode estar em qualquer sistema operacional e em qualquer aplicativo, desde que você esteja acessando uma porta de escuta TCP. O recurso Conexões Híbridas não sabe e nem se importa com o protocolo de aplicativo ou o que você está acessando. Ele apenas fornece acesso à rede.

Para saber mais, confira a [Documentação do Serviço de Aplicativo para Conexões Híbridas](../app-service/app-service-hybrid-connections.md). Essas mesmas etapas de configuração dão suporte ao Azure Functions.

>[!IMPORTANT]
> Só há suporte para Conexões Híbridas em planos do Windows. Não há suporte para o Linux.

## <a name="outbound-ip-restrictions"></a>Restrições de IP de saída

As restrições de IP de saída estão disponíveis em um plano Premium, plano do Serviço de Aplicativo ou Ambiente do Serviço de Aplicativo. Você pode configurar as restrições de saída para a rede virtual em que seu Ambiente do Serviço de Aplicativo está implantado.

Quando você integra um aplicativo de funções em um plano Premium ou plano do Serviço de Aplicativo com uma rede virtual, o aplicativo ainda pode fazer chamadas de saída para a Internet por padrão. Ao adicionar a configuração de aplicativo `WEBSITE_VNET_ROUTE_ALL=1`, você força todo o tráfego de saída a ser enviado para sua rede virtual, em que as regras do grupo de segurança de rede podem ser usadas para restringir o tráfego.

## <a name="automation"></a>Automação
As APIs a seguir permitem gerenciar programaticamente integrações de rede virtual regional:

+ **CLI do Azure**: Use os [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) comandos para adicionar, listar ou remover uma integração de rede virtual regional.  
+ **Modelos de ARM**: a integração de rede virtual regional pode ser habilitada usando um modelo de Azure Resource Manager. Para obter um exemplo completo, consulte [este modelo de início rápido do Functions](https://azure.microsoft.com/resources/templates/101-function-premium-vnet-integration/).

## <a name="troubleshooting"></a>Solução de problemas

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre rede e o Azure Functions:

* [Siga o tutorial sobre como começar com a integração de rede virtual](./functions-create-vnet.md)
* [Leia as perguntas frequentes sobre rede do Functions](./functions-networking-faq.md)
* [Saiba mais sobre a integração de rede virtual com o Serviço de Aplicativo/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Saiba mais sobre as redes virtuais no Azure](../virtual-network/virtual-networks-overview.md)
* [Habilitar mais recursos de rede e de controle com Ambiente do Serviço de Aplicativo](../app-service/environment/intro.md)
* [Conectar-se aos recursos individuais locais sem alterações de firewall usando Conexões Híbridas](../app-service/app-service-hybrid-connections.md)
