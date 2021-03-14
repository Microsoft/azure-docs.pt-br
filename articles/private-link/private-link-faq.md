---
title: Perguntas frequentes sobre o link privado do Azure
description: Saiba mais sobre o link privado do Azure.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: 4e81d8f88a7c01b6d302bcdaa88559159bed04ea
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709391"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Perguntas frequentes sobre o link privado do Azure

## <a name="private-link"></a>Link Privado

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>O que é o ponto de extremidade privado do Azure e o serviço de vínculo privado do Azure?

- **[Ponto de extremidade privado do Azure](private-endpoint-overview.md)**: o ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura a um serviço da plataforma de link privado do Azure. Você pode usar pontos de extremidade privados para se conectar a um serviço de PaaS do Azure que dá suporte ao link privado ou ao seu próprio serviço de vínculo privado.
- **[Serviço de vínculo privado do Azure](private-link-service-overview.md)**: o serviço de vínculo privado do Azure é um serviço criado por um provedor de serviços. Atualmente, um serviço de vínculo privado pode ser anexado à configuração de IP de front-end de um Standard Load Balancer. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Como o tráfego é enviado ao usar o link privado?
O tráfego é enviado de forma privada usando o backbone da Microsoft. Ele não atravessa a Internet. O link privado do Azure não armazena dados do cliente.
 
### <a name="what-is-the-difference-between-service-endpoints-and-private-endpoints"></a>Qual é a diferença entre pontos de extremidade de serviço e pontos de extremidade privados?
- Pontos de extremidade privados concedem acesso à rede para recursos específicos por trás de um determinado serviço, fornecendo segmentação granular. O tráfego pode acessar o recurso de serviço no local sem usar pontos de extremidade públicos.
- Um ponto de extremidade de serviço permanece um endereço IP roteável publicamente.  Um ponto de extremidade privado é um IP privado no espaço de endereço da rede virtual em que o ponto de extremidade privado está configurado.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Qual é a relação entre o serviço de link privado e o ponto de extremidade privado?
Vários tipos de recursos de link privado dão suporte ao acesso por meio do ponto de extremidade privado. Os recursos incluem os serviços de PaaS do Azure e seu próprio serviço de vínculo privado. É uma relação um-para-muitos. 

Um serviço de vínculo privado recebe conexões de vários pontos de extremidade privados. Um ponto de extremidade privado se conecta a um serviço de vínculo privado.    

### <a name="do-i-need-to-disable-network-policies-for-private-link"></a>É necessário desabilitar as políticas de rede para o link privado
Sim. O ponto de extremidade privado e o serviço de vínculo privado precisam desabilitar as políticas de rede para funcionar corretamente. Ambas têm propriedades independentes umas das outras.

## <a name="private-endpoint"></a>Ponto de extremidade privado 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Posso criar vários pontos de extremidade privados na mesma VNet? Eles podem se conectar a diferentes serviços? 
Sim. Você pode ter vários pontos de extremidade privados na mesma VNet ou sub-rede. Eles podem se conectar a diferentes serviços.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Eu preciso de uma sub-rede dedicada para pontos de extremidade privados? 
Não. Você não precisa de uma sub-rede dedicada para pontos de extremidade privados. Você pode escolher um IP de ponto de extremidade privado de qualquer sub-rede da VNet na qual seu serviço está implantado.  
 
### <a name="can-a-private-endpoint-connect-to-private-link-services-across-azure-active-directory-tenants"></a>Um ponto de extremidade privado pode se conectar aos serviços de vínculo privado entre Azure Active Directory locatários? 
Sim. Pontos de extremidade privados podem se conectar a serviços de vínculo privado ou a um PaaS do Azure entre Azure Active Directory locatários. Pontos de extremidade privados que se conectam entre locatários exigem uma aprovação de solicitação manual. 
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>O ponto de extremidade privado pode se conectar aos recursos de PaaS do Azure nas regiões do Azure?
Sim. Pontos de extremidade privados podem se conectar aos recursos de PaaS do Azure nas regiões do Azure.

### <a name="can-i-modify-my-private-endpoint-network-interface-nic-"></a>Posso modificar minha NIC (interface de rede de ponto de extremidade) privada?
Quando um ponto de extremidade privado é criado, uma NIC somente leitura é atribuída. Isso não pode ser modificado e permanecerá para o ciclo de vida do ponto de extremidade privado.

## <a name="private-link-service"></a>Serviço de Link Privado
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Quais são os pré-requisitos para criar um serviço de link privado? 
Os back-ends de seu serviço devem estar em uma rede virtual e por trás de um Standard Load Balancer.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Como posso dimensionar meu serviço de vínculo privado? 
Você pode dimensionar seu serviço de vínculo privado de algumas maneiras diferentes: 
- Adicionar VMs de back-end ao pool por trás de seu Standard Load Balancer 
- Adicione um IP ao serviço de vínculo privado. Permitimos até 8 IPs por serviço de link privado.  
- Adicione um novo serviço de vínculo privado a Standard Load Balancer. Permitimos até oito serviços de link privado por balanceador de carga.   

### <a name="what-is-natnetwork-address-translation-ip-configuration-used-in-private-link-service-how-can-i-scale-in-terms-of-available-ports-and-connections"></a>O que é a configuração de IP NAT (conversão de endereços de rede) usada no serviço de link privado? Como posso dimensionar em termos de conexões e portas disponíveis? 

A configuração de IP de NAT garante que não haja conflito de IP entre a origem (lado do consumidor) e o espaço de endereço de destino (provedor de serviço) fornecendo o NAT de origem no tráfego de link privado no lado de destino (lado do provedor de serviço). O endereço IP de NAT aparecerá como IP de origem para todos os pacotes recebidos pelo seu serviço e IP de destino para todos os pacotes enviados pelo seu serviço.  O IP de NAT pode ser escolhido de qualquer sub-rede em uma rede virtual do provedor de serviços. 

Cada IP de NAT fornece conexões TCP de 64K (portas de 64K) por VM por trás do Standard Load Balancer. Para dimensionar e adicionar mais conexões, você pode adicionar novos IPs de NAT ou adicionar mais VMs por trás do Standard Load Balancer. Isso irá dimensionar a disponibilidade da porta e permitir mais conexões. As conexões serão distribuídas entre IPs NAT e VMs por trás do Standard Load Balancer.

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Posso conectar meu serviço a vários pontos de extremidade privados?
Sim. Um serviço de vínculo privado pode receber conexões de vários pontos de extremidade privados. No entanto, um ponto de extremidade privado só pode se conectar a um serviço de link privado.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Como devo controlar a exposição do meu serviço de vínculo privado?
Você pode controlar a exposição usando a configuração de visibilidade no serviço de vínculo privado. A visibilidade dá suporte a três configurações:

- **Nenhum** -somente assinaturas com acesso RBAC do Azure podem localizar o serviço. 
- Assinaturas somente **restritivas** que são aprovadas e com acesso de RBAC do Azure podem localizar o serviço. 
- **Todos** -todos podem localizar o serviço. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Posso criar um serviço de vínculo privado com o Load Balancer básico? 
Não. Não há suporte para o serviço de vínculo privado em um balanceador de carga básico.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Uma sub-rede dedicada é necessária para o serviço de vínculo privado? 
Não. O serviço de vínculo privado não requer uma sub-rede dedicada. Você pode escolher qualquer sub-rede em sua VNet onde o serviço é implantado.   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Sou um provedor de serviços usando o link privado do Azure. É necessário ter certeza de que todos os meus clientes têm espaço IP exclusivo e não se sobrepõem ao meu espaço de IP? 
Não. O link privado do Azure fornece essa funcionalidade para você. Não é necessário ter espaço de endereço não sobreposto com o espaço de endereço do seu cliente. 

##  <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Link Privado do Azure](private-link-overview.md)
