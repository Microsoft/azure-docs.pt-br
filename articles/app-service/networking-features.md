---
title: Recursos de implantação - serviço de aplicativo do Azure de rede | Microsoft Docs
description: Como usar o serviço de aplicativo de vários recursos de rede
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: db29d0761084e32d601dc9c6d94082cd09bc5d18
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655459"
---
# <a name="app-service-networking-features"></a>Recursos de rede do serviço de aplicativo

Aplicativos no serviço de aplicativo do Azure podem ser implantados de várias maneiras. Por padrão, o serviço de aplicativo aplicativos hospedados são diretamente acessível pela internet e só podem acessar pontos de extremidade de internet hospedado. No entanto, muitos aplicativos de cliente necessário controlar o tráfego de rede de entrada e saída. Há vários recursos disponíveis no serviço de aplicativo para atender a essas necessidades. O desafio é saber qual recurso deve ser usado para resolver um problema específico. Este documento destina-se a ajudar os clientes a determinar qual recurso deve ser usado com base em alguns casos de uso de exemplo.

Há dois tipos de implantação primário para o serviço de aplicativo do Azure. O serviço público multilocatário, que hospeda o serviço de aplicativo planos gratuito, compartilhado, básico, Standard, Premium e Premiumv2 não há SKUs de preços. Em seguida, há o locatário único aplicativo de serviço ASE (ambiente), que hospeda os planos de serviço de aplicativo isolado SKU diretamente na sua rede Virtual do Azure (VNet). Os recursos usados podem variar de acordo se você estiver no serviço de multilocatário ou em um ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Recursos de rede de serviço de aplicativo multilocatário 

O serviço de aplicativo do Azure é um sistema distribuído. As funções que lidam com solicitações de entrada HTTP/HTTPS são chamadas de front-ends. As funções que hospedam a carga de trabalho do cliente são chamadas de trabalhadores. Todas as funções em uma implantação do serviço de aplicativo existem em uma rede de multilocatário. Como há muitos clientes diferentes na mesma unidade de escala do serviço de aplicativo, você não pode se conectar a rede do serviço de aplicativo diretamente à sua rede. Em vez de se conectar as redes, precisamos de recursos para lidar com os diferentes aspectos de comunicação do aplicativo. Os recursos que manipulam as solicitações para seu aplicativo não podem ser usados para resolver problemas ao fazer chamadas do seu aplicativo. Da mesma forma, os recursos que resolvam problemas para chamadas do seu aplicativo não podem ser usados para resolver problemas ao seu aplicativo.  

| Recursos de entrada | Recursos de saída |
|---------------------|-------------------|
| Atribuído o endereço de aplicativo | Conexões Híbridas |
| Restrições de acesso | Gateway necessário integração VNet |
| Pontos de extremidade de serviço | Integração de rede virtual (visualização) |

Salvo indicação em contrário, todos os recursos podem ser usados juntos. Você pode misturar os recursos para solucionar seus vários problemas.

## <a name="use-case-and-features"></a>Caso de uso e recursos

Para qualquer determinado caso de uso, pode haver algumas maneiras de resolver o problema.  O recurso correto para usar, às vezes, é devido a motivos além de apenas o caso de uso em si. Os casos de uso de entrada a seguir sugerem como usar recursos de rede do serviço de aplicativo para resolver problemas sobre como controlar o tráfego direcionado para seu aplicativo. 
 
| Casos de uso de entrada | Recurso |
|---------------------|-------------------|
| Dar suporte às necessidades SSL com base em IP para seu aplicativo | Atribuído o endereço de aplicativo |
| Não compartilhado, o endereço de entrada dedicado para seu aplicativo | Atribuído o endereço de aplicativo |
| Restringir o acesso ao seu aplicativo de um conjunto de endereços bem definidos | Restrições de acesso |
| Expor o meu aplicativo em IPs privados na minha VNet | ILB ASE </br> Gateway de aplicativo com pontos de extremidade de serviço |
| Restringir o acesso ao meu aplicativo de recursos em uma rede virtual | Pontos de extremidade de serviço </br> ILB ASE |
| Expor o meu aplicativo em um IP privado na minha VNet | ILB ASE </br> IP privado para a entrada no Gateway de aplicativo com pontos de extremidade de serviço |
| Proteger meu aplicativo com um WAF | O Gateway de aplicativo + ASE ILB </br> Gateway de aplicativo com pontos de extremidade de serviço </br> Frente do Azure com as restrições de acesso |
| Balancear o tráfego de meus aplicativos em diferentes regiões | Frente do Azure com as restrições de acesso | 
| Balancear o tráfego na mesma região | Gateway de aplicativo com pontos de extremidade de serviço | 

Os casos de uso de saída a seguir sugerem como usar recursos de rede do serviço de aplicativo para atender às necessidades de acesso de saída para seu aplicativo. 

| Casos de uso de saída | Recurso |
|---------------------|-------------------|
| Acessar recursos em uma rede Virtual do Azure na mesma região | Integração VNet </br> ASE |
| Acessar recursos em uma rede Virtual do Azure em uma região diferente | Gateway necessário integração VNet </br> ASE e o emparelhamento de rede virtual |
| Acesse recursos protegidos com pontos de extremidade de serviço | Integração VNet </br> ASE |
| Acessar recursos em uma rede privada não está conectado ao Azure | Conexões Híbridas |
| Acessar recursos em circuitos do ExpressRoute | Integração de rede virtual (por enquanto, restritos para endereços RFC 1918) </br> ASE | 


### <a name="default-networking-behavior"></a>Comportamento de rede padrão

As unidades de escala do serviço de aplicativo do Azure dão suporte a muitos clientes em cada implantação. Os planos gratuito e compartilhado SKU hospedam cargas de trabalho de cliente nos trabalhos de multilocatário. Basic e acima planos hospedar cliente cargas de trabalho que são dedicadas a apenas um plano de serviço de aplicativo (ASP). Se você tiver um plano de serviço de aplicativo padrão, todos os aplicativos nesse plano serão executado no mesmo trabalho. Se você expandir o trabalhador, em seguida, todos os aplicativos nesse ASP serão replicados em um novo trabalho para cada instância em que o ASP. Os trabalhos que são usados para Premiumv2 são diferentes de trabalhadores usados para os outros planos. Cada implantação do serviço de aplicativo tem um endereço IP que é usado para todo o tráfego de entrada para os aplicativos na implantação do serviço de aplicativo. Há contudo em qualquer lugar de 4 a 11 endereços usados para fazer chamadas de saída. Esses endereços são compartilhados por todos os aplicativos na implantação do serviço de aplicativo. Os endereços de saída são diferentes com base nos tipos de trabalho diferentes. Isso significa que os endereços usados por um gratuito, compartilhado, básico, Standard e Premium ASPs são diferentes dos endereços usados para chamadas de saída de ASPs o Premiumv2. Se você examinar as propriedades para seu aplicativo, você pode ver os endereços de entrada e saídos que são usados pelo seu aplicativo. Se você precisar bloquear uma dependência com uma ACL de IP, use o possibleOutboundAddresses. 

![Propriedades do aplicativo](media/networking-features/app-properties.png)

Serviço de aplicativo tem um número de pontos de extremidade que são usados para gerenciar o serviço.  Esses endereços são publicados em um documento separado e também estão na marca de serviço AppServiceManagement IP. A marca AppServiceManagement só é usada com um aplicativo de serviço ASE (ambiente) em que você precisa permitir o tráfego. O serviço de aplicativo endereços de entrada são controlados na marcação de serviço AppService IP. Não há nenhuma marca de serviço IP que contém os endereços de saída usados pelo serviço de aplicativo. 

![Diagrama de entrada e saído do serviço de aplicativo](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Atribuído o endereço de aplicativo 

O recurso de endereço atribuído de aplicativo é um produto do recurso de SSL com base em IP e é acessado por configurar o SSL com o seu aplicativo. Esse recurso pode ser usado para chamadas SSL com base em IP, mas ele também pode ser usado para fornecer um endereço que somente ele tem de seu aplicativo. 

![Diagrama de endereço de atribuídos ao aplicativo](media/networking-features/app-assigned-address.png)

Quando você usa um endereço atribuído de aplicativo, o tráfego ainda passa pelas mesmas funções de front-end que lidar com todo o tráfego de entrada para a unidade de escala do serviço de aplicativo. O endereço atribuído ao seu aplicativo, no entanto, só é usado pelo seu aplicativo. Os casos de uso para esse recurso são:

* Dar suporte às necessidades SSL com base em IP para seu aplicativo
* Definir um endereço dedicado para seu aplicativo que não é compartilhado com qualquer outra coisa

Você pode aprender como definir um endereço em seu aplicativo com o tutorial em [SSL baseado em IP configurando][appassignedaddress]. 

### <a name="access-restrictions"></a>Restrições de acesso 

O permite do recurso de restrições de acesso filtrar **entrada** solicitações com base no endereço IP de origem. A ação de filtragem ocorre nas funções de front-end upstream de reverte o trabalho onde seus aplicativos estão em execução. Como as funções front-end são upstream das funções de trabalho, o recurso de restrições de acesso pode ser considerado como proteção de nível de rede para seus aplicativos. O recurso permite que você crie uma lista de permissão e negação blocos de endereço que são avaliados na ordem de prioridade. Ele é semelhante ao recurso de grupo de segurança de rede (NSG) que existe na rede do Azure.  Você pode usar esse recurso em um ASE ou no serviço de multilocatário. Quando usado com um ASE ILB, você pode restringir o acesso de blocos de endereço privado.

![Restrições de acesso](media/networking-features/access-restrictions.png)

O recurso de restrições de acesso ajuda em cenários em que você deseja restringir os endereços IP que podem ser usados para acessar seu aplicativo. Entre o uso casos para esse recurso são:

* Restringir o acesso ao seu aplicativo de um conjunto de endereços bem definidos 
* Restringir o acesso ao disponível por meio de um serviço de balanceamento de carga, como o Azure porta da frente. Se você quiser bloquear o tráfego de entrada à frente do Azure, criar regras para permitir o tráfego de 147.243.0.0/16 e 2a01:111:2050::/ / 44. 

![Restrições de acesso com a porta da frente](media/networking-features/access-restrictions-afd.png)

Se você quiser bloquear o acesso ao seu aplicativo para que possa ser acessado somente de recursos em sua rede Virtual do Azure (VNet), você precisa de um endereço público estático em tudo o que seu código-fonte está em sua rede virtual. Se os recursos não tiverem um endereço público, você deve usar o recurso de pontos de extremidade de serviço. Saiba como habilitar esse recurso com o tutorial [configurando restrições de acesso][iprestrictions].

### <a name="service-endpoints"></a>Pontos de extremidade de serviço

Pontos de extremidade de serviço permite que você bloqueie **entrada** acesso ao seu aplicativo, de modo que o endereço de origem deve vir de um conjunto de sub-redes que você selecionar. Esse recurso funciona em conjunto com as restrições de acesso de IP. Pontos de extremidade de serviço são definidos na experiência do usuário como as restrições de acesso de IP. Você pode criar uma lista de regras de acesso permitir/negar que inclui endereços públicos, bem como sub-redes em suas redes virtuais. Esse recurso oferece suporte a cenários como:

![pontos de extremidade de serviço](media/networking-features/service-endpoints.png)

* Como configurar um Gateway de aplicativo com seu aplicativo para bloquear o tráfego de entrada para seu aplicativo
* Restringindo o acesso ao seu aplicativo para recursos em sua rede virtual. Isso pode incluir as VMs, os ASEs ou até mesmo outros aplicativos que usam a integração de rede virtual 

![pontos de extremidade de serviço com o gateway de aplicativo](media/networking-features/service-endpoints-appgw.png)

Você pode aprender mais sobre como configurar pontos de extremidade de serviço com o seu aplicativo no tutorial em [configurando restrições de acesso de ponto de extremidade de serviço][serviceendpoints]
 
### <a name="hybrid-connections"></a>Conexões Híbridas

As conexões híbridas de serviço de aplicativo permite que seus aplicativos façam **saída** chamadas para pontos de extremidade TCP especificados. O ponto de extremidade pode ser local, em uma rede virtual ou em qualquer lugar que permite que o tráfego de saída para o Azure na porta 443. O recurso requer a instalação de um agente de retransmissão chamado Gerenciador de Conexão híbrida (HCM) em um host mais recente ou o Windows Server 2012. O HCM precisa ser capaz de alcançar a retransmissão do Azure na porta 443. O HCM pode ser baixado do aplicativo serviço híbrido conexões de interface do usuário no portal. 

![Fluxo de rede de conexões híbridas](media/networking-features/hybrid-connections.png)

O recurso conexões híbridas do aplicativo serviço baseia-se na capacidade de conexões híbridas do Azure. Serviço de aplicativo usa uma forma especializada do recurso que dá suporte apenas a fazer chamadas de saída do seu aplicativo para um host TCP e a porta. Esse host e a porta só precisam resolver no host em que o HCM ser instalado. Quando o aplicativo, serviço de aplicativo, faz uma pesquisa de DNS no host e porta definidos em sua Conexão híbrida, o tráfego será redirecionado automaticamente para ir por meio de Conexão híbrida e limite o Gerenciador de Conexão híbrida. Para saber mais sobre conexões híbridas, leia a documentação sobre [as conexões híbridas de serviço de aplicativo][hybridconn]

Geralmente, esse recurso é usado para:

* Acessar recursos em redes privadas que não estão conectados ao Azure com uma VPN ou ExpressRoute
* Suporte de lift- and -shift de aplicativos no local para o serviço de aplicativo sem a necessidade de também mover bancos de dados de suporte  
* Fornece acesso com segurança a um único host e porta por Conexão híbrida. A maioria dos recursos de rede abrem o acesso a uma rede e com as conexões híbridas tiver apenas o único host e a porta que você pode acessar.
* Cenários de cobertura não cobertos por outros métodos de conectividade de saída
* Realiza o desenvolvimento no serviço de aplicativo em que os aplicativos podem facilmente aproveitar recursos locais 

Porque o recurso permite o acesso a recursos locais sem uma brecha de firewall de entrada, ele é popular entre os desenvolvedores. Os outros saída o serviço de aplicativo recursos de rede são muito Azure Virtual rede relacionados. Conexões híbridas não tem uma dependência em percorrer uma rede virtual e pode ser usado para uma maior variedade de necessidades de rede. É importante observar que o recurso de conexões de híbridas do serviço de aplicativo não se importa com ou saber o que está fazendo sobre ele. Isso é dizer que você pode usá-lo para acessar um banco de dados, um serviço web ou um soquete TCP arbitrário em um mainframe. O recurso de túneis essencialmente pacotes TCP. 

Embora as conexões híbridas é popular para desenvolvimento, ele também é usado em vários aplicativos de produção. Ele é ótimo para acessar um serviço web ou banco de dados, mas não é apropriado para situações que envolvem a criação de muitas conexões. 

### <a name="gateway-required-vnet-integration"></a>Gateway necessário integração VNet 

O gateway necessário recurso de integração de rede virtual do serviço de aplicativo permite que seu aplicativo para verificar **saída** solicitações em uma rede Virtual do Azure. O recurso funciona ao conectar-se o host que está em execução no seu aplicativo para um gateway de rede Virtual na sua rede virtual com uma VPN ponto a site. Quando você configura o recurso, seu aplicativo obtém um dos endereços de ponto para site atribuídos a cada instância. Esse recurso permite que você acesse recursos em VNets do Gerenciador de recursos em qualquer região ou clássico. 

![Gateway necessário integração VNet](media/networking-features/gw-vnet-integration.png)

Esse recurso resolve o problema de acesso aos recursos em outras redes virtuais e até mesmo pode ser usado para se conectar por meio de uma rede virtual a outras redes virtuais ou até mesmo localmente. Ele não funciona com o ExpressRoute conectado redes conectadas de redes virtuais mas não com a VPN Site a site. É normalmente inadequado usar esse recurso de um aplicativo em um ambiente de serviço de aplicativo (ASE), pois o ASE já está em sua rede virtual. Os casos de uso que resolve esse recurso são:

* Acesso a recursos no IPs privados em suas redes virtuais do Azure 
* Acesso a recursos locais se há uma VPN site a site 
* Acesso a recursos em VNets emparelhadas 

Quando esse recurso está habilitado, o seu aplicativo usará o servidor DNS configurado com a VNet de destino. Você pode ler mais sobre esse recurso na documentação sobre [integração de rede virtual do serviço de aplicativo][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Integração VNet

O gateway necessária recurso Integração VNet é muito útil, mas ainda não resolve acessando recursos pelo ExpressRoute. Na parte superior de necessidade de alcançar em conexões do ExpressRoute, é necessário para aplicativos poder fazer chamadas para serviços protegidos do ponto de extremidade do serviço. Para resolver ambos essas necessidades adicionais, foi adicionado a outro recurso de integração de rede virtual. O novo recurso de integração de rede virtual permite que você coloque o back-end do seu aplicativo em uma sub-rede em uma VNet do Resource Manager na mesma região. Esse recurso não está disponível de um ambiente de serviço de aplicativo, que já está em uma rede virtual. Esse recurso permite a:

* Acesso a recursos em VNets do Gerenciador de recursos na mesma região
* Acessar os recursos que são protegidos com pontos de extremidade de serviço 
* Acessar os recursos que são acessíveis em conexões de ExpressRoute ou VPN

![Integração VNet](media/networking-features/vnet-integration.png)

Esse recurso está em versão prévia e não deve ser usado para cargas de trabalho de produção. Para saber mais sobre esse recurso, leia os documentos na [integração de rede virtual do serviço de aplicativo][vnetintegration].

## <a name="app-service-environment"></a>Ambiente do Serviço de Aplicativo 

Um ambiente de serviço de aplicativo (ASE) é uma implantação de único locatário do serviço de aplicativo do Azure que é executado em sua rede virtual. O ASE permite que os casos de uso, como:

* Acessar recursos em sua rede virtual
* Acessar recursos em ExpressRoute
* Expor seus aplicativos com um endereço privado em sua rede virtual 
* Acessar os recursos nos pontos de extremidade de serviço 

Com um ASE, você não precisa usar recursos como integração de rede virtual ou pontos de extremidade de serviço porque o ASE já está em sua rede virtual. Se você quiser acessar recursos, como SQL ou armazenamento em pontos de extremidade de serviço, habilite os pontos de extremidade de serviço na sub-rede do ASE. Se você quiser acessar recursos na rede virtual, não há nenhuma configuração adicional necessária.  Se você quiser acessar recursos pelo ExpressRoute, você já estiver na rede virtual e não é necessário configurar nada no ASE ou de aplicativo dentro dele. 

Porque os aplicativos no ASE ILB podem ser expostos em um endereço IP privado, você pode facilmente adicionar dispositivos de WAF para expor apenas aos aplicativos que você deseja para a internet e proteger o restante. Ele é adaptado para fácil desenvolvimento de aplicativos de várias camadas. 

Há algumas coisas que não são possíveis do serviço multilocatário que são de um ASE. Elas incluem coisas como:

* Expor seus aplicativos em um endereço IP privado
* Proteger todo o tráfego de saída com controles de rede que não fazem parte do seu aplicativo 
* Hospedar seus aplicativos em um serviço de locatário único 
* Escalar verticalmente para muitos mais instâncias do que são possíveis no serviço de multilocatário 
* Carregar certificados de cliente de autoridade de certificação privados para uso por seus aplicativos com a autoridade de certificação privada protegida pontos de extremidade 
* Forçar o TLS 1.1 em todos os aplicativos hospedados no sistema sem qualquer possibilidade de desabilitação no nível do aplicativo 
* Forneça um endereço de saída dedicado para todos os aplicativos no ASE que não são compartilhadas com todos os clientes 

![ASE em uma rede virtual](media/networking-features/app-service-environment.png)

O ASE fornece a melhor história em torno de hospedagem de aplicativo isolado e dedicado, mas vem com alguns desafios de gerenciamento. Algumas coisas a considerar antes de usar um ASE operacional são:
 
 * Um ASE é executado dentro de sua rede virtual, mas tem dependências fora da VNet. Essas dependências devem ser permitidas. Leia mais em [considerações de rede para um ambiente de serviço de aplicativo][networkinfo]
 * Um ASE não se adapta imediatamente, como o serviço de multilocatário. Você precisa prever as necessidades de dimensionamento em vez de dimensionar de forma reativa. 
 * Um ASE tem maior com antecedência custo associado a ele. Para obter o máximo proveito de seu ASE, você deve planejar a colocação de várias cargas de trabalho em um ASE em vez de que ele seja usado para esforços small
 * Os aplicativos em um ASE não é possível restringir o acesso a alguns aplicativos em um ASE e outros não.
 * O ASE está em uma sub-rede e quaisquer regras de rede se aplicam a todo o tráfego para e do ASE. Se você quiser atribuir regras de tráfego de entrada para apenas um aplicativo, use restrições de acesso. 

## <a name="combining-features"></a>Combinando os recursos 

Os recursos que anotou para o serviço multilocatário podem ser usados juntos para resolver casos de uso mais elaborados. Dois dos casos de uso mais comuns são descritas aqui, mas eles são apenas exemplos. Compreendendo o que fazem os vários recursos, você pode resolver quase todas as suas necessidades de arquitetura do sistema.

### <a name="inject-app-into-a-vnet"></a>Injetar o aplicativo em uma rede virtual

Uma solicitação comum está em como colocar seu aplicativo em uma rede virtual. Colocando seu aplicativo em uma rede virtual significa que os pontos de extremidade de entrada e saídos para um aplicativo em uma rede virtual. O ASE fornece a melhor solução para resolver esse problema, mas você pode obter o máximo do que é necessário com o serviço multilocatário, combinando os recursos. Por exemplo, você pode hospedar somente aplicativos da intranet com endereços privados de entrada e saídos por:

* Criar um Gateway de aplicativo com o endereço privado de entrada e saído
* Protegendo o tráfego de entrada para seu aplicativo com pontos de extremidade de serviço 
* Use a nova integração da rede virtual para que o back-end do seu aplicativo está em sua rede virtual 

Esse estilo de implantação não seria fornecer um endereço dedicado para o tráfego de saída à internet ou lhe oferece a capacidade de bloquear o tráfego de saída de seu aplicativo.  Esse estilo de implantação você forneceria uma muito do que você somente obtém com um ASE. 

### <a name="create-multi-tier-applications"></a>Criar aplicativos de várias camadas

Um aplicativo de várias camado é um aplicativo em que os aplicativos de back-end de API só podem ser acessados da camada de front-end. Para criar um aplicativo de várias camadas, você pode:

* Usar a integração de rede virtual para conectar-se o back-end do aplicativo web de front-end com uma sub-rede em uma rede virtual
* Usar pontos de extremidade de serviço para proteger o tráfego de entrada para seu aplicativo de API somente vindo de sub-rede usada pelo seu aplicativo web de front-end

![aplicativo de várias camadas](media/networking-features/multi-tier-app.png)

Você pode ter vários aplicativos front-end usam o mesmo aplicativo de API usando a integração de rede virtual de outros aplicativos de front-end e pontos de extremidade de serviço do aplicativo de API com suas sub-redes.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
