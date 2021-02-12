---
title: O que é o serviço de vínculo privado do Azure?
description: Saiba mais sobre o serviço de vínculo privado do Azure.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 7983a80da8a5ca9d900e44515b5e078cc9d70d79
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684179"
---
# <a name="what-is-azure-private-link-service"></a>O que é o serviço de vínculo privado do Azure?

O serviço de Link Privado do Azure é a referência para seu serviço que é desenvolvido com o Link Privado do Azure. Seu serviço que está sendo executado por trás [do Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md) pode ser habilitado para acesso de link privado para que os consumidores de seu serviço possam acessá-lo de forma privada em seu próprio VNets. Seus clientes podem criar um ponto de extremidade privado dentro de sua VNet e mapeá-lo para esse serviço. Este artigo explica os conceitos relacionados ao lado do provedor de serviço. 

:::image type="content" source="./media/private-link-service-overview/consumer-provider-endpoint.png" alt-text="Fluxo de trabalho do serviço de vínculo privado" border="true":::

*Figura: serviço de vínculo privado do Azure.*

## <a name="workflow"></a>Fluxo de trabalho

![Fluxo de trabalho do serviço de vínculo privado](media/private-link-service-overview/private-link-service-workflow.png)


*Figura: fluxo de trabalho do serviço de vínculo privado do Azure.*

### <a name="create-your-private-link-service"></a>Criar seu serviço de vínculo privado

- Configure seu aplicativo para executar por trás de um balanceador de carga padrão em sua rede virtual. Se você já tiver seu aplicativo configurado por trás de um balanceador de carga padrão, poderá ignorar esta etapa.   
- Crie um serviço de vínculo privado referenciando o balanceador de carga acima. No processo de seleção do balanceador de carga, escolha a configuração de IP de front-end onde você deseja receber o tráfego. Escolha uma sub-rede para endereços IP de NAT para o serviço de link privado. É recomendável ter pelo menos oito endereços IP de NAT disponíveis na sub-rede. Todo o tráfego do consumidor parecerá originar esse pool de endereços IP privados para o provedor de serviços. Escolha as propriedades/configurações apropriadas para o serviço de link privado.    

    > [!NOTE]
    > O serviço de vínculo privado do Azure só tem suporte em Standard Load Balancer. 
    
### <a name="share-your-service"></a>Compartilhar seu serviço

Depois de criar um serviço de vínculo privado, o Azure gerará um moniker nomeado globalmente exclusivo chamado "alias" com base no nome que você fornecer para o serviço. Você pode compartilhar o alias ou o URI de recurso do seu serviço com seus clientes offline. Os consumidores podem iniciar uma conexão de link privado usando o alias ou o URI de recurso.
 
### <a name="manage-your-connection-requests"></a>Gerenciar suas solicitações de conexão

Depois que um consumidor inicia uma conexão, o provedor de serviços pode aceitar ou rejeitar a solicitação de conexão. Todas as solicitações de conexão serão listadas na propriedade **privateendpointconnections** no serviço de link privado.
 
### <a name="delete-your-service"></a>Excluir seu serviço

Se o serviço de vínculo privado não estiver mais em uso, você poderá excluí-lo. No entanto, antes de excluir o serviço, verifique se não há nenhuma conexão de ponto de extremidade privada associada a ele. Você pode rejeitar todas as conexões e excluir o serviço.

## <a name="properties"></a>Propriedades

Um serviço de vínculo privado especifica as seguintes propriedades: 

|Propriedade |Explicação  |
|---------|---------|
|Estado de provisionamento (provisioningState)  |Uma propriedade somente leitura que lista o estado de provisionamento atual para o serviço de vínculo privado. Os Estados de provisionamento aplicáveis são: "excluindo; Falha ao Foi Atualizando ". Quando o estado de provisionamento for "êxito", você terá provisionado com êxito o serviço de vínculo privado.        |
|Alias (alias)     | Alias é uma cadeia de caracteres somente leitura globalmente exclusiva para seu serviço. Ele ajuda a mascarar os dados do cliente para seu serviço e, ao mesmo tempo, cria um nome de fácil compartilhamento para seu serviço. Quando você cria um serviço de vínculo privado, o Azure gera o alias para o serviço que você pode compartilhar com seus clientes. Seus clientes podem usar esse alias para solicitar uma conexão ao seu serviço.          |
|Visibilidade (visibilidade)     | Visibility é a propriedade que controla as configurações de exposição para seu serviço de vínculo privado. Os provedores de serviços podem optar por limitar a exposição a seu serviço a assinaturas com permissões do Azure RBAC (controle de acesso baseado em função), um conjunto restrito de assinaturas ou todas as assinaturas do Azure.          |
|Aprovação automática (autoaprovação)    |   A aprovação automática controla o acesso automatizado ao serviço de vínculo privado. As assinaturas especificadas na lista aprovação automática são aprovadas automaticamente quando uma conexão é solicitada de pontos de extremidade privados nessas assinaturas.          |
|Load Balancer configuração de IP de front-end (loadBalancerFrontendIpConfigurations)    |    O serviço de vínculo privado está vinculado ao endereço IP de front-end de um Standard Load Balancer. Todo o tráfego destinado ao serviço alcançará o front-end do SLB. Você pode configurar regras SLB para direcionar esse tráfego para os pools de back-end apropriados onde seus aplicativos estão em execução. As configurações de IP de front-end do balanceador de carga são diferentes das configurações de IP NAT.      |
|Configuração de IP de NAT (ipConfigurations)    |    Essa propriedade refere-se à configuração de IP NAT (conversão de endereços de rede) para o serviço de link privado. O IP de NAT pode ser escolhido de qualquer sub-rede em uma rede virtual do provedor de serviços. O serviço de vínculo privado executa NAT-ing no tráfego do link privado. Isso garante que não haja nenhum conflito de IP entre a origem (lado do consumidor) e o espaço de endereço de destino (provedor de serviço). No lado de destino (lado do provedor de serviço), o endereço IP de NAT aparecerá como IP de origem para todos os pacotes recebidos pelo seu serviço e IP de destino para todos os pacotes enviados pelo seu serviço.       |
|Conexões de ponto de extremidade privado (privateEndpointConnections)     |  Essa propriedade lista os pontos de extremidade privados que se conectam ao serviço de vínculo privado. Vários pontos de extremidade privados podem se conectar ao mesmo serviço de vínculo privado e o provedor de serviços pode controlar o estado de pontos de extremidade privados individuais.        |
|Proxy TCP v2 (EnableProxyProtocol)     |  Essa propriedade permite que o provedor de serviços use o proxy TCP v2 para recuperar informações de conexão sobre o consumidor de serviço. O provedor de serviços é responsável por configurar as configurações do destinatário para poder analisar o cabeçalho do protocolo proxy v2.        |
|||


### <a name="details"></a>Detalhes

- O serviço de vínculo privado pode ser acessado de pontos de extremidade privados aprovados em qualquer região pública. O ponto de extremidade privado pode ser alcançado da mesma rede virtual, VNets emparelhada de modo regional, VNets emparelhada globalmente e local usando conexões VPN privadas ou ExpressRoute. 
 
- Ao criar um serviço de vínculo privado, uma interface de rede é criada para o ciclo de vida do recurso. Essa interface não é gerenciável pelo cliente.
 
- O serviço de vínculo privado deve ser implantado na mesma região que a rede virtual e a Standard Load Balancer.  
 
- Um único serviço de vínculo privado pode ser acessado de vários pontos de extremidade privados que pertencem a diferentes VNets, assinaturas e/ou locatários Active Directory. A conexão é estabelecida por meio de um fluxo de trabalho de conexão. 
 
- Vários serviços de vínculo privado podem ser criados no mesmo Standard Load Balancer usando configurações de IP de front-end diferentes. Há limites para o número de serviços de vínculo privado que você pode criar por Standard Load Balancer e por assinatura. Para obter detalhes, confira  [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
 
- O serviço de vínculo privado pode ter mais de uma configuração de IP NAT vinculada a ela. A escolha de mais de uma configuração de IP NAT pode ajudar os provedores de serviços a dimensionar. Hoje, os provedores de serviços podem atribuir até oito endereços IP de NAT por serviço de vínculo privado. Com cada endereço IP de NAT, você pode atribuir mais portas para suas conexões TCP e, portanto, escalar horizontalmente. Depois de adicionar vários endereços IP NAT a um serviço de vínculo privado, você não pode excluir os endereços IP de NAT. Isso é feito para garantir que as conexões ativas não sejam afetadas durante a exclusão dos endereços IP de NAT.


## <a name="alias"></a>Alias

**Alias** é um nome globalmente exclusivo para seu serviço. Ele ajuda a mascarar os dados do cliente para seu serviço e, ao mesmo tempo, cria um nome de fácil compartilhamento para seu serviço. Quando você cria um serviço de vínculo privado, o Azure gera um alias para o serviço que você pode compartilhar com seus clientes. Seus clientes podem usar esse alias para solicitar uma conexão ao seu serviço.

O alias é composto por três partes: *prefixo*. *GUID*. *Sufixo*

- Prefixo é o nome do serviço. Você pode escolher seu próprio prefixo. Depois que "alias" for criado, você não poderá alterá-lo; portanto, selecione o prefixo adequadamente.  
- O GUID será fornecido pela plataforma. Isso ajuda a tornar o nome globalmente exclusivo. 
- O sufixo é acrescentado pelo Azure: *Region*. Azure. privatelinkservice 

Alias completo:  *prefixo*. {GUID}. *Region*. Azure. privatelinkservice  

## <a name="control-service-exposure"></a>Controlar a exposição do serviço

O serviço de vínculo privado fornece opções para controlar a exposição de seu serviço por meio da configuração de "visibilidade". Você pode tornar o serviço particular para consumo de diferentes VNets que você possui (somente permissões do RBAC do Azure), restringir a exposição a um conjunto limitado de assinaturas em que você confia ou torná-lo público para que todas as assinaturas do Azure possam solicitar conexões no serviço de link privado. Suas configurações de visibilidade decidem se um consumidor pode se conectar ao seu serviço ou não. 

## <a name="control-service-access"></a>Acesso ao serviço de controle

Os consumidores que têm exposição (controlada pela configuração de visibilidade) para o serviço de vínculo privado podem criar um ponto de extremidade privado em seu VNets e solicitar uma conexão com o serviço de vínculo privado. A conexão de ponto de extremidade particular será criada em um estado "pendente" no objeto de serviço de link privado. O provedor de serviços é responsável por agir na solicitação de conexão. Você pode aprovar a conexão, rejeitar a conexão ou excluir a conexão. Somente as conexões aprovadas podem enviar tráfego para o serviço de link privado.

A ação de aprovar as conexões pode ser automatizada usando a propriedade aprovação automática no serviço de vínculo privado. A aprovação automática é uma capacidade para os provedores de serviços aprovarem um conjunto de assinaturas para acesso automatizado ao serviço. Os clientes precisarão compartilhar suas assinaturas offline para que os provedores de serviços adicionem à lista de aprovação automática. A aprovação automática é um subconjunto da matriz de visibilidade. A visibilidade controla as configurações de exposição, enquanto a aprovação automática controla as configurações de aprovação do seu serviço. Se um cliente solicitar uma conexão de uma assinatura na lista aprovação automática, a conexão será aprovada automaticamente e a conexão será estabelecida. Os provedores de serviço não precisam mais aprovar manualmente a solicitação. Por outro lado, se um cliente solicitar uma conexão de uma assinatura na matriz de visibilidade e não na matriz de aprovação automática, a solicitação atingirá o provedor de serviços, mas o provedor de serviços precisará aprovar as conexões manualmente.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>Obtendo informações de conexão usando o proxy TCP v2

Ao usar o serviço de vínculo privado, o endereço IP de origem dos pacotes provenientes do ponto de extremidade privado é convertido de endereço de rede (NAT) no lado do provedor de serviço usando o IP de NAT alocado da rede virtual do provedor. Portanto, os aplicativos recebem o endereço IP NAT alocado em vez do endereço IP de origem real dos consumidores de serviço. Se o seu aplicativo precisar de um endereço IP de origem real do lado do consumidor, você poderá habilitar o protocolo de proxy em seu serviço e recuperar as informações do cabeçalho do protocolo de proxy. Além do endereço IP de origem, o cabeçalho do protocolo proxy também carrega a LinkId do ponto de extremidade privado. A combinação de endereço IP de origem e LinkId pode ajudar os provedores de serviços a identificar exclusivamente seus consumidores. Para obter mais informações sobre o protocolo proxy, visite [aqui](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt). 

Essas informações são codificadas usando um vetor TLV (tipo-tamanho-valor) personalizado da seguinte maneira:

Detalhes do TLV personalizado:

|Campo |Comprimento (octetos)  |Descrição  |
|---------|---------|----------|
|Type  |1        |PP2_TYPE_AZURE (0xEE)|
|Comprimento  |2      |Comprimento do valor|
|Valor  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |UINT32 (4 bytes) que representa a LINKID do ponto de extremidade privado. Codificado no formato little endian.|

 > [!NOTE]
 > O provedor de serviços é responsável por verificar se o serviço por trás do balanceador de carga padrão está configurado para analisar o cabeçalho do protocolo de proxy de acordo com a [especificação](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) quando o protocolo de proxy está habilitado no serviço de vínculo privado. A solicitação falhará se a configuração do protocolo de proxy estiver habilitada no serviço de vínculo privado, mas o serviço do provedor de serviços não estiver configurado para analisar o cabeçalho. Da mesma forma, a solicitação falhará se o serviço do provedor de serviços estiver esperando um cabeçalho de protocolo de proxy enquanto a configuração não estiver habilitada no serviço de vínculo privado. Depois que a configuração de protocolo de proxy estiver habilitada, o cabeçalho do protocolo proxy também será incluído em investigações de integridade HTTP/TCP do host para as máquinas virtuais de back-end, mesmo que não haja nenhuma informação do cliente no cabeçalho. 

## <a name="limitations"></a>Limitações

A seguir estão as limitações conhecidas ao usar o serviço de link privado:
- Com suporte apenas em Standard Load Balancer 
- Dá suporte apenas ao tráfego IPv4
- Dá suporte somente ao tráfego TCP e UDP

## <a name="next-steps"></a>Próximas etapas
- [Criar um serviço de vínculo privado usando Azure PowerShell](create-private-link-service-powershell.md)
- [Criar um serviço de vínculo privado usando CLI do Azure](create-private-link-service-cli.md)
