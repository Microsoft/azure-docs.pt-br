---
title: "Como criar e usar um Balanceador de Carga Interna com um Ambiente do Serviço de Aplicativo | Microsoft Docs"
description: Criar e usar um ASE com um ILB
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: c0f942cb11edc6d6212914b3134e25c3fbc3d3ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Como usar um Balanceador de Carga Interno com um Ambiente do Serviço do Aplicativo

> [!NOTE] 
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v1. Há uma versão mais recente do Ambiente de Serviço de Aplicativo que é mais fácil de usar e é executado na infraestrutura mais avançada. Para saber mais sobre a nova versão, comece com [Introdução ao Ambiente do Serviço de Aplicativo](intro.md).
>

O recurso ASE (Ambiente do Serviço de Aplicativo) é uma opção de serviço Premium do Serviço de Aplicativo do Azure que fornece uma capacidade configuração avançada não disponível em carimbos com vários locatários. Essencialmente, o recurso ASE implanta o Serviço de Aplicativo do Azure em sua VNet (Rede virtual do Azure). Para obter uma maior compreensão dos recursos oferecidos pelos ambientes do serviço de aplicativo, leia a documentação [O que é um ambiente do serviço de aplicativo][WhatisASE]. Se você não conhece os benefícios de operar em uma VNet, leia as [Perguntas frequentes de Rede Virtual do Azure][virtualnetwork]. 

## <a name="overview"></a>Visão geral
Um ASE pode ser implantado com um ponto de extremidade acessível da internet ou com um endereço IP em sua VNet. Para definir o endereço IP como um endereço de VNet, você precisa implantar seu ASE com um ILB (Balanceador de Carga Interno). Quando seu ASE é configurado com um ILB, você fornece:

* seu próprio domínio ou subdomínio. Para facilitar, este documento considera o subdomínio, mas você pode configurá-lo de qualquer maneira. 
* o certificado usado para HTTPS
* Gerenciamento de DNS para o subdomínio. 

Em troca, você pode fazer coisas como:

* hospedar aplicativos de intranet, por exemplo, aplicativos de linha de negócios, com segurança na nuvem, que você acessa por meio de uma VPN de ExpressRoute Site a Site
* hospedar aplicativos na nuvem que não estão listados em servidores de DNS públicos
* criar aplicativos back-end isolados da internet, aos quais seus aplicativos front-end podem ser integrados com segurança

#### <a name="disabled-functionality"></a>Funcionalidade desabilitada
Há algumas coisas que você não pode fazer ao usar um ASE com ILB. Entre elas:

* usar IPSSL
* atribuir IP endereços para aplicativos específicos
* comprar e usar um certificado com um aplicativo por meio do portal. Obviamente, você ainda pode obter certificados diretamente com uma Autoridade de Certificação e usá-los com seus aplicativos, mas não por meio do Portal do Azure.

## <a name="creating-an-ilb-ase"></a>Criar um ILB do ASE
A criação de um ILB do ASE não é muito diferente da criação normal de um ASE. Para conferir uma discussão mais profunda sobre como criar um ASE, leia [Como criar um Ambiente do Serviço de Aplicativo][HowtoCreateASE]. O processo de criação de um ILB do ASE é o mesmo tanto para a criação de uma VNet durante a criação do ASE quanto para a seleção de uma VNet pré-existente. Para criar um ILB do ASE: 

1. No Portal do Azure, escolha **Novo -> Web + Móvel -> Ambiente do Serviço de Aplicativo**
2. Selecione sua assinatura
3. Selecione ou crie um grupo de recursos
4. Selecione ou crie uma VNet
5. Crie uma sub-rede, se você selecionar uma VNet
6. Selecione **Rede Virtual/Localização -> Configuração de VNet** e defina o Tipo de VIP como Interno
7. Forneça um nome de subdomínio (este será o subdomínio usado para aplicativos criados nesse ASE)
8. Selecione OK e Criar

![][1]

Na folha Rede Virtual, há uma opção de Configuração de Rede Virtual. Isso permite a escolha entre um VIP Externo ou um VIP Interno. O padrão é Externo. Se você tiver definido como Externo, seu ASE usará um VIP acessível pela internet. Se você selecionar Interno, seu ASE será configurado com um ILB em um endereço IP em sua VNet. 

Depois de selecionar Interno, a capacidade de adicionar mais endereços IP ao seu ASE será removida e, em vez disso, você precisará fornecer o subdomínio do ASE. Em um ASE com um VIP Externo, o nome do ASE é usado no subdomínio para aplicativos criados nesse ASE. Se o seu ASE recebeu o nome de ***contosotest*** e seu aplicativo nesse ASE recebeu o nome de ***mytest***, o subdomínio estará no formato ***contosotest.p.azurewebsites.net*** e a URL desse aplicativo será ***mytest.contosotest.p.azurewebsites.net***. Se você definir o Tipo VIP como Interno, o nome de seu ASE não será usado no subdomínio do ASE. Especifique o subdomínio explicitamente. Se o subdomínio era ***contoso.corp.net*** e você criou um aplicativo nesse ASE chamado ***timereporting***, a URL para esse aplicativo seria ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Aplicativos em um ILB do ASE
A criação de um aplicativo em um ILB do ASE é igual à criação normal de um aplicativo em um ASE. 

1. No Portal do Azure, escolha **Novo -> Web + Móvel -> Web** ou **Móvel** ou **Aplicativo de API**
2. Insira o nome do aplicativo
3. Escolha a assinatura
4. Escolha ou crie um grupo de recursos
5. Selecione ou crie um ASP (Plano do Serviço de Aplicativo). Se está criando um novo ASP, selecione seu ASE como o local e selecione o pool de trabalho no qual você deseja criar o ASP. Ao criar o ASP, selecione seu ASE como o local e o pool de trabalho. Ao especificar o nome do aplicativo, você verá que o subdomínio no nome do aplicativo é substituído pelo subdomínio do ASE. 
6. Selecione Criar. Você deve marcar a caixa de seleção **Fixar ao painel** se quiser que o aplicativo apareça em seu painel. 

![][2]

Sob o nome do aplicativo, o nome de subdomínio é atualizado para refletir o subdomínio de seu ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Validação posterior à criação do ILB do ASE
Um ASE com ILB é ligeiramente diferente do ASE que não tem um ILB. Como já observamos, é necessário gerenciar seu próprio DNS e você também precisa fornecer seu próprio certificado para conexões HTTPS. 

Após a criação de seu ASE, você observará que o subdomínio mostra o subdomínio especificado e há um novo item no menu **Configuração** chamado **Certificado ILB**. O ASE é criado com um certificado autoassinado, o que torna mais fácil testar o HTTPS. O portal informará que você precisa fornecer seu próprio certificado para HTTPS, mas isso é para direcionar a ter um certificado que acompanha seu subdomínio. 

![][3]

Se você está simplesmente experimentando coisas e não souber como criar um certificado, use o aplicativo de console IIS MMC para criar um certificado autoassinado. Após a criação, você poderá exportá-lo como um arquivo .pfx e carregá-lo na interface de usuário do Certificado do ILB. Quando você acessa um site protegido por um certificado autoassinado, seu navegador avisa você de que o site que você está acessando não é seguro devido à incapacidade de validar o certificado. Se você quiser evitar esse aviso, precisará de um certificado devidamente assinado que corresponda ao seu subdomínio e tenha uma cadeia de confiança reconhecida pelo seu navegador.

![][6]

Se você quiser experimentar o fluxo com seus próprios certificados e testar o acesso HTTP e HTTPS para o seu ASE:

1. Acesse a interface do usuário do ASE após a criação do ASE em **ASE -> Configurações -> Certificados ILB**
2. Defina o certificado ILB selecionando o arquivo pfx do certificado e forneça a senha. Esta etapa demora um pouco mais para ser processada, e a mensagem de que uma operação de dimensionamento está em andamento será exibida.
3. Obtenha o endereço do ILB para seu ASE (**ASE -> Propriedades -> Endereço IP Virtual**)
4. Crie um aplicativo Web no ASE após a criação 
5. Crie uma VM se você não tem uma nessa VNET (não na mesma sub-rede que o ASE, ou tudo pode falhar)
6. Configure o DNS para seu subdomínio. Você pode usar caracteres curinga com o subdomínio em seu DNS ou, se quer realizar alguns testes simples, editar o arquivo host na VM para definir o nome do aplicativo Web como o endereço IP VIP. Se o seu ASE tem o nome de subdomínio .ilbase.com e você colocou o aplicativo Web mytestapp para ter como endereço mytestapp.ilbase.com, defina isso em seu arquivo host. (No Windows, o arquivo host está em C:\Windows\System32\drivers\etc\ )
7. Use um navegador nessa VM e acesse http://mytestapp.ilbase.com (ou o nome do aplicativo Web com o subdomínio)
8. Use um navegador nessa VM e acesse https://mytestapp.ilbase.com. Será necessário aceitar a falta de segurança se estiver usando um certificado autoassinado. 

O endereço IP de seu ILB está listado em suas Propriedades como o Endereço IP Virtual

![][4]

## <a name="using-an-ilb-ase"></a>Como usar um ASE com ILB
#### <a name="network-security-groups"></a>Grupos de segurança de rede
Um ASE com ILB permite o isolamento da rede para seus aplicativos, como se os aplicativos não pudessem ser acessados ou não fossem até mesmo conhecidos pela internet. Isso é excelente para hospedar sites de intranet, como aplicativos de linha de negócios. Quando for necessário restringir o acesso ainda mais, você poderá usar os NSGs (Grupos de Segurança de Rede) para controlar o acesso no nível da rede. 

Se você quiser usar os NSGs para restringir o acesso, precisará fazer com que a comunicação que o ASE precisa para operar não seja interrompida. Embora o acesso HTTP/HTTPS ocorra apenas por meio do ILB usado pelo ASE, o ASE ainda depende do recurso fora da VNet. Para ver qual acesso de rede ainda é necessário, examine as informações no documento sobre [Como controlar o tráfego de entrada para um ambiente do Serviço de Aplicativo][ControlInbound] e o documento sobre [Detalhes da configuração de rede para ambientes do Serviço de Aplicativo com o ExpressRoute][ExpressRoute]. 

Para configurar seus NSGs, você precisa saber o endereço IP usado pelo Azure para gerenciar seu ASE. Esse endereço IP também será o endereço IP de saída de seu ASE se ele fizer solicitações da internet. O endereço IP de saída para o ASE permanece estático durante a vida de seu ASE. Se você excluir e recriar seu ASE, você obterá um novo endereço IP. Para localizar esse endereço IP, acesse **Configurações -> Propriedades** e localize o **Endereço IP de Saída**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Gerenciamento geral do ASE com ILB
O gerenciamento de um ASE com ILB é basicamente o mesmo que gerenciar um ASE normalmente. Você precisa aumentar seus pools de trabalho para hospedar mais instâncias de ASP e dimensionar os servidores Front-End para lidar com o aumento do tráfego HTTP/HTTPS. Para obter informações gerais sobre como gerenciar a configuração de um ASE, leia o documento sobre [Configurar um ambiente do Serviço de Aplicativo][ASEConfig]. 

Os itens adicionais de gerenciamento são gerenciamento de certificados e gerenciamento de DNS. Você precisa obter e carregar o certificado usado para HTTPS após a criação do ASE com ILB e substitui-lo antes que expire. Como Azure é proprietário do domínio de base, podemos fornecer certificados para ASEs com um VIP externo. Como o subdomínio usado por um ASE com ILB pode ser qualquer coisa, você precisa fornecer seu próprio certificado para HTTPS. 

#### <a name="dns-configuration"></a>Configuração de DNS
Ao usar um VIP Externo, o DNS é gerenciado pelo Azure. Qualquer aplicativo criado em seu ASE é automaticamente adicionado ao DNS do Azure, que é um DNS público. Em um ASE com ILB, você precisa gerenciar seu próprio DNS. Para um subdomínio específico, como contoso.corp.net, você precisa criar registros de DNS A que apontem para o endereço ILB para:

    * 
    publicação de ftp *.scm 


## <a name="getting-started"></a>Introdução
Para se familiarizar com os ambientes de serviço de aplicativo, consulte [Introdução aos ambientes de Serviço de Aplicativo][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
