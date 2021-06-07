---
title: Segurança de rede para recursos da grade de eventos do Azure
description: Este artigo descreve como usar marcas de serviço para saída, regras de firewall de IP para entrada e pontos de extremidade privados para entrada com a grade de eventos do Azure.
author: VidyaKukke
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: vkukke
ms.openlocfilehash: 10c9b165041f0a4a1f09511f17bef3629353c3b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94917521"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Segurança de rede para recursos da grade de eventos do Azure
Este artigo descreve como usar os seguintes recursos de segurança com a grade de eventos do Azure: 

- Marcas de serviço para saída
- Regras de firewall IP para entrada
- Pontos de extremidade privados para entrada


## <a name="service-tags"></a>Marcas de serviço
Uma marca de serviço representa um grupo de prefixos de endereço IP de um determinado serviço do Azure. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços, minimizando a complexidade de atualizações frequentes das regras de segurança de rede. Para obter mais informações sobre marcas de serviço, consulte [visão geral das marcas de serviço](../virtual-network/service-tags-overview.md).

Você pode usar marcas de serviço para definir os controles de acesso à rede em [grupos de segurança de rede](../virtual-network/network-security-groups-overview.md#security-rules) ou no [Firewall do Azure](../firewall/service-tags.md). Use marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, **AzureEventGrid**) no campo de *origem* ou *destino* apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente.

| Marca de serviço | Finalidade | É possível usar entrada ou saída? | Pode ser regional? | É possível usar com o Firewall do Azure? |
| --- | -------- |:---:|:---:|:---:|
| AzureEventGrid | Grade de Eventos do Azure. | Ambos | Não | Não |


## <a name="ip-firewall"></a>Firewall de IP 
A grade de eventos do Azure dá suporte a controles de acesso baseados em IP para publicação em tópicos e domínios. Com controles baseados em IP, você pode limitar os editores a um tópico ou domínio a apenas um conjunto de computadores e serviços de nuvem aprovados. Esse recurso complementa os [mecanismos de autenticação](security-authentication.md) com suporte na grade de eventos.

Por padrão, o tópico e o domínio podem ser acessados pela Internet, desde que a solicitação venha com autenticação e autorização válidas. Com o firewall de IP, você pode restringir ainda mais a um conjunto de endereços IP ou intervalos de endereços IP na notação [CIDR (roteamento Inter-Domain sem classe)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Os Publicadores provenientes de qualquer outro endereço IP serão rejeitados e receberão uma resposta 403 (proibido).

Para obter instruções detalhadas sobre como configurar o firewall de IP para tópicos e domínios, consulte [Configurar o firewall de IP](configure-firewall.md).

## <a name="private-endpoints"></a>Pontos de extremidade privados
Você pode usar [pontos de extremidade privados](../private-link/private-endpoint-overview.md) para permitir a entrada de eventos diretamente de sua rede virtual para seus tópicos e domínios com segurança por meio de um [link privado](../private-link/private-link-overview.md) sem passar pela Internet pública. Um ponto de extremidade privado é uma interface de rede especial para um serviço do Azure em sua VNet. Quando você cria um ponto de extremidade privado para seu tópico ou domínio, ele fornece conectividade segura entre clientes em sua VNet e seu recurso de grade de eventos. O ponto de extremidade privado recebe um endereço IP do intervalo de endereços IP de sua VNet. A conexão entre o ponto de extremidade privado e o serviço de grade de eventos usa um link privado seguro.

![Diagrama de arquitetura](./media/network-security/architecture-diagram.png)

O uso de pontos de extremidade privados para o recurso da grade de eventos permite que você:

- Proteja o acesso ao seu tópico ou domínio de uma VNet pela rede de backbone da Microsoft em oposição à Internet pública.
- Conecte-se com segurança de redes locais que se conectam à VNet usando VPN ou rotas expressas com emparelhamento privado.

Quando você cria um ponto de extremidade privado para um tópico ou domínio em sua VNet, uma solicitação de consentimento é enviada para aprovação para o proprietário do recurso. Se o usuário que solicita a criação do ponto de extremidade privado também for um proprietário do recurso, essa solicitação de consentimento será aprovada automaticamente. Caso contrário, a conexão estará em estado **pendente** até ser aprovada. Os aplicativos na VNet podem se conectar ao serviço de grade de eventos por meio do ponto de extremidade privado diretamente, usando as mesmas cadeias de conexão e mecanismos de autorização que eles usariam de outra forma. Os proprietários de recursos podem gerenciar solicitações de consentimento e os pontos de extremidade privados, por meio da guia **pontos de extremidade particulares** para o recurso no portal do Azure.

### <a name="connect-to-private-endpoints"></a>Conectar-se a pontos de extremidade privados
Os editores em uma VNet usando o ponto de extremidade privado devem usar a mesma cadeia de conexão para o tópico ou domínio como clientes que se conectam ao ponto de extremidade público. A resolução DNS roteia automaticamente as conexões da VNet para o tópico ou domínio por meio de um link privado. A grade de eventos cria uma [zona DNS privada](../dns/private-dns-overview.md) anexada à VNet com a atualização necessária para os pontos de extremidade privados, por padrão. No entanto, se você estiver usando seu próprio servidor DNS, talvez seja necessário fazer alterações adicionais na configuração do DNS.

### <a name="dns-changes-for-private-endpoints"></a>Alterações de DNS para pontos de extremidade particulares
Quando você cria um ponto de extremidade privado, o registro DNS CNAME do recurso é atualizado para um alias em um subdomínio com o prefixo `privatelink` . Por padrão, é criada uma zona DNS privada que corresponde ao subdomínio do link privado. 

Quando você resolve o tópico ou a URL do ponto de extremidade do domínio de fora da VNet com o ponto de extremidade privado, ele é resolvido para o ponto de extremidade público do serviço. Os registros de recurso DNS para ' Topica ', quando resolvidos de **fora da VNet** que hospeda o ponto de extremidade privado, serão:

| Nome                                          | Type      | Valor                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<Azure traffic manager profile\>

Você pode negar ou controlar o acesso de um cliente fora da VNet por meio do ponto de extremidade público usando o [Firewall de IP](#ip-firewall). 

Quando resolvido da VNet que hospeda o ponto de extremidade privado, o tópico ou a URL do ponto de extremidade do domínio é resolvido para o endereço IP do ponto de extremidade privado. Os registros de recurso DNS para o tópico ' Topica ', quando resolvidos de **dentro da VNet** que hospeda o ponto de extremidade privado, serão:

| Nome                                          | Type      | Valor                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

Essa abordagem permite o acesso ao tópico ou ao domínio usando a mesma cadeia de conexão para clientes na VNet que hospeda os pontos de extremidade privados e clientes fora da VNet.

Se você estiver usando um servidor DNS personalizado em sua rede, os clientes poderão resolver o FQDN do tópico ou do ponto de extremidade do domínio para o endereço IP do ponto de extremidade privado. Configure o servidor DNS para delegar seu subdomínio de link privado para a zona DNS privada para a VNet ou configure os registros a para `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` com o endereço IP do ponto de extremidade privado.

O nome de zona DNS recomendado é `privatelink.eventgrid.azure.net` .

### <a name="private-endpoints-and-publishing"></a>Pontos de extremidade privados e publicação

A tabela a seguir descreve os vários Estados da conexão de ponto de extremidade privada e os efeitos na publicação:

| Estado da Conexão   |  Publicação com êxito (Sim/não) |
| ------------------ | -------------------------------|
| Aprovado           | Sim                            |
| Rejeitado           | Não                             |
| Pendente            | Não                             |
| Desconectado       | Não                             |

Para que a publicação seja bem-sucedida, o estado de conexão do ponto de extremidade privado deve ser **aprovado**. Se uma conexão for rejeitada, ela não poderá ser aprovada usando o portal do Azure. A única possibilidade é excluir a conexão e criar uma nova, em vez disso.

## <a name="pricing-and-quotas"></a>Preços e cotas
**Pontos de extremidade privados** estão disponíveis nas camadas básica e Premium da grade de eventos. A grade de eventos permite que até 64 conexões de ponto de extremidade privado sejam criadas por tópico ou domínio. 

O recurso de **Firewall de IP** está disponível nas camadas básica e Premium da grade de eventos. Permitimos que até 16 regras de firewall IP sejam criadas por tópico ou domínio.

## <a name="next-steps"></a>Próximas etapas
Você pode configurar o firewall IP para o recurso de grade de eventos para restringir o acesso pela Internet pública de apenas um conjunto selecionado de endereços IP ou intervalos de endereços IP. Para obter as instruções passo a passo, consulte [Configurar o firewall de IP](configure-firewall.md).

Você pode configurar pontos de extremidade privados para restringir o acesso somente de redes virtuais selecionadas. Para obter as instruções passo a passo, consulte [Configurar pontos de extremidade privados](configure-private-endpoints.md).

Para solucionar problemas de conectividade de rede, consulte [solucionar](troubleshoot-network-connectivity.md) problemas de conectividade de rede