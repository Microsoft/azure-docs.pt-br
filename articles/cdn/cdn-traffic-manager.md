---
title: Fazer failover em vários pontos de extremidade com o Gerenciador de tráfego
titleSuffix: Azure Content Delivery Network
description: Saiba como configurar o failover em vários pontos de extremidade de rede de distribuição de conteúdo do Azure usando o Gerenciador de tráfego do Azure.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 10/08/2020
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: d2d3bd43a0f17167e855d7e678a96cd79fe42237
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92777734"
---
# <a name="failover-across-multiple-endpoints-with-azure-traffic-manager"></a>Failover em vários pontos de extremidade com o Gerenciador de tráfego do Azure

Ao configurar o CDN (Azure Content Delivery Network), você pode selecionar o provedor e a camada de preços ideais para suas necessidades. 

A CDN do Azure, com sua infraestrutura distribuída globalmente, por padrão, cria redundância local e geográfica e balanceamento de carga global para melhorar a disponibilidade e o desempenho do serviço. 

Se um local não estiver disponível para fornecer conteúdo, as solicitações serão roteadas automaticamente para outro local. O ponto de presença ideal (POP) é usado para atender a cada solicitação de cliente. O roteamento automático é baseado em fatores como local de solicitação e carga do servidor.
 
Se você tiver vários perfis CDN, poderá melhorar ainda mais a disponibilidade e o desempenho com o Gerenciador de Tráfego do Azure. 

Use o Gerenciador de tráfego do Azure com a CDN do Azure para balancear a carga entre vários pontos de extremidade CDN para:
 
* Failover
* Balanceamento de carga geográfica 

Em um cenário típico de failover, todas as solicitações de cliente são direcionadas para o perfil CDN primário. 

Se o perfil não estiver disponível, as solicitações serão direcionadas para o perfil secundário.  As solicitações são retomadas para seu perfil primário quando ele volta a ficar online.

Usando o Azure Traffic Manager dessa maneira garante que seu aplicativo web esteja sempre disponível. 

Este artigo fornece orientações e um exemplo de como configurar o failover com perfis de: 

* **CDN Standard do Azure da Verizon**
* **CDN Standard do Azure da Akamai**

Também há suporte para a **CDN do Azure da Microsoft** .

## <a name="create-azure-cdn-profiles"></a>Criar perfis da CDN do Azure
Crie dois ou mais perfis de CDN do Azure e pontos de extremidade com provedores diferentes.

1. Crie dois perfis de CDN:
    * **CDN Standard do Azure da Verizon**
    * **CDN Standard do Azure da Akamai** 

    Crie os perfis seguindo as etapas em [criar um novo perfil de CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN vários perfis](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Em cada um dos novos perfis, crie pelo menos um ponto de extremidade seguindo as etapas em [Criar um novo ponto de extremidade de CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="create-traffic-manager-profile"></a>Criar perfil do Gerenciador de tráfego
Crie um perfil do Gerenciador de tráfego do Azure e configure o balanceamento de carga em seus pontos de extremidade CDN. 

1. Crie um perfil do Gerenciador de Tráfego do Azure seguindo as etapas em [Criar um perfil do Gerenciador de Tráfego](../traffic-manager/quickstart-create-traffic-manager-profile.md). 

    * **Método de roteamento** , selecione **prioridade** .

2. Adicione seus pontos de extremidade CDN em seu perfil do Gerenciador de Tráfego seguindo as etapas em [Incluir pontos de extremidade do Gerenciador de Tráfego](../traffic-manager/quickstart-create-traffic-manager-profile.md#add-traffic-manager-endpoints)

    * **Digite** , selecione **pontos de extremidade externos** .
    * **Prioridade** , insira um número.

    Por exemplo, crie **cdndemo101akamai.azureedge.net** com uma prioridade **1** e **cdndemo101verizon.azureedge.net** com uma prioridade **2** .

   ![Pontos de extremidade do Gerenciador de Tráfego da CDN](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="configure-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Configurar o domínio personalizado na CDN do Azure e no Gerenciador de tráfego do Azure
Depois de configurar os perfis CDN e Gerenciador de tráfego, siga estas etapas para adicionar o mapeamento de DNS e registrar o domínio personalizado nos pontos de extremidade da CDN. Neste exemplo, o nome de domínio personalizado é **cdndemo101.dustydogpetcare.online** .

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


2.  Do seu perfil de CDN do Azure, selecione o primeiro ponto de extremidade do CDN (Akamai). Selecione **Adicionar domínio personalizado** e insira **cdndemo101. dustydogpetcare. online** . Verifique se a marca de seleção para validar o domínio personalizado está verde. 

    A CDN do Azure usa o subdomínio **cdnverify** para validar o mapeamento de DNS para concluir este processo de registro. Para obter mais informações, consulte [criar um registro DNS CNAME](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Esta etapa permite que a CDN do Azure reconheça o domínio personalizado para que ele possa responder a suas solicitações.
    
    > [!NOTE]
    > Para habilitar o TLS em um **Azure CDN do Akamai** Profiles, você deve fazer o CNAME direto do domínio personalizado para seu ponto de extremidade. Ainda não há suporte para o cdnverify para habilitar o TLS. 
    >

3.  Retorne ao site do provedor de domínio do seu domínio personalizado. Atualize o primeiro mapeamento DNS que você criou. Mapeie o domínio personalizado para o segundo ponto de extremidade da CDN.
                             
    Por exemplo: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Seu perfil de CDN do Azure, selecione o segundo ponto de extremidade do CDN (Verizon) e repita a etapa 2. Selecione **Adicionar domínio personalizado** e digite **cdndemo101. dustydogpetcare. online** .
 
Depois de concluir essas etapas, o serviço de várias CDN com recursos de failover será configurado com o Gerenciador de tráfego do Azure. 

Você pode acessar as URLs de teste de seu domínio personalizado. 

Para testar a funcionalidade, desative o ponto de extremidade CDN principal e verifique se a solicitação foi movida corretamente para o ponto de extremidade CDN secundário. 

## <a name="next-steps"></a>Próximas etapas
Você pode configurar outros métodos de roteamento, como geográfico, para balancear a carga entre diferentes pontos de extremidade da CDN. 

Para obter mais informações, consulte [Configurar o método de roteamento de tráfego geográfico usando o Gerenciador de Tráfego](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).