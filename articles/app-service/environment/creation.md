---
title: Criar um Ambiente do Serviço de Aplicativo
description: Saiba como criar um Ambiente do Serviço de Aplicativo.
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 52a43874060748479beebc994bf0a9b663cde9dc
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020172"
---
# <a name="create-an-app-service-environment"></a>Criar um Ambiente do Serviço de Aplicativo

> [!NOTE]
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v3 (versão prévia)
> 

O [ambiente do serviço de aplicativo (ase)][Intro] é uma implantação de locatário único do serviço de aplicativo que injeta em sua VNet (rede virtual) do Azure.  O ASEv3 só dá suporte à exposição de aplicativos em um endereço privado em sua vnet. Quando um ASEv3 é criado durante a visualização, esses recursos são adicionados à sua assinatura.

- Ambiente do Serviço de Aplicativo
- Ponto de extremidade privado

Uma implantação de um ASE exigirá o uso de duas sub-redes.  Uma sub-rede manterá o ponto de extremidade privado.  Essa sub-rede pode ser usada para outras coisas, como VMs.  A outra sub-rede é usada para chamadas de saída feitas a partir do ASE.  Essa sub-rede não pode ser usada para mais nada além do ASE. 

## <a name="before-you-create-your-ase"></a>Antes de criar seu ASE

Depois que o ASE for criado, você não poderá alterar:

- Localização
- Assinatura
- Resource group
- VNet (rede virtual) do Azure usada
- Sub-redes usadas
- Tamanho da sub-rede
- Nome do seu ASE

A sub-rede de saída precisa ser grande o suficiente para manter o tamanho máximo que você dimensionará seu ASE. Escolha uma sub-rede grande o suficiente para dar suporte às suas necessidades de dimensionamento máximo, uma vez que ela não pode ser alterada após a criação. O tamanho recomendado é de/24 com endereços 256.

Depois que o ASE é criado, você pode adicionar aplicativos a ele. Quando seu ASEv3 não tem planos do serviço de aplicativo, há uma cobrança como se você tivesse uma instância de um plano do serviço de aplicativo do I1v2 em seu ASE.  

O ASEv3 só é oferecido em regiões selecionadas. Mais regiões serão adicionadas à medida que a versão prévia for voltada para a GA. 

## <a name="creating-an-ase-in-the-portal"></a>Criando um ASE no portal

1. Para criar um ASEv3, pesquise no Marketplace por **ambiente do serviço de aplicativo (versão prévia)**.  
2. Noções básicas: selecione a assinatura, selecione ou crie o grupo de recursos e insira o nome do seu ASE.  O nome do ASE também será usado para o sufixo de domínio de seu ASE.  Se o nome do ASE for *contoso* , o sufixo do domínio será *contoso.appserviceenvironment.net*.  Esse nome será definido automaticamente em sua zona privada de DNS do Azure usada pela vnet em que o ASE está implantado. 

    ![Guia criar noções básicas do Ambiente do Serviço de Aplicativo](./media/creation/creation-basics.png)

3. Hospedagem: selecione preferência de sistema operacional, implantação de grupo de hosts. A preferência de so indica o sistema operacional que você usará inicialmente para seus aplicativos neste ASE. Você pode adicionar aplicativos do outro sistema operacional após a criação do ASE. A implantação de grupo de hosts é usada para selecionar hardware dedicado. Com o ASEv3, você pode selecionar habilitado e, em seguida, parar em hardware dedicado. Você é cobrado por todo o host dedicado com a criação do ASE e, em seguida, um preço reduzido para as instâncias do plano do serviço de aplicativo. 

    ![Ambiente do Serviço de Aplicativo hospedar seleções](./media/creation/creation-hosting.png)

4. Rede: selecione ou crie sua rede virtual, selecione ou crie sua sub-rede de entrada, selecione ou crie sua sub-rede de saída. Qualquer sub-rede usada para saída deve estar vazia e delegada a Microsoft. Web/hostingEnvironments. Se você criar sua sub-rede por meio do portal, a sub-rede será automaticamente delegada para você.

    ![Ambiente do Serviço de Aplicativo seleções de rede](./media/creation/creation-networking.png)

5. Revisar e criar: Verifique se a configuração está correta e selecione criar. Seu ASE levará aproximadamente uma hora para ser criado. 

    ![Ambiente do Serviço de Aplicativo revisar e criar](./media/creation/creation-review.png)

Depois que a criação do ASE for concluída, você poderá selecioná-la como um local ao criar seus aplicativos. Para saber mais sobre como criar aplicativos em seu novo ASE, leia [usando um ambiente do serviço de aplicativo][UsingASE]

## <a name="os-preference"></a>Preferência de so
Em um ASE, você pode ter aplicativos do Windows, aplicativos Linux ou ambos. No ASEv2, a preferência inicial selecionada durante a criação adiciona a infraestrutura de alta disponibilidade para esse sistema operacional durante a criação do ASE. Para adicionar aplicativos do outro sistema operacional, basta tornar os aplicativos como de costume e selecionar o sistema operacional desejado. No ASEv3, isso não afetará o comportamento de back-end appreciatively.  

## <a name="dedicated-hosts"></a>Hosts dedicados
O ASE normalmente é implantado em VMs que são provisionadas em um hipervisor multilocatário. Se você precisar implantar em sistemas dedicados, incluindo o hardware, poderá provisionar seu ASE em hosts dedicados. Na visualização inicial do ASEv3, os hosts dedicados entram em um par. Cada host dedicado está em uma zona de disponibilidade separada, se a região permitir. As implantações dedicadas do ASE baseadas em host têm o preço diferente do normal. Há um encargo para o host dedicado e, em seguida, outro encargo para cada instância do plano do serviço de aplicativo.  

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
