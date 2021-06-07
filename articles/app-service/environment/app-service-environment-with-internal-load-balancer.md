---
title: Criar um ASE v1 ILB
description: Criando e usando um ASE com um ILB. Este documento é fornecido somente para clientes que usam o ASE v1 herdado.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c4e5dedf2075a2e13cc91c5eed2c0f03ba498b97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021513"
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
* crie aplicativos de back-end isolados de internet, com os quais seus aplicativos de front-end podem integrar com segurança

#### <a name="disabled-functionality"></a>Funcionalidade desabilitada
Há algumas coisas que você não pode fazer ao usar um ASE com ILB. Entre elas:

* usar IPSSL
* atribuir IP endereços para aplicativos específicos
* comprar e usar um certificado com um aplicativo por meio do portal. Obviamente, você ainda pode obter certificados diretamente com uma Autoridade de Certificação e usá-los com seus aplicativos, mas não através do Portal do Azure.

## <a name="creating-an-ilb-ase"></a>Criar um ILB do ASE
A criação de um ILB do ASE não é muito diferente da criação normal de um ASE. Para uma discussão mais detalhada sobre a criação de um ASE, consulte [Como criar um Ambiente do Serviço de Aplicativo][HowtoCreateASE]. O processo de criação de um ILB do ASE é o mesmo tanto para a criação de uma VNet durante a criação do ASE quanto para a seleção de uma VNet pré-existente. Para criar um ASE ILB: 

1. No Portal do Azure, selecione **Criar um recurso -> Web + Celular -> Ambiente do Serviço de Aplicativo**.
2. Selecione sua assinatura.
3. Selecione ou crie um grupo de recursos.
4. Selecione ou crie uma VNet.
5. Crie uma sub-rede, se você selecionar uma VNet.
6. Selecione **rede virtual/local-configuração de > VNet** e defina o tipo de VIP como interno.
7. Forneça um nome de subdomínio (esse será o subdomínio usado para aplicativos criados nesse ASE).
8. Selecione **OK** e **criar**.

![Mostra as telas usadas para criar um ASE ILB.][1]

No painel Rede Virtual, há uma opção de Configuração VNet que permite selecionar entre um VIP externo ou VIP interno. O padrão é Externo. Se você configurou para Externo, o ASE usará um VIP acessível pela internet. Se você selecionar Interno, o ASE será configurado com um ILB em um endereço IP na sua VNet. 

Após selecionar Interno, a capacidade de adicionar mais endereços IP ao ASE é removida e, em vez disso, será necessário fornecer o subdomínio do ASE. Em um ASE com um VIP Externo, o nome do ASE é usado no subdomínio para aplicativos criados nesse ASE. Se seu ase for chamado de ***ContosoTest** _ e seu aplicativo nesse ase for chamado de _*_myTest_*_, o subdomínio será do formato _*_ContosoTest.p.azurewebsites.net_*_ e a URL desse aplicativo será _* _myTest.ContosoTest.p.azurewebsites.net_* *. Se você definir o tipo de VIP como interno, o nome do ASE não será usado no subdomínio para o ASE. Você especifica o subdomínio explicitamente. Se o subdomínio for ***contoso.Corp.net**_ e você tiver feito um aplicativo nesse ase chamado _*_Reporting_*_, a URL para esse aplicativo será _ *_timereporting.contoso.Corp.net_* *.

## <a name="apps-in-an-ilb-ase"></a>Aplicativos em um ILB do ASE
A criação de um aplicativo em um ILB do ASE é igual à criação normal de um aplicativo em um ASE. 

1. No Portal do Azure, selecione **Criar um recurso -> Web + Celular -> Web** ou **Celular** ou **Aplicativo de API**.
2. Digite o nome do aplicativo.
3. Selecione sua assinatura.
4. Selecione ou crie um grupo de recursos.
5. Selecione ou crie um ASP (Plano do Serviço de Aplicativo). Se estiver criando um novo ASP, selecione o ASE como local e selecione o pool de trabalho no qual você quer que o ASP seja criado. Quando você cria o ASP, você seleciona o ASE como o local e o pool de trabalho. Ao especificar o nome do aplicativo, você verá que o subdomínio no nome do aplicativo é substituído pelo subdomínio do ASE. 
6. Selecione **Criar**. Certifique-se de selecionar a caixa de seleção **Fixar ao painel**, se quiser que o aplicativo apareça em seu painel. 

![Mostra como criar um aplicativo em um ASE ILB no portal do Azure.][2]

Sob o nome do aplicativo, o nome de subdomínio é atualizado para refletir o subdomínio de seu ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Validação posterior à criação do ILB do ASE
Um ASE com ILB é ligeiramente diferente do ASE que não tem um ILB. Como já observamos, é necessário gerenciar seu próprio DNS e você também precisa fornecer seu próprio certificado para conexões HTTPS. 

Após a criação de seu ASE, você observará que o subdomínio mostra o subdomínio especificado e há um novo item no menu **Configuração** chamado **Certificado ILB**. O ASE é criado com um certificado autoassinado, o que torna mais fácil testar o HTTPS. O portal informa que você precisa fornecer seu próprio certificado para HTTPS, mas isso é para incentivá-lo a ter um certificado que acompanhe seu subdomínio. 

![Mostra o subdomínio que você especificou quando criou o ASE.][3]

Se você está simplesmente experimentando coisas e não souber como criar um certificado, use o aplicativo de console IIS MMC para criar um certificado autoassinado. Após a criação, você poderá exportá-lo como um arquivo .pfx e carregá-lo na interface do usuário do Certificado ILB. Ao acessar um site protegido por um certificado autoassinado, o navegador fornecerá um aviso de que o site que você está acessando não é seguro devido à incapacidade de validar o certificado. Se você quiser evitar esse aviso, você precisará de um certificado devidamente assinado que corresponda ao seu subdomínio e tenha uma cadeia de confiança reconhecida pelo seu navegador.

![Mostra como usar o aplicativo de console MMC do IIS para criar um certificado autoassinado.][6]

Se você quiser experimentar o fluxo com seus próprios certificados e testar o acesso HTTP e HTTPS para o seu ASE:

1. Vá para a interface do usuário do ASE depois que o ASE for criado **ase-> configurações-> certificados ILB**.
2. Defina o certificado ILB selecionando o arquivo pfx do certificado e forneça a senha. Essa etapa demora um pouco para processar e a mensagem de que uma operação de escala está em andamento é exibida.
3. Obtenha o endereço ILB para seu ASE (**ase-> Propriedades-> endereço IP virtual**).
4. Crie um aplicativo Web no ASE após a criação. 
5. Crie uma VM, se você não tiver uma naquela VNET (não crie na mesma sub-rede do ASE ou poderão falhar).
6. Configure o DNS para seu subdomínio. Você pode usar caracteres curinga com o subdomínio em seu DNS ou, se quer realizar alguns testes simples, editar o arquivo host na VM para definir o nome do aplicativo Web como o endereço IP VIP. Se o ASE tiver o nome de subdomínio .ilbase.com e você criou o aplicativo Web mytestapp para que ele fosse endereçado em mytestapp.ilbase.com, configure isso no arquivo de hosts. (No Windows, o arquivo dos hosts está em C:\Windows\System32\drivers\etc\)
7. Use um navegador nessa VM e acesse `https://mytestapp.ilbase.com` (ou qualquer nome do aplicativo Web com o seu subdomínio).
8. Use um navegador nessa VM e acesse `https://mytestapp.ilbase.com`. Você deverá aceitar a falta de segurança se estiver usando um certificado autoassinado. 

O endereço IP de seu ILB está listado nas Propriedades como o Endereço IP Virtual.

![Mostra que o endereço IP para seu ILB está listado em suas propriedades como o endereço IP virtual.][4]

## <a name="using-an-ilb-ase"></a>Como usar um ASE com ILB
#### <a name="network-security-groups"></a>Grupos de segurança de rede
Um ASE ILB permite isolamento de rede para seus aplicativos. Os aplicativos não são acessíveis ou mesmo conhecidos pela internet. Essa abordagem é excelente para hospedar sites da Intranet, como aplicativos de linha de negócios. Quando for necessário restringir ainda mais o acesso, você ainda poderá usar os NSGs (Grupos de Segurança de Rede) para controlar o acesso no nível da rede. 

Se você quiser usar os NSGs para restringir ainda mais o acesso, deverá certificar-se de que a comunicação necessária para o ASE operar não será interrompida. Mesmo que o acesso HTTP/HTTPS seja somente através do ILB usado pelo ASE, o ASE ainda depende de recursos fora da VNet. Para ver o acesso à rede ainda necessário, consulte [Controlar o Tráfego de Entrada para um Ambiente do Serviço de Aplicativo][ControlInbound] e  [Detalhes da configuração da rede para Ambientes do Serviço de Aplicativo com ExpressRoute][ExpressRoute]. 

Para configurar os NSGs, é necessário conhecer o endereço IP utilizado pelo Azure para gerenciar o ASE. Esse endereço IP também será o endereço IP de saída de seu ASE se ele fizer solicitações da internet. O endereço IP de saída para o ASE permanece estático durante o ciclo de vida do seu ASE. Se você excluir e recriar seu ASE, você obterá um novo endereço IP. Para localizar o endereço IP, acesse **Configurações -> Propriedades** e localize o **Endereço IP de Saída**. 

![Mostra onde você pode encontrar o endereço IP de saída para seu ASE.][5]

#### <a name="general-ilb-ase-management"></a>Gerenciamento geral do ASE com ILB
O gerenciamento de um ASE com ILB é basicamente o mesmo que gerenciar um ASE normalmente. É necessário escalar verticalmente seus pools de trabalho para hospedar mais instâncias ASP e escalar verticalmente os servidores front-end para lidar com quantidades aumentadas de tráfego HTTP/HTTPS. Para obter informações gerais sobre como gerenciar a configuração de um ASE, consulte [Configurar um Ambiente do Serviço de Aplicativo][ASEConfig]. 

Os itens adicionais de gerenciamento são gerenciamento de certificados e gerenciamento de DNS. É necessário obter e carregar o certificado usado para HTTPS após a criação do ASE com ILB e substitui-lo antes que expire. Como Azure é proprietário do domínio de base, ele pode fornecer certificados para ASEs com um VIP Externo. Como o subdomínio usado por um ASE com ILB pode ser qualquer coisa, é necessário fornecer seu próprio certificado para HTTPS. 

#### <a name="dns-configuration"></a>Configuração de DNS
Ao usar um VIP Externo, o DNS é gerenciado pelo Azure. Qualquer aplicativo criado no ASE é automaticamente adicionado ao DNS do Azure, que é um DNS público. Em um ASE ILB, é necessário gerenciar seu próprio DNS. Para um subdomínio específico, como contoso.corp.net, é necessário criar registros de DNS A que apontem para o endereço ILB para:

- \*
- *.scm
- FTP
- Publicar

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
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md