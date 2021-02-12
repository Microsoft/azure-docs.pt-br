---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 3f9dd35959980eef4e1bec550bf7e9f583cf30d2
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98225649"
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

Por padrão, seu aplicativo roteia apenas o tráfego RFC1918 para sua VNet. Se você quiser rotear todo o tráfego de saída para sua VNet, aplique a configuração do aplicativo WEBSITE_VNET_ROUTE_ALL ao seu aplicativo. Para definir a configuração do aplicativo:

1. Vá para a interface do usuário de **configuração** no portal do seu aplicativo. Selecione **Nova configuração de aplicativo**.
1. Insira **WEBSITE_VNET_ROUTE_ALL** na caixa **nome** e digite **1** na caixa **valor** .

   ![Fornecer configuração de aplicativo][4]

1. Selecione **OK**.
1. Clique em **Salvar**.

> [!NOTE]
> Se você rotear todo o tráfego de saída para sua VNet, ele estará sujeito aos NSGs e UDRs que são aplicados à sua sub-rede de integração. Quando você roteia todo o tráfego de saída para sua VNet, seus endereços de saída ainda são os endereços de saída listados nas propriedades do aplicativo, a menos que você forneça rotas para enviar o tráfego em outro lugar.

Há algumas limitações no uso da integração VNet com o VNets na mesma região:

* Você não pode acessar recursos entre conexões de emparelhamento globais.
* O recurso está disponível em todas as unidades de escala do serviço de aplicativo nas Premium V2 e Premium v3. Ele também está disponível no padrão, mas somente em unidades de escala do serviço de aplicativo mais recentes. Se você estiver em uma unidade de escala mais antiga, só poderá usar o recurso de um plano do serviço de aplicativo Premium v2. Se você quiser ter certeza de poder usar o recurso em um plano do serviço de aplicativo padrão, crie seu aplicativo em um plano do serviço de aplicativo Premium v3. Esses planos têm suporte apenas em nossas unidades de escala mais recentes. Se desejar depois disso, você poderá reduzir verticalmente.  
* A sub-rede de integração pode ser usada por apenas um plano do serviço de aplicativo.
* O recurso não pode ser usado por aplicativos de plano isolado que estão em um Ambiente do Serviço de Aplicativo.
* O recurso requer uma sub-rede não utilizada que seja a/28 ou maior em uma VNet Azure Resource Manager.
* O aplicativo e a VNet devem estar na mesma região.
* Não é possível excluir uma VNet com um aplicativo integrado. Remova a integração antes de excluir a VNet.
* Você pode ter apenas uma integração VNet regional por plano do serviço de aplicativo. Vários aplicativos no mesmo plano do serviço de aplicativo podem usar a mesma VNet.
* Você não pode alterar a assinatura de um aplicativo ou um plano enquanto há um aplicativo que está usando a integração VNet regional.
* Seu aplicativo não pode resolver endereços em Zonas Privadas do DNS do Azure sem alterações de configuração

A integração VNet depende do uso de uma sub-rede dedicada.  Quando você provisiona uma sub-rede, a sub-rede do Azure perde 5 IPs para desde o início. Um endereço é usado da sub-rede de integração para cada instância do plano. Se você dimensionar seu aplicativo para quatro instâncias, quatro endereços serão usados. O débito de 5 endereços do tamanho da sub-rede significa que o máximo de endereços disponíveis por bloco CIDR são:

- /28 tem 11 endereços
- /27 tem endereço 27
- 26 tem 59 endereços

Se você escalar ou reduzir verticalmente, precisará dobrar o seu endereço por um curto período de tempo. Os limites em tamanho significa que as instâncias com suporte reais disponíveis por tamanho de sub-rede serão, se sua sub-rede for uma:

- /28, sua escala horizontal máxima é de 5 instâncias
- /27, sua escala horizontal máxima é de 13 instâncias
- /26, sua escala horizontal máxima é 29 instâncias

Os limites indicados na escala horizontal máxima pressupõem que você precisará escalar ou reduzir verticalmente em qualquer tamanho ou SKU em algum momento. 

Como o tamanho da sub-rede não pode ser alterado após a atribuição, use uma sub-rede que seja grande o suficiente para acomodar qualquer escala que seu aplicativo possa alcançar. Para evitar problemas com a capacidade de sub-rede, um/26 com 64 endereços é o tamanho recomendado.  

Se você quiser que seus aplicativos em outro plano alcancem uma VNet que já está conectada por aplicativos em outro plano, selecione uma sub-rede diferente daquela que está sendo usada pela integração VNet pré-existente.

O recurso tem suporte total para aplicativos Windows e Linux, incluindo [contêineres personalizados](../articles/app-service/quickstart-custom-container.md). Todos os comportamentos agem da mesma entre aplicativos do Windows e aplicativos do Linux.

### <a name="service-endpoints"></a>Pontos de extremidade de serviço

A integração VNet regional permite que você use pontos de extremidade de serviço. Para usar pontos de extremidade de serviço com seu aplicativo, use a integração VNet regional para se conectar a uma VNet selecionada e, em seguida, configure os pontos de extremidade de serviço com o serviço de destino na sub-rede usada para a integração. Se você quisesse acessar um serviço em pontos de extremidade de serviço:

1. configurar a integração VNet regional com seu aplicativo Web
1. Vá para o serviço de destino e configure os pontos de extremidade de serviço em relação à sub-rede usada para integração

### <a name="network-security-groups"></a>Grupos de Segurança de Rede

Você pode usar grupos de segurança de rede para bloquear o tráfego de entrada e saída para recursos em uma VNet. Um aplicativo que usa a integração VNet regional pode usar um [grupo de segurança de rede][VNETnsg] para bloquear o tráfego de saída para recursos em sua VNet ou na Internet. Para bloquear o tráfego para endereços públicos, você deve ter a configuração de aplicativo WEBSITE_VNET_ROUTE_ALL definida como 1. As regras de entrada em um NSG não se aplicam ao seu aplicativo porque a integração VNet afeta apenas o tráfego de saída de seu aplicativo.

Para controlar o tráfego de entrada para seu aplicativo, use o recurso de restrições de acesso. Um NSG que é aplicado à sua sub-rede de integração está em vigor, independentemente de quaisquer rotas aplicadas à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL for definido como 1 e você não tiver nenhuma rota que afete o tráfego de endereço público em sua sub-rede de integração, todo o tráfego de saída ainda estará sujeito a NSGs atribuído à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL não estiver definido, NSGs só será aplicado ao tráfego RFC1918.

### <a name="routes"></a>Rotas

Você pode usar tabelas de rotas para rotear o tráfego de saída do seu aplicativo para onde quiser. Por padrão, as tabelas de rotas afetam apenas o tráfego de destino do RFC1918. Se você definir WEBSITE_VNET_ROUTE_ALL como 1, todas as suas chamadas de saída serão afetadas. As rotas definidas em sua sub-rede de integração não afetarão as respostas a solicitações de aplicativo de entrada. Os destinos comuns podem incluir dispositivos de firewall ou gateways.

Se você quiser rotear todo o tráfego de saída local, poderá usar uma tabela de rotas para enviar todo o tráfego de saída para o gateway de ExpressRoute. Se você rotear o tráfego para um gateway, certifique-se de definir rotas na rede externa para enviar respostas.

As rotas Border Gateway Protocol (BGP) também afetam o tráfego do aplicativo. Se você tiver rotas BGP de algo como um gateway de ExpressRoute, o tráfego de saída do aplicativo será afetado. Por padrão, as rotas BGP afetam apenas o tráfego de destino RFC1918. Se WEBSITE_VNET_ROUTE_ALL for definido como 1, todo o tráfego de saída poderá ser afetado por suas rotas BGP.

### <a name="azure-dns-private-zones"></a>Zonas Privadas do DNS do Azure 

Depois que o aplicativo se integra à sua VNet, ele usa o mesmo servidor DNS com o qual sua VNet está configurada. Por padrão, seu aplicativo não funcionará com Zonas Privadas do DNS do Azure. Para trabalhar com Zonas Privadas do DNS do Azure, você precisa adicionar as seguintes configurações de aplicativo:


1. WEBSITE_DNS_SERVER com o valor 168.63.129.16
1. WEBSITE_VNET_ROUTE_ALL com valor 1


Essas configurações enviarão todas as suas chamadas de saída do seu aplicativo para sua VNet, além de habilitar seu aplicativo para usar zonas privadas do DNS do Azure.   Essas configurações enviarão todas as chamadas de saída do seu aplicativo para sua VNet. Além disso, ele permitirá que o aplicativo use o DNS do Azure consultando a zona de DNS privado no nível de trabalho. Essa funcionalidade deve ser usada quando um aplicativo em execução estiver acessando uma zona de DNS privado.

> [!NOTE]
>A tentativa de adicionar um domínio personalizado a um aplicativo Web usando DNS privado zona não é possível com o Integração VNET. A validação de domínio personalizada é feita no nível do controlador, não no nível de trabalho, o que impede que os registros DNS sejam vistos. Para usar um domínio personalizado de uma zona de DNS privado, a validação precisaria ser ignorada usando um gateway de aplicativo ou ILB Ambiente do Serviço de Aplicativo.

### <a name="private-endpoints"></a>Pontos de extremidade privados

Se desejar fazer chamadas para pontos de extremidade [privados][privateendpoints], você precisará garantir que as pesquisas de DNS serão resolvidas para o ponto de extremidades privado. Para garantir que as pesquisas de DNS do seu aplicativo apontem para seus pontos de extremidade privados, você pode:

* integre com Zonas Privadas do DNS do Azure. Se sua VNet não tiver um servidor DNS personalizado, isso será automático
* Gerencie o ponto de extremidade privado no servidor DNS usado pelo seu aplicativo. Para fazer isso, você precisa saber o endereço do ponto de extremidade privado e, em seguida, apontar o ponto de extremidade que está tentando acessar para esse endereço com um registro A.
* configurar seu próprio servidor DNS para encaminhar para as zonas privadas do DNS do Azure

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
