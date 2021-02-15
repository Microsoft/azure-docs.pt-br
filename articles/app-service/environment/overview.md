---
title: Visão geral de Ambiente do Serviço de Aplicativo
description: Visão geral sobre o Ambiente do Serviço de Aplicativo
author: ccompy
ms.assetid: 3d37f007-d6f2-4e47-8e26-b844e47ee919
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fbc498fcd654d16936c2548528e2600be68a2ad9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663444"
---
# <a name="app-service-environment-overview"></a>Visão geral de Ambiente do Serviço de Aplicativo 

> [!NOTE]
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v3 (versão prévia)
> 

O Ambiente de Serviço de Aplicativo do Azure é um recurso do Serviço de Aplicativo do Azure que fornece um ambiente totalmente isolado e dedicado a executar com segurança os aplicativos do Serviço de Aplicativo em grande escala. Essa capacidade pode hospedar:

- Aplicativos Web do Windows
- Aplicativos Web do Linux
- Contêineres do Docker
- Funções

Os ASEs (Ambientes do Serviço de Aplicativo) são apropriados para cargas de trabalho de aplicativos que exijam:

- Alta escala.
- Isolamento e acesso seguro à rede.
- Alta utilização de memória.
- Solicitações altas por segundo (RPS). Você pode fazer várias ASEs em uma única região do Azure ou em várias regiões do Azure. Essa flexibilidade torna o ASEs ideal para o dimensionamento horizontal de aplicativos sem monitoração de estado com um requisito de RPS alto.

Os aplicativos host do ASE de apenas um cliente e fazem isso em um de seus VNets. Os clientes têm controle refinado sobre o tráfego de rede de entrada e de saída do aplicativo. Os aplicativos podem estabelecer conexões seguras de alta velocidade por VPNs para recursos corporativos locais.

O ASEv3 vem com seu próprio tipo de preço, isolado v2.
Os ambientes de serviço de aplicativo v3 fornecem um ao redor para proteger seus aplicativos em uma sub-rede de sua rede e fornece sua própria implantação privada do serviço de Azure App.
Vários ASEs podem ser usados para escalar horizontalmente. Os aplicativos executados nos ASEs podem ter seu acesso restrito por dispositivos upstream como WAFs (firewalls do aplicativo Web). Para saber mais, confira WAF (Firewall do aplicativo Web).

## <a name="usage-scenarios"></a>Cenários de uso

O Ambiente do Serviço de Aplicativo tem muitos casos de uso, incluindo:

- Aplicativos internos de linha de negócios
- Aplicativos que precisam de mais de 30 instâncias ASP
- Sistema de locatário único para atender aos requisitos internos de conformidade ou segurança
- Hospedagem de aplicativos isolados de rede
- Aplicativos de várias camadas

Há vários recursos de rede que permitem que aplicativos no serviço de aplicativo multilocatário alcancem recursos isolados da rede ou se tornem isolados da rede. Esses recursos são habilitados no nível do aplicativo.  Com um ASE, não há nenhuma configuração adicional nos aplicativos para que eles estejam na VNet. Os aplicativos são implantados em um ambiente isolado de rede que já está em uma VNet. Na parte superior do ASE que hospeda aplicativos isolados de rede, ele também é um sistema de locatário único. Não há outros clientes usando o ASE. Se você realmente precisar de uma história de isolamento completa, também poderá fazer com que seu ASE seja implantado no hardware dedicado. Entre Hospedagem de aplicativo isolada de rede, locação única e a capacidade 

## <a name="dedicated-environment"></a>Ambiente dedicado
Um ASE é dedicado exclusivamente a uma única assinatura e pode hospedar 200 instâncias do plano do serviço de aplicativo. O intervalo pode abranger 100 instâncias em um único plano do Serviço de Aplicativo para a 100 planos do Serviço de Aplicativo de instância única e tudo que houver entre essas duas opções.

Um ASE é composto de funções de trabalho e front-ends. Os front-ends são responsáveis pela terminação HTTP/HTTPS, bem como pelo balanceamento de cargas automático de solicitações do aplicativo em um ASE. Os front-ends são automaticamente adicionados já que os planos de Serviço de Aplicativo no ASE são escalados horizontalmente.

As funções de trabalho são funções que hospedam aplicativos cliente. As funções de trabalho estão disponíveis em três tamanhos fixos:

- Dois vCPU/8 GB de RAM
- Quatro vCPU/16 GB de RAM
- Oito vCPU/32 GB de RAM

Os clientes não precisam gerenciar front-ends e trabalhos. Toda a infraestrutura é automaticamente. Como os planos de Serviço de Aplicativo são criados ou dimensionados em um ASE, a infraestrutura necessária é adicionada ou removida conforme necessário.

Há um encargo para instâncias de plano do serviço de aplicativo v2 isoladas. Se você não tiver nenhum plano do serviço de aplicativo em seu ASE, será cobrado como se você tivesse um plano do serviço de aplicativo com uma instância dos dois principais funcionários.

## <a name="virtual-network-support"></a>Suporte de rede virtual
O recurso ASE é uma implantação do Serviço de Aplicativo Azure diretamente na rede virtual do Azure Resource Manager. Um ASE sempre existe em uma sub-rede de uma rede virtual. Você pode usar os recursos de segurança de redes virtuais para controlar as comunicações de rede de entrada e de saída para seus aplicativos.

Grupos de Segurança de Rede restringem a comunicação da rede de entrada com a sub-rede na qual um ASE reside. Você pode usar NSGs para executar aplicativos por trás de dispositivos e serviços upstream, tais como WAFs e provedores SaaS de rede.

Aplicativos frequentemente precisam acessar recursos corporativos, como bancos de dados internos e serviços da Web. Se você implantar o ASE em uma rede virtual que tem uma conexão VPN à rede local, os aplicativos no ASE poderão acessar os recursos locais. Essa capacidade é verdadeira independentemente de a VPN ser uma VPN Site a Site ou Azure ExpressRoute.

## <a name="preview"></a>Versão Prévia
O Ambiente do Serviço de Aplicativo v3 está em visualização pública.  Alguns recursos estão sendo adicionados durante a progressão da visualização. As limitações atuais do ASEv3 incluem:

- Incapacidade de dimensionar um plano do serviço de aplicativo para além de cinco instâncias
- Incapacidade de obter um contêiner de um registro privado
- Incapacidade de recursos do serviço de aplicativo sem suporte atualmente para passar pela VNet do cliente
- Nenhum modelo de implantação externo com um ponto de extremidade acessível pela Internet
- Não há suporte para linha de comando (AZ CLI e PowerShell)
- Nenhum recurso de atualização de ASEv2 para ASEv3
- Sem suporte a FTP
- Não há suporte para alguns recursos do serviço de aplicativo que passam pela VNet do cliente. Backup/restauração, Key Vault referências em configurações de aplicativo, usando um registro de contêiner privado e o log de diagnóstico para o armazenamento não funcionará com pontos de extremidade de serviço ou pontos de extremidade privados
    
### <a name="asev3-preview-architecture"></a>Arquitetura de visualização do ASEv3
Na visualização do ASEv3, o ASE usará pontos de extremidade privados para dar suporte ao tráfego de entrada. O ponto de extremidade privado será substituído por balanceadores de carga por GA. Durante a visualização, o ASE não terá suporte interno para um ponto de extremidade acessível pela Internet. Você pode adicionar um gateway de aplicativo para tal finalidade. O ASE precisa de recursos em duas sub-redes.  O tráfego de entrada fluirá por meio de um ponto de extremidade privado. O ponto de extremidade privado pode ser colocado em qualquer sub-rede, desde que ele tenha um endereço disponível que possa ser usado por pontos de extremidade privados.  A sub-rede de saída deve estar vazia e delegada a Microsoft. Web/hostingEnvironments. Enquanto usado pelo ASE, a sub-rede de saída não pode ser usada para nada mais.

Com o ASEv3, não há nenhum requisito de entrada ou de rede de saída na sub-rede do ASE. Você pode controlar o tráfego com os grupos de segurança de rede e as tabelas de rotas e isso afetará apenas o tráfego do aplicativo. Não exclua o ponto de extremidade privado associado ao seu ASE, pois essa ação não pode ser desfeita. O ponto de extremidade privado usado para o ASE é usado para todos os aplicativos no ASE. 
