---
title: Configurar o failover em vários pontos de extremidade de CDN do Azure com o Gerenciador de Tráfego do Azure | Microsoft Docs
description: Saiba mais sobre como definir o Gerenciador de tráfego do Azure com pontos de extremidade da CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 276fe9352d0c4ca7ec525b88d65689b56c0ba027
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593340"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Configurar o failover em vários pontos de extremidade de CDN do Azure com o Gerenciador de Tráfego do Azure

Ao configurar o CDN (Azure Content Delivery Network), você pode selecionar o provedor e a camada de preços ideais para suas necessidades. A CDN do Azure, com sua infraestrutura distribuída globalmente, por padrão, cria redundância local e geográfica e balanceamento de carga global para melhorar a disponibilidade e o desempenho do serviço. Se um local não estiver disponível para veicular conteúdo, as solicitações serão roteadas automaticamente para outro local e o POP ideal (com base em fatores como local da solicitação e carga do servidor) será usado para atender a cada solicitação do cliente. 
 
Se você tiver vários perfis CDN, poderá melhorar ainda mais a disponibilidade e o desempenho com o Gerenciador de Tráfego do Azure. Você pode usar o Gerenciador de Tráfego do Azure com o CDN do Azure para balancear a carga entre vários pontos de extremidade CDN para failover, balanceamento de carga geográfica e outros cenários. Em um cenário de failover típico, todas as solicitações do cliente são direcionadas primeiro para o perfil CDN principal; se o perfil não estiver disponível, as solicitações serão passadas para o perfil CDN secundário até que seu perfil CDN principal esteja novamente online. Usando o Azure Traffic Manager dessa maneira garante que seu aplicativo web esteja sempre disponível. 

Este artigo fornece orientação e um exemplo de como configurar o failover com perfis **CDN Standard do Azure da Verizon** e **CDN Standard do Azure da Akamai**.

## <a name="set-up-azure-cdn"></a>Configurar uma CDN do Azure 
Crie dois ou mais perfis de CDN do Azure e pontos de extremidade com provedores diferentes.

1. Crie um **Padrão CDN do Azure da Verizon** e **Padrão CDN do Azure do perfil Akamai** seguindo as etapas em [Crie um novo perfil CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN vários perfis](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Em cada um dos novos perfis, crie pelo menos um ponto de extremidade seguindo as etapas em [Criar um novo ponto de extremidade de CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Configurar Gerenciador de Tráfego do Azure
Criar um perfil do Gerenciador de tráfego do Azure e configurar o balanceamento de carga entre os pontos de extremidade CDN. 

1. Crie um perfil do Gerenciador de Tráfego do Azure seguindo as etapas em [Criar um perfil do Gerenciador de Tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile). 

    Para **método de roteamento**, selecione **prioridade**.

2. Adicione seus pontos de extremidade CDN em seu perfil do Gerenciador de Tráfego seguindo as etapas em [Incluir pontos de extremidade do Gerenciador de Tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    Para **tipo**, selecione **pontos de extremidade externos**. Para **prioridade**, insira um número.

    Por exemplo, crie *cdndemo101akamai.azureedge.net* com uma prioridade *1* e *cdndemo101verizon.azureedge.net* com uma prioridade *2*.

   ![Pontos de extremidade do Gerenciador de Tráfego da CDN](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Configurar um domínio personalizado na CDN do Azure e o Gerenciador de tráfego do Azure
Depois de configurar seus perfis do CDN e do Gerenciador de Tráfego, siga estas etapas para adicionar o mapeamento de DNS e registrar o domínio personalizado nos pontos de extremidade da CDN. Neste exemplo, o nome de domínio personalizado é *cdndemo101.dustydogpetcare.online*.

1. Vá para o site do provedor de domínio de seu domínio personalizado, como o GoDaddy, e crie duas entradas CNAME de DNS. 

    a. Para a primeira entrada CNAME, mapeie seu domínio personalizado, com o subdomínio cdnverify, para o seu ponto de extremidade CDN. Essa entrada é uma etapa obrigatória para registrar o domínio personalizado no ponto de extremidade da CDN que você adicionou ao Gerenciador de Tráfego na etapa 2.

      Por exemplo: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Para a segunda entrada CNAME, mapeie seu domínio personalizado, sem o subdomínio cdnverify, para o seu ponto de extremidade CDN. Essa entrada mapeia o domínio personalizado ao Gerenciador de Tráfego. 

      Por exemplo: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Se o domínio está atualmente ativo e não pode ser interrompido, siga esta etapa pela última vez. Verifique se os pontos de extremidade do CDN e os domínios do gerenciador de tráfego estão ativos antes de atualizar o DNS do seu domínio personalizado para o Gerenciador de Tráfego.
    >


2.  Do seu perfil de CDN do Azure, selecione o primeiro ponto de extremidade do CDN (Akamai). Selecione **Adicionar domínio personalizado** e de entrada *cdndemo101.dustydogpetcare.online*. Verifique se a marca de seleção para validar o domínio personalizado está verde. 

    A CDN do Azure usa o subdomínio *cdnverify* para validar o mapeamento de DNS para concluir este processo de registro. Para obter mais informações, consulte [criar um registro DNS CNAME](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Esta etapa permite que a CDN do Azure reconheça o domínio personalizado para que ele possa responder a suas solicitações.
    
    > [!NOTE]
    > Para habilitar o SSL em um **Azure CDN da Akamai** perfis, você deve diretamente cname do domínio personalizado para seu ponto de extremidade. ainda não há suporte para cdnverify para habilitar o SSL. 
    >

3.  Retorne ao site do provedor de domínio do seu domínio personalizado e atualize o primeiro mapeamento de DNS criado para que o domínio personalizado seja mapeado para o segundo ponto de extremidade do CDN.
                             
    Por exemplo: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Seu perfil de CDN do Azure, selecione o segundo ponto de extremidade do CDN (Verizon) e repita a etapa 2. Selecione **Adicionar domínio personalizado**e de entrada *cdndemo101.dustydogpetcare.online*.
 
Depois de concluir essas etapas, seu serviço de multi-CDN com recursos de failover é configurado com o Gerenciador de Tráfego do Azure. Você poderá acessar o teste de URLs do seu domínio personalizado. Para testar a funcionalidade, desative o ponto de extremidade CDN principal e verifique se a solicitação foi movida corretamente para o ponto de extremidade CDN secundário. 

## <a name="next-steps"></a>Próximas etapas
Você também pode configurar outros métodos de roteamento, como geográfico, para balancear a carga entre diferentes pontos finais da CDN. Para obter mais informações, consulte [Configurar o método de roteamento de tráfego geográfico usando o Gerenciador de Tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



