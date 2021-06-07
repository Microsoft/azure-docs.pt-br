---
title: Configurar o túnel forçado
description: Saiba como habilitar o Ambiente do Serviço de Aplicativo para funcionar quando o tráfego de saída for forçado em túnel em sua rede virtual.
author: ccompy
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 95a4d00a27a0da363561f469b4c5e9e2ad16463c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97510491"
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Configurar seu Ambiente de Serviço de Aplicativo com tunelamento forçado

Um Ambiente do Serviço de Aplicativo (ASE) é uma implantação do Serviço de Aplicativo do Azure na Rede Virtual do Azure de um cliente. Muitos clientes configuram suas redes virtuais do Azure para extensões de suas redes locais com VPNs ou conexões ExpressRoute do Azure. O túnel forçado é quando você redireciona o tráfego associado de Internet para a VPN ou uma solução de virtualização. Normalmente, soluções de virtualização são usadas para inspecionar e auditar o tráfego de rede de saída. 

O ASE tem inúmeras dependências externas, que são descritas no documento [Arquitetura de rede do Ambiente do Serviço de Aplicativo][network]. Normalmente, todo o tráfego de dependência de saída do ASE deve percorrer o VIP que está provisionado com o ASE. Se você alterar o roteamento de tráfego de ou para o ASE sem seguir as informações a seguir, o ASE irá parar de funcionar.

Em uma rede virtual do Azure, o roteamento é feito com base na LPM (correspondência de prefixo mais longo). Se houver mais de uma rota com a mesma correspondência LPM, uma rota será selecionada com base em sua origem na seguinte ordem:

* UDR (rota definida pelo usuário)
* Rota BGP (quando o ExpressRoute é usado)
* Rota de sistema

Para saber mais sobre o roteamento em uma rede virtual, leia [Rotas definidas pelo usuário e encaminhamento de IP][routes]. 

Se quiser encaminhar seu tráfego de saída do ASE para algum lugar sem ser diretamente para a Internet, você tem as seguintes opções:

* Habilitar seu ASE para ter acesso direto à internet
* Configurar sua sub-rede do ASE para ignorar rotas BGP
* Configurar sua sub-rede do ASE para usar Pontos de Extremidade de Serviço para o SQL do Azure e o Armazenamento do Azure
* Adicione seus próprios IPs para o firewall do SQL do Azure ASE

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Habilitar o Ambiente do Serviço de Aplicativo para ter acesso direto à Internet

Para habilitar o ASE para ir diretamente para a Internet, mesmo se sua rede virtual do Azure estiver configurada com o ExpressRoute, você pode:

* Configurar o ExpressRoute para anunciar 0.0.0.0/0. Por padrão, ele encaminha todo o tráfego de saída local.
* Crie um UDR com um prefixo de endereço 0.0.0.0/0 e um tipo de próximo salto de Internet e aplique-o à sub-rede do ASE.

Se você fizer essas duas alterações, o tráfego destinado à Internet proveniente da sub-rede do Ambiente do Serviço de Aplicativo não será forçado a ir para a conexão ExpressRoute.

Se a rede já está roteando tráfego no local, você precisa criar uma sub-rede para hospedar seu ASE e configurar o UDR para ele antes de tentar implantar o ASE.  

> [!IMPORTANT]
> As rotas definidas em uma UDR devem ser específicas o suficiente para ter precedência sobre todas as rotas anunciadas pela configuração do ExpressRoute. O exemplo anterior usa o intervalo de endereços amplo 0.0.0.0/0. É possível que ele seja acidentalmente substituído pelos anúncios de rota que usam intervalos de endereços mais específicos.
>
> Os Ambientes do Serviço de Aplicativo não são compatíveis com configurações do ExpressRoute que façam anúncios de modo cruzado de rotas do caminho de emparelhamento público para o caminho de emparelhamento privado. Configurações do ExpressRoute com emparelhamento público configurado recebem anúncios de rota da Microsoft. Os anúncios contêm um grande conjunto de intervalos de endereços do Microsoft Azure. Se os intervalos de endereços forem anunciados de modo cruzado no caminho de emparelhamento privado, todos os pacotes de rede de saída da sub-rede do Ambiente do Serviço de Aplicativo serão encaminhados para uma infraestrutura de rede local do cliente. Por padrão, esse fluxo de rede não é compatível com os Ambientes do Serviço de Aplicativo. Uma solução para esse problema é parar as rotas de anúncios cruzados do caminho de emparelhamento público para o caminho de emparelhamento privado. Outra solução é habilitar o Ambiente do Serviço de Aplicativo para trabalhar em uma configuração de túnel forçado.

![Acesso direto à Internet][1]

## <a name="configure-your-ase-subnet-to-ignore-bgp-routes"></a>Configurar sua sub-rede do ASE para ignorar rotas BGP ## 

Você pode configurar sua sub-rede do ASE para ignorar rotas BGP.  Quando configurado para ignorar rotas BGP, o ASE será capaz de acessar suas dependências sem problemas.  No entanto, você precisará criar UDRs a fim de habilitar seus aplicativos a acessar recursos locais.

Para configurar sua sub-rede do ASE para ignorar rotas BGP:

* crie um UDR e atribua-o à sua sub-rede do ASE, se você ainda não tiver uma.
* No Portal do Azure, abra a interface do usuário da tabela de rota atribuída à sua sub-rede do ASE.  Selecione a Configuração.  Defina a Propagação de rota do gateway de rede virtual como Desabilitada.  Clique em Salvar. A documentação sobre a desativação disso está no documento [Criar uma tabela de rotas][routetable].

Após configurar a sub-rede do ASE para ignorar todas as rotas BGP, seus aplicativos não poderão mais alcançar localmente. Para permitir que os aplicativos acessem recursos locais, edite o UDR atribuído à sua sub-rede do ASE e adicione rotas aos intervalos de endereços locais. O tipo do próximo salto deve ser definido como o gateway de rede Virtual. 


## <a name="configure-your-ase-with-service-endpoints"></a>Configure seu ASE com Pontos de Extremidade de Serviço ##

Para encaminhar todo o tráfego de saída de seu ASE, exceto o que vai para o SQL Azure e o Armazenamento do Azure, execute as etapas a seguir:

1. Crie uma tabela de rota e atribua-a à sua sub-rede do ASE. Encontre os endereços que correspondem à sua região aqui: [Endereços de gerenciamento de Ambiente do Serviço de Aplicativo][management]. Crie rotas para esses endereços com um próximo salto da Internet. Essas rotas são necessárias porque o tráfego do gerenciamento de entrada do Ambiente do Serviço de Aplicativo deve responder do mesmo endereço do qual ele foi enviado.   

2. Habilite os Pontos de Extremidade de Serviço com o SQL do Azure e o Armazenamento do Azure com sua sub-rede do ASE.  Após essa etapa estar concluída, você pode configurar sua rede virtual com o túnel forçado.

Para obter detalhes sobre como implantar um ASE com um modelo, leia [Criando um Ambiente do Serviço de Aplicativo usando um modelo][template].

Os Pontos de Extremidade de Serviço permitem restringir o acesso aos serviços de vários locatários para um conjunto de sub-redes e redes virtuais do Azure. Você pode saber mais sobre os Pontos de Extremidade de Serviço na documentação [Pontos de Extremidade de Serviço de Rede Virtual][serviceendpoints]. 

Quando você habilita Pontos de Extremidade de Serviço em um recurso, existem rotas criadas com prioridade mais alta que todas as outras rotas. Se você usar Pontos de Extremidade de Serviço com um ASE em túnel forçado, o SQL do Azure e o tráfego de gerenciamento do Armazenamento do Azure não é forçado em túnel. O outro tráfego de dependência do ASE é forçado em túnel e não pode ser perdido senão o ASE não funcionará corretamente.

Quando os Pontos de Extremidade de Serviço estão habilitados em uma sub-rede com uma instância do SQL do Azure, todas as instâncias do SQL do Azure conectadas à sub-rede devem ter os Pontos de Extremidade de Serviço habilitados. Se quiser acessar várias instâncias do SQL do Azure da mesma sub-rede, você não pode habilitar os Pontos de Extremidade de Serviço em uma instância do SQL do Azure e não em outra.  O Armazenamento do Azure não possuem o mesmo comportamento do SQL do Azure.  Ao habilitar os Pontos de Extremidade de Serviço com o Armazenamento do Azure, você bloqueia o acesso a esse recurso de sua sub-rede, mas ainda pode acessar outras contas de Armazenamento do Azure, mesmo se elas não tiverem os Pontos de Extremidade de Serviço habilitados.  

Se você configurar o túnel forçado com um dispositivo de filtro de rede, lembre-se de que o ASE tem dependências, além do SQL do Azure e do Armazenamento do Azure. Se o tráfego for bloqueado para essas dependências, o ASE não funcionará corretamente.

![Túnel forçado com pontos de extremidade de serviço][2]

## <a name="add-your-own-ips-to-the-ase-azure-sql-firewall"></a>Adicione seus próprios IPs para o firewall do SQL do Azure ASE ##

Para encapsular todo o tráfego de saída de seu ASE, exceto o que vai para o Armazenamento do Azure, execute as etapas a seguir:

1. Crie uma tabela de rota e atribua-a à sua sub-rede do ASE. Encontre os endereços que correspondem à sua região aqui: [Endereços de gerenciamento de Ambiente do Serviço de Aplicativo][management]. Crie rotas para esses endereços com um próximo salto da Internet. Essas rotas são necessárias porque o tráfego do gerenciamento de entrada do Ambiente do Serviço de Aplicativo deve responder do mesmo endereço do qual ele foi enviado. 

2. Habilite os Pontos de Extremidade de Serviço com o Armazenamento do Azure com sua sub-rede do ASE

3. Obtenha os endereços que serão usados para todo o tráfego de saída do seu Ambiente de Serviço de Aplicativo com a Internet. Se você estiver roteando o tráfego no local, esses endereços serão seus IPs de gateway ou NATs. Se desejar rotear o tráfego de saída do Ambiente do Serviço de Aplicativo por meio de uma NVA, o endereço de saída será o IP público da NVA.

4. _Para definir os endereços de saída em um Ambiente do Serviço de Aplicativo existente:_ Acesse resources.azure.com e vá para Subscription/\<subscription id>/resourceGroups/\<ase resource group>/providers/Microsoft.Web/hostingEnvironments/\<ase name>. Em seguida, será possível ver o JSON que descreve o Ambiente do Serviço de Aplicativo. Certifique-se de que consta **leitura/gravação** na parte superior. Selecione **Editar**. Role até a parte inferior. Altere o valor **userWhitelistedIpRanges** de **nulo** para algo semelhante ao seguinte. Use os endereços que você deseja definir como o intervalo de endereços de saída. 

    ```json
    "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"]
    ```

   Selecione **PUT** na parte superior. Essa opção dispara uma operação de dimensionamento no Ambiente do Serviço de Aplicativo e ajusta o firewall.

_Para criar seu ASE com os endereços de saída_: siga as instruções em [Criar um Ambiente do Serviço de Aplicativo com um modelo][template] e baixe o modelo apropriado.  Edite a seção "recursos" no arquivo azuredeploy.json, mas não no bloco "propriedades" e inclua uma linha para **userWhitelistedIpRanges** com seus valores.

```json
"resources": [
    {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/hostingEnvironments",
        "name": "[parameters('aseName')]",
        "kind": "ASEV2",
        "location": "[parameters('aseLocation')]",
        "properties": {
            "name": "[parameters('aseName')]",
            "location": "[parameters('aseLocation')]",
            "ipSslAddressCount": 0,
            "internalLoadBalancingMode": "[parameters('internalLoadBalancingMode')]",
            "dnsSuffix" : "[parameters('dnsSuffix')]",
            "virtualNetwork": {
                "Id": "[parameters('existingVnetResourceId')]",
                "Subnet": "[parameters('subnetName')]"
            },
            "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]
        }
    }
]
```

Essas alterações enviam tráfego para o Armazenamento do Azure diretamente a partir do ASE e permitem o acesso para o SQL do Azure de endereços adicionais que não sejam o VIP do ASE.

   ![Túnel forçado com lista de permitidos do SQL][3]

## <a name="preventing-issues"></a>Como evitar problemas ##

Se a comunicação entre o ASE e suas dependências for interrompida, o ASE ficará não íntegro.  Se ele permanecer não íntegro por muito tempo, o ASE ficará suspenso. Para cancelar a suspensão do ASE, siga as instruções em seu portal do ASE.

Além de simplesmente interromper a comunicação, você pode afetar adversamente o ASE introduzindo muita latência. Muito latência pode ocorrer se o ASE estiver muito longe de sua rede local.  Exemplos de muito longe incluiriam atravessar um oceano ou continente para acessar a rede local. A latência também pode ser apresentada devido a congestionamento da intranet ou restrições de largura de banda de saída.


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/asedependencies.png
[2]: ./media/forced-tunnel-support/forcedtunnelserviceendpoint.png
[3]: ./media/forced-tunnel-support/forcedtunnelexceptstorage.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[routetable]: ../../virtual-network/manage-route-table.md#create-a-route-table
