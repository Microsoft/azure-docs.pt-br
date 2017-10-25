---
title: "Introdução aos ambientes do Serviço de Aplicativo do Azure"
description: "Uma breve visão geral sobre os ambientes do Serviço de Aplicativo do Azure"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 322cf2ebbe83d00fcebcec618e07141d26f4f255
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-app-service-environments"></a>Introdução aos ambientes do Serviço de Aplicativo #
 
## <a name="overview"></a>Visão geral ##

Um Ambiente do Serviço de Aplicativo do Azure é um recurso do Serviço de Aplicativo do Azure que fornece um ambiente totalmente isolado e dedicado a executar com segurança os aplicativos do Serviço de Aplicativo em grande escala. Esse recurso pode hospedar os aplicativos Web, [aplicativos móveis][mobileapps], aplicativos de API e [funções][Functions].

Os ASEs (Ambientes do Serviço de Aplicativo) são apropriados para cargas de trabalho de aplicativos que exijam:

- Escala muito alta.
- Isolamento e acesso à rede segura.
- Alta utilização de memória.

Os clientes podem criar vários ASEs dentro de uma única região do Azure, bem como entre várias regiões do Azure. Isso torna os ASEs ideais para escalar horizontalmente camadas de aplicativo sem monitoração de estado para dar suporte a cargas de trabalho RPS altas.

Os ASEs são isolados para executar somente aplicativos de um único cliente e sempre são implantados em uma rede virtual. Os clientes têm controle refinado sobre o tráfego de rede de entrada e de saída do aplicativo. Os aplicativos podem estabelecer conexões seguras de alta velocidade por VPNs para recursos corporativos locais.

* Os ASEs permitem a hospedagem de aplicativos de grande escala com acesso à rede segura. Para obter mais informações, consulte o [Aprofundamento no AzureCon](https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/) sobre ASEs.
* Vários ASEs podem ser usados para escalar horizontalmente. Para obter mais informações, consulte [Como configurar uma marca do aplicativo distribuído geograficamente](app-service-app-service-environment-geo-distributed-scale.md).
* ASEs podem ser usados para configurar a arquitetura de segurança, conforme mostrado no Aprofundamento no AzureCon. Para ver como a arquitetura de segurança exibida no AzureCon Deep Dive foi configurada, consulte o [artigo sobre a como implementar uma arquitetura de segurança em camadas](app-service-app-service-environment-layered-security.md) com os ambientes do Serviço de Aplicativo.
* Os aplicativos executados nos ASEs podem ter seu acesso restrito por dispositivos upstream como WAFs (firewalls do aplicativo Web). Para saber mais, confira [Configurar um WAF para ambientes do Serviço de Aplicativo](app-service-app-service-environment-web-application-firewall.md).

## <a name="dedicated-environment"></a>Ambiente dedicado ##

Um ASE é dedicado exclusivamente a uma única assinatura e pode hospedar 100 instâncias. O intervalo pode abranger 100 instâncias em um único plano do Serviço de Aplicativo para a 100 planos do Serviço de Aplicativo de instância única e tudo que houver entre essas duas opções.

Um ASE é composto de funções de trabalho e front-ends. Os front-ends são responsáveis pela terminação HTTP/HTTPS, bem como pelo balanceamento de cargas automático de solicitações do aplicativo em um ASE. Os front-ends são automaticamente adicionados já que os planos de Serviço de Aplicativo no ASE são escalados horizontalmente.

As funções de trabalho são funções que hospedam aplicativos cliente. As funções de trabalho estão disponíveis em três tamanhos fixos:

* Um núcleo/3,5 GB de RAM
* Dois núcleos/7 GB de RAM
* Quatro núcleos/14 GB de RAM

Os clientes não precisam gerenciar os front-ends e as funções de trabalho. Toda a infraestrutura é adicionada automaticamente cconforme os clientes dimensionam os planos de Serviço do Aplicativo. Como os planos de Serviço de Aplicativo são criados ou dimensionados em um ASE, a infraestrutura necessária é adicionada ou removida conforme necessário.

Existe uma taxa mensal fixa para um ASE que paga pela infraestrutura e não altera com o tamanho do ASE. Além disso, há um custo por núcleo do plano do Serviço do Aplicativo. Todos os aplicativos hospedados no ASE estão em um SKU de preços Isolado. Para obter informações sobre preços de um ASE, consulte a página [Preços do Serviço de Aplicativo][Pricing] e examine as opções disponíveis para ASEs.

## <a name="virtual-network-support"></a>Suporte de rede virtual ##

Um ASE pode ser criado apenas em uma rede virtual do Azure Resource Manager. Para saber mais sobre redes virtuais do Azure, consulte [Perguntas frequentes sobre redes virtuais do Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/). Um ASE sempre existe em uma rede virtual e mais precisamente, dentro de uma sub-rede de uma rede virtual. Você pode usar os recursos de segurança de redes virtuais para controlar as comunicações de rede de entrada e de saída para seus aplicativos.

Um ASE pode ser voltado para a Internet com um endereço IP público ou voltado para o interior com apenas um endereço de ILB (balanceador de carga Interno) do Azure.

[Grupos de Segurança de Rede][NSGs] restringem a comunicação de rede de entrada à sub-rede na qual um ASE reside. Você pode usar NSGs para executar aplicativos por trás de dispositivos e serviços upstream, tais como WAFs e provedores SaaS de rede.

Aplicativos frequentemente precisam acessar recursos corporativos, como bancos de dados internos e serviços da Web. Se você implantar o ASE em uma rede virtual que tem uma conexão VPN à rede local, os aplicativos no ASE poderão acessar os recursos locais. Essa capacidade é verdadeira independentemente de a VPN ser uma VPN [Site a Site](https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/) ou [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/).

Para obter mais informações sobre o funcionamento dos ASEs com redes virtuais e redes locais, consulte [Considerações de rede do Ambiente do Serviço de Aplicativo][ASENetwork].

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicativo v1 ##

O Ambiente do Serviço de Aplicativo tem duas versões: ASEv1 e ASEv2. As informações anteriores foram baseadas no ASEv2. Esta seção mostra as diferenças entre o ASEv1 e o ASEv2. 

No ASEv1, você precisa gerenciar todos os recursos manualmente. Isso inclui os front-ends, as funções de trabalho e os endereços IP usados para o SSL baseado em IP. Antes de poder escalar horizontalmente o plano do Serviço de Aplicativo, primeiro você precisa escalar horizontalmente o pool de trabalho no qual você deseja hospedá-lo.

O ASEv1 usa um modelo de preço diferente do ASEv2. No ASEv1, você paga por cada núcleo alocado. Isso inclui os núcleos usados para front-ends ou funções de trabalho que não hospedam nenhuma carga de trabalho. No ASEv1, o tamanho máximo de escala padrão de um ASE é de 55 hosts no total. Isso inclui funções de trabalho e front-ends. Uma vantagem do ASEv1 é que ele pode ser implantado em uma rede virtual clássica, bem como em uma rede virtual do Resource Manager. Para saber mais sobre o ASEv1, consulte [Introdução ao Ambiente do Serviço de Aplicativo v1][ASEv1Intro].

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
