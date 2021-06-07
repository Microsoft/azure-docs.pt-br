---
title: Criar um ILB ASE com o ARM
description: Saiba como criar um ILB ASE (Ambiente do Serviço de Aplicativo com um balanceador de carga interno) usando modelos do Azure Resource Manager. Isole totalmente seus aplicativos da Internet.
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 27c9198558a730d0af49077d6f5baa6db4789416
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009544"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Criar e usar um Ambiente do Serviço de Aplicativo de Balanceador de Carga Interno 

O Ambiente do Serviço de Aplicativo do Azure é uma implantação do Serviço de Aplicativo do Azure em uma sub-rede de uma VNet (rede virtual) do Azure. Há duas maneiras de implantar um ASE (ambiente do serviço de aplicativo): 

- Com um VIP em um endereço IP externo, geralmente chamado de ASE externo.
- Com um VIP em um endereço IP interno, geralmente chamado de ASE ILB devido ao ponto de extremidade interno ser um balanceador de carga interno (ILB). 

Este artigo mostra como criar um ASE ILB. Para obter uma visão geral do ASE, confira [Introdução aos ambientes do Serviço de Aplicativo][Intro]. Para saber como criar um ASE externo, confira [Como criar um ASE externo][MakeExternalASE].

## <a name="overview"></a>Visão geral 

Um ASE pode ser implantado com um ponto de extremidade acessível pela Internet ou com um endereço IP em sua VNet. Para definir o endereço IP como um endereço de VNet, o ASE deve ser implantado com um ILB. Quando você implanta o ASE com um ILB, você deve fornecer o nome do seu ASE. O nome do ASE é usado no sufixo de domínio para os aplicativos em seu ASE.  O sufixo de domínio do seu ILB ASE é &lt;ASE name&gt;.appserviceenvironment.net. Aplicativos feitos em um ILB ASE não são colocados no DNS público. 

Versões anteriores do ILB ASE exigiam que você fornecesse um sufixo de domínio e um certificado padrão para conexões HTTPS. O sufixo de domínio não é mais coletado na criação do ILB ASE, e um certificado padrão também não é mais é coletado. Agora, quando você cria um ILB ASE, o certificado padrão é fornecido pela Microsoft e é de confiança do navegador. Ainda é possível definir nomes de domínio personalizados em aplicativos em seu ASE e definir certificados nesses nomes de domínio personalizados. 

Com um ILB ASE, você pode fazer coisas como:

-   hospedar aplicativos de intranet com segurança na nuvem, que são acessados site a site ou por ExpressRoute.
-   Proteger aplicativos com um dispositivo WAF
-   Hospedar aplicativos na nuvem que não estão listados em servidores DNS públicos.
-   Criar aplicativos back-end isolados da Internet, que podem ser integrados com seus aplicativos front-end com segurança.

### <a name="disabled-functionality"></a>Funcionalidade desabilitada ###

Há algumas coisas que você não pode fazer ao usar um ASE ILB:

-   Usar SSL baseado em IP.
-   Atribuir endereços IP a aplicativos específicos.
-   Comprar e usar um certificado com um aplicativo por meio do portal do Azure. Você pode obter certificados diretamente de uma autoridade de certificação e usá-los com seus aplicativos. Não é possível obtê-los por meio do portal do Azure.

## <a name="create-an-ilb-ase"></a>Criar um ASE ILB ##

Para criar um ASE ILB:

1. No portal do Azure, selecione **Criar um recurso** > **Web** > **Ambiente do Serviço de Aplicativo** .

2. Selecione sua assinatura.

3. Selecione ou crie um grupo de recursos.

4. Insira o nome do seu Ambiente do Serviço de Aplicativo.

5. Selecione o tipo de IP virtual Interno.

    ![Criação de ASE](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

> [!NOTE]
> O nome do Ambiente do Serviço de Aplicativo deve ter, no máximo, 37 caracteres.

6. Selecione Rede

7. Selecione ou crie uma Rede Virtual. Se você criar uma nova VNet, ela será definida com um intervalo de endereços de 192.168.250.0/23. Para criar uma VNet com um intervalo de endereços diferentes ou em um grupo de recursos diferente do ASE, use o portal de criação da Rede Virtual do Azure. 

8. Selecione ou crie uma sub-rede vazia. Se você quiser selecionar uma sub-rede, ela deverá estar vazia e não delegada. O tamanho da sub-rede não pode ser alterado após a criação do ASE. O tamanho recomendado é `/24`, que tem 256 endereços e é compatível com um ASE com tamanho máximo e quaisquer necessidades de dimensionamento. 

    ![Rede do ASE][1]

7. Selecione **Revisar e Criar** e selecione **Criar**.


## <a name="create-an-app-in-an-ilb-ase"></a>Como criar um aplicativo em uma ASE ILB ##

Você pode criar um aplicativo em uma ASE ILB da mesma maneira que você cria um aplicativo em um ASE.

1. No portal do Azure, selecione **Criar um recurso** > **Web** > **Aplicativo Web**.

1. Digite o nome do aplicativo.

1. Selecione a assinatura.

1. Selecione ou crie um grupo de recursos.

1. Selecione a Publicação, Pilha de Runtime e Sistema Operacional.

1. Selecione um local que seja um ILB ASE existente.  Você também pode criar um novo ASE durante a criação do aplicativo, selecionando um plano do Serviço de Aplicativo Isolado. Se você quiser criar um novo ASE, selecione a região na qual você deseja criar o ASE.

1. Selecione ou crie um plano do Serviço de Aplicativo. 

1. Selecione **Revisar e Criar** e selecione **Criar** quando tudo estiver pronto.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Trabalhos da Web, Funções e o ILB ASE 

As Funções e os trabalhos da Web são suportados em um ILB ASE, mas para que o portal funcione com eles, você deve ter acesso de rede ao site SCM.  Isso significa que seu navegador deve estar em um host que esteja na rede virtual ou conectado a ela. Se o ILB ASE tiver um nome de domínio que não terminam em *appserviceenvironment.net*, você precisará fazer seu navegador confiar no certificado HTTPS que está sendo usado por seu site do scm.

## <a name="dns-configuration"></a>Configuração de DNS 

Quando você usa um ASE Externo, os aplicativos criados no ASE são registrados com o DNS do Azure. Não há etapas adicionais em um ASE Externo para os aplicativos fiquem disponíveis publicamente. Com um ASE do ILB, você precisa gerenciar seu DNS. Você pode fazer isso em seu servidor DNS ou em Zonas Privadas do DNS do Azure.

Para configurar o DNS em seu servidor DNS com o ASE do ILB:

1. crie uma zona para &lt;nome do ASE&gt;.appserviceenvironment.net
2. crie um registro A nessa zona que aponte * para o endereço IP do ILB
3. crie um registro A nessa zona que aponte @ para o endereço IP do ILB
4. crie uma zona em &lt;nome do ASE&gt;.appserviceenvironment.net chamada scm
5. crie um registro A na zona scm que aponte * para o endereço IP do ILB

Para configurar o DNS em Zonas Privadas do DNS do Azure:

1. crie uma zona privada de DNS do Azure chamada &lt;ASE name&gt;.appserviceenvironment.net
2. crie um registro A nessa zona que aponte * para o endereço IP do ILB
3. crie um registro A nessa zona que aponte @ para o endereço IP do ILB
4. crie um registro A nessa zona que aponte *.scm para o endereço IP do ILB

As configurações de DNS do sufixo de domínio padrão do ASE não restringem seus aplicativos a serem acessados apenas por esses nomes. Você pode definir um nome de domínio personalizado sem nenhuma validação em seus aplicativos em um ASE do ILB. Se quiser criar uma zona chamada contoso.net, você poderá fazer isso e apontá-la para o endereço IP do ILB. O nome de domínio personalizado funciona para solicitações de aplicativo, mas não para o site do scm. O site do scm só está disponível em &lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net.

A zona chamada .&lt;asename&gt;.appserviceenvironment.net é globalmente exclusiva. Antes de maio de 2019, os usuários podiam especificar o sufixo de domínio do ASE do ILB. Se quisesse usar .contoso.com como sufixo de domínio, você poderia fazer isso e o site do scm seria incluído. Havia desafios com esse modelo, incluindo o gerenciamento do certificado SSL padrão, a falta de logon único com o site do scm e a necessidade de usar um certificado curinga. O processo de atualização de certificado padrão do ASE do ILB também era problemático e causava a reinicialização do aplicativo. Para resolver esses problemas, o comportamento do ASE do ILB foi alterado de maneira a passar a usar um sufixo de domínio baseado no nome do ASE e com um sufixo de propriedade da Microsoft. A alteração no comportamento do ASE do ILB afeta apenas o ASEs do ILB criados após maio de 2019. ASEs do ILB preexistente ainda precisam gerenciar o certificado padrão do ASE e sua configuração de DNS.

## <a name="publish-with-an-ilb-ase"></a>Publicação com um ASE ILB

Para cada aplicativo criado, há dois pontos de extremidade. Em um ILB ASE, você tem *&lt;nome do aplicativo&gt;.&lt;Domínio ILB ASE&gt;* e *&lt;nome do aplicativo&gt;.scm.&lt;Domínio ILB ASE&gt;* . 

O nome do site SCM leva você para o console do Kudu, que é chamado de **Portal avançado** no portal do Azure. O console do Kudu lhe permite visualizar as variáveis de ambiente, explorar o disco, usar um console e muito mais. Para obter mais informações, confira [Console do Kudu para o Serviço de Aplicativo do Azure][Kudu]. 

Sistemas CI baseados na internet, como o GitHub e o Azure DevOps ainda funcionam com uma ASE ILB se o agente de build for acessível pela internet e na mesma rede como ASE ILB. Dessa forma, no caso do Azure DevOps, se o agente de build for criado na mesma VNET como ILB ASE (sub-rede diferente é adequada), poderá extrair o código do git do Azure DevOps e implantar ao ASE ILB. Se você não quiser criar seu próprio agente de build, você precisa usar um sistema de CI que usa um modelo de pull, como o Dropbox.

Os pontos de extremidade de publicação para aplicativos em um ASE ILB usam o domínio com o qual o ASE ILB foi criado. Este domínio é exibido no perfil de publicação do aplicativo e na folha do portal do aplicativo (**Visão geral** > **Essentials** e também **Propriedades**). Se você tiver um ILB ASE com o sufixo de domínio *&lt;nome do ASE&gt;.appserviceenvironment.net* e um aplicativo chamado *mytest*, use *mytest.&lt;nome do ASE&gt;.appserviceenvironment.net* para FTP e *mytest.scm.contoso.net* para implantação da Web.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Configurar um ILB ASE com um dispositivo WAF ##

Você pode combinar um dispositivo WAF (firewall do aplicativo Web) com seu ILB ASE para expor para a internet somente os aplicativos que você quiser e manter o restante acessível apenas na VNet. Isso permite que você crie aplicativos seguros de várias camadas, entre outras coisas.

Para saber mais sobre como configurar o ILB ASE com um dispositivo WAF, confira [Configuração de um firewall do aplicativo Web com o Ambiente do Serviço de Aplicativo][ASEWAF]. Este artigo mostra como usar uma solução de virtualização Barracuda com o seu ASE. Outra opção é usar o gateway de aplicativo do Azure. O gateway de aplicativo usa as regras básicas do OWASP para proteger os aplicativos colocados atrás dele. Para obter mais informações sobre o Gateway de Aplicativo do Azure, confira [Introdução ao firewall de aplicativo Web do Azure][AppGW].

## <a name="ilb-ases-made-before-may-2019"></a>ILB ASEs criados antes de maio de 2019

ILB ASEs criados antes de maio de 2019 exigiam a definição do sufixo de domínio durante a criação do ASE. Também exigiam o upload de um certificado padrão baseado nesse sufixo de domínio. Além disso, com um ILB ASE mais antigos, você não pode executar logon único no console do Kudu com aplicativos no ILB ASE. Ao configurar o DNS para um ILB ASE mais antigo, você precisa definir o registro A de caractere curinga em uma zona que corresponda ao seu sufixo de domínio. 

## <a name="get-started"></a>Introdução ##

* Para começar a usar ASEs, confira [Introdução aos ambientes do Serviço de Aplicativo][Intro]. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
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
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../overview.md#app-service-on-linux
