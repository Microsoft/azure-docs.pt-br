---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 821746856cb37781c8f6a2e58659ce7db43e1479
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609372"
---
O uso da integração de VNet regional permite que seu aplicativo acesse:

* Recursos em uma VNet na mesma região que seu aplicativo.
* Recursos no VNets emparelhados com a VNet à qual seu aplicativo está integrado.
* Serviços protegidos do ponto de extremidade de serviço.
* Recursos entre as conexões do Azure ExpressRoute.
* Recursos na VNet com a qual você está integrado.
* Recursos entre conexões emparelhadas, que incluem conexões do Azure ExpressRoute.
* Pontos de extremidade privados 

Ao usar a integração VNet com o VNets na mesma região, você pode usar os seguintes recursos de rede do Azure:

* **NSGs (grupos de segurança de rede)**: você pode bloquear o tráfego de saída com um NSG que é colocado em sua sub-rede de integração. As regras de entrada não se aplicam porque você não pode usar a integração VNet para fornecer acesso de entrada ao seu aplicativo.
* **UDRs (tabelas de rotas)**: você pode inserir uma tabela de rotas na sub-rede de integração para enviar o tráfego de saída onde desejar.

Por padrão, seu aplicativo roteia apenas o tráfego RFC1918 para sua VNet. Se você quiser rotear todo o tráfego de saída para sua VNet, use as seguintes etapas para adicionar a `WEBSITE_VNET_ROUTE_ALL` configuração em seu aplicativo: 

1. Vá para a interface do usuário de **configuração** no portal do seu aplicativo. Selecione **Nova configuração de aplicativo**.
1. Digite `WEBSITE_VNET_ROUTE_ALL` na caixa **nome** e digite `1` na caixa **valor** .

   ![Fornecer configuração de aplicativo][4]

1. Selecione **OK**.
1. Selecione **Salvar**.

> [!NOTE]
> Quando você roteia todo o tráfego de saída para sua VNet, ele está sujeito ao NSGs e UDRs que são aplicados à sua sub-rede de integração. Quando `WEBSITE_VNET_ROUTE_ALL` é definido como `1` , o tráfego de saída ainda é enviado dos endereços listados nas propriedades do aplicativo, a menos que você forneça rotas que direcionam o tráfego em outro lugar.
> 
> A integração VNet regional não é capaz de usar a porta 25.

Há algumas limitações no uso da integração VNet com o VNets na mesma região:

* Você não pode acessar recursos entre conexões de emparelhamento globais.
* O recurso está disponível em todas as unidades de escala do serviço de aplicativo nas Premium V2 e Premium v3. Ele também está disponível no padrão, mas somente em unidades de escala do serviço de aplicativo mais recentes. Se você estiver em uma unidade de escala mais antiga, só poderá usar o recurso de um plano do serviço de aplicativo Premium v2. Se você quiser ter certeza de que pode usar o recurso em um plano do serviço de aplicativo padrão, crie seu aplicativo em um plano do serviço de aplicativo Premium v3. Esses planos têm suporte apenas em nossas unidades de escala mais recentes. Se desejar depois disso, você poderá reduzir verticalmente.  
* A sub-rede de integração pode ser usada por apenas um plano do serviço de aplicativo.
* O recurso não pode ser usado por aplicativos de plano isolado que estão em um Ambiente do Serviço de Aplicativo.
* O recurso requer uma sub-rede não utilizada que seja a/28 ou maior em uma VNet Azure Resource Manager.
* O aplicativo e a VNet devem estar na mesma região.
* Não é possível excluir uma VNet com um aplicativo integrado. Remova a integração antes de excluir a VNet.
* Você pode ter apenas uma integração VNet regional por plano do serviço de aplicativo. Vários aplicativos no mesmo plano do serviço de aplicativo podem usar a mesma VNet.
* Você não pode alterar a assinatura de um aplicativo ou um plano enquanto há um aplicativo que está usando a integração VNet regional.
* Seu aplicativo não pode resolver endereços em Zonas Privadas do DNS do Azure sem alterações de configuração.

A integração VNet depende de uma sub-rede dedicada. Quando você provisiona uma sub-rede, a sub-rede do Azure perde cinco IPs desde o início. Um endereço é usado da sub-rede de integração para cada instância do plano. Quando você dimensiona seu aplicativo para quatro instâncias, são usados quatro endereços. 

Quando você aumenta ou reduz verticalmente, o espaço de endereço necessário é dobrado por um curto período de tempo. Isso afeta as instâncias reais disponíveis com suporte para um determinado tamanho de sub-rede. A tabela a seguir mostra o máximo de endereços disponíveis por bloco CIDR e o impacto que isso tem na escala horizontal:

| Tamanho do bloco CIDR | Máximo de endereços disponíveis | Escala horizontal máxima (instâncias)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| / 28             | 11                      | 5                               |
| / 27             | 27                      | 13                              |
| / 26             | 59                      | 29                              |

<sup>*</sup>Pressupõe que você precisará escalar ou reduzir verticalmente em qualquer tamanho ou SKU em algum momento. 

Como o tamanho da sub-rede não pode ser alterado após a atribuição, use uma sub-rede que seja grande o suficiente para acomodar qualquer escala que seu aplicativo possa alcançar. Para evitar problemas com a capacidade de sub-rede, você deve usar um/26 com endereços 64.  

Quando você quiser que seus aplicativos em outro plano alcancem uma VNet que já esteja conectada por aplicativos em outro plano, selecione uma sub-rede diferente daquela que está sendo usada pela integração VNet pré-existente.

O recurso tem suporte total para aplicativos Windows e Linux, incluindo [contêineres personalizados](../articles/app-service/quickstart-custom-container.md). Todos os comportamentos agem da mesma entre aplicativos do Windows e aplicativos do Linux.

### <a name="service-endpoints"></a>Pontos de extremidade de serviço

A integração VNet regional permite que você alcance os serviços do Azure que são protegidos com pontos de extremidade de serviço. Para acessar um serviço protegido de ponto de extremidade de serviço, você deve fazer o seguinte:

1. Configure a integração VNet regional com seu aplicativo Web para se conectar a uma sub-rede específica para integração.
1. Vá para o serviço de destino e configure os pontos de extremidade de serviço em relação à sub-rede de integração.

### <a name="network-security-groups"></a>Grupos de segurança de rede

Você pode usar grupos de segurança de rede para bloquear o tráfego de entrada e saída para recursos em uma VNet. Um aplicativo que usa a integração VNet regional pode usar um [grupo de segurança de rede][VNETnsg] para bloquear o tráfego de saída para recursos em sua VNet ou na Internet. Para bloquear o tráfego para endereços públicos, você deve ter a configuração de aplicativo `WEBSITE_VNET_ROUTE_ALL` definida como `1` . As regras de entrada em um NSG não se aplicam ao seu aplicativo porque a integração VNet afeta apenas o tráfego de saída de seu aplicativo.

Para controlar o tráfego de entrada para seu aplicativo, use o recurso de restrições de acesso. Um NSG que é aplicado à sua sub-rede de integração está em vigor, independentemente de quaisquer rotas aplicadas à sua sub-rede de integração. Se `WEBSITE_VNET_ROUTE_ALL` é definido como `1` e você não tem nenhuma rota que afete o tráfego de endereço público em sua sub-rede de integração, todo o tráfego de saída ainda está sujeito a NSGs atribuído à sua sub-rede de integração. Quando `WEBSITE_VNET_ROUTE_ALL` não está definido, NSGs são aplicados somente ao tráfego RFC1918.

### <a name="routes"></a>Rotas

Você pode usar tabelas de rotas para rotear o tráfego de saída do seu aplicativo para onde quiser. Por padrão, as tabelas de rotas afetam apenas o tráfego de destino do RFC1918. Quando você define `WEBSITE_VNET_ROUTE_ALL` como `1` , todas as suas chamadas de saída são afetadas. As rotas definidas em sua sub-rede de integração não afetarão as respostas a solicitações de aplicativo de entrada. Os destinos comuns podem incluir dispositivos de firewall ou gateways.

Se você quiser rotear todo o tráfego de saída local, poderá usar uma tabela de rotas para enviar todo o tráfego de saída para o gateway de ExpressRoute. Se você rotear o tráfego para um gateway, certifique-se de definir rotas na rede externa para enviar respostas.

As rotas Border Gateway Protocol (BGP) também afetam o tráfego do aplicativo. Se você tiver rotas BGP de algo como um gateway de ExpressRoute, o tráfego de saída do aplicativo será afetado. Por padrão, as rotas BGP afetam apenas o tráfego de destino RFC1918. Quando `WEBSITE_VNET_ROUTE_ALL` é definido como `1` , todo o tráfego de saída pode ser afetado por suas rotas BGP.

### <a name="azure-dns-private-zones"></a>Zonas privadas do DNS do Azure 

Depois que o aplicativo se integra à sua VNet, ele usa o mesmo servidor DNS com o qual sua VNet está configurada. Por padrão, seu aplicativo não funcionará com as zonas privadas do DNS do Azure. Para trabalhar com as zonas privadas do DNS do Azure, você precisa adicionar as seguintes configurações de aplicativo:

1. `WEBSITE_DNS_SERVER` com valor `168.63.129.16`
1. `WEBSITE_VNET_ROUTE_ALL` com valor `1`

Essas configurações enviam todas as suas chamadas de saída do seu aplicativo para sua VNet e permitem que seu aplicativo acesse uma zona privada de DNS do Azure. Com essas configurações, seu aplicativo pode usar o DNS do Azure consultando a zona privada DNS no nível de trabalho.  

### <a name="private-endpoints"></a>Pontos de extremidade privados

Se você quiser fazer chamadas para pontos de extremidade [privados][privateendpoints], deverá certificar-se de que suas pesquisas de DNS sejam resolvidas para o ponto de extremidades privado. Você pode impor esse comportamento de uma das seguintes maneiras: 

* Integre com as zonas privadas do DNS do Azure. Quando sua VNet não tem um servidor DNS personalizado, isso é feito automaticamente.
* Gerencie o ponto de extremidade privado no servidor DNS usado pelo seu aplicativo. Para fazer isso, você deve saber o endereço do ponto de extremidade privado e apontar o ponto de extremidade que está tentando acessar para esse endereço usando um registro A.
* Configure seu próprio servidor DNS para encaminhar para as zonas privadas do DNS do Azure.

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
