---
title: Dimensionar um aplicativo no ASE v1
description: Dimensionando um aplicativo em um Ambiente do Serviço de Aplicativo. Este documento é fornecido somente para clientes que usam o ASE v1 herdado.
author: ccompy
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0e665ec27da0a898e754817f946b965ac7360fda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86220551"
---
# <a name="scaling-apps-in-an-app-service-environment-v1"></a>Dimensionando aplicativos em um Ambiente do Serviço de Aplicativo v1
No Serviço de Aplicativo do Azure normalmente há três itens que você pode dimensionar:

* plano de preços
* tamanho do trabalho 
* número de instâncias.

Em um ASE não é necessário selecionar ou alterar o plano de preços.  Em termos de recursos, ele já está no nível de recurso de preços Premium.  

Com relação aos tamanhos de trabalho, o administrador do ASE pode atribuir o tamanho do recurso de computação a ser usado para cada pool de trabalho.  Isso significa que você pode ter o Pool de Trabalhadores 1 com recursos de computação P4 e o Pool de Trabalhadores 2 com recursos de computação P1, se desejado.  Eles não precisam estar em ordem de tamanho.  Para obter detalhes sobre os tamanhos e seus preços, confira o documento aqui [Preços do Serviço de Aplicativo do Azure][AppServicePricing].  Isso deixa as opções de dimensionamento para aplicativos Web e Planos do Serviço de Aplicativo em um Ambiente do Serviço de Aplicativo sendo:

* seleção do pool de trabalhadores
* número de instâncias

A alteração de qualquer um dos itens é feita por meio da interface do usuário apropriada mostrada para os seus Planos do Serviço de Aplicativo hospedados no ASE.  

![Captura de tela que mostra onde exibir os detalhes do plano de serviço de escala e o plano de serviço do pool de trabalho.][1]

Você não pode escalar verticalmente seu ASP além do número de recursos de computação disponíveis no pool de trabalho em que o ASP está localizado.  Se precisar de recursos de computação no pool de trabalho, providencie para que o administrador do ASE os adicione.  Para obter informações sobre a reconfiguração de seu ASE, leia as informações fornecidas aqui: [Como configurar um ambiente do Serviço de Aplicativo][HowtoConfigureASE].  Também convém aproveitar os recursos de autoescala do ASE para adicionar capacidade com base no agendamento ou em métricas.  Para obter mais detalhes sobre como configurar a autoescala para o ambiente ASE propriamente dito, confira [Como configurar a autoescala para um Ambiente do Serviço de Aplicativo][ASEAutoscale].

Você pode criar vários planos de serviço de aplicativo usando recursos de computação de pools de trabalho diferentes, ou pode usar o mesmo pool de trabalho.  Por exemplo, se tiver (10) recursos de computação disponíveis no Pool de trabalho 1, você poderá optar por criar um plano de serviço de aplicativo que usa (6) recursos de computação e um segundo plano de serviço de aplicativo que usa (4) recursos de computação.

### <a name="scaling-the-number-of-instances"></a>Dimensionando o número de instâncias
Quando você cria seu aplicativo Web em um Ambiente do Serviço de Aplicativo, ele começa com uma instância.  Em seguida, você pode escalar horizontalmente para mais instâncias a fim de fornecer recursos de computação adicionais para seu aplicativo.   

Se seu ASE tiver capacidade suficiente, isso é muito simples.  Acesse seu Plano do Serviço de Aplicativo que contém os sites que você deseja escalar verticalmente e selecione Escala.  Isso abre a interface do usuário em que você pode definir manualmente a escala para o ASP ou configurar as regras de autoescala para ele.  Para dimensionar manualmente seu aplicativo, basta definir ***Scale de** _ para _ *_uma contagem de instâncias inserida manualmente_* *.  Daqui, arraste o controle deslizante para a quantidade desejada ou insira-a na caixa ao lado do controle deslizante.  

![Captura de tela que mostra onde você pode definir a escala para o ASP ou configurar regras de dimensionamento automático para o ASP.][2] 

As regras de autoescala para um ASP em um ASE funcionam da mesma forma que em uma condição normal.  Você pode selecionar ***percentual de CPU** _ em _*_Dimensionar por_*_ e criar regras de dimensionamento automático para seu ASP com base na porcentagem de CPU ou pode criar regras mais complexas usando _as regras de agendamento e desempenho_* *.  Para ver detalhes mais completos sobre como configurar a autoescala, use o guia [Escalar um aplicativo no Serviço de Aplicativo do Azure][AppScale]. 

### <a name="worker-pool-selection"></a>seleção do pool de trabalhadores
Como observamos anteriormente, a seleção do pool de trabalho é acessada da interface do usuário do ASP.  Abra a folha do ASP que deseja escalar e selecione o pool de trabalho.  Você verá todos os pools de trabalhadores que configurou no seu Ambiente do Serviço de Aplicativo.  Se você tiver somente um pool de trabalhadores, verá somente o pool listado.  Para alterar a localização de pool de trabalho do ASP, basta selecionar o pool de trabalho para o qual deseja mover seu Plano do Serviço de Aplicativo.  

![Captura de tela que mostra onde você pode alterar em qual pool de trabalho seu ASP está.][3]

Antes de mover seu ASP de um pool de trabalho para outro, é importante se certificar de que você terá a capacidade adequada para o ASP.  Na lista de pools de trabalhadores, não apenas o nome do pool de trabalhadores está listado, mas você também pode ver quantos trabalhadores estão disponíveis nesse pool de trabalhadores.  Certifique-se de que há instâncias suficientes disponíveis para conter o seu Plano do Serviço de Aplicativo.  Se precisar de mais recursos de computação no pool de trabalhadores para o qual deseja mover, providencie para que o administrador do seu ASE os adicione.  

> [!NOTE]
> Mover um ASP de um pool de trabalho causará inicializações a frio dos aplicativos nesse ASP.  Isso pode fazer com que as solicitações sejam executadas lentamente enquanto o aplicativo é inicializado a frio nos novos recursos de computação.  A inicialização a frio pode ser evitada usando a [funcionalidade de aquecimento do aplicativo][AppWarmup] no Serviço de Aplicativo do Azure.  O módulo Inicialização de Aplicativos descrito neste artigo também funciona para inicializações a frio porque o processo de inicialização também é invocado quando aplicativos são inicializados a frio em novos recursos de computação. 
> 
> 

## <a name="getting-started"></a>Introdução
Para se familiarizar com os Ambientes do Serviço de Aplicativo, confira [Como criar um Ambiente do Serviço de Aplicativo][HowtoCreateASE]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../manage-scale-up.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../manage-scale-up.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
