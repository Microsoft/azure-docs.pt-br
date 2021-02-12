---
title: Criar um ASE v1
description: Descrição do fluxo de criação para um ambiente do serviço de aplicativo v1. Este documento é fornecido somente para clientes que usam o ASE v1 herdado.
author: ccompy
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 543050bc899c257c4ad5e0d0c399a1de6f0f58f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005191"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Como criar um Ambiente do Serviço de Aplicativo v1 

> [!NOTE]
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v1. Há uma versão mais recente do Ambiente de Serviço de Aplicativo que é mais fácil de usar e é executado na infraestrutura mais avançada. Para saber mais sobre a nova versão, comece com [Introdução ao Ambiente do Serviço de Aplicativo](intro.md).
> 

### <a name="overview"></a>Visão geral
O Ambiente de Serviço de Aplicativo (ASE) é uma opção de serviço Premium do Serviço de Aplicativo do Azure que fornece um recurso de configuração avançada não disponível em carimbos com vários locatários. O recurso ASE essencialmente implanta o Serviço de Aplicativo do Azure na rede virtual de um cliente. Para obter uma maior compreensão dos recursos oferecidos pelos ambientes do serviço de aplicativo, leia a documentação [O que é um ambiente do serviço de aplicativo][WhatisASE].

### <a name="before-you-create-your-ase"></a>Antes de criar seu ASE
É importante estar ciente dos itens que você não pode alterar. Os aspectos que você não pode alterar quanto ao ASE após sua criação são:

* Localização
* Assinatura
* Grupo de recursos
* VNET usada
* Sub-rede usada 
* Tamanho da sub-rede

Ao escolher uma rede virtual e especificar uma sub-rede, verifique se ela é grande o suficiente para acomodar qualquer crescimento futuro. 

### <a name="creating-an-app-service-environment-v1"></a>Criando um Ambiente do Serviço de Aplicativo v1
Para criar um ambiente do serviço de aplicativo v1, você pode pesquisar o Azure Marketplace para ***ambiente do serviço de aplicativo v1** _ ou passar por _ *criar um recurso**-> **Web + celular**  ->  **ambiente do serviço de aplicativo**. Para criar um ASEv1:

1. Forneça o nome do seu ASE. O nome que você especificou para o ASE será usado para os aplicativos criados no ASE. Se o nome do ASE fosse appsvcenvdemo, o nome do subdomínio seria: *appsvcenvdemo.p.azurewebsites.net*. Se você tivesse criado um aplicativo nomeado *mytestapp*, ele seria endereçável em *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Você não pode usar espaços em branco no nome do ASE. Se você usar letras maiúsculas entre os caracteres do nome, o nome de domínio será a versão total em letras minúsculas desse nome. Se você usa um ILB, seu nome ASE não é usado em seu subdomínio, mas é declarado explicitamente durante a criação do ASE.
   
    ![Captura de tela que mostra como criar um Ambiente do Serviço de Aplicativo (ASE).][1]
2. Selecione sua assinatura. A assinatura que você usa para o seu ASE também se aplicará a todas as aplicações que você criar nesse ASE. Você não pode colocar seu ASE em uma VNet que esteja em outra assinatura.
3. Selecione ou especifique um novo grupo de recursos. O grupo de recursos usado para seu ASE deve ser o mesmo usado para sua rede virtual. Se você selecionar uma VNet já existente, a seleção de grupo de recursos para o ASE será atualizada para refletir a de sua rede virtual.
   
    ![Captura de tela que mostra como selecionar ou modificar um novo grupo de recursos.][2]
4. Faça suas seleções de Rede Virtual e Local. Você pode optar por criar uma nova rede virtual ou selecionar uma rede virtual já existente. Se selecionar uma nova rede virtual, você poderá especificar um nome e local. A nova VNet terá o intervalo de endereços 192.168.250.0/23 e uma sub-rede denominada **padrão** que é definida como 192.168.250.0/24. Você pode simplesmente selecionar um VNet pré-existente clássico ou do Gerenciador de Recursos. A seleção do tipo de VIP determina se seu ASE pode ser acessado diretamente por meio da Internet (Externo) ou se ele usa um Balanceador de Carga Interno (ILB). Para saber mais sobre eles, leia [Usando um Balanceador de Carga Interno com um Ambiente de Serviço de Aplicativo][ILBASE]. Se você selecionar um tipo de VIP externo, você poderá selecionar quantos endereços IP externos o sistema será criado para fins de IP SSL. Se selecionar Interno, você precisará especificar o subdomínio que seu ASE usará. Os ASEs podem ser implantados em redes virtuais que usam os intervalos de endereço público *ou* espaços de endereço *ou* RFC1918 (ou seja, endereços privados). Para usar uma rede virtual com um intervalo de endereços públicos, você precisará criar a VNet antecipadamente. Ao selecionar uma VNet já existente, você precisará criar uma nova sub-rede durante a criação do ASE. **Você não pode usar uma sub-rede criada previamente no Portal. Você pode criar um ASE com uma sub-rede pré-existente se criar seu ASE usando um modelo do Resource Manager.** Para criar um ASE por meio de um modelo, use as informações fornecidas aqui, [Criar um ambiente de serviço de aplicativo do modelo][ILBAseTemplate] e aqui, [Criar um ambiente de serviço de aplicativo do ILB do modelo][ASEfromTemplate].

### <a name="details"></a>Detalhes
É criado um ASE com dois Front-Ends e dois trabalhos. Os Front-Ends atuam como os pontos de extremidade HTTP/HTTPS e enviam tráfego para as Funções de Trabalho, que são funções que hospedam seus aplicativos. Você pode ajustar a quantidade após a criação do ASE e pode até mesmo configurar regras de dimensionamento automático nesses pools de recursos. Para obter mais detalhes sobre a colocação em escala manual, o gerenciamento e o monitoramento de um ambiente de serviço de aplicativo, acesse: [Como configurar um Ambiente de Serviço de Aplicativo][ASEConfig] 

Pode existir apenas um ASE na sub-rede usada pelo ASE. A sub-rede não pode ser usada para algo diferente de ASE

### <a name="after-app-service-environment-v1-creation"></a>Após a criação de um Ambiente do Serviço de Aplicativo v1
Após a criação do ASE é possível ajustar:

* Quantidade de Front-Ends (mínimo: 2)
* Quantidade de processadores (mínimo: 2)
* Quantidade de endereços IP disponíveis para SSL de IP
* Tamanho de recursos de computação usados pelos Front-Ends ou Processadores (o tamanho mínimo de Front-End é P2)

Há mais detalhes sobre o gerenciamento da colocação em escala manual e monitoramento de Ambientes de Serviço de Aplicativo aqui: [Como configurar um Ambiente de Serviço de Aplicativo][ASEConfig] 

Para saber mais sobre o dimensionamento automático, há um guia aqui: [Como configurar o dimensionamento automático para um Ambiente de Serviço de Aplicativo][ASEAutoscale]

Há dependências adicionais que não estão disponíveis para personalização, como o banco de dados e o armazenamento. Esses são gerenciados pelo Azure e fornecidos com o sistema. O armazenamento do sistema dá suporte a até 500 GB para todo o Ambiente de Serviço do Aplicativo e o banco de dados é ajustado pelo Azure como necessário, por meio do dimensionamento do sistema.

## <a name="getting-started"></a>Introdução
Para começar a usar Ambientes de Serviço de Aplicativo v1, veja [Introdução ao Ambiente de Serviço de Aplicativo v1][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
