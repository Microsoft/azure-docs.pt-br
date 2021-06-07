---
title: Escala com distribuição geográfica
description: Saiba como escalonar horizontalmente os aplicativos que usam a distribuição geográfica com o Gerenciador de Tráfego e os Ambientes de Serviço de Aplicativo.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18, references_regions
ms.openlocfilehash: 004b32118521f72c5b59ad7bab2d4e41244b85c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85833597"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Escala distribuída geograficamente com ambientes de Serviço de Aplicativo
## <a name="overview"></a>Visão geral

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Cenários de aplicativos que exigem escala muito alta podem exceder a capacidade de recursos de computação disponível para uma única implantação de um aplicativo.  Aplicativos de votação, eventos esportivos e de entretenimento televisionados são exemplos de cenários que exigem escala extremamente alta. Os requisitos de alta escala podem ser atendidos por meio da expansão horizontal de aplicativos. Para lidar com requisitos de carga extrema, muitas implantações de aplicativo podem ser feitas em uma única região e entre regiões.

Os ambientes de serviço de aplicativo são uma plataforma ideal para expansão horizontal. Depois de selecionar uma configuração de Ambiente do Serviço de Aplicativo que possa dar suporte a uma taxa de solicitação conhecida, os desenvolvedores podem implantar ambientes de serviço de aplicativo adicionais na maneira "cookie cortador" para obter uma capacidade de carga de pico desejada.

Por exemplo, suponha que um aplicativo em execução em uma configuração de Ambiente do Serviço de Aplicativo tenha sido testado para manipular solicitações de 20 mil por segundo (RPS).  Se a capacidade de carga de pico desejada for de 100 mil RPS, cinco (5) ambientes de serviço de aplicativo poderão ser criados e configurados para garantir que o aplicativo possa lidar com a carga de projeto máxima.

Já que os clientes normalmente acessam aplicativos usando um domínio personalizado, os desenvolvedores precisam ter uma maneira de distribuir solicitações de aplicativo entre todas as instâncias do Ambiente de Serviço de Aplicativo.  Uma ótima maneira de atingir esse objetivo é resolver o domínio personalizado usando um [perfil do Gerenciador de tráfego do Azure][AzureTrafficManagerProfile].  O perfil do Gerenciador de Tráfego pode ser configurado para apontar para todos os Ambientes de Serviço de Aplicativo individuais.  O Traffic Manager lidará automaticamente com a distribuição de clientes em todos os ambientes do serviço de aplicativo, com base nas configurações de balanceamento de carga no perfil do Gerenciador de tráfego.  Essa abordagem funciona independentemente de todos os Ambientes de Serviço de Aplicativo serem implantados em uma única região do Azure ou distribuídos pelo mundo todo em várias regiões do Azure.

Além disso, uma vez que os clientes acessam aplicativos usando o domínio personalizado, eles não estão cientes do número de Ambientes de Serviço de Aplicativo que estão executando um aplicativo.  Sendo assim, os desenvolvedores podem adicionar e remover Ambientes de Serviço de Aplicativo rápida e facilmente baseados na carga de tráfego observada.

O diagrama conceitual abaixo mostra um aplicativo escalado horizontalmente em três Ambientes de Serviço de Aplicativo dentro de uma única região.

![Arquitetura conceitual][ConceptualArchitecture] 

O restante deste tópico explica as etapas envolvidas na configuração de uma topologia distribuída para o aplicativo de exemplo usando vários Ambientes de Serviço de Aplicativo.

## <a name="planning-the-topology"></a>Planejando a topologia
Antes de criar uma superfície de aplicativo distribuído, é bom ter algumas informações prévias.

* **Domínio personalizado para o aplicativo:**  Qual é o nome de domínio personalizado que os clientes usarão para acessar o aplicativo?  Para o aplicativo de exemplo, o nome de domínio personalizado é `www.scalableasedemo.com` .
* **Domínio do Gerenciador de tráfego:** Escolha um nome de domínio ao criar um [perfil do Gerenciador de tráfego do Azure][AzureTrafficManagerProfile].  Esse nome será combinado com o sufixo *trafficmanager.net* para registrar uma entrada de domínio gerenciada pelo Gerenciador de Tráfego.  Para o aplicativo de exemplo, o nome escolhido é *scalable-ase-demo*.  Assim, o nome de domínio completo gerenciado pelo Gerenciador de Tráfego é *scalable-ase-demo.trafficmanager.net*.
* **Estratégia para dimensionar o volume do aplicativo:**  o volume do aplicativo será distribuído entre vários Ambientes do Serviço de Aplicativo em uma única região?  Várias regiões?  Uma combinação de ambas as abordagens?  Baseie a decisão sobre as expectativas de onde o tráfego do cliente será originado e como o restante da infra-estrutura de back-end de suporte de um aplicativo pode ser dimensionada.  Por exemplo, com um aplicativo de 100% sem monitoração de estado, um aplicativo pode ser amplamente dimensionado usando uma combinação de muitos ambientes de serviço de aplicativo em cada região do Azure, multiplicado por ambientes de serviço de aplicativo implantados em várias regiões do Azure.  Com mais de 15 regiões globais do Azure disponíveis para sua escolha, os clientes podem realmente criar uma superfície de aplicativo de hiperescala em todo o mundo.  Para o aplicativo de exemplo usado neste artigo, três ambientes de serviço de aplicativo foram criados em uma única região do Azure (EUA Central do Sul).
* **Convenção de nomenclatura para os Ambientes do Serviço de Aplicativo:**  Cada Ambiente do Serviço de Aplicativo requer um nome exclusivo.  Além de um ou dois ambientes de serviço de aplicativo, é útil ter uma Convenção de nomenclatura para ajudar a identificar cada Ambiente do Serviço de Aplicativo.  Para o aplicativo de exemplo, foi usada uma Convenção de nomenclatura simples.  Os nomes dos três Ambientes de Serviço de Aplicativo são *fe1ase*, *fe2ase* e *fe3ase*.
* **Convenção de nomenclatura para os aplicativos:**  como várias instâncias do aplicativo serão implantadas, é necessário um nome para cada instância do aplicativo implantado.  Um recurso pouco conhecido, mas conveniente dos ambientes do serviço de aplicativo, é que o mesmo nome de aplicativo pode ser usado em vários ambientes de serviço de aplicativo.  Como cada Ambiente do Serviço de Aplicativo tem um sufixo de domínio exclusivo, os desenvolvedores podem optar por reutilizar exatamente o mesmo nome de aplicativo em cada ambiente.  Por exemplo, um desenvolvedor poderia ter aplicativos chamados da seguinte maneira:  *MyApp.foo1.p.azurewebsites.net*, *MyApp.Foo2.p.azurewebsites.net*, *MyApp.foo3.p.azurewebsites.net*, etc.  No entanto, para o aplicativo de exemplo, cada instância de aplicativo também tem um nome exclusivo.  Os nomes de instância de aplicativo usados são *webfrontend1*, *webfrontend2* e *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Configurando o perfil do Gerenciador de Tráfego
Quando várias instâncias de um aplicativo são implantadas em vários Ambientes de Serviço de Aplicativo, as instâncias de aplicativos individuais podem ser registradas com o Gerenciador de Tráfego.  Para o aplicativo de exemplo, um perfil do Gerenciador de tráfego é necessário para *Scalable-ase-demo.trafficmanager.net* que pode rotear clientes para qualquer uma das seguintes instâncias do aplicativo implantado:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  uma instância do aplicativo de exemplo implantada no primeiro Ambiente do Serviço de Aplicativo.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  uma instância do aplicativo de exemplo implantada no segundo Ambiente do Serviço de Aplicativo.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  uma instância do aplicativo de exemplo implantada no terceiro Ambiente do Serviço de Aplicativo.

A maneira mais fácil de registrar vários pontos de extremidade de serviço Azure App, todos em execução na **mesma** região do Azure, é com o [suporte ao Gerenciador de tráfego][ARMTrafficManager]do PowerShell Azure Resource Manager.  

A primeira etapa é a criação de um perfil do Gerenciador de Tráfego do Azure.  O código abaixo mostra como o perfil foi criado para o aplicativo de exemplo:

```azurepowershell-interactive
$profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Observe como o parâmetro *RelativeDnsName* foi definido como *scalable-ase-demo*.  Esse parâmetro faz com que o nome de domínio *Scalable-ase-demo.trafficmanager.net* seja criado e associado a um perfil do Gerenciador de tráfego.

O parâmetro *TrafficRoutingMethod* define o Gerenciador de tráfego da política de balanceamento de carga que usará para determinar como espalhar a carga do cliente em todos os pontos de extremidade disponíveis.  Neste exemplo, o método *ponderado* foi escolhido.  Devido a essa opção, as solicitações do cliente serão distribuídas por todos os pontos de extremidade do aplicativo registrado com base nos pesos relativos associados a cada ponto. 

Com o perfil criado, cada instância do aplicativo é adicionada ao perfil como um ponto de extremidade do Azure nativo.  O código a seguir busca uma referência a cada aplicativo Web de front-end. Em seguida, ele adiciona cada aplicativo como um ponto de extremidade do Gerenciador de tráfego por meio do parâmetro *TargetResourceId* .

```azurepowershell-interactive
$webapp1 = Get-AzWebApp -Name webfrontend1
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

$webapp2 = Get-AzWebApp -Name webfrontend2
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

$webapp3 = Get-AzWebApp -Name webfrontend3
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
```

Observe como não há uma chamada para *Add-AzureTrafficManagerEndpointConfig* em cada instância do aplicativo individual.  O parâmetro *TargetResourceId* em cada comando do PowerShell faz referência a uma das três instâncias de aplicativo implantadas.  O perfil do Gerenciador de Tráfego distribuirá a carga entre todos os três pontos de extremidade registrados no perfil.

Todos os três pontos de extremidade usam o mesmo valor (10) para o parâmetro *Weight* .  Essa situação resulta na disseminação de solicitações do cliente pelo Gerenciador de tráfego entre todas as três instâncias de aplicativo de maneira relativamente uniforme. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Apontando o domínio personalizado do aplicativo no domínio do Gerenciador de Tráfego
A última etapa necessária é apontar o domínio personalizado do aplicativo para o domínio do Gerenciador de Tráfego.  Para o aplicativo de exemplo, aponte para `www.scalableasedemo.com` `scalable-ase-demo.trafficmanager.net` .  Conclua esta etapa com o registrador de domínio que gerencia o domínio personalizado.  

Usando as ferramentas de gerenciamento de seu registrador domínio, um registro CNAME precisa ser criado apontando para o domínio personalizado no domínio do Gerenciador de Tráfego.  A figura a seguir mostra um exemplo da aparência dessa configuração CNAME:

![CNAME para um domínio personalizado][CNAMEforCustomDomain] 

Embora não seja abordado neste tópico, lembre-se de que cada instância de aplicativo individual precisa ter também o domínio personalizado registrado nele.  Caso contrário, se uma solicitação a fizer em uma instância de aplicativo e o aplicativo não tiver registrado o domínio personalizado com o aplicativo, a solicitação falhará.

Neste exemplo, o domínio personalizado é `www.scalableasedemo.com` , e cada instância do aplicativo tem o domínio personalizado associado a ele.

![Domínio personalizado][CustomDomain] 

Para uma recapitulação do registro de um domínio personalizado com aplicativos de serviço Azure App, consulte [registrando domínios personalizados][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Experimentando a topologia distribuída
O resultado final da configuração do Gerenciador de Tráfego e do DNS é que as solicitações ao `www.scalableasedemo.com` fluirão através da seguinte sequência:

1. Um navegador ou dispositivo fará uma pesquisa de DNS por `www.scalableasedemo.com`
2. A entrada CNAME no registrador de domínio faz com que a pesquisa de DNS seja redirecionada para o Gerenciador de Tráfego do Azure.
3. Uma pesquisa de DNS é feita para *scalable-ase-demo.trafficmanager.net* em um dos servidores DNS do Gerenciador de Tráfego do Azure.
4. Com base na política de balanceamento de carga especificada anteriormente no parâmetro *TrafficRoutingMethod* , o Gerenciador de tráfego seleciona um dos pontos de extremidade configurados. Em seguida, ele retorna o FQDN do ponto de extremidade para o navegador ou dispositivo.
5. Como o FQDN do ponto de extremidade é a URL de uma instância de aplicativo que é executada em um Ambiente do Serviço de Aplicativo, o navegador ou dispositivo solicitará que um servidor DNS Microsoft Azure resolva o FQDN para um endereço IP. 
6. O navegador ou dispositivo enviará a solicitação HTTP/S para o endereço IP.  
7. A solicitação chegará em uma das instâncias do aplicativo em execução em um dos Ambientes de Serviço do Aplicativo.

A imagem do console abaixo mostra uma pesquisa de DNS para o domínio personalizado do aplicativo de exemplo. Ele é resolvido com êxito para uma instância de aplicativo que é executada em um dos três ambientes de serviço de aplicativo de exemplo (nesse caso, o segundo dos três ambientes de serviço de aplicativo):

![Pesquisa de DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Informações e links adicionais
Documentação sobre o [suporte do Gerenciador de tráfego do Azure Resource Manager][ARMTrafficManager]PowerShell.  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
