---
title: Recursos de rede
description: Saiba mais sobre os recursos de rede no serviço Azure App e saiba quais recursos são necessários para segurança ou outras funcionalidades.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/26/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 9ba85ecfe2b57ceb1eed5c51929107a95f5a4669
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889137"
---
# <a name="app-service-networking-features"></a>Recursos de rede do serviço de aplicativo

Você pode implantar aplicativos no serviço de Azure App de várias maneiras. Por padrão, os aplicativos hospedados no serviço de aplicativo são acessíveis diretamente pela Internet e podem acessar apenas pontos de extremidade hospedados na Internet. Mas, para muitos aplicativos, você precisa controlar o tráfego de rede de entrada e saída. Há vários recursos no serviço de aplicativo para ajudá-lo a atender a essas necessidades. O desafio é saber qual recurso usar para resolver um determinado problema. Este artigo o ajudará a determinar qual recurso usar, com base em alguns casos de uso de exemplo.

Há dois tipos de implantação principais para Azure App serviço: 
- O serviço público multilocatário hospeda planos do serviço de aplicativo nas SKUs de preços gratuita, compartilhada, básica, Standard, Premium, PremiumV2 e PremiumV3. 
- Os Ambiente do Serviço de Aplicativo de locatário único (ASE) hospedam planos de serviço de aplicativo SKU isolados diretamente em sua rede virtual do Azure. 

Os recursos que você usar dependerão se você estiver no serviço multilocatário ou em um ASE. 

## <a name="multitenant-app-service-networking-features"></a>Recursos de rede do serviço de aplicativo multilocatário 

Azure App serviço é um sistema distribuído. As funções que tratam solicitações HTTP ou HTTPS de entrada são chamadas de *front-ends*. As funções que hospedam a carga de trabalho do cliente são chamadas de *trabalhadores*. Todas as funções em uma implantação do serviço de aplicativo existem em uma rede multilocatário. Como há muitos clientes diferentes na mesma unidade de escala do serviço de aplicativo, você não pode conectar a rede do serviço de aplicativo diretamente à sua rede. 

Em vez de conectar as redes, você precisa de recursos para lidar com os vários aspectos da comunicação do aplicativo. Os recursos que manipulam solicitações *para* seu aplicativo não podem ser usados para resolver problemas quando você faz chamadas *de* seu aplicativo. Da mesma forma, os recursos que resolvem problemas de chamadas de seu aplicativo não podem ser usados para resolver problemas em seu aplicativo.  

| Recursos de entrada | Recursos de saída |
|---------------------|-------------------|
| Endereço atribuído ao aplicativo | Conexões Híbridas |
| Restrições de acesso | Integração VNET exigida pelo gateway |
| Pontos de extremidade de serviço | Integração VNet |
| Pontos de extremidade privados ||

Além das exceções indicadas, você pode usar todos esses recursos juntos. Você pode misturar os recursos para resolver seus problemas.

## <a name="use-cases-and-features"></a>Casos de uso e recursos

Para qualquer caso de uso específico, pode haver algumas maneiras de resolver o problema. A escolha do melhor recurso às vezes vai além do próprio caso de uso. Os seguintes casos de uso de entrada sugerem como usar os recursos de rede do serviço de aplicativo para resolver problemas com o controle de tráfego indo para seu aplicativo:
 
| Caso de uso de entrada | Recurso |
|---------------------|-------------------|
| Dar suporte às necessidades de SSL baseado em IP para seu aplicativo | Endereço atribuído ao aplicativo |
| Suporte ao endereço de entrada dedicado não compartilhado para seu aplicativo | Endereço atribuído ao aplicativo |
| Restringir o acesso ao seu aplicativo de um conjunto de endereços bem definidos | Restrições de acesso |
| Restringir o acesso ao seu aplicativo de recursos em uma rede virtual | Pontos de extremidade de serviço </br> ILB ASE </br> Pontos de extremidade privados |
| Expor seu aplicativo em um IP privado em sua rede virtual | ILB ASE </br> Pontos de extremidade privados </br> IP privado para o tráfego de entrada em uma instância do gateway de aplicativo com pontos de extremidade de serviço |
| Proteger seu aplicativo com um WAF (firewall do aplicativo Web) | Gateway de aplicativo e ASE ILB </br> Gateway de aplicativo com pontos de extremidade privados </br> Gateway de Aplicativo com pontos de extremidade de serviço </br> Porta frontal do Azure com restrições de acesso |
| Balancear a carga do tráfego para seus aplicativos em regiões diferentes | Porta frontal do Azure com restrições de acesso | 
| Balancear a carga do tráfego na mesma região | [Gateway de Aplicativo com pontos de extremidade de serviço][appgwserviceendpoints] | 

Os seguintes casos de uso de saída sugerem como usar os recursos de rede do serviço de aplicativo para resolver as necessidades de acesso de saída para seu aplicativo:

| Caso de uso de saída | Recurso |
|---------------------|-------------------|
| Acessar recursos em uma rede virtual do Azure na mesma região | Integração VNet </br> ASE |
| Acessar recursos em uma rede virtual do Azure em uma região diferente | Integração VNET exigida pelo gateway </br> ASE e emparelhamento de rede virtual |
| Acessar recursos protegidos com pontos de extremidade de serviço | Integração VNet </br> ASE |
| Acessar recursos em uma rede privada que não está conectada ao Azure | Conexões Híbridas |
| Acessar recursos nos circuitos do Azure ExpressRoute | Integração VNet </br> ASE | 
| Proteger o tráfego de saída de seu aplicativo Web | Integração VNet e grupos de segurança de rede </br> ASE | 
| Rotear o tráfego de saída do seu aplicativo Web | Integração VNet e tabelas de rotas </br> ASE | 


### <a name="default-networking-behavior"></a>Comportamento de rede padrão

Azure App unidades de escala de serviço dão suporte a muitos clientes em cada implantação. Os planos de SKU gratuito e compartilhado hospedam cargas de trabalho do cliente em trabalhos multilocatários. Os planos básico e superior hospedam cargas de trabalho do cliente que são dedicadas a apenas um plano do serviço de aplicativo. Se você tiver um plano do serviço de aplicativo padrão, todos os aplicativos nesse plano serão executados no mesmo trabalho. Se você escalar horizontalmente o trabalho, todos os aplicativos nesse plano do serviço de aplicativo serão replicados em um novo trabalhador para cada instância em seu plano do serviço de aplicativo. 

#### <a name="outbound-addresses"></a>Endereços de saída

As VMs de trabalho são divididas em grande parte pelos planos do serviço de aplicativo. Os planos gratuito, compartilhado, básico, Standard e Premium usam o mesmo tipo de VM de trabalho. O plano PremiumV2 usa outro tipo de VM. O PremiumV3 usa ainda outro tipo de VM. Ao alterar a família de VMs, você obtém um conjunto diferente de endereços de saída. Se você dimensionar de Standard para PremiumV2, seus endereços de saída serão alterados. Se você dimensionar de PremiumV2 para PremiumV3, seus endereços de saída serão alterados. Em algumas unidades de escala mais antigas, os endereços de entrada e de saída serão alterados quando você dimensionar de Standard para PremiumV2. 

Há vários endereços que são usados para chamadas de saída. Os endereços de saída usados pelo seu aplicativo para fazer chamadas de saída são listados nas propriedades do seu aplicativo. Esses endereços são compartilhados por todos os aplicativos em execução na mesma família de VMs de trabalho na implantação do serviço de aplicativo. Se você quiser ver todos os endereços que seu aplicativo pode usar em uma unidade de escala, há a propriedade chamada `possibleOutboundAddresses` que os listará. 

![Captura de tela que mostra as propriedades do aplicativo.](media/networking-features/app-properties.png)

O serviço de aplicativo tem vários pontos de extremidade que são usados para gerenciar o serviço.  Esses endereços são publicados em um documento separado e também estão na `AppServiceManagement` marca de serviço IP. A `AppServiceManagement` marca é usada somente em ambientes de serviço de aplicativo em que você precisa permitir esse tráfego. Os endereços de entrada do serviço de aplicativo são controlados na `AppService` marca de serviço de IP. Não há nenhuma marca de serviço IP que contenha os endereços de saída usados pelo serviço de aplicativo. 

![Diagrama que mostra o tráfego de entrada e de saída do serviço de aplicativo.](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Endereço atribuído ao aplicativo 

O recurso de endereço atribuído ao aplicativo é um offshoot do recurso SSL baseado em IP. Você o acessa Configurando o SSL com seu aplicativo. Você pode usar esse recurso para chamadas SSL baseadas em IP. Você também pode usá-lo para dar a seu aplicativo um endereço que só tenha. 

![Diagrama que ilustra o endereço atribuído ao aplicativo.](media/networking-features/app-assigned-address.png)

Quando você usa um endereço atribuído ao aplicativo, seu tráfego ainda passa pelas mesmas funções de front-end que manipulam todo o tráfego de entrada na unidade de escala do serviço de aplicativo. Mas o endereço atribuído ao seu aplicativo é usado somente pelo seu aplicativo. Casos de uso para este recurso:

* Dê suporte às necessidades de SSL baseado em IP para seu aplicativo.
* Defina um endereço dedicado para seu aplicativo que não é compartilhado.

Para saber como definir um endereço em seu aplicativo, consulte [Adicionar um certificado TLS/SSL no serviço de Azure app][appassignedaddress]. 

### <a name="access-restrictions"></a>Restrições de acesso 

As restrições de acesso permitem filtrar solicitações de *entrada* . A ação de filtragem ocorre nas funções de front-end que são upstream das funções de trabalho em que seus aplicativos estão em execução. Como as funções de front-end são upstream dos trabalhadores, você pode considerar as restrições de acesso como proteção de nível de rede para seus aplicativos. 

Esse recurso permite que você crie uma lista de regras de permissão e negação que são avaliadas em ordem de prioridade. É semelhante ao recurso NSG (grupo de segurança de rede) na rede do Azure. Você pode usar esse recurso em um ASE ou no serviço multilocatário. Ao usá-lo com um ASE ILB ou um ponto de extremidade privado, você pode restringir o acesso de blocos de endereços privados.
> [!NOTE]
> Até 512 regras de restrição de acesso podem ser configuradas por aplicativo. 

![Diagrama que ilustra as restrições de acesso.](media/networking-features/access-restrictions.png)

#### <a name="ip-based-access-restriction-rules"></a>Regras de restrição de acesso baseado em IP

O recurso de restrições de acesso baseado em IP ajuda quando você deseja restringir os endereços IP que podem ser usados para acessar seu aplicativo. Há suporte para IPv4 e IPv6. Alguns casos de uso para esse recurso:
* Restrinja o acesso ao seu aplicativo de um conjunto de endereços bem definidos. 
* Restrinja o acesso ao tráfego proveniente de um serviço de balanceamento de carga externo ou de outros dispositivos de rede com endereços IP de egresso conhecidos. 

Para saber como habilitar esse recurso, consulte [Configurando restrições de acesso][iprestrictions].

> [!NOTE]
> As regras de restrição de acesso baseado em IP tratam apenas dos intervalos de endereços de rede virtual quando seu aplicativo está em um Ambiente do Serviço de Aplicativo. Se seu aplicativo estiver no serviço multilocatário, você precisará usar [pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) para restringir o tráfego para selecionar sub-redes em sua rede virtual.

#### <a name="access-restriction-rules-based-on-service-endpoints"></a>Regras de restrição de acesso baseadas em pontos de extremidade de serviço 

Os pontos de extremidade de serviço permitem bloquear o acesso de *entrada* ao seu aplicativo para que o endereço de origem deva vir de um conjunto de sub-redes que você selecionar. Esse recurso funciona junto com as restrições de acesso de IP. Os pontos de extremidade de serviço não são compatíveis com a depuração remota. Se você quiser usar a depuração remota com seu aplicativo, o cliente não poderá estar em uma sub-rede que tenha pontos de extremidade de serviço habilitados. O processo de configuração de pontos de extremidade de serviço é semelhante ao processo de configuração de restrições de acesso IP. Você pode criar uma lista de permissão/negação de regras de acesso que inclua endereços públicos e sub-redes em suas redes virtuais. 

Alguns casos de uso para esse recurso:

* Configure um gateway de aplicativo com seu aplicativo para bloquear o tráfego de entrada para seu aplicativo.
* Restrinja o acesso ao seu aplicativo aos recursos em sua rede virtual. Esses recursos podem incluir VMs, ASEs ou até outros aplicativos que usam a integração de VNet. 

![Diagrama que ilustra o uso de pontos de extremidade de serviço com o gateway de aplicativo.](media/networking-features/service-endpoints-appgw.png)

Para saber mais sobre como configurar pontos de extremidade de serviço com seu aplicativo, consulte [Azure app restrições de acesso de serviço][serviceendpoints].

#### <a name="access-restriction-rules-based-on-service-tags"></a>Regras de restrição de acesso baseadas em marcas de serviço

As [marcas de serviço do Azure][servicetags] são conjuntos bem definidos de endereços IP para os serviços do Azure. As marcas de serviço agrupam os intervalos de IP usados em vários serviços do Azure e, muitas vezes, também estão no escopo de regiões específicas. Isso permite que você filtre o tráfego de *entrada* de serviços específicos do Azure. 

Para obter uma lista completa de marcas e mais informações, visite o link de marca de serviço acima. Para saber como habilitar esse recurso, consulte [Configurando restrições de acesso][iprestrictions].

#### <a name="http-header-filtering-for-access-restriction-rules"></a>Filtragem de cabeçalho HTTP para regras de restrição de acesso

Para cada regra de restrição de acesso, você pode adicionar filtragem de cabeçalho http adicional. Isso permite inspecionar ainda mais a solicitação e o filtro de entrada com base em valores de cabeçalho HTTP específicos. Cada cabeçalho pode ter até 8 valores por regra. Atualmente, há suporte para a seguinte lista de cabeçalhos http: 
* X-Forwarded-For
* X-Forwarded-Host
* X-Azure-FDID
* X-FD-HealthProbe

Alguns casos de uso para filtragem de cabeçalho HTTP são:
* Restringir o acesso ao tráfego de servidores proxy encaminhando o nome do host
* Restringir o acesso a uma instância específica do Azure front door com uma regra de marca de serviço e a restrição de cabeçalho X-Azure-FDID

### <a name="private-endpoint"></a>Ponto de extremidade privado

O ponto de extremidade privado é uma interface de rede que conecta você de forma privada e segura ao seu aplicativo Web pelo link privado do Azure. O ponto de extremidade privado usa um endereço IP privado de sua rede virtual, colocando efetivamente o aplicativo Web em sua rede virtual. Esse recurso é apenas para fluxos de *entrada* para seu aplicativo Web.
Para obter mais informações, consulte [usando pontos de extremidade privados para o aplicativo Web do Azure][privateendpoints].

Alguns casos de uso para esse recurso:

* Restrinja o acesso ao seu aplicativo de recursos em uma rede virtual. 
* Expor seu aplicativo em um IP privado em sua rede virtual. 
* Proteja seu aplicativo com um WAF.

Os pontos de extremidade privados impedem a vazamento de dados porque a única coisa que você pode acessar por meio do ponto de extremidade privado é o aplicativo com o qual ele está configurado. 
 
### <a name="hybrid-connections"></a>Conexões Híbridas

O serviço de aplicativo Conexões Híbridas permite que seus aplicativos façam chamadas de *saída* para pontos de extremidade TCP especificados. O ponto de extremidade pode ser local, em uma rede virtual ou em qualquer lugar que permita o tráfego de saída para o Azure na porta 443. Para usar o recurso, você precisa instalar um agente de retransmissão chamado Gerenciador de Conexões Híbridas em um host Windows Server 2012 ou mais recente. Gerenciador de Conexões Híbridas precisa ser capaz de acessar a retransmissão do Azure na porta 443. Você pode baixar Gerenciador de Conexões Híbridas da interface do usuário Conexões Híbridas do serviço de aplicativo no Portal. 

![Diagrama que mostra o Conexões Híbridas fluxo de rede.](media/networking-features/hybrid-connections.png)

O serviço de aplicativo Conexões Híbridas se baseia no recurso de Conexões Híbridas de retransmissão do Azure. O serviço de aplicativo usa uma forma especializada do recurso que dá suporte apenas para fazer chamadas de saída de seu aplicativo para um host e uma porta TCP. Esse host e a porta precisam apenas ser resolvidos no host em que o Gerenciador de Conexões Híbridas está instalado. 

Quando o aplicativo, no serviço de aplicativo, faz uma pesquisa de DNS no host e na porta definida em sua conexão híbrida, o tráfego é redirecionado automaticamente para passar pela conexão híbrida e fora de Gerenciador de Conexões Híbridas. Para saber mais, consulte [conexões híbridas do serviço de aplicativo][hybridconn].

Esse recurso é comumente usado para:

* Acesse recursos em redes privadas que não estão conectadas ao Azure com uma VPN ou ExpressRoute.
* Dê suporte à migração de aplicativos locais para o serviço de aplicativo sem a necessidade de mover bancos de dados de suporte.  
* Fornecer acesso com segurança aprimorada a um único host e porta por conexão híbrida. A maioria dos recursos de rede abre o acesso a uma rede. Com Conexões Híbridas, você só pode acessar o único host e a porta.
* Aborde cenários não cobertos por outros métodos de conectividade de saída.
* Execute o desenvolvimento no serviço de aplicativo de forma que permita que os aplicativos usem facilmente os recursos locais. 

Como esse recurso permite o acesso a recursos locais sem um buraco de firewall de entrada, ele é popular com os desenvolvedores. Os outros recursos de rede do serviço de aplicativo de saída estão relacionados à rede virtual do Azure. Conexões Híbridas não depende de passar por uma rede virtual. Ele pode ser usado para uma variedade maior de necessidades de rede. 

Observe que o serviço de aplicativo Conexões Híbridas não está ciente do que você está fazendo sobre ele. Portanto, você pode usá-lo para acessar um banco de dados, um serviço Web ou um soquete TCP arbitrário em um mainframe. Essencialmente, o recurso encapsula pacotes TCP. 

Conexões Híbridas é popular para desenvolvimento, mas também é usado em aplicativos de produção. É ótimo para acessar um serviço Web ou banco de dados, mas não é apropriado para situações que envolvem a criação de várias conexões. 

### <a name="gateway-required-vnet-integration"></a>Integração VNET exigida pelo gateway 

Gateway-a integração VNet do serviço de aplicativo necessária permite que seu aplicativo faça solicitações de *saída* em uma rede virtual do Azure. O recurso funciona conectando o host em que seu aplicativo está sendo executado em um gateway de rede virtual em sua rede virtual usando uma VPN ponto a site. Quando você configura o recurso, seu aplicativo obtém um dos endereços ponto a site atribuídos a cada instância. Esse recurso permite que você acesse recursos em redes virtuais clássicas ou Azure Resource Managers em qualquer região. 

![Diagrama que ilustra a integração VNet exigida pelo Gateway.](media/networking-features/gw-vnet-integration.png)

Esse recurso resolve o problema de acessar recursos em outras redes virtuais. Ele pode até ser usado para se conectar por meio de uma rede virtual a outras redes virtuais ou locais. Ele não funciona com redes virtuais conectadas ao ExpressRoute, mas funciona com redes conectadas VPN site a site. Normalmente, é inapropriado usar esse recurso de um aplicativo em um Ambiente do Serviço de Aplicativo (ASE) porque o ASE já está em sua rede virtual. Casos de uso para este recurso:

* Acesse recursos em IPs privados em suas redes virtuais do Azure. 
* Acesse recursos locais se houver uma VPN site a site. 
* Acesse recursos em redes virtuais emparelhadas. 

Quando esse recurso estiver habilitado, seu aplicativo usará o servidor DNS com o qual a rede virtual de destino está configurada. Para obter mais informações sobre esse recurso, consulte [integração VNet do serviço de aplicativo][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Integração VNet

O gateway-a integração VNet necessária é útil, mas não resolve o problema de acessar recursos no ExpressRoute. Além de precisar alcançar entre conexões de ExpressRoute, há a necessidade de os aplicativos serem capazes de fazer chamadas para serviços protegidos pelo ponto de extremidade de serviço. Outro recurso de integração VNet pode atender a essas necessidades. 

O novo recurso de integração VNet permite que você coloque o back-end de seu aplicativo em uma sub-rede em uma rede virtual do Resource Manager na mesma região que o seu aplicativo. Esse recurso não está disponível em um Ambiente do Serviço de Aplicativo, que já está em uma rede virtual. Casos de uso para este recurso:

* Acesse recursos em redes virtuais do Resource Manager na mesma região.
* Acesse recursos que são protegidos com pontos de extremidade de serviço. 
* Acesse recursos que são acessíveis em conexões de ExpressRoute ou VPN.
* Ajudar a proteger todo o tráfego de saída. 
* Forçar túnel de todo o tráfego de saída. 

![Diagrama que ilustra a integração VNet.](media/networking-features/vnet-integration.png)

Para saber mais, consulte [integração VNet do serviço de aplicativo][vnetintegration].

### <a name="app-service-environment"></a>Ambiente do Serviço de Aplicativo 

Um Ambiente do Serviço de Aplicativo (ASE) é uma implantação de locatário único do serviço de Azure App que é executado em sua rede virtual. Alguns casos, para esse recurso:

* Acesse recursos em sua rede virtual.
* Acesse recursos no ExpressRoute.
* Expor seus aplicativos com um endereço privado em sua rede virtual. 
* Acesse recursos em pontos de extremidade de serviço. 

Com um ASE, você não precisa usar recursos como a integração VNet ou pontos de extremidade de serviço porque o ASE já está em sua rede virtual. Se você quiser acessar recursos como o SQL ou o armazenamento do Azure em pontos de extremidade de serviço, habilite os pontos de extremidade de serviço na sub-rede do ASE. Se você quiser acessar recursos na rede virtual, não precisará fazer nenhuma configuração adicional. Se você quiser acessar recursos no ExpressRoute, você já está na rede virtual e não precisa configurar nada no ASE ou nos aplicativos nele. 

Como os aplicativos em um ASE ILB podem ser expostos em um endereço IP privado, você pode facilmente adicionar dispositivos WAF para expor apenas os aplicativos que você deseja para a Internet e ajudar a manter o restante seguro. Esse recurso pode ajudar a facilitar o desenvolvimento de aplicativos multicamadas. 

No momento, algumas coisas não são possíveis do serviço multilocatário, mas são possíveis de um ASE. Estes são alguns exemplos:

* Expor seus aplicativos em um endereço IP privado.
* Ajudar a proteger todo o tráfego de saída com controles de rede que não fazem parte de seu aplicativo.
* Hospede seus aplicativos em um serviço de locatário único. 
* Escale verticalmente para muitas outras instâncias do que é possível no serviço multilocatário. 
* Carregue certificados de cliente de autoridade de certificação privada para uso por seus aplicativos com pontos de extremidade protegidos por AC privada.
* Force o TLS 1,1 em todos os aplicativos hospedados no sistema sem qualquer capacidade de desabilitá-lo no nível do aplicativo. 
* Forneça um endereço de saída dedicado para todos os aplicativos em seu ASE que não são compartilhados com os clientes. 

![Diagrama que ilustra um ASE em uma rede virtual.](media/networking-features/app-service-environment.png)

O ASE fornece a melhor história sobre hospedagem de aplicativo isolada e dedicada, mas envolve alguns desafios de gerenciamento. Algumas coisas a considerar antes de usar um ASE operacional:
 
 * Um ASE é executado dentro de sua rede virtual, mas tem dependências fora da rede virtual. Essas dependências devem ser permitidas. Para obter mais informações, consulte [considerações de rede para um ambiente do serviço de aplicativo][networkinfo].
 * Um ASE não é dimensionado imediatamente como o serviço multilocatário. Você precisa prever as necessidades de dimensionamento em vez de dimensionar reativamente. 
 * Um ASE tem um custo antecipado maior. Para aproveitar ao máximo seu ASE, você deve planejar colocar várias cargas de trabalho em um ASE, em vez de usá-lo para pequenos esforços.
 * Os aplicativos em um ASE não podem restringir seletivamente o acesso a alguns aplicativos no ASE e não a outros.
 * Um ASE está em uma sub-rede e todas as regras de rede se aplicam a todo o tráfego de e para esse ASE. Se você quiser atribuir regras de tráfego de entrada para apenas um aplicativo, use as restrições de acesso. 

## <a name="combining-features"></a>Combinando recursos 

Os recursos indicados para o serviço multilocatário podem ser usados juntos para resolver casos de uso mais elaborados. Dois dos casos de uso mais comuns são descritos aqui, mas são apenas exemplos. Ao entender o que os diversos recursos fazem, você pode atender a quase todas as suas necessidades de arquitetura do sistema.

### <a name="place-an-app-into-a-virtual-network"></a>Coloque um aplicativo em uma rede virtual

Você pode imaginar como colocar um aplicativo em uma rede virtual. Se você colocar seu aplicativo em uma rede virtual, os pontos de extremidade de entrada e saída para o aplicativo estarão dentro da rede virtual. Um ASE é a melhor maneira de resolver esse problema. Mas você pode atender à maioria das suas necessidades no serviço multilocatário combinando recursos. Por exemplo, você pode hospedar aplicativos somente de intranet com endereços de entrada e saída privados:

* Criar um gateway de aplicativo com endereços de entrada e saída privados.
* Proteger o tráfego de entrada para seu aplicativo com pontos de extremidade de serviço. 
* Usando o novo recurso de integração de VNet para que o back-end do seu aplicativo esteja em sua rede virtual. 

Esse estilo de implantação não lhe dará um endereço dedicado para o tráfego de saída para a Internet ou a capacidade de bloquear todo o tráfego de saída de seu aplicativo. Ele fornecerá a você uma grande parte do que você só obteria com um ASE. 

### <a name="create-multitier-applications"></a>Criar aplicativos multicamadas

Um aplicativo multicamadas é um aplicativo no qual os aplicativos de back-end da API podem ser acessados somente da camada de front-end. Há duas maneiras de criar um aplicativo multicamadas. Comece usando a integração VNet para conectar seu aplicativo Web front-end a uma sub-rede em uma rede virtual. Isso permitirá que seu aplicativo Web faça chamadas em sua rede virtual. Depois que o aplicativo de front-end estiver conectado à rede virtual, você precisará decidir como bloquear o acesso ao aplicativo de API. Você pode:

* Hospede o front-end e o aplicativo de API no mesmo ILB ASE e expor o aplicativo de front-end para a Internet usando um gateway de aplicativo.
* Hospede o front-end no serviço multilocatário e o back-end em um ASE ILB.
* Hospede o front-end e o aplicativo de API no serviço multilocatário.

Se você estiver hospedando o front-end e o aplicativo de API para um aplicativo multicamadas, você poderá:

- Expor seu aplicativo de API usando pontos de extremidade privados em sua rede virtual:

  ![Diagrama que ilustra o uso de pontos de extremidade privados em um aplicativo de duas camadas.](media/networking-features/multi-tier-app-private-endpoint.png)

- Use pontos de extremidade de serviço para garantir que o tráfego de entrada para seu aplicativo de API Venha apenas da sub-rede usada pelo aplicativo Web de front-end:

  ![Diagrama que ilustra o uso de pontos de extremidade de serviço para ajudar a proteger um aplicativo.](media/networking-features/multi-tier-app.png)

Aqui estão algumas considerações para ajudá-lo a decidir qual método usar:

* Ao usar pontos de extremidade de serviço, você só precisa proteger o tráfego para o aplicativo de API para a sub-rede de integração. Isso ajuda a proteger o aplicativo de API, mas você ainda pode ter dados vazamento de seu aplicativo de front-end para outros aplicativos no serviço de aplicativo.
* Ao usar pontos de extremidade privados, você tem duas sub-redes em jogo, o que adiciona complexidade. Além disso, o ponto de extremidade privado é um recurso de nível superior e adiciona sobrecarga de gerenciamento. O benefício de usar pontos de extremidade privados é que você não tem a possibilidade de vazamento de dados. 

Qualquer um dos métodos funcionará com vários front-ends. Em uma pequena escala, os pontos de extremidade de serviço são mais fáceis de usar porque você simplesmente habilita pontos de extremidade de serviço para o aplicativo de API na sub-rede de integração de front-end. Ao adicionar mais aplicativos de front-end, você precisa ajustar cada aplicativo de API para incluir pontos de extremidade de serviço com a sub-rede de integração. Quando você usa pontos de extremidade privados, há mais complexidade, mas você não precisa alterar nada em seus aplicativos de API depois de definir um ponto de extremidade privado. 

### <a name="line-of-business-applications"></a>Aplicativos de linha de negócios

Aplicativos LOB (linha de negócios) são aplicativos internos que normalmente não são expostos para acesso da Internet. Esses aplicativos são chamados de dentro de redes corporativas onde o acesso pode ser estritamente controlado. Se você usar um ASE ILB, será fácil hospedar seus aplicativos de linha de negócios. Se você usar o serviço multilocatário, poderá usar pontos de extremidade privados ou usar pontos de extremidade de serviço combinados com um gateway de aplicativo. Há dois motivos para usar um gateway de aplicativo com pontos de extremidade de serviço em vez de usar pontos de extremidade privados:
* Você precisa de proteção WAF em seus aplicativos LOB.
* Você deseja balancear a carga para várias instâncias de seus aplicativos LOB.

Se nenhuma dessas necessidades for aplicável, você será melhor usar pontos de extremidade privados. Com pontos de extremidade privados disponíveis no serviço de aplicativo, você pode expor seus aplicativos em endereços privados em sua rede virtual. O ponto de extremidade privado que você coloca em sua rede virtual pode ser acessado em conexões de ExpressRoute e VPN. 

Configurar pontos de extremidade privados vai expor seus aplicativos em um endereço privado, mas você precisará configurar o DNS para alcançar esse endereço local. Para fazer essa configuração funcionar, você precisará encaminhar a zona privada do DNS do Azure que contém seus pontos de extremidade privados para seus servidores DNS locais. As zonas privadas do DNS do Azure não dão suporte ao encaminhamento de zona, mas você pode dar suporte ao encaminhamento de zona usando um servidor DNS para essa finalidade. O modelo de [encaminhador de DNS](https://azure.microsoft.com/resources/templates/301-dns-forwarder/) facilita o encaminhamento da zona privada do DNS do Azure para seus servidores DNS locais.

## <a name="app-service-ports"></a>Portas do serviço de aplicativo

Se você verificar o serviço de aplicativo, encontrará várias portas que são expostas para conexões de entrada. Não há como bloquear ou controlar o acesso a essas portas no serviço multilocatário. Aqui está a lista de portas expostas:

| Uso | Porta ou portas |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  Gerenciamento | 454, 455 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Depuração remota no Visual Studio  |  4020, 4022, 4024 |
|  Serviço de Implantação da Web | 8172 |
|  Uso da infraestrutura | 7654, 1221 |

<!--Links-->
[appassignedaddress]: ./configure-ssl-certificate.md
[iprestrictions]: ./app-service-ip-restrictions.md
[serviceendpoints]: ./app-service-ip-restrictions.md
[hybridconn]: ./app-service-hybrid-connections.md
[vnetintegrationp2s]: ./web-sites-integrate-with-vnet.md
[vnetintegration]: ./web-sites-integrate-with-vnet.md
[networkinfo]: ./environment/network-info.md
[appgwserviceendpoints]: ./networking/app-gateway-with-service-endpoints.md
[privateendpoints]: ./networking/private-endpoint.md
[servicetags]: ../virtual-network/service-tags-overview.md