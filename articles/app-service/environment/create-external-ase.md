---
title: Criar um ASE externo
description: Saiba como criar um ambiente do serviço de aplicativo com um aplicativo nele ou criar um ASE (vazio) autônomo.
author: ccompy
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c953c31792b8d01199d409cbd91124138a6ebb15
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92927440"
---
# <a name="create-an-external-app-service-environment"></a>Como criar um ambiente externo do Serviço de Aplicativo

O Ambiente do Serviço de Aplicativo do Azure é uma implantação do Serviço de Aplicativo do Azure em uma sub-rede de uma VNet (rede virtual) do Azure.

> [!NOTE]
> Cada Ambiente do Serviço de Aplicativo tem um VIP (IP virtual), que pode ser usado para contatar o Ambiente do Serviço de Aplicativo.

Há duas maneiras de implantar um ASE (ambiente do serviço de aplicativo):

- Com um VIP em um endereço IP externo, geralmente chamado de ASE externo.
- Com o VIP em um endereço IP interno, geralmente chamado de ASE ILB devido ao ponto de extremidade interno ser um Balanceador de Carga Interno (ILB).

Este artigo mostra como criar um ASE Externo. Para obter uma visão geral do ASE, confira [Uma introdução ao ambiente do serviço de aplicativo][Intro]. Para obter informações sobre como criar uma ASE ILB, confira [Criação e uso de uma ASE ILB][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Antes de criar seu ASE

Depois de criar o seu ASE, você não pode alterar os seguintes itens:

- Localização
- Assinatura
- Resource group
- VNET usada
- Sub-rede usada
- Tamanho da sub-rede

> [!NOTE]
> Ao escolher uma rede virtual e especificar uma sub-rede, certifique-se de que ela seja grande o suficiente para acomodar as necessidades futuras de crescimento e dimensionamento. É recomendável um tamanho de `/24` com 256 endereços.
>

## <a name="three-ways-to-create-an-ase"></a>Três maneiras de criar um ASE

Há três maneiras de criar uma ASE:

- **Durante a criação de um plano do serviço de aplicativo**. Esse método cria o ASE e o plano do serviço de aplicativo em uma única etapa.
- **Como uma ação autônoma**. Este método cria um ASE autônomo, que é um ASE em branco. Esse método é um processo de criação de ASE mais avançado. Você pode usá-lo para criar um ASE com um ILB.
- **De um modelo do Azure Resource Manager**. Esse método é para usuários avançados. Para obter mais informações, confira [Como criar um ASE a partir de um modelo][MakeASEfromTemplate].

Um ASE externo tem um VIP público, o que significa que todo o tráfego HTTP/HTTPS para os aplicativos do ASE atinge um endereço IP acessível pela Internet. Um ASE com um ILB tem um endereço IP de sub-rede usado pelo ASE. Os aplicativos hospedados em um ASE ILB não são expostos diretamente à Internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Criar um ASE e um plano do Serviço de Aplicativo simultaneamente

O plano do Serviço de Aplicativo é um contêiner de aplicativos. Ao criar um aplicativo no serviço de aplicativo, você escolhe ou cria um plano do serviço de aplicativo. Os Ambientes do Serviço de Aplicativo contêm os planos do Serviço de Aplicativo; que por sua vez contêm os aplicativos.

Para criar um ASE durante a criação de um plano do serviço de aplicativo:

1. Na [portal do Azure](https://portal.azure.com/), selecione **criar um recurso**  >  **Web + celular**  >  **aplicativo Web**.

    ![Captura de tela do portal do Azure mostrando Web + Celular selecionados no Azure Marketplace e a Screen para a criação de um novo aplicativo Web aberto à direita.][1]

2. Selecione sua assinatura. O aplicativo e o ASE são criados nas mesmas assinaturas.

3. Selecione ou crie um grupo de recursos. Você pode usar os grupos de recursos para gerenciar recursos relacionados do Azure como uma unidade. Os grupos de recursos são úteis quando deseja estabelecer regras de controle de acesso baseado em função nos seus aplicativos. Para saber mais, confira [Visão geral do Azure Resource Manager][ARMOverview].

4. Selecione seu sistema operacional (Windows, Linux ou Docker). 

5. Clique no plano do serviço de aplicativo e, em seguida, selecione **Criar Novo**. Aplicativos web do Linux e aplicativos web do Windows não podem estar no mesmo Plano de Serviço de Aplicativo, mas podem estar no mesmo Ambiente de Serviço de Aplicativo. 

    ![Captura de tela da portal do Azure mostrando o painel do aplicativo Web, o painel plano do serviço de aplicativo e o novo painel plano do serviço de aplicativo aberto.][2]

6. Na lista suspensa **local**, selecione a região na qual deseja criar o ASE. Se você selecionar um ASE existente, não será criado um novo ASE. O plano do serviço de aplicativo é criado no ASE que você selecionou. 

7. Selecione **Tipo de preços** e escolha um dos SKUs de preços **isolados**. Se você escolher um cartão SKU **isolado** e um local que não seja um ASE; será criado um ASE novo no local. Para iniciar o processo de criação de um ASE, selecione **Selecionar**. O SKU **Isolado** só está disponível em conjunto com um ASE. Também não é possível usar nenhum outro SKU de preços em um ASE além do **isolado**. 

    ![Seleção de tipo de preços][3]

8. Insira o nome do ASE. Esse nome é usado no nome endereçável para os seus aplicativos. Se o nome do ASE for _appsvcenvdemo_, o nome do domínio será *.appsvcenvdemo.p.azurewebsites.net*. Se você criar um aplicativo chamado *mytestapp*, ele será endereçável em mytestapp.appsvcenvdemo.p.azurewebsites.net. Você não pode usar um espaço em branco no nome. Se você usar caracteres maiúsculos, o nome do domínio será a versão minúscula do nome escolhido.

    ![Nome do plano do serviço de aplicativo novo][4]

9. Especifica os detalhes da sua rede virtual do Azure. Escolha **Criar novo** ou **Selecionar existente**. A opção de selecionar uma VNet existente está disponível somente se você tiver uma rede virtual na região selecionada. Se você selecionar **Criar novo**, insira um nome para a VNet. Então, é criado um novo VNet do Resource Manager com o nome inserido. Ele usa o espaço de endereço `192.168.250.0/23` na região selecionada. Se você escolher **Selecionar Existente**, precisará:

    a. Selecione o bloco de endereço da VNet, se tiver mais de um.

    b. Digite um novo nome de sub-rede.

    c. Selecionar o tamanho da sub-rede. *Não se esqueça de selecionar um tamanho grande o suficiente para acomodar o crescimento futuro do seu ASE.* Recomendamos `/24` , que tem 256 endereços e pode lidar com um ase de tamanho máximo. Não recomendamos `/28`, por exemplo, porque tem somente 16 endereços disponíveis. A infraestrutura usa pelo menos sete endereços e a Rede do Azure usa outros 5. Em uma sub-rede `/28`, você fica com um dimensionamento máximo de 4 instâncias do Plano do Serviço de Aplicativo para uma ASE externa e apenas 3 instâncias do Plano do Serviço de Aplicativo para uma ASE ILB.

    d. Selecione o intervalo de IP da sub-rede.

10. Selecione **Criar** para criar o ASE. Esse processo também cria o plano de serviço de aplicativo e o aplicativo. O ASE, o plano de serviço de aplicativo e o aplicativo estarão na mesma assinatura e no mesmo grupo de recursos. Se o seu ASE precisa de um grupo de recursos separado ou se você precisa de um ASE ILB, siga as etapas seguintes para criar um ASE por si só.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Criar uma ASE e um aplicativo Web do Linux usando também uma imagem personalizada do Docker

1. No [Portal do Azure](https://portal.azure.com/), **Criar um Recurso** > **Web + Celular** > **Aplicativo Web para Contêineres.** 

    ![Captura de tela da portal do Azure mostrando Web + Celular selecionado no Azure Marketplace e no painel de Aplicativo Web para Contêineres aberto à direita.][7]

1. Selecione sua assinatura. O aplicativo e o ASE são criados nas mesmas assinaturas.

1. Selecione ou crie um grupo de recursos. Você pode usar os grupos de recursos para gerenciar recursos relacionados do Azure como uma unidade. Os grupos de recursos são úteis quando deseja estabelecer regras de controle de acesso baseado em função nos seus aplicativos. Para saber mais, confira [Visão geral do Azure Resource Manager][ARMOverview].

1. Clique no plano do serviço de aplicativo e, em seguida, selecione **Criar Novo**. Aplicativos web do Linux e aplicativos web do Windows não podem estar no mesmo Plano de Serviço de Aplicativo, mas podem estar no mesmo Ambiente de Serviço de Aplicativo. 

    ![Captura de tela da portal do Azure mostrando o painel Aplicativo Web para Contêineres, o painel plano do serviço de aplicativo e o novo painel plano do serviço de aplicativo aberto.][8]

1. Na lista suspensa **local**, selecione a região na qual deseja criar o ASE. Se você selecionar um ASE existente, não será criado um novo ASE. O plano do serviço de aplicativo é criado no ASE que você selecionou. 

1. Selecione **Tipo de preços** e escolha um dos SKUs de preços **isolados**. Se você escolher um cartão SKU **isolado** e um local que não seja um ASE; será criado um ASE novo no local. Para iniciar o processo de criação de um ASE, selecione **Selecionar**. O SKU **Isolado** só está disponível em conjunto com um ASE. Também não é possível usar nenhum outro SKU de preços em um ASE além do **isolado**. 

    ![Seleção de tipo de preços][3]

1. Insira o nome do ASE. Esse nome é usado no nome endereçável para os seus aplicativos. Se o nome do ASE for _appsvcenvdemo_, o nome do domínio será *.appsvcenvdemo.p.azurewebsites.net*. Se você criar um aplicativo chamado *mytestapp*, ele será endereçável em mytestapp.appsvcenvdemo.p.azurewebsites.net. Você não pode usar um espaço em branco no nome. Se você usar caracteres maiúsculos, o nome do domínio será a versão minúscula do nome escolhido.

    ![Nome do plano do serviço de aplicativo novo][4]

1. Especifica os detalhes da sua rede virtual do Azure. Escolha **Criar novo** ou **Selecionar existente**. A opção de selecionar uma VNet existente está disponível somente se você tiver uma rede virtual na região selecionada. Se você selecionar **Criar novo**, insira um nome para a VNet. Então, é criado um novo VNet do Resource Manager com o nome inserido. Ele usa o espaço de endereço `192.168.250.0/23` na região selecionada. Se você escolher **Selecionar Existente**, precisará:

    a. Selecione o bloco de endereço da VNet, se tiver mais de um.

    b. Digite um novo nome de sub-rede.

    c. Selecionar o tamanho da sub-rede. *Não se esqueça de selecionar um tamanho grande o suficiente para acomodar o crescimento futuro do seu ASE.* Recomendamos `/24`, que tem 128 endereços e pode manipular um ASE de tamanho máximo. Não recomendamos `/28`, por exemplo, porque tem somente 16 endereços disponíveis. A infraestrutura usa pelo menos sete endereços e a Rede do Azure usa outros 5. Em uma sub-rede `/28`, você fica com um dimensionamento máximo de 4 instâncias do Plano do Serviço de Aplicativo para uma ASE externa e apenas 3 instâncias do Plano do Serviço de Aplicativo para uma ASE ILB.

    d. Selecione o intervalo de IP da sub-rede.

1.  Selecione “Configurar o contêiner”.
    * Insira o nome de imagem personalizada (você pode usar o Registro de Contêiner do Azure, Hub do Docker e seu próprio registro privado). Se você não quiser usar seu próprio contêiner personalizado, basta pegar seu código e usar uma imagem interna com o Serviço de Aplicativo no Linux, usando as instruções acima. 

    ![Configurar o contêiner][9]

1. Selecione **Criar** para criar o ASE. Esse processo também cria o plano de serviço de aplicativo e o aplicativo. O ASE, o plano de serviço de aplicativo e o aplicativo estarão na mesma assinatura e no mesmo grupo de recursos. Se o seu ASE precisa de um grupo de recursos separado ou se você precisa de um ASE ILB, siga as etapas seguintes para criar um ASE por si só.


## <a name="create-an-ase-by-itself"></a>Criar um ASE sozinho

Se você criar um ASE autônomo, ele estará vazio. Um ASE vazio ainda incorrerá em um encargo mensal pela infraestrutura. Siga estas etapas para criar um ASE com um ILB ou para criar um ASE em seu próprio grupo de recursos. Depois de criar o seu ASE, você pode criar aplicativos nele usando o processo normal. Selecione o ASE novo como o local.

1. Pesquise o Azure Marketplace para **Ambiente do Serviço de Aplicativo**, ou selecione **Criar um recurso** > **Web Celular** > **Ambiente do Serviço de Aplicativo**. 

1. Insira o nome do seu ASE. O nome inserido é usado nos aplicativos criados no ASE. Se o nome for *mynewdemoase*, o nome do subdomínio será *.mynewdemoase.p.azurewebsites.net*. Se você criar um aplicativo chamado *mytestapp*, ele será endereçável em mytestapp.mynewdemoase.p.azurewebsites.net. Você não pode usar um espaço em branco no nome. Se você usar caracteres maiúsculos, o nome do domínio será a versão totalmente minúscula do nome escolhido. Se você usar um ILB, o nome do ASE não será usado no subdomínio, mas sim declarado explicitamente durante a criação do ASE.

    ![Nomenclatura ASE][5]

1. Selecione sua assinatura. Todos os aplicativos no ASE também usarão essa mesma assinatura. Você não pode colocar o seu ASE em uma VNet que está em outra assinatura.

1. Selecione ou especifique um novo grupo de recursos. Use o mesmo grupo de recursos no seu ASE que você usou na sua VNet. Se você selecionar uma VNet existente, a seleção do grupo de recursos para o ASE será atualizada para refletir a sua VNet. *Para criar um ASE com um grupo de recursos diferente do grupo de recursos da VNet, use um modelo do Resource Manager.* Para criar um ASE a partir de um modelo, consulte [criar um ambiente do serviço de aplicativo por meio de um modelo][MakeASEfromTemplate].

    ![Seleção de grupo de recursos][6]

1. Selecione a VNet e o local. Você pode criar uma nova VNet ou selecionar uma VNet existente: 

    * Se selecionar uma VNet nova, você poderá especificar um nome e local. 
    
    * A VNet nova tem o intervalo de endereços 192.168.250.0/23 e uma sub-rede denominada padrão. A sub-rede é definida como 192.168.250.0/24. Você só pode selecionar uma VNet do Resource Manager. A seleção do **Tipo de VIP** determina se o ASE pode ser acessado diretamente pela Internet (Externo) ou se ele usa um ILB. Para saber mais estas opções, confira [Como criar e usar um balanceador de carga interno com um ambiente do serviço de aplicativo][MakeILBASE]. 

      * Se selecionar um **tipo de VIP****externo**, você poderá selecionar com quantos endereços IP externos o sistema será criado para fins do SSL baseado em IP. 
    
      * Ao selecionar **Interno** para o **Tipo de VIP**, você deve especificar o domínio usado pelo seu ASE. Você pode implantar um ASE em uma VNet que usa os intervalos de endereço público ou privado. Para usar uma VNet com um intervalo de endereços públicos, você precisa criar a VNet antecipadamente. 
    
    * Ao selecionar uma VNet existente, uma nova sub-rede é criada quando o ASE é criado. *Você não pode usar uma sub-rede criada previamente no Portal. Você pode criar um ASE com uma sub-rede existente se usar um modelo do Resource Manager.* Para criar um ASE com base em um modelo, confira [Como criar um ambiente do serviço de aplicativo com base em um modelo][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicativo v1

Você ainda pode criar instâncias da primeira versão do ambiente do serviço de aplicativo (ASEv1). Para iniciar esse processo, pesquise o Marketplace para **ambiente do serviço de aplicativo v1**. Para criar o ASE basta usar o mesmo método de criação do ASE autônomo. Quando ele for concluído, o ASEv1 tem dois front-ends e dois trabalhos. Com o ASEv1, você precisa gerenciar os front-ends e os trabalhos. Eles não são adicionados automaticamente durante a criação dos planos do serviço de aplicativo. Os front-ends atuam como os pontos de extremidade HTTP/HTTPS e enviam o tráfego para os trabalhos. Os trabalhos são as funções que hospedam seus aplicativos. Você pode ajustar a quantidade de front-ends e trabalhos depois de criar o seu ASE. 

Para saber mais sobre o ASEv1, consulte [Introdução ao Ambiente do Serviço de Aplicativo v1][ASEv1Intro]. Para obter mais informações sobre o dimensionamento, gerenciamento e monitoramento do ASEv1, confira [Como configurar um ambiente do serviço de aplicativo][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



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
