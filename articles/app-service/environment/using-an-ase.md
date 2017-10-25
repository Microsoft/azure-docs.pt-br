---
title: "Usar um ambiente do Serviço de Aplicativo do Azure"
description: "Como criar, publicar e dimensionar aplicativos em um ambiente do Serviço de Aplicativo do Azure"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 64746f7b1a09e35b35e794f5a11d69bef39a03a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-an-app-service-environment"></a>Usar um ambiente do Serviço de Aplicativo #

## <a name="overview"></a>Visão geral ##

O Ambiente do Serviço de Aplicativo do Azure é uma implantação do Serviço de Aplicativo do Azure em uma sub-rede em uma rede virtual do Azure do cliente. Ele consiste em:

- **Front-ends**: os front-ends são onde terminam o HTTP/HTTPS em um ambiente de serviço de aplicativo (ASE).
- **Funções de Trabalho**: são os recursos que hospedam os aplicativos.
- **Banco de Dados**: contém informações que definem o ambiente.
- **Armazenamento**: o armazenamento é usado para hospedar os aplicativos publicados pelo cliente.

> [!NOTE]
> Há duas versões do Ambiente do Serviço de Aplicativo: ASEv1 e ASEv2. No ASEv1, é necessário gerenciar os recursos antes de usá-los. Para saber como configurar e gerenciar o ASEv1, veja [Configurar um ambiente do Serviço de Aplicativo v1][ConfigureASEv1]. O restante deste artigo concentra-se em ASEv2.
>
>

Implante um ASE (ASEv1 e ASEv2) com um VIP externo ou interno para o acesso ao aplicativo. A implantação com um VIP externo é geralmente chamada de ASE externo. A versão interna é chamada de ILB ASE, porque ele usa um balanceador de carga interno (ILB). Para saber mais sobre o ASE ILB, veja [Criar e usar um ASE ILB][MakeILBASE].

## <a name="create-a-web-app-in-an-ase"></a>Criar um aplicativo Web em um ASE ##

Para criar um aplicativo Web em um ASE, use o mesmo processo que quando você o cria normalmente, mas com algumas pequenas diferenças. Quando você cria um novo plano do Serviço de Aplicativos:

- Em vez de escolher uma localização geográfica na qual implantar o aplicativo, escolha um ASE como a localização.
- Todos os planos do Serviço de Aplicativo criados em um ASE devem estar em um tipo de preço Isolado.

Se você não tiver um ASE, crie um seguindo as instruções em [Criar um ambiente do Serviço de Aplicativo][MakeExternalASE].

Para criar um aplicativo Web em um ASE:

1. Selecione **Novo** > **Web + Móvel** > **Aplicativo Web**.

2. Insira um nome para o aplicativo Web. Se você já selecionou um plano do Serviço de Aplicativo em um ASE, o nome de domínio do aplicativo reflete o nome de domínio do ASE.

    ![Seleção de nome de aplicativo Web][1]

3. Selecione uma assinatura.

4. Insira um nome para um novo grupo de recursos ou selecione **Usar existente** e escolha um na lista suspensa.

5. Selecione um plano do Serviço de Aplicativo existente no ASE ou crie um novo com as seguintes etapas:

    a. Selecione **Criar Novo**.

    b. Insira o nome do plano do Serviço de Aplicativo.

    c. Selecione o ASE na lista suspensa **Localização**.

    d. Selecione o tipo de preço **Isolado**. Selecione **Selecionar**.

    e. Selecione **OK**.
    
    ![Tipos de preço Isolados][2]

6. Selecione **Criar**.

## <a name="how-scale-works"></a>Como funciona a escala ##

Cada aplicativo do Serviço de Aplicativo é executado em um plano do Serviço de Aplicativo. O modelo de contêiner é os ambientes mantêm os planos do Serviço de Aplicativo e os planos do Serviço de Aplicativo mantêm os aplicativos. Ao dimensionar um aplicativo, você dimensiona o plano do Serviço de Aplicativo e, portanto, dimensiona todos os aplicativos do mesmo plano.

No ASEv2, quando você dimensiona um plano do Serviço de Aplicativo, a infraestrutura necessária é adicionada automaticamente. Há um atraso de tempo nas operações de escala, conforme a infraestrutura é adicionada. No ASEv1, a infraestrutura necessária precisa ser adicionada antes de criar ou escalar horizontalmente o plano do Serviço de Aplicativo. 

No Serviço de Aplicativo multilocatário, geralmente, o dimensionamento é imediato, porque um pool de recursos está imediatamente disponível para dar suporte. Em um ASE, não há nenhum buffer desse tipo e os recursos são alocados conforme a necessidade.

Em um ASE, é possível escalar verticalmente até 100 instâncias. Essas 100 instâncias podem estar todas em um único plano do Serviço de Aplicativo ou ser distribuídas entre vários planos do Serviço de Aplicativo.

## <a name="ip-addresses"></a>Endereços IP ##

O Serviço de Aplicativo tem a capacidade de alocar um endereço IP dedicado a um aplicativo. Essa funcionalidade está disponível depois que você configura um SSL baseado em IP, conforme descrito em [Associar um certificado SSL personalizado existente aos aplicativos Web do Azure][ConfigureSSL]. No entanto, em um ASE, há uma exceção notável. Não é possível adicionar outros endereços IP a serem usados para um SSL baseado em IP em um ILB ASE.

No ASEv1, você precisa alocar os endereços IP como recursos antes de poder usá-los. No ASEv2, use-o em seu aplicativo assim como faria no Serviço de Aplicativo multilocatário. Sempre há um endereço extra no ASEv2, com até 30 endereços IP. Sempre que você usar um, outro será adicionado, de forma que sempre haja um endereço imediatamente disponível para uso. Um atraso de tempo é necessário para alocar outro endereço IP, o que impede a adição de endereços IP em sucessão rápida.

## <a name="front-end-scaling"></a>Dimensionamento de front-end ##

No ASEv2, ao escalar horizontalmente os planos do Serviço de Aplicativo, as funções de trabalho são adicionadas automaticamente para dar suporte a eles. Cada ASE é criado com dois front-ends. Além disso, o front-ends automaticamente dimensionam a uma taxa de um front-end para cada 15 instâncias nos planos do Serviço de Aplicativo. Por exemplo, se você tiver 15 instâncias, terá três front-ends. Se você dimensionar para 30 instâncias, terá quatro front-ends e assim por diante.

Esse número de front-ends deve ser mais do que o suficiente para a maioria dos cenários. No entanto, você pode dimensionar a uma taxa mais rápida. Você pode diminuir a taxa para um front-end para cada cinco instâncias. Há um custo para alterar a taxa. Para saber mais, veja [Preços do Serviço de Aplicativo do Azure][Pricing].

Os recursos do front-end são o ponto de extremidade HTTP/HTTPS do ASE. Com a configuração de front-end padrão, o uso de memória por front-end é consistentemente cerca de 60%. Cargas de trabalho de cliente não são executadas em um front-end. O fator chave para um front-end com relação à escala é a CPU, que é controlada principalmente pelo tráfego HTTPS.

## <a name="app-access"></a>Acesso ao aplicativo ##

Em um ASE externo, o domínio que é usado quando você cria aplicativos é diferente do Serviço de Aplicativo multilocatário. Isso inclui o nome do ASE. Para saber mais sobre como criar um ASE Externo, veja [Criar um ambiente do Serviço de Aplicativo][MakeExternalASE]. O nome de domínio em um ASE Externo é semelhante a *.&lt;asename&gt;.p.azurewebsites.net*. Por exemplo, se o ASE for chamado _external-ase_ e você hospedar um aplicativo chamado _contoso_ nesse ASE, você o acessaria nas seguintes URLs:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

A URL contoso.scm.external-ase.p.azurewebsites.net é usada para acessar o console do Kudu ou para publicar o aplicativo usando a implantação da Web. Para obter informações sobre o console do Kudu, consulte [Console do Kudu para o Serviço de Aplicativo do Azure][Kudu]. O console do Kudu fornece uma interface do usuário da Web para depuração, upload de arquivos, edição de arquivos e muito mais.

Em um ASE ILB, você determina o domínio no momento da implantação. Para saber mais sobre como criar um ASE ILB, veja [Criar e usar um ASE ILB][MakeILBASE]. Se você especificar o nome de domínio _ilb-ase.info_, os aplicativos nesse ASE usarão esse domínio durante a criação do aplicativo. Para o aplicativo chamado _contoso_, as URLs são:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publicação ##

Assim como ocorre com o Serviço de Aplicativo multilocatário, em um ASE, é possível publicar com:

- Implantação da Web.
- FTP.
- Integração contínua.
- Operação de arrastar e soltar no console do Kudu.
- Um IDE como o Visual Studio, Eclipse ou IntelliJ IDEA.

Com um ASE externo, todas essas opções de publicação comportam-se da mesma maneira. Para saber mais, veja [Implantação no Serviço de Aplicativo do Azure][AppDeploy]. 

A principal diferença na publicação é em relação a um ASE ILB. Com um ASE ILB, os pontos de extremidade de publicação estão disponíveis apenas por meio do ILB. O ILB está em um IP privado na sub-rede do ASE, na rede virtual. Se você não tiver acesso à rede ao ILB, não poderá publicar nenhum aplicativo nesse ASE. Conforme observado em [Criar e usar um ASE ILB][MakeILBASE], você precisa configurar o DNS para os aplicativos no sistema. Isso inclui o ponto de extremidade SCM. Se eles não estiverem definidos corretamente, você não poderá publicar. Os IDEs também precisam ter acesso à rede ao ILB para publicar diretamente nele.

Sistemas de CI baseados na Internet, como GitHub e Visual Studio Team Services, não funcionam com um ASE ILB, porque o ponto de extremidade de publicação não é acessível pela Internet. Em vez disso, você precisa usar um sistema de CI que usa um modelo pull, como o Dropbox.

Os pontos de extremidade de publicação para aplicativos em um ASE ILB usam o domínio com o qual o ASE ILB foi criado. Você pode ver isso no perfil de publicação do aplicativo e na folha do portal do aplicativo (em **Visão Geral** > **Informações Básicas** e também em **Propriedades**). 

## <a name="pricing"></a>Preços ##

O SKU de preço chamado **Isolado** foi criado para uso apenas com ASEv2. Todos os planos do Serviço de Aplicativo que são hospedados no ASEv2 estão no SKU de preços Isolado. As taxas de plano do Serviço de Aplicativo Isolado podem variar por região. 

Além do preço dos planos do Serviço de Aplicativo, há um valor fixo para o ASE em si. O valor fixo não muda com o tamanho do seu ASE e paga pela infraestrutura de ASE a uma taxa de dimensionamento padrão de 1 front-end adicional para cada 15 instâncias do plano do Serviço de Aplicativo.  

Se a taxa de escala padrão de 1 front-end para cada 15 instâncias do plano do Serviço de Aplicativo não for rápida o suficiente, você poderá ajustar a taxa na qual front-ends são adicionados ou o tamanho dos front-ends.  Quando você ajustar a taxa ou o tamanho, pagará pelos núcleos de front-end que não seriam adicionados por padrão.  

Por exemplo, se você ajustar a taxa de escala para 10, um front-end será adicionado para cada 10 instâncias nos planos do Serviço de Aplicativo. O valor fixo abrange uma taxa de escala de um front-end para cada 15 instâncias. Com uma taxa escala de 10, você pode pagar uma taxa para o terceiro front-end adicionado para as 10 instâncias de plano de Serviço de Aplicativo. Você não precisará pagar por ele quando você chegar a 15 instâncias porque ele terá sido adicionado automaticamente.

Se você ajustar o tamanho dos front-ends para dois núcleos, mas não ajustar a taxa, você pagará pelos núcleos extras.  Um ASE é criado com dois front-ends, então, até mesmo abaixo do limite de dimensionamento automático você pagaria por dois núcleos adicionais se aumentasse o tamanho para front-ends com dois núcleos.

Para saber mais, veja [Preços do Serviço de Aplicativo do Azure][Pricing].

## <a name="delete-an-ase"></a>Excluir um ASE ##

Para excluir um ASE: 

1. Use **Excluir** na parte superior da folha **Ambiente de Serviço de Aplicativo**. 

2. Insira o nome do seu ASE para confirmar que deseja excluí-lo. Quando você exclui um ASE, exclui todo o conteúdo dentro dele também. 

    ![Exclusão de ASE][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


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
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../app-service-deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
