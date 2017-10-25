---
title: "Como configurar um Ambiente do Serviço de Aplicativo v1"
description: "Configuração, gerenciamento e monitoramento do Ambiente do Serviço de Aplicativo v1"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: 34fb3f15c03a3d3ef5f0a27081539bf0a6d19c5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-an-app-service-environment-v1"></a>Configuração de um Ambiente do Serviço de Aplicativo v1

> [!NOTE]
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v1.  Há uma versão mais recente do Ambiente do Serviço de Aplicativo que é mais fácil de usar e é executada em infraestrutura mais avançada. Para saber mais sobre a nova versão, comece com [Introdução ao Ambiente do Serviço de Aplicativo](intro.md).
> 

## <a name="overview"></a>Visão geral
Em um nível elevado, um Ambiente do Serviço de Aplicativo do Azure consiste em vários componentes principais:

* Recursos de computação que estão em execução no serviço hospedado do Ambiente do Serviço de Aplicativo
* Armazenamento
* Um banco de dados
* Uma VNet (rede virtual) do Azure Clássica (V1) ou do Resource Manager (V2) 
* Uma sub-rede na qual está em execução o serviço hospedado do Ambiente do Serviço de Aplicativo

### <a name="compute-resources"></a>Recursos de computação
Use os recursos de computação para os quatro pools de recursos.  Cada ASE (Ambiente do Serviço de Aplicativo) tem um conjunto de front-ends e três pools de trabalho possíveis. Você não precisa usar todos os três pools de trabalho. Se quiser, poderá usar apenas um ou dois.

Os hosts nos pools de recursos (front-ends e trabalhadores) não podem ser acessados diretamente pelos locatários. Você não pode usar o RDP (protocolo de área de trabalho remota) para se conectar a eles, alterar o provisionamento ou agir como um administrador.

Você pode definir o tamanho e a quantidade de pools de recursos. Em um ASE, você tem quatro opções de tamanho, que são rotuladas P1 a P4. Para obter detalhes sobre esses tamanhos e seus preços, confira [Preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/).
A alteração da quantidade ou do tamanho é chamada de uma operação de escala.  Apenas uma operação de escala pode ocorrer por vez.

**Front-ends**Os front-ends são os pontos de extremidade HTTP/HTTPS para os aplicativos mantidos em seu ASE. Não execute cargas de trabalho nos front-ends.

* Um ASE começa com duas P2s, o que é suficiente para cargas de trabalho de desenvolvimento e teste e para cargas de trabalho de produção de nível baixo. Recomendamos P3 para cargas de trabalho de produção moderada a pesada.
* Para as cargas de trabalho de produção moderada a pesada, recomendamos que você tenha pelo menos quatro P3s a fim de garantir uma quantidade suficiente de front-ends em execução durante a manutenção agendada. As atividades de manutenção agendada desligarão um front-end de cada vez. Isso reduz a capacidade de front-end disponível no geral durante as atividades de manutenção.
* Os front-ends podem demorar até uma hora para serem provisionados. 
* Para obter um ajuste mais fino do dimensionamento, você deve monitorar a porcentagem de CPU, a porcentagem de Memória e as métricas de Solicitações Ativas do pool de front-end. Se os percentuais de memória ou CPU ficam acima de 70% ao executar P3s, adicione mais front-ends. Se a média de valores de Solicitações Ativas for de 15 mil a 20 mil por front-end, você também deverá adicionar mais front-ends. O objetivo geral é manter os percentuais de CPU e de Memória abaixo de 70%, e as Solicitações Ativas com uma média abaixo de 15 mil solicitações por front-end durante a execução de P3s.  

**Trabalhos**Os trabalhos são os locais onde seus aplicativos são realmente executados. Ao escalar verticalmente seus Planos de Serviço de Aplicativo, você usará os trabalhadores no pool de trabalhadores associado.

* Não é possível adicionar trabalhadores instantaneamente. Eles podem levar até uma hora para provisionar.
* O dimensionamento de um recurso de computação para qualquer pool demorará menos de uma hora por domínio de atualização. Há 20 domínios de atualização em um ASE. Se você tiver dimensionado o tamanho de computação de um pool de trabalho com 10 instâncias, talvez demore até 10 horas para a conclusão.
* se você alterar o tamanho dos recursos de computação usados em um pool de trabalho, causará inicializações a frio dos aplicativos em execução nesse pool de trabalho.

Veja a seguir a maneira mais rápida de alterar o tamanho do recurso de computação de um pool de trabalho que não esteja executando aplicativos:

* Reduza a quantidade de trabalhadores para 2.  A escala mínima de redução de tamanho no portal é 2. A desalocação de suas instâncias vai levar alguns minutos. 
* Selecionar o novo tamanho de computação e o número de instâncias. A partir daqui, a conclusão demorará até duas horas.

Se os aplicativos exigirem um recurso de computação maior, você não poderá aproveitar a orientação anterior. Em vez de alterar o tamanho do pool de trabalho que hospeda esses aplicativos, você pode preencher outro pool de trabalho com trabalhos do tamanho desejado e mover seus aplicativos para esse pool.

* Criar instâncias adicionais com o tamanho de computação necessário em outro pool de trabalho. Isso levará uma hora para ser concluído.
* Reatribuir os Planos de Serviço de Aplicativo que hospedam os aplicativos que precisam de um tamanho maior para o pool de trabalho recém-configurado. Essa é uma operação rápida que deve demorar menos de um minuto para ser concluída.  
* Reduzir verticalmente o primeiro pool de trabalho se você não precisar mais dessas instâncias não utilizadas. Essa operação leva alguns minutos para ser concluída.

**Dimensionamento automático**: uma das ferramentas que podem ajudá-lo a gerenciar o consumo de recursos de computação é o dimensionamento automático. Você pode usar o dimensionamento automático para o front-end ou para os pools de trabalho. Você pode aumentar suas instâncias de algum tipo de pool de manhã e reduzi-las durante a noite. Ou você pode adicionar instâncias quando o número de trabalhador disponíveis em um pool de trabalho cair abaixo de um certo limite.

Se você quiser definir regras de dimensionamento automático com relação às métricas de pool de recursos de computação, lembre-se do tempo necessário para o provisionamento. Para obter mais detalhes sobre o dimensionamento automático dos Ambientes do Serviço de Aplicativo, confira [Como configurar o dimensionamento automático em um Ambiente de Serviço de Aplicativo][ASEAutoscale].

### <a name="storage"></a>Armazenamento
Cada ASE é configurado com 500 GB de armazenamento. Esse espaço é usado em todos os aplicativos do ASE. Esse espaço de armazenamento faz parte do ASE e atualmente não pode ser alternado para usar seu espaço de armazenamento. Se você estiver fazendo ajustes no roteamento ou segurança de rede virtual, ainda será necessário permitir o acesso ao Armazenamento do Azure ou o ASE não funcionará.

### <a name="database"></a>Banco de dados
O banco de dados armazena as informações que definem o ambiente, bem como detalhes sobre os aplicativos executados nele. Isso também é uma parte da assinatura gerenciada pelo Azure. Não é algo que você tenha a capacidade direta de manipular. Se você estiver fazendo ajustes no roteamento ou segurança de rede virtual, ainda será necessário permitir o acesso ao SQL Azure ou o ASE não funcionará.

### <a name="network"></a>Rede
A rede virtual usada com seu ASE pode ser uma daquelas criadas durante a criação do ASE ou uma já existente. Quando você criar a sub-rede durante a criação do ASE, obrigará o ASE a estar no mesmo grupo de recursos que a rede virtual. Se você precisar que o grupo de recursos usado pelo seu ASE seja diferente da sua rede virtual, então precisará criar seu ASE usando um modelo do resource manager.

Há algumas restrições na rede virtual que é usada para um ASE:

* A rede virtual deve ser uma rede virtual regional.
* Deve haver uma sub-rede com oito ou mais endereços nos quais o ASE é implantado.
* Quando uma sub-rede é usada para hospedar um ASE, o intervalo de endereços da sub-rede não pode ser alterado. Por esse motivo, recomendamos que a sub-rede contenha pelo menos 64 endereços a fim acomodar qualquer crescimento futuro do ASE.
* Nada mais pode existir na sub-rede além do ASE.

Ao contrário do serviço hospedado que contém o ASE, a [rede virtual][virtualnetwork] e a sub-rede estão sob o controle do usuário.  Você pode administrar sua rede virtual por meio da interface do usuário de rede virtual ou do PowerShell.  Um ASE pode ser implantado em uma VNet clássica ou do Resource Manager.  O portal e as experiências de API são um pouco diferentes entre as VNets Clássica e do Resource Manager, mas a experiência do ASE é a mesma.

A rede virtual que é usada para hospedar um ASE pode usar endereços IP RFC1918 privados ou endereços IP públicos.  Se você deseja usar um intervalo de IP que não é coberto pelo RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), precisa criar sua rede virtual e sub-rede a ser usada pelo seu ASE antes de criá-lo.

Já que esse recurso coloca o serviço de aplicativo do Azure em sua rede virtual, isso significa que seus aplicativos hospedados em seu ASE agora podem acessar diretamente recursos disponibilizados por meio de VPNs (redes virtuais) site a site ou o ExpressRoute. Os aplicativos em seus ambientes do serviço de aplicativo não exigem recursos de rede adicionais para acessar os recursos disponíveis para a rede virtual hospedando seu Ambiente do Serviço de Aplicativo. Isso significa que você não precisa usar a Integração VNET ou as Conexões Híbridas para acessar recursos na sua rede virtual ou conectados a ela. Entretanto, você ainda pode usar ambos os recursos para acessar recursos em redes que não estejam conectadas à sua rede virtual.

Por exemplo, você pode usar a Integração VNET para integrar-se a uma Rede Virtual que esteja em sua assinatura mas que não esteja conectada à rede virtual em que seu ASE está. Você também ainda pode usar as Conexões Híbridas para acessar recursos em outras redes, como faria normalmente.  

Caso você tenha configurado sua rede virtual com uma VPN do ExpressRoute, deverá estar ciente das necessidades de rotas de um ASE. Há algumas configurações de UDR (rota definida pelo usuário) que são incompatíveis com um ASE. Para obter mais detalhes sobre como executar um ASE em uma rede virtual com o ExpressRoute, confira [Executando um Ambiente de Serviço de Aplicativo em uma rede virtual com o ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Protegendo o tráfego de entrada
Há dois métodos principais para controlar o tráfego de entrada para seu ASE.  Você pode usar NSGs (Grupos de Segurança de Rede) para controlar quais endereços IP podem acessar seu ASE conforme descrito aqui [Como controlar o tráfego de entrada em um Ambiente de Serviço de Aplicativo](app-service-app-service-environment-control-inbound-traffic.md) e também pode configurar seu ASE com um ILB (Balanceador de Carga Interno).  Esses recursos também podem ser usados juntos para restringir o acesso usando NSGs para seu ASE ILB.

Quando você cria um ASE, ele cria um VIP em sua rede virtual.  Há dois tipos de VIP, internos e externos.  Quando você cria um ASE com um VIP externo, seus aplicativos no ASE poderão ser acessados por meio de um endereço IP que pode ser roteado na Internet. Quando você seleciona seu ASE interno, ele será configurado com um ILB e não poderá ser acessado diretamente pela Internet.  Um ASE ILB ainda requer um VIP externo, mas ele é usado somente para acesso de gerenciamento e manutenção do Azure.  

Durante a criação do ASE ILB, você fornecerá o subdomínio usado pelo ASE ILB e terá que gerenciar seu próprio DNS para o subdomínio que especificar.  Como você define o nome de subdomínio, também precisa gerenciar o certificado usado para acesso HTTPS.  Após a criação do ASE, você precisará fornecer o certificado.  Para saber mais sobre a criação e o uso de um ASE ILB, leia [Usando um Balanceador de Carga Interno com um Ambiente de Serviço de Aplicativo][ILBASE]. 

## <a name="portal"></a>Portal
Você pode gerenciar e monitorar o ambiente de serviço de aplicativo usando a interface do usuário no portal do Azure. Se você tiver um ASE, provavelmente verá o símbolo do Serviço de Aplicativo em sua barra lateral. Esse símbolo é usado para representar Ambientes do Serviço de Aplicativo no portal do Azure:

![Símbolo do Ambiente do Serviço de Aplicativo][1]

Para abrir a interface do usuário que lista todos os seus Ambientes de Serviço de Aplicativo, você poderá usar o ícone ou selecionar a divisa (símbolo ">") na parte inferior da barra lateral e selecionar Ambientes de Serviço de Aplicativo. Selecionando um dos ASEs listados, você abre a interface do usuário que é usada para monitorá-lo e gerenciá-lo.

![Interface do usuário para monitorar e gerenciar seu Ambiente de Serviço de Aplicativo][2]

A primeira folha mostra algumas propriedades do seu ASE junto com um gráfico de métricas por pool de recursos. Algumas das propriedades mostradas no bloco **Fundamentos** também são hiperlinks que abrirão a folha associada a ele. Por exemplo, você pode selecionar o Nome da **rede virtual** para abrir a interface do usuário associada à rede virtual em que o ASE está sendo executado. Os **Planos do Serviço de Aplicativo** e os **Aplicativos** abrem folhas que listam esses itens em seu ASE.  

### <a name="monitoring"></a>Monitoramento
Os gráficos permitem a você ver uma variedade de métricas de desempenho em cada pool de recursos. Para o pool de front-ends, você pode monitorar a média de memória e de CPU . Para pools de trabalho, você pode monitorar a quantidade usada e a quantidade disponível.

Vários Planos do Serviço de Aplicativo podem usar os trabalhadores em um pool de trabalho. A carga de trabalho não é distribuída da mesma forma que ocorre com servidores front-end. Portanto, o uso de CPU e de memória não oferece muitas informações úteis. É mais importante controlar quantos trabalhadores foram usados e estão disponíveis, especialmente se você estiver gerenciando esse sistema para outros usuários.  

Você também pode usar todas as métricas que podem ser controladas nos gráficos para configurar alertas. A configuração de alertas funciona da mesma forma como em qualquer lugar no Serviço de Aplicativo. Você pode definir um alerta da interface do usuário **Alertas** ou pressionando **Adicionar Alerta** na interface do usuário de métricas.

![Interface do usuário Métricas][3]

As métricas que acabamos de discutir são as métricas do Ambiente do Serviço de Aplicativo. Também há métricas disponíveis no nível do Plano do Serviço de Aplicativo. Nesse local, o monitoramento de CPU e da memória faz muito sentido.

Em um ASE, todos os Planos de Serviço de Aplicativo são planos de serviço de aplicativo dedicados. Isso significa que somente aplicativos executados nos hosts alocados para aquele Plano do Serviço de Aplicativo são aplicativos desse Plano do Serviço de Aplicativo. Para ver detalhes sobre o Plano do Serviço de Aplicativo, simplesmente abra o ASP de qualquer uma das listas na interface do usuário do ASE ou de **Procurar Planos do Serviço de Aplicativo** (o que listará todos eles).   

### <a name="settings"></a>Configurações
Na folha do ASE, há uma seção **Configurações** que contém vários recursos importantes:

**Configurações** > **Propriedades**: a folha **Configurações** será aberta automaticamente quando você abrir a folha de seu ASE. As **Propriedades**estão na parte superior. Há alguns itens aqui que são redundantes para o que você vê em **Fundamentos**, mas o que é muito útil é o **Endereço VIP Virtual**, bem como o **Endereço IP de Saída**.

![Folha Configurações e propriedades][4]

**Configurações** > **Endereços IP**: ao criar um aplicativo de IP SSL (protocolo SSL) em seu ASE, você precisará de um endereço IP SSL. Para obter um, seu ASE precisa de endereços IP SSL próprios que possam ser alocados. Quando um ASE é criado, ele conta com um endereço IP SSL para essa finalidade, mas você pode adicionar mais. Há uma cobrança por endereços IP SSL adicionais, conforme indicado em [Preços do Serviço de Aplicativo][AppServicePricing] (na seção Conexões SSL). O preço adicional é o preço do IP SSL.

**Configurações** > **Pool de Front-ends** / **Pools de Trabalho**: cada uma dessas folhas do pool de recursos só mostra as informações daquele pool de recursos, além de fornecer controles para o dimensionamento completo do pool de recursos.  

A folha base de cada pool de recursos oferece um gráfico com métricas para o pool de recursos. Assim como acontece com os gráficos da folha ASE, você pode entrar no gráfico e configurar alertas como desejado. A configuração de um alerta da folha ASE para um pool de recursos específico é igual à configuração feita do pool de recursos. Da folha **Configurações** do pool de trabalho, você tem acesso a todos os Aplicativos ou os Planos do Serviço de Aplicativo em execução nesse pool de trabalho.

![Interface do usuário de configurações do pool de trabalho][5]

### <a name="portal-scale-capabilities"></a>Recursos de dimensionamento do portal
Existem três operações em escala:

* Alteração do número de endereços IP no ASE que estão disponíveis para uso do IP SSL.
* Alteração do tamanho do recurso de computação usado em um pool de recursos.
* Alteração do número de recursos de computação usados em um pool de recursos manualmente ou por meio de dimensionamento automático.

No portal, existem três maneiras de controlar quantos servidores você tem nos pools de recursos:

* Uma operação de dimensionamento da folha principal do ASE na parte superior. Você pode fazer várias alterações de configuração de escala nos pools de front-end e de trabalho. Elas são aplicadas como uma única operação.
* Uma operação de dimensionamento manual da folha **Escala** de pool de recursos individuais, que está em **Configurações**.
* Dimensionamento Automático, que você configura na folha **Escala** do pool de recursos individuais.

Para usar a operação Escala na folha ASE, arraste o controle deslizante até a quantidade desejada e salve. Essa interface do usuário também oferece suporte à alteração do tamanho.  

![Interface do usuário de escala][6]

Para usar os recursos manuais ou de dimensionamento automático em um pool de recursos específico, acesse **Configurações** > **Pool de Front-end** / **Pools de Trabalho**, como apropriado. Em seguida, abra o pool que você deseja alterar. Vá para **Configurações** > **Escalar Horizontalmente** ou **Configurações** > **Escalar Verticalmente**. A folha **Escalar Horizontalmente** permite o controle da quantidade de instâncias. **Escalar Verticalmente** permite que você controle o tamanho do recurso.  

![Interface do usuário de configurações de escala][7]

## <a name="fault-tolerance-considerations"></a>Considerações sobre a tolerância a falhas
Você pode configurar um Ambiente do Serviço de Aplicativo para usar até um total de 55 recursos de computação. Desses 55 recursos de computação, apenas 50 podem ser usados para cargas de trabalho do host. A razão para isso é dupla. Há um mínimo de dois recursos de computação front-end.  Com isso, restam até 53 para dar suporte a alocação do pool de trabalho. Para oferecer tolerância a falhas, você precisa ter um recurso de computação adicional alocado de acordo com as seguintes regras:

* Cada pool de trabalho precisa de pelo menos um recurso de computação adicional que não está disponível para atribuição a uma carga de trabalho.
* Quando a quantidade de recursos de computação em um pool de trabalho ultrapassa um determinado valor, outro recurso de computação torna-se necessário para tolerância a falhas. Esse não é o caso no pool de front-end.

Dentro de qualquer pool de trabalho individual, os requisitos de tolerância a falhas são aqueles para um determinado valor de X recursos atribuídos a um pool de trabalho:

* Se X estiver entre 2 e 20, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho será X-1.
* Se X estiver entre 21 a 40, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho será X-2.
* Se X estiver entre 41 a 53, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho será X-3.

A superfície mínima tem dois servidores front-end e dois trabalhadores.  Com as instruções acima, veja alguns exemplos para que tudo fique mais claro:  

* Se você tiver 30 trabalhadores em um único pool, então 28 deles poderão ser usados para hospedar cargas de trabalho.
* Se você tiver dois trabalhadores em um único pool, então um deles poderá ser usado para hospedar cargas de trabalho.
* Se você tiver 20 trabalhadores em um único pool, então 19 deles poderão ser usados para hospedar cargas de trabalho.  
* Se você tiver 21 trabalhadores em um único pool, então ainda assim 19 deles poderão ser usados para hospedar cargas de trabalho.  

O aspecto de tolerância a falhas é importante, mas você precisará tê-lo em mente quando dimensionar acima de determinados limites. Se quiser adicionar mais capacidade a partir de 20 instâncias, insira 22 ou mais, já que 21 não adicionam mais capacidade. O mesmo será verdadeiro se o número de instâncias for maior do que 40, onde o próximo número que adicionará capacidade será 42.  

## <a name="deleting-an-app-service-environment"></a>Excluindo um Ambiente do Serviço de Aplicativo
Se você deseja excluir um ambiente do serviço de aplicativo, simplesmente use a ação **Excluir** na parte superior da folha Ambiente do Serviço de Aplicativo. Quando você fizer isso, será solicitada a inserção do nome do seu Ambiente do Serviço de Aplicativo para confirmar a operação. Observe que quando você exclui um Ambiente do Serviço de Aplicativo, exclui todo o conteúdo dentro dele também.  

![Interface do usuário Excluir um Ambiente de Serviço de Aplicativo][9]  

## <a name="getting-started"></a>Introdução
Para se familiarizar com os Ambientes de Serviços de Aplicativo, consulte [Como criar um Ambiente de Serviço de Aplicativo](app-service-web-how-to-create-an-app-service-environment.md).

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
