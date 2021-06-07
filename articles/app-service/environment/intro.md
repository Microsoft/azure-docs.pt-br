---
title: Introdução
description: Saiba como os Ambientes do Serviço de Aplicativo do Azure ajudam você a dimensionar, proteger e otimizar seus aplicativos em um ambiente totalmente isolado e dedicado.
author: ccompy
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.topic: overview
ms.date: 04/19/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 6fff19498e9ca70991d3190165df70a48136f502
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92631131"
---
# <a name="introduction-to-the-app-service-environments"></a>Introdução aos Ambientes de Serviço de Aplicativo #
## <a name="overview"></a>Visão geral ##

O Ambiente de Serviço de Aplicativo do Azure é um recurso do Serviço de Aplicativo do Azure que fornece um ambiente totalmente isolado e dedicado a executar com segurança os aplicativos do Serviço de Aplicativo em grande escala. Essa capacidade pode hospedar:

* Aplicativos Web do Windows
* Aplicativos Web do Linux 
* Contêineres do Docker
* Aplicativos móveis
* Funções

Os ASEs (Ambientes do Serviço de Aplicativo) são apropriados para cargas de trabalho de aplicativos que exijam:

* Uma escala muito alta.
* Isolamento e acesso seguro à rede.
* Alta utilização de memória.

Os clientes podem criar vários ASEs dentro de uma única região do Azure, bem como entre várias regiões do Azure. Isso torna os ASEs ideais para escalar horizontalmente camadas de aplicativo sem monitoração de estado para dar suporte a cargas de trabalho com altas RPS (solicitações por segundo).

Os ASEs hospedam aplicativos de apenas um cliente e fazem isso em uma das VNets. Os clientes têm controle refinado sobre o tráfego de rede de entrada e de saída do aplicativo. Os aplicativos podem estabelecer conexões seguras de alta velocidade por VPNs para recursos corporativos locais.

* ASE vem com seu próprio preço da camada, saiba como a [oferta isolada](https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment) ajuda a gerar hiperescala e segurança.
* [V2 de Ambientes do Serviço de Aplicativo](https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment) fornece uma proteção completa para seus aplicativos em uma sub-rede de sua rede e fornece sua própria implementação privada do Serviço de Aplicativo do Azure.
* Vários ASEs podem ser usados para escalar horizontalmente. Para obter mais informações, consulte [Como configurar uma marca do aplicativo distribuído geograficamente](app-service-app-service-environment-geo-distributed-scale.md).
* ASEs podem ser usados para configurar a arquitetura de segurança, conforme mostrado no Aprofundamento no AzureCon. Para ver como a arquitetura de segurança exibida no AzureCon Deep Dive foi configurada, consulte o [artigo sobre a como implementar uma arquitetura de segurança em camadas](app-service-app-service-environment-layered-security.md) com os ambientes do Serviço de Aplicativo.
* Os aplicativos executados nos ASEs podem ter seu acesso restrito por dispositivos upstream como WAFs (firewalls do aplicativo Web). Para saber mais, confira [WAF (Firewall do aplicativo Web)][AppGW].
* Os Ambientes do Serviço de Aplicativo podem ser implantados em AZ (Zonas de Disponibilidade) usando a anexação de zona.  Confira [Suporte do Ambiente do Serviço de Aplicativo para Zonas de Disponibilidade][ASEAZ] para obter mais detalhes.

## <a name="dedicated-environment"></a>Ambiente dedicado ##

Um ASE é dedicado exclusivamente a uma única assinatura e pode hospedar 100 instâncias do Plano do Serviço de Aplicativo. O intervalo pode abranger 100 instâncias em um único plano do Serviço de Aplicativo para a 100 planos do Serviço de Aplicativo de instância única e tudo que houver entre essas duas opções.

Um ASE é composto de funções de trabalho e front-ends. Os front-ends são responsáveis pela terminação HTTP/HTTPS, bem como pelo balanceamento de cargas automático de solicitações do aplicativo em um ASE. Os front-ends são automaticamente adicionados já que os planos de Serviço de Aplicativo no ASE são escalados horizontalmente.

As funções de trabalho são funções que hospedam aplicativos cliente. As funções de trabalho estão disponíveis em três tamanhos fixos:

* Um vCPU/3,5 GB de RAM
* Dois vCPU/7 GB de RAM
* Quatro vCPU/14 GB de RAM

Os clientes não precisam gerenciar os front-ends e as funções de trabalho. Toda a infraestrutura é adicionada automaticamente cconforme os clientes escalonam horizontalmente os planos de Serviço do Aplicativo. Como os planos de Serviço de Aplicativo são criados ou dimensionados em um ASE, a infraestrutura necessária é adicionada ou removida conforme necessário.

Existe uma taxa mensal fixa para um ASE que paga pela infraestrutura e não altera com o tamanho do ASE. Além disso, há um custo por vCPU do plano do Serviço do Aplicativo. Todos os aplicativos hospedados no ASE estão em um SKU de preços Isolado. Para obter informações sobre os preços de um ASE, confira a página [Preços do Serviço de Aplicativo][Pricing] e examine as opções disponíveis para ASEs.

## <a name="virtual-network-support"></a>Suporte de rede virtual ##

O recurso ASE é uma implantação do Serviço de Aplicativo Azure diretamente na rede virtual do Azure Resource Manager. Para saber mais sobre redes virtuais do Azure, consulte [Perguntas frequentes sobre redes virtuais do Azure](../../virtual-network/virtual-networks-faq.md). Um ASE sempre existe em uma rede virtual e mais precisamente, dentro de uma sub-rede de uma rede virtual. Você pode usar os recursos de segurança de redes virtuais para controlar as comunicações de rede de entrada e de saída para seus aplicativos.

Um ASE pode ser voltado para a Internet com um endereço IP público ou voltado para o interior com apenas um endereço de ILB (balanceador de carga Interno) do Azure.

[Grupos de Segurança de Rede][NSGs] restringem a comunicação da rede de entrada com a sub-rede na qual um ASE reside. Você pode usar NSGs para executar aplicativos por trás de dispositivos e serviços upstream, tais como WAFs e provedores SaaS de rede.

Aplicativos frequentemente precisam acessar recursos corporativos, como bancos de dados internos e serviços da Web. Se você implantar o ASE em uma rede virtual que tem uma conexão VPN à rede local, os aplicativos no ASE poderão acessar os recursos locais. Essa capacidade é verdadeira independentemente de a VPN ser uma VPN [Site a Site](../../vpn-gateway/vpn-gateway-multi-site.md) ou [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Para obter mais informações sobre o funcionamento dos ASEs com redes virtuais e redes locais, confira [Considerações de rede do Ambiente do Serviço de Aplicativo][ASENetwork].

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud/player]

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicativo v1 ##

O Ambiente do Serviço de Aplicativo tem duas versões: ASEv1 e ASEv2. As informações anteriores foram baseadas no ASEv2. Esta seção mostra as diferenças entre o ASEv1 e o ASEv2. 

No ASEv1, você precisa gerenciar todos os recursos manualmente. Isso inclui os front-ends, as funções de trabalho e os endereços IP usados para o SSL baseado em IP. Antes de poder escalar horizontalmente o plano do Serviço de Aplicativo, primeiro você precisa escalar horizontalmente o pool de trabalho no qual você deseja hospedá-lo.

O ASEv1 usa um modelo de preço diferente do ASEv2. No ASEv1, você paga por cada vCPU alocado. Isso inclui os vCPUs usados para front-ends ou funções de trabalho que não hospedem nenhuma carga de trabalho. No ASEv1, o tamanho máximo de escala padrão de um ASE é de 55 hosts no total. Isso inclui funções de trabalho e front-ends. Uma vantagem do ASEv1 é que ele pode ser implantado em uma rede virtual clássica, bem como em uma rede virtual do Resource Manager. Para saber mais sobre o ASEv1, confira [Introdução ao Ambiente do Serviço de Aplicativo v1][ASEv1Intro].

<!--Links-->
[App Service Environments v2]: https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment
[Isolated offering]: https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ASEAZ]: https://azure.github.io/AppService/2019/12/12/App-Service-Environment-Support-for-Availability-Zones.html