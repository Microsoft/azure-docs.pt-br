---
title: Criar um aplicativo Web no ASE v1
description: Saiba como criar aplicativos Web em um Ambiente do Serviço de Aplicativo v1. Este documento é fornecido somente para clientes que usam o ASE v1 herdado.
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fa92002cfef070218902aa7919900dde9fe5a9b5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505402"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Criar um aplicativo Web em um Ambiente do Serviço de Aplicativo v1

> [!NOTE]
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v1.  Há uma versão mais recente do Ambiente do Serviço de Aplicativo que é mais fácil de usar e é executada em infraestrutura mais avançada. Para saber mais sobre a nova versão, comece com a [introdução ao ambiente do serviço de aplicativo](intro.md).
> 

## <a name="overview"></a>Visão geral
Este tutorial mostra como criar aplicativos Web e planos do Serviço de Aplicativo em um [Ambiente do Serviço de Aplicativo v1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Se você quiser saber como criar um aplicativo Web mas se não precisar fazê-lo em um Ambiente de Serviço de Aplicativo, consulte [Criar um aplicativo Web .NET](../quickstart-dotnetcore.md) ou um dos tutoriais relacionados para outras linguagens e estruturas.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial supõe que você tenha criado um Ambiente de Serviço de Aplicativo. Se você ainda não tiver feito isso, consulte [Criar um Ambiente de Serviço de Aplicativo](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Criar um aplicativo Web
1. No [Portal do Azure](https://portal.azure.com/), clique em **Criar um recurso > Web + Celular > Aplicativo Web**. 
   
    ![Captura de tela que mostra onde criar um aplicativo Web no portal do Azure.][1]
2. Selecione sua assinatura.  
   
    Se você tiver várias assinaturas, lembre-se de que, para criar um aplicativo no Ambiente de Serviço de Aplicativo, precisará usar a mesma assinatura que usou ao criar o ambiente. 
3. Selecione ou crie um grupo de recursos.
   
    Os *grupos de recursos* permitem gerenciar recursos do Azure relacionados como uma unidade e são úteis ao estabelecer regras do Azure *RBAC (controle de acesso baseado em função)* para seus aplicativos. Para obter mais informações, confira [Visão geral do Azure Resource Manager][ResourceGroups]. 
4. Selecione ou crie um plano do Serviço de Aplicativo.
   
    *planos do Serviço de Aplicativo* são conjuntos gerenciados de aplicativos Web.  Normalmente, quando você seleciona os preços, o preço cobrado é aplicado a um plano do serviço de aplicativo em vez de aplicado aos aplicativos individuais. Em um ASE você paga pelas instâncias de computação alocadas para o ASE em vez do que foi listado com o ASP.  Para aumentar o número de instâncias de um aplicativo Web, você aumenta as instâncias do seu plano do Serviço de Aplicativo e isso afeta todos os aplicativos Web nesse plano.  Alguns recursos, como slots de site ou integração de VNET, também têm restrições de quantidade dentro do plano.  Para obter mais informações, consulte [visão geral dos planos de serviço Azure app](../overview-hosting-plans.md)
   
    Você pode identificar os planos do Serviço de Aplicativo no ASE examinando o local indicado abaixo do nome do plano.  
   
    ![A captura de tela que mostra o foi para exibir os planos do serviço de aplicativo em seu ASE.][5]
   
    Se você quiser usar um plano de serviço do Serviço de Aplicativo que já exista no seu Ambiente de Serviço de Aplicativo, selecione-o. Se você quiser criar um novo plano do Serviço de Aplicativo, consulte a seção seguinte deste tutorial, [Criar um plano do Serviço de Aplicativo em um Ambiente de Serviço de Aplicativo](#createplan).
5. Insira o nome do aplicativo Web e clique em **Criar**. 
   
    Se seu ASE usar um VIP Externo, a URL de um aplicativo em um ASE será: [*sitename*].[*nome do seu Ambiente do Serviço de Aplicativo*].p.azurewebsites.net em vez de [*sitename*].azurewebsites.net
   
    Se o seu ASE usa um VIP interno, a URL de um aplicativo nesse ASE é: [*sitename*]. [*subdomínio especificado durante a criação do ASE*]   
    Depois de selecionar o ASP durante a criação do ASE você verá o subdomínio de atualização abaixo **Nome**

## <a name="create-an-app-service-plan"></a><a name="createplan"></a> Criar um plano do serviço de aplicativo
Quando você cria um plano do Serviço de Aplicativo em um Ambiente do Serviço de Aplicativo, as opções do seu trabalhador serão diferentes caso não haja trabalhadores compartilhados em um ASE.  Os trabalhadores que você precisa usar são aqueles que foram alocados para o ASE pelo administrador.  Isso significa que, para criar um novo plano, você precisa ter mais trabalhadores alocados para o pool de trabalho do ASE do que o número total de instâncias em todos os seus planos já existentes nesse pool de trabalho.  Se não tiver trabalhadores suficientes em seu pool de trabalho ASE para criar o plano, você precisará trabalhar com seu administrador do ASE para adicioná-los.

Outra diferença em relação aos planos do Serviço de Aplicativo hospedados por um Ambiente do Serviço de Aplicativo é a ausência de seleção de preços.  Quando você tem um ambiente do serviço de aplicativo, você está pagando por recursos de computação usados pelo sistema e não tem encargos adicionais para os planos nesse ambiente.  Normalmente, quando você cria um plano do Serviço de Aplicativo, você seleciona um plano de preços que determina sua cobrança.  Um ambiente do serviço de aplicativo é essencialmente um local privado onde você pode criar conteúdo.  Você paga pelo ambiente e não para hospedar seu conteúdo.

As instruções a seguir mostram como criar um plano do Serviço de Aplicativo enquanto você estiver criando um aplicativo Web, como explicado na seção anterior do tutorial.

1. Clique em **Criar Novo** na interface do usuário de seleção de planos e forneça um nome para seu plano como normalmente faria fora de um ASE.
2. Selecione o ASE que você deseja usar em sua seleção de local.
   
    Como um Ambiente do Serviço de Aplicativo é, essencialmente, um local de implantação particular, ele é mostrado no Local. 
   
    ![Captura de tela que mostra o seletor de local para selecionar o ASE desejado.][2]
   
    Após a seleção de um ASE no seletor de local, a interface do usuário de criação de Serviço de Aplicativo é atualizada.  O local agora mostra o nome do sistema ASE e a região em que ele está, e o seletor de plano de preços será substituído por um seletor de pool de trabalho.  
   
    ![Captura de tela que mostra os detalhes do sistema do ASE depois de selecionar o ASE no seletor de local.][3]

### <a name="selecting-a-worker-pool"></a>Selecionando o pool de trabalho
Normalmente no Serviço de Aplicativo do Azure e fora de um Ambiente do Serviço de Aplicativo, há três tamanhos da computação disponíveis com a seleção de um plano de preços dedicado.  De maneira semelhante, para um ASE, você pode definir até três pools de trabalho e especificar o tamanho da computação que é usado para esse pool de trabalho.  O que isso significa para locatários do ASE é que, em vez de selecionar um plano de preços com tamanho da computação para o plano do Serviço de Aplicativo, selecione o que chamamos de um *pool de trabalho*.  

A interface do usuário de seleção de pool de trabalho mostra o tamanho da computação usado para esse pool de trabalho abaixo do nome.  A quantidade disponível se refere a quantas instâncias de computação estão disponíveis para uso no pool.  O pool pode, na realidade, ter mais instâncias que esse número, mas esse valor se refere simplesmente a quantas não estão em uso.  Se você precisar ajustar o seu ambiente de serviço de aplicativo para adicionar mais recursos de computação, consulte [Configurando seu ambiente de serviço de aplicativo](app-service-web-configure-an-app-service-environment.md).

![Captura de tela que mostra o painel do pool de trabalho em que você pode selecionar os pools de trabalho para o ASE.][4]

Neste exemplo, você verá apenas dois pools de trabalho disponíveis. Isso ocorre porque o administrador do ASE alocou hosts apenas nesses dois pools de trabalho.  O terceiro aparecerá quando houver VMs alocadas para ele.  

## <a name="after-web-app-creation"></a>Após a criação do aplicativo Web
Há algumas considerações sobre a execução de aplicativos Web e o gerenciamento de planos do Serviço de Aplicativo em um ASE que precisam ser levadas em conta.  

Como observado anteriormente, o proprietário do ASE é responsável pelo tamanho do sistema e, como resultado, responsável também por garantir que haja capacidade suficiente para hospedar os planos do Serviço de Aplicativo desejados. Se não houver nenhum processador disponível, você não poderá criar o plano do Serviço de Aplicativo.  Isso também é verdadeiro ao dimensionar seu aplicativo Web.  Se você precisar de mais instâncias, será necessário que o administrador de seu ambiente do serviço de aplicativo adicione mais processadores.

Depois de criar seu aplicativo Web e o plano do Serviço de Aplicativo, será uma boa ideia escalá-lo verticalmente.  Em um ASE, é sempre necessário ter pelo menos 2 instâncias do seu plano do Serviço de Aplicativo para proporcionar tolerância a falhas para seus aplicativos.  O dimensionamento de um plano do Serviço de Aplicativo em um ASE é igual ao normal por meio da interface do usuário do plano do Serviço de Aplicativo.  Para obter mais informações sobre o dimensionamento, [Como dimensionar um aplicativo Web em um Ambiente de Serviço de Aplicativo](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/management/overview.md
[AzurePowershell]: /powershell/azure/