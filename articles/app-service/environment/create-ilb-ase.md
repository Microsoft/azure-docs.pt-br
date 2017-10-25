---
title: "Como criar e usar um balanceador de carga interno com um ambiente do Serviço de Aplicativo do Azure"
description: "Detalhes sobre como criar e usar um ambiente do Serviço de Aplicativo do Azure isolado da Internet"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: cc7bdd7860506c20187dc913b72111824d1737ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>Como criar e usar um balanceador de carga interno com um ambiente do Serviço de Aplicativo #

 O Ambiente do Serviço de Aplicativo do Azure é uma implantação do Serviço de Aplicativo do Azure em uma sub-rede de uma VNet (rede virtual) do Azure. Há duas maneiras de implantar um ASE (Ambiente do Serviço de Aplicativo): 

- Com um VIP em um endereço IP externo, geralmente chamado de ASE externo.
- Com um VIP em um endereço IP interno, geralmente chamado de ASE ILB devido ao ponto de extremidade interno ser um balanceador de carga interno (ILB). 

Este artigo mostra como criar um ASE ILB. Para obter uma visão geral do ASE, confira [Introdução aos ambientes do Serviço de Aplicativo][Intro]. Para saber como criar um ASE externo, confira [Como criar um ASE externo][MakeExternalASE].

## <a name="overview"></a>Visão geral ##

Um ASE pode ser implantado com um ponto de extremidade acessível pela Internet ou com um endereço IP em sua VNet. Para definir o endereço IP como um endereço de VNet, o ASE deve ser implantado com um ILB. Para implantar o ASE com um ILB, você deve fornecer:

-   Seu próprio domínio, que você usa quando cria seus aplicativos.
-   O certificado usado para HTTPS.
-   Gerenciamento de DNS para o seu domínio.

Em troca, você pode fazer coisas como:

-   Hospedar aplicativos da intranet na nuvem com segurança e acessá-los por meio de uma VPN do Azure ExpressRoute ou site a site.
-   Hospedar aplicativos na nuvem que não estão listados em servidores DNS públicos.
-   Criar aplicativos back-end isolados da Internet, que podem ser integrados com seus aplicativos front-end com segurança.

### <a name="disabled-functionality"></a>Funcionalidade desabilitada ###

Há algumas coisas que você não pode fazer ao usar um ASE ILB:

-   Usar SSL baseado em IP.
-   Atribuir endereços IP a aplicativos específicos.
-   Comprar e usar um certificado com um aplicativo por meio do portal do Azure. Você pode obter certificados diretamente de uma autoridade de certificação e usá-los com seus aplicativos. Não é possível obtê-los por meio do portal do Azure.

## <a name="create-an-ilb-ase"></a>Criar um ASE ILB ##

Para criar um ASE ILB:

1. No Portal do Azure, escolha **Novo** > **Web + Móvel** > **Ambiente do Serviço de Aplicativo** .

2. Selecione sua assinatura.

3. Selecione ou crie um grupo de recursos.

4. Selecione ou crie uma VNet.

5. Ao selecionar uma VNet existente, você precisa criar uma sub-rede para manter o ASE. Lembre-se de definir um tamanho de sub-rede grande o suficiente para acomodar qualquer crescimento futuro do ASE. O tamanho recomendado é `/25`, que tem 128 endereços e é compatível com um ASE com tamanho máximo. E o tamanho mínimo que você pode selecionar é `/28`. Dependendo da necessidade da infraestrutura, esse tamanho pode ser dimensionado para um máximo de 11 instâncias.

    * Vá além do padrão máximo de 100 instâncias nos planos do serviço de aplicativo.

    * Dimensione cerca de 100, mas com dimensionamento de front-end mais rápido.

6. Selecione **Rede Virtual/Local** > **Configuração de Rede Virtual**. Defina o **Tipo de VIP** como **Interno**.

7. Digite um nome de domínio. Esse domínio é usado para aplicativos criados neste ASE. Há algumas restrições. Ele não pode ser:

    * net   

    * azurewebsites.net

    * p.azurewebsites.net

    * &lt;asename&gt;.p.azurewebsites.net

   O nome de domínio personalizado usado para aplicativos e o nome de domínio usado pelo seu ASE não podem ser os mesmos. Em um ASE ILB com o nome de domínio _contoso.com_, não é possível usar nomes de domínio personalizados para seus aplicativos, como:

    * www.contoso.com

    * abcd.def.contoso.com

    * abcd.contoso.com

   Se você souber os nomes de domínio personalizados dos seus aplicativos, escolha um domínio para o ASE ILB que não seja conflitante com os nomes de domínio personalizados. Neste exemplo, você pode usar algo como *contoso-internal.com* para o domínio do seu ASE porque isso não entra em conflito com os nomes de domínio personalizados que terminam em *.contoso.com*.

8. Selecione **OK** e, então, selecione **Criar**.

    ![Criação de ASE][1]

Na folha **Rede Virtual**, há uma opção **Configuração da Rede Virtual**. Você pode usá-la para selecionar um VIP Externo ou um VIP Interno. O padrão é **Externo**. Se você selecionar **Externo**, o seu ASE usará um VIP acessível pela Internet. Se você selecionar **Interno**, o seu ASE será configurado com um ILB em um endereço IP na sua VNet.

Depois de selecionar **Interno**, não é possível adicionar mais endereços IP ao seu ASE. Em vez disso, você precisa fornecer o domínio do ASE. Em um ASE com um VIP Externo, o nome do ASE é usado no domínio dos aplicativos criados nesse ASE.

Se você definir **Tipo de VIP** como **Interno**, o nome do ASE não será usado no domínio do ASE. Especifique o domínio explicitamente. Se o domínio for *contoso.corp.net* e você criar um aplicativo nesse ASE chamado *timereporting*, a URL desse aplicativo será timereporting.contoso.corp.net.


## <a name="create-an-app-in-an-ilb-ase"></a>Como criar um aplicativo em uma ASE ILB ##

Você pode criar um aplicativo em uma ASE ILB da mesma maneira que você cria um aplicativo em um ASE.

1. No Portal do Azure, escolha **Novo** > **Web + Móvel** > **Web** ou **Móvel** ou **Aplicativo de API**.

2. Digite o nome do aplicativo.

3. Selecione a assinatura.

4. Selecione ou crie um grupo de recursos.

5. Selecione ou crie um plano do Serviço de Aplicativo. Se você quiser criar um novo plano do Serviço de Aplicativo, selecione o seu ASE como o local. Selecione o pool de trabalhos no qual deseja criar o seu plano do Serviço de Aplicativo. Ao criar o plano do Serviço de Aplicativo, selecione o ASE como a localização e o pool de trabalhos. Ao especificar o nome do aplicativo, o domínio sob o nome do aplicativo é substituído pelo domínio do ASE.

6. Selecione **Criar**. Se você deseja que o aplicativo seja exibido no painel, selecione a caixa de seleção **Fixar no painel**.

    ![Criação do plano do Serviço de Aplicativo][2]

    No **Nome do aplicativo**, o nome de domínio é atualizado para refletir o domínio do ASE.

## <a name="post-ilb-ase-creation-validation"></a>Validação posterior à criação do ASE ILB ##

Um ASE com ILB é ligeiramente diferente do ASE que não tem um ILB. Como já foi observado, você precisa gerenciar o seu próprio DNS. Você também precisa fornecer o seu próprio certificado para conexões HTTPS.

Depois de criar o seu ASE, o nome de domínio mostra o domínio especificado. Um novo item chamado **Certificado ILB** aparece no menu **Configurações**. O ASE é criado com um certificado que não especifica o domínio do ASE ILB. Se você usar o ASE com esse certificado, o navegador informará que ele é inválido. Esse certificado facilita o teste de HTTPS, mas você precisa carregar o seu próprio certificado vinculado ao domínio ASE ILB. Essa etapa é necessária independentemente se o seu certificado é auto-assinado ou adquirido de uma autoridade de certificação.

![Nome de domínio ASE ILB][3]

O ASE ILB precisa de um certificado SSL válido. Use autoridades de certificação internas, compre um certificado de um emissor externo ou use um certificado auto-assinado. Independentemente da origem do certificado SSL, os seguintes atributos de certificado devem ser configurados corretamente:

* **Entidade**: esse atributo deve ser definido como *.seu-domínio-raiz-aqui.
* **Nome Alternativo da Entidade**: esse atributo deve incluir **.seu-domínio-raiz-aqui* e **.scm.seu-domínio-raiz-aqui*. Conexões SSL para o site SCM/Kudu associados a cada aplicativo usam um endereço no formato *nome-do-seu-aplicativo.scm.seu-domínio-raiz-aqui*.

Converta ou salve o certificado SSL como um arquivo .pfx. O arquivo .pfx deve incluir todos os certificados intermediários e raiz. Proteja-o com uma senha.

Se você quiser criar um certificado auto-assinado, pode usar os comandos do PowerShell aqui. Lembre-se de usar o nome de domínio do ASE ILB em vez de *internal.contoso.com*: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "\*.internal-contoso.com","\*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

O certificado gerado por comandos do PowerShell é sinalizado por navegadores porque o certificado não foi criado por uma autoridade de certificação que está na cadeia de confiança do seu navegador. Para obter um certificado que o navegador confie, procure uma autoridade de certificação comercial da cadeia de confiança do seu navegador. 

![Defina o certificado ILB][4]

Para carregar seus próprios certificados e testar o acesso:

1. Depois que o ASE for criado, acesse a interface do usuário do ASE. Selecione **ASE** > **Configurações** > **Certificado ILB**.

2. Para definir o certificado ILB, selecione o arquivo .pfx do certificado e digite a senha. Esta etapa leva algum tempo para processar. Será exibida uma mensagem informando que uma operação de carregamento está em andamento.

3. Obtenha o endereço do ILB para seu ASE. Selecione **ASE** > **Propriedades** > **Endereço IP Virtual**.

4. Depois de criar o ASE, crie um aplicativo Web nele.

5. Se não tiver uma VM naquela VNet, crie uma.

    > [!NOTE] 
    > Não tente criar essa VM na mesma sub-rede que o ASE porque irá falhar ou causar problemas.
    >
    >

6. Defina o DNS para o domínio do ASE. Você pode usar um caractere curinga com o seu domínio no DNS. Para fazer alguns testes simples, edite o arquivo de hosts na sua VM para definir o nome do aplicativo Web para o endereço IP VIP:

    a. Se o ASE tiver o nome de domínio _.ilbase.com_ e você criar o aplicativo Web chamado _mytestapp_, ele será endereçado em _mytestapp.ilbase.com_. Em seguida, você definirá _mytestapp.ilbase.com_ para resolver o endereço do ILB. (No Windows, o arquivo dos hosts está em _C:\Windows\System32\drivers\etc\_.)

    b. Para testar a publicação de implantação da Web ou o acesso ao console avançado, crie um registro para _mytestapp.scm.ilbase.com_.

7. Use um navegador nessa VM e acesse http://mytestapp.ilbase.com. (Ou acesse o nome do aplicativo Web, independente do nome que você escolheu, com o seu domínio.)

8. Use um navegador nessa VM e acesse https://mytestapp.ilbase.com. Se você usar um certificado auto-assinado, aceite a falta de segurança.

    O endereço IP do ILB está listado em **Endereços IP**. Essa lista também contém os endereços IP usados pelo VIP externo e para o tráfego de gerenciamento de entrada.

    ![Endereço IP do ILB][5]

## <a name="web-jobs-functions-and-the-ilb-ase"></a>Trabalhos da Web, Funções e o ILB ASE ##

As Funções e os trabalhos da Web são suportados em um ILB ASE, mas para que o portal funcione com eles, você deve ter acesso de rede ao site SCM.  Isso significa que seu navegador deve estar em um host que esteja na rede virtual ou conectado a ela.  

Quando você usa o Azure Functions em um ASE ILB, você pode receber uma mensagem de erro informando: "Não é possível recuperar as funções no momento. Tente novamente mais tarde.” Esse erro ocorre porque a interface do usuário de Funções aproveita o site SCM por HTTPS e o certificado raiz não está na cadeia de navegadores de confiança. Os trabalhos da Web têm um problema semelhante. Para evitar esse problema, você pode executar uma destas ações:

- Adicionar o certificado ao seu repositório de certificados confiáveis. Isso desbloqueia o Edge e o Internet Explorer.
- Use o Chrome e acesse o site SCM primeiro, aceite o certificado não confiável e vá para o portal.
- Use um certificado comercial que esteja na sua cadeia de navegadores confiáveis.  Essa é a melhor opção.  

## <a name="dns-configuration"></a>Configuração de DNS ##

Ao usar um VIP Externo, o DNS é gerenciado pelo Azure. Qualquer aplicativo criado no ASE é automaticamente adicionado ao DNS do Azure, que é um DNS público. Em um ASE ILB, é necessário gerenciar seu próprio DNS. Para um domínio específico, como _contoso.net_, você precisa criar registros DNS A no seu DNS que apontam para o endereço do ILB de:

- *.contoso.net
- *.scm.contoso.net

Se o domínio do ASE ILB for usado para várias tarefas fora desse ASE, talvez seja necessário gerenciar o DNS baseado no nome de aplicativo. Esse método é um desafio, pois você precisa adicionar cada nome de aplicativo novo em seu DNS ao criá-lo. Por esse motivo, recomendamos que você use um domínio dedicado.

## <a name="publish-with-an-ilb-ase"></a>Publicação com um ASE ILB ##

Para cada aplicativo criado, há dois pontos de extremidade. Em um ASE ILB, você tem *&lt;nome do aplicativo>.&lt;Domínio do ASE ILB>* e *&lt;nome do aplicativo>.scm.&lt;Domínio do ASE ILB>*. 

O nome do site SCM leva você para o console do Kudu, que é chamado de **Portal avançado** no portal do Azure. O console do Kudu lhe permite visualizar as variáveis de ambiente, explorar o disco, usar um console e muito mais. Para obter mais informações, consulte [Console do Kudu para o Serviço de Aplicativo do Azure][Kudu]. 

No Serviço de Aplicativo multilocatário e em um ASE Externo, há logon único entre o portal do Azure e o console do Kudu. No entanto, para o ASE ILB, você precisa usar suas credenciais de publicação para entrar no console do Kudu.

Sistemas de CI baseados na Internet, como GitHub e o Visual Studio Team Services, não funcionam em ASE ILB porque o ponto de extremidade de publicação não está acessível pela Internet. Em vez disso, você precisa usar um sistema de CI que usa um modelo pull, como o Dropbox.

Os pontos de extremidade de publicação para aplicativos em um ASE ILB usam o domínio com o qual o ASE ILB foi criado. Este domínio é exibido no perfil de publicação do aplicativo e na folha do portal do aplicativo (**Visão geral** > **Essentials** e também **Propriedades**). Se você tiver um ASE ILB com o subdomínio *contoso.net* e um aplicativo chamado *mytest*, use *mytest.contoso.net* para FTP e *mytest.scm.contoso.net* para implantação da Web.

## <a name="couple-an-ilb-ase-with-a-waf-device"></a>Como acoplar um ASE ILB com um dispositivo WAF ##

O Serviço de Aplicativo do Azure fornece várias medidas de segurança que protegem o sistema. Eles também ajudam a determinar se um aplicativo foi atacado por um hacker. A melhor proteção para um aplicativo Web é acoplar uma plataforma de hospedagem, como o Serviço de Aplicativo do Azure, com um firewall de aplicativo Web (WAF). Como o ASE ILB tem um ponto de extremidade do aplicativo isolado da rede, ele é perfeito para esse uso.

Para saber mais sobre como configurar o ASE ILB com um dispositivo WAF, confira [Configuração de um firewall do aplicativo Web com o ambiente do serviço de aplicativo][ASEWAF]. Este artigo mostra como usar uma solução de virtualização Barracuda com o seu ASE. Outra opção é usar o gateway de aplicativo do Azure. O gateway de aplicativo usa as regras básicas do OWASP para proteger os aplicativos colocados atrás dele. Para obter mais informações sobre o gateway de aplicativo do Azure, confira [Introdução ao firewall de aplicativo Web do Azure][AppGW].

## <a name="get-started"></a>Introdução ##

* Para começar a usar ASEs, confira [Introdução aos ambientes do Serviço de Aplicativo][Intro].
 
<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

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
