---
title: Integrar o aplicativo com a rede Virtual do Azure - Serviço de Aplicativo do Azure
description: Mostra como conectar um aplicativo do Azure no Serviço de Aplicativo do Azure a uma rede virtual do Azure nova ou existente
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 20ef71f98817a57f884e9c5a3cef4ceeaebe74eb
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498431"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar seu aplicativo Web a uma Rede Virtual do Azure
Este documento descreve o recurso de integração de rede virtual do serviço Azure App e como configurá-lo com aplicativos no [serviço Azure app](https://go.microsoft.com/fwlink/?LinkId=529714). [Redes virtuais do Azure][VNETOverview] (VNets) permitem que você coloque muitos dos seus recursos do Azure em uma rede roteável que não é da Internet.  

O serviço de Azure App tem duas variações. 

1. Os sistemas multilocatário que dão suporte a toda a gama de planos de preço, exceto Isolado
2. O Ambiente do Serviço de Aplicativo (ASE), que é implantado em sua VNet e dá suporte a aplicativos de plano de preços isolados

Este documento percorre os dois recursos de integração VNet, que é para uso no serviço de aplicativo multilocatário. Se seu aplicativo estiver em [ambiente do serviço de aplicativo][ASEintro], ele já estará em uma VNet e não exigirá o uso do recurso de integração VNet para alcançar recursos na mesma VNet. Para obter detalhes sobre todos os recursos de rede do serviço de aplicativo, leia [recursos de rede do serviço de aplicativo](networking-features.md)

Há dois formulários para o recurso de integração VNet

1. Uma versão habilita a integração com o VNets na mesma região. Essa forma do recurso requer uma sub-rede em uma VNet na mesma região. Este recurso ainda está em visualização, mas tem suporte para cargas de trabalho de produção do aplicativo do Windows com algumas limitações indicadas abaixo.
2. A outra versão permite a integração com o VNets em outras regiões ou com o VNets clássico. Esta versão do recurso requer a implantação de um gateway de rede virtual em sua VNet. Esse é o recurso baseado em VPN ponto a site e só é suportado com aplicativos do Windows.

Um aplicativo pode usar apenas um formulário do recurso de integração VNet de cada vez. Em seguida, a pergunta é qual recurso você deve usar. Você pode usar o para muitas coisas. No entanto, os diferenciadores claros são:

| Problema  | Solução | 
|----------|----------|
| Deseja alcançar um endereço RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) na mesma região | Integração de VNet regional |
| Deseja acessar recursos em uma VNet clássica ou em uma VNet em outra região | Integração VNet necessária do gateway |
| Deseja alcançar os pontos de extremidade RFC 1918 no ExpressRoute | Integração de VNet regional |
| Deseja acessar recursos entre pontos de extremidade de serviço | Integração de VNet regional |

Nenhum recurso permitirá que você alcance endereços não RFC 1918 no ExpressRoute. Para fazer isso, você precisa usar um ASE por enquanto.

O uso da integração VNet regional não conecta sua VNet ao local ou configura pontos de extremidade de serviço. Isso é uma configuração de rede separada. A integração VNet regional simplesmente permite que seu aplicativo faça chamadas entre esses tipos de conexão.

Independentemente da versão usada, a integração VNet dá ao seu aplicativo Web acesso aos recursos em sua rede virtual, mas não concede acesso privado de entrada ao seu aplicativo Web a partir da rede virtual. Acesso ao site privado significa tornar seu aplicativo acessível somente de uma rede privada, como de dentro de uma rede virtual do Azure. A integração VNet é apenas para fazer chamadas de saída de seu aplicativo para sua VNet. 

O recurso de integração VNet:

* requer um plano de preços Standard, Premium ou PremiumV2 
* dá suporte a TCP e UDP
* funciona com aplicativos do serviço de aplicativo e aplicativos de funções

Há algumas coisas a que a Integração VNet não dá suporte, incluindo:

* montagem de unidade
* integração ao AD 
* NetBios

## <a name="regional-vnet-integration"></a>Integração de VNet regional 

Quando a integração VNet é usada com VNets na mesma região que seu aplicativo, ele requer o uso de uma sub-rede delegada com pelo menos 32 endereços. A sub-rede não pode ser usada para nada mais. Chamadas de saída feitas de seu aplicativo serão feitas a partir dos endereços na sub-rede delegada. Quando você usa essa versão da integração VNet, as chamadas são feitas de endereços em sua VNet. O uso de endereços em sua VNet permite que seu aplicativo:

* Fazer chamadas para serviços protegidos de ponto de extremidade de serviço
* Acessar recursos entre conexões do ExpressRoute
* Acesse recursos na VNet à qual você está conectado
* Acessar recursos entre conexões emparelhadas, incluindo conexões de ExpressRoute

Esse recurso está em versão prévia, mas há suporte para cargas de trabalho de produção do aplicativo do Windows com as seguintes limitações:

* Você só pode alcançar endereços que estejam no intervalo RFC 1918. Esses são endereços nos blocos de endereços 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.
* Você não pode acessar recursos entre conexões de emparelhamento globais
* Você não pode definir rotas no tráfego proveniente de seu aplicativo para sua VNet
* O recurso só está disponível em unidades de escala do serviço de aplicativo mais recentes que dão suporte a planos do serviço de aplicativo do PremiumV2.
* A sub-rede de integração só pode ser usada por um plano do serviço de aplicativo
* O recurso não pode ser usado por aplicativos de plano isolado que estão em um Ambiente do Serviço de Aplicativo
* O recurso requer uma sub-rede não usada que seja de/27 com 32 endereços ou maior em sua VNet do Resource Manager
* O aplicativo e a VNet devem estar na mesma região
* Você não pode excluir uma VNet com um aplicativo integrado. Você deve remover a integração primeiro 
* Você pode ter apenas uma integração VNet regional por plano do serviço de aplicativo. Vários aplicativos no mesmo plano do serviço de aplicativo podem usar a mesma VNet. 

Um endereço é usado para cada instância de plano do serviço de aplicativo. Se você dimensionou seu aplicativo para 5 instâncias, esses são os cinco endereços usados. Como o tamanho da sub-rede não pode ser alterado após a atribuição, você deve usar uma sub-rede que seja grande o suficiente para acomodar qualquer escala que seu aplicativo possa atingir. A/27 com 32 endereços é o tamanho recomendado, pois isso acomodaria um plano do serviço de aplicativo Premium que é dimensionado para 20 instâncias.

Se você quiser que seus aplicativos em outro plano do serviço de aplicativo alcancem uma VNet que está conectada ao já por aplicativos em outro plano do serviço de aplicativo, será necessário selecionar uma sub-rede diferente daquela que está sendo usada pela integração VNet pré-existente.  

O recurso também está em versão prévia para Linux. Para usar o recurso de integração VNet com uma VNet do Resource Manager na mesma região:

1. Vá para a interface do usuário de rede no portal. Se seu aplicativo for capaz de usar o novo recurso, você verá uma opção para adicionar VNet (versão prévia).  

   ![Selecionar integração VNet][6]

1. Selecione **Adicionar uma VNet (versão prévia)** .  

1. Selecione a VNet do Resource Manager que você deseja integrar e, em seguida, crie uma nova sub-rede ou escolha uma sub-rede vazia já existente. A integração leva menos de um minuto para ser concluída. Durante a integração, o aplicativo é reiniciado.  Quando a integração for concluída, você verá detalhes sobre a VNet que integrou, com uma faixa na parte superior que informa que o recurso está em versão prévia.

   ![Selecionar a VNet e a sub-rede][7]

Depois que seu aplicativo estiver integrado à sua VNet, ele usará o mesmo servidor DNS com o qual sua VNet está configurada. 

Para desconectar o aplicativo da VNet, selecione **Desconectar**. Isso reiniciará o aplicativo Web. 


#### <a name="web-app-for-containers"></a>Aplicativo Web para Contêineres

Se você usar o serviço de aplicativo no Linux com as imagens internas, o recurso de integração VNet regional funcionará sem alterações adicionais. Se você usar Aplicativo Web para Contêineres, precisará modificar a imagem do Docker para usar a integração VNet. Na imagem do Docker, use a variável de ambiente PORT como a porta de escuta do servidor Web principal, em vez de usar um número de porta codificado. A variável de ambiente PORT é definida automaticamente pela plataforma do serviço de aplicativo no momento da inicialização do contêiner.

### <a name="service-endpoints"></a>Pontos de Extremidade de Serviço

O novo recurso de integração de VNet permite que você use pontos de extremidade de serviço.  Para usar pontos de extremidade de serviço com seu aplicativo, use a nova Integração VNet para se conectar a uma VNet selecionada e, em seguida, configure pontos de extremidade de serviço na sub-rede que você usou para a integração. 


### <a name="how-vnet-integration-works"></a>Como a Integração VNet funciona

Os aplicativos no serviço de aplicativo são hospedados em funções de trabalho. Os planos de preços básicos e mais altos são planos de hospedagem dedicados em que não há nenhuma outra carga de trabalho de clientes em execução nos mesmos trabalhadores. A integração VNet funciona por meio da montagem de interfaces virtuais com endereços na sub-rede delegada. Como o endereço de está em sua VNet, ele tem acesso à maioria das coisas de ou por meio de sua VNet, assim como uma VM em sua VNet. A implementação de rede é diferente de executar uma VM em sua VNet e é por isso que alguns recursos de rede ainda não estão disponíveis ao usar esse recurso.

![Integração da VNet](media/web-sites-integrate-with-vnet/vnet-integration.png)

Quando a integração VNet estiver habilitada, seu aplicativo ainda fará chamadas de saída para a Internet por meio dos mesmos canais que o normal. Os endereços de saída listados no portal de propriedades do aplicativo ainda são os endereços usados pelo seu aplicativo. As alterações para seu aplicativo são chamadas para serviços protegidos de ponto de extremidade de serviço ou endereços RFC 1918 vão para sua VNet. 

O recurso só dá suporte a uma interface virtual por trabalho.  Uma interface virtual por trabalho significa uma integração VNet regional por plano do serviço de aplicativo. Todos os aplicativos no mesmo plano do serviço de aplicativo podem usar a mesma integração de VNet, mas se você precisar de um aplicativo para se conectar a uma VNet adicional, será necessário criar outro plano do serviço de aplicativo. A interface virtual usada não é um recurso ao qual os clientes têm acesso direto.

Devido à natureza de como essa tecnologia Opera, o tráfego usado com a integração VNet não aparece no observador de rede ou nos logs de fluxo do NSG.  

## <a name="gateway-required-vnet-integration"></a>Integração VNet necessária do gateway 

O gateway exigiu o recurso de integração VNet:

* Pode ser usado para se conectar ao VNets em qualquer região, seja ele o Resource Manager ou o VNets clássico
* Permite que um aplicativo se conecte a apenas uma VNet por vez
* Permite que até cinco VNets sejam integradas com o em um plano do serviço de aplicativo 
* Permite que a mesma VNet seja usada por vários aplicativos em um plano do serviço de aplicativo sem afetar o número total que pode ser usado por um plano do serviço de aplicativo.  Se você tiver 6 aplicativos usando a mesma VNet no mesmo plano do serviço de aplicativo, isso contará como uma VNet sendo usada. 
* Requer um gateway de rede virtual configurado com VPN ponto a site
* Não tem suporte para uso com aplicativos do Linux
* Dá suporte a um SLA de 99,9% devido ao SLA no gateway

Este recurso não oferece suporte a:

* Acessando recursos no ExpressRoute 
* Acessando recursos entre Pontos de Extremidade de Serviço 

### <a name="getting-started"></a>Introdução

Veja aqui algumas coisas para se ter em mente antes de conectar seu aplicativo Web a uma rede virtual:

* Uma rede virtual de destino precisa ter a VPN ponto a site habilitada com um gateway baseado em rota antes que possa ser conectada a um aplicativo. 
* A VNet deve estar na mesma assinatura que o ASP (Plano do Serviço de Aplicativo).
* Os aplicativos que se integram a uma VNet usam o DNS especificado daquela VNet.

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurar um gateway em sua VNet ###

Se você já tiver um gateway configurado com endereços de ponto a site, você poderá pular para a configuração da Integração VNet com seu aplicativo.  
Para criar um gateway:

1. [Crie uma sub-rede de gateway][creategatewaysubnet] em sua VNet.  

1. [Crie o gateway de VPN][creategateway]. Selecione um tipo VPN baseado em rota.

1. [Defina os endereços ponto a site][setp2saddresses]. Se o gateway não estiver no SKU básico, então o IKEV2 precisará ser desabilitado na configuração ponto a site e o SSTP deverá ser selecionado. O espaço de endereço deve estar nos blocos de endereço RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Se você estiver criando apenas o gateway para uso com a integração VNet do serviço de aplicativo, não será necessário carregar um certificado. A criação do gateway pode levar 30 minutos. Você não poderá integrar seu aplicativo com sua VNet até que o gateway seja provisionado. 

### <a name="configure-vnet-integration-with-your-app"></a>Configurar a Integração VNet com o aplicativo 

Para habilitar a Integração VNet com o aplicativo: 

1. Vá para seu aplicativo no portal do Azure e abra as configurações do aplicativo e selecione Rede > Integração VNet. O ASP deve estar em um SKU Standard ou melhor para usar o recurso de integração VNet. 
 ![Interface do usuário de Integração VNet][1]

1. Selecione **Adicionar VNet**. 
 ![Adicionar Integração VNet][2]

1. Selecione sua VNet. 
  ![Selecione sua VNet][8]
  
Seu aplicativo será reiniciado após essa última etapa.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Como funciona o recurso de integração VNet do gateway

O recurso de integração VNet do gateway necessário foi criado sobre a tecnologia VPN ponto a site. A tecnologia ponto a site limita o acesso à rede apenas à máquina virtual que está hospedando o aplicativo. Os aplicativos são restritos apenas ao envio de tráfego para a Internet, por meio de Conexões Híbridas ou por meio da Integração VNet. 

![Como a Integração VNet funciona][3]

## <a name="managing-vnet-integration"></a>Gerenciando a integração VNet
A capacidade de se conectar e desconectar de uma VNet está no nível dos aplicativos. As operações que podem afetar a Integração VNet entre vários aplicativos estão no nível do Plano do Serviço de Aplicativo. No aplicativo > rede > Portal de integração VNet, você pode obter detalhes sobre sua VNet. Você pode ver informações semelhantes no nível ASP na rede > ASP > Portal de integração VNet, incluindo quais aplicativos nesse plano do serviço de aplicativo estão usando uma determinada integração.

 ![Detalhes da VNet][4]

As informações disponíveis para você na interface do usuário de integração de VNet são as mesmas entre os portais do aplicativo e do ASP.

* Nome da VNet – links para a interface do usuário da rede virtual
* Localização – reflete a localização de sua VNet. A integração com uma rede virtual em outra localização pode causar problemas de latência para seu aplicativo. 
* Status do certificado – reflete se os certificados estão sincronizados entre o Plano do Serviço de Aplicativo e a VNet.
* Status do gateway – se você estiver usando a integração VNet necessária do gateway, poderá ver o status do gateway.
* Espaço de endereço da VNet – mostra o espaço de endereços IP para sua VNet. 
* Espaço de endereço ponto a site – mostra o espaço de endereços IP ponto a site para sua VNet. Ao fazer chamadas em sua VNet ao usar o recurso de gateway necessário, o endereço do seu aplicativo será um desses endereços. 
* Espaço de endereço site a site – você pode usar as VPNs site a site para conectar sua VNet a seus recursos locais ou a outras VNet. Os intervalos de IP definidos com essa conexão VPN são mostrados aqui.
* Servidores DNS – mostra os servidores DNS configurados com sua VNet.
* Endereços IP roteados para a rede virtual – mostra os blocos de endereços roteados usado para direcionar o tráfego em sua VNet 

A única operação que você pode executar no modo de exibição de aplicativo da Integração VNet é desconectar-se da VNet à qual seu aplicativo está atualmente conectado. Para desconectar o aplicativo de uma VNet, selecione **Desconectar**. Seu aplicativo será reiniciado quando você se desconectar de uma VNet. Desconectar-se não altera a VNet. A sub-rede ou o gateway não é removido. Se você quiser excluir sua VNet, primeiro você precisa desconectar seu aplicativo da VNet e excluir os recursos nele, como gateways. 

Para acessar a interface do usuário da Integração VNet do ASP, abra a interface do usuário do ASP e selecione **Rede**.  Na Integração VNet, selecione **Clique aqui para configurar** para abrir a interface do usuário do Status de Recurso de Rede.

![Informações da Integração VNet do ASP][5]

A interface do usuário da Integração VNet do ASP mostrará todas as redes virtuais que são usadas pelos aplicativos no seu ASP. Para ver detalhes em cada VNet, clique na VNet que lhe interessa. Existem duas ações que você pode executar aqui.

* **Sincronizar rede**. A operação de rede de sincronização é apenas para o recurso de integração VNet dependente de gateway. A execução de uma operação de rede de sincronização garante que os certificados e as informações de rede estejam em sincronia. Se você adicionar ou alterar o DNS da VNet, será necessário executar uma operação **Sincronizar rede**. Esta operação reiniciará todos os aplicativos usando essa VNet.
* **Adicionar rotas** A adição de rotas orientará o tráfego de saída em sua VNet.

**Roteamento** As rotas que são definidas em sua VNet são usadas para direcionar o tráfego para sua VNet vindo de seu aplicativo. Se você precisar enviar o tráfego de saída adicional para a VNet, você poderá adicionar esses blocos de endereço aqui. Essa funcionalidade só funciona com a integração VNet necessária do gateway.

**Certificados** do Quando o gateway exigia a integração VNet habilitada, há uma troca necessária de certificados para garantir a segurança da conexão. Juntamente com os certificados, há a configuração do DNS, as rotas e outros itens semelhantes que descrevem a rede.
Se os certificados ou as informações de rede forem alterados, você precisará clicar em "Sincronizar Rede". Quando clica em "Sincronizar Rede", você causa uma breve interrupção na conectividade entre o aplicativo e a VNet. Embora seu aplicativo não seja reiniciado, a perda de conectividade pode fazer com que seu site não funcione corretamente. 

## <a name="accessing-on-premises-resources"></a>Como acessar recursos locais
Aplicativos podem acessar recursos locais ao integrarem-se com VNets que têm conexões site a site. Se estiver usando a integração VNet necessária do gateway, você precisará atualizar suas rotas de gateway de VPN locais com seus blocos de endereço ponto a site. Quando o VPN site a site é configurado pela primeira vez, os scripts usados para configurá-lo devem configurar as rotas adequadamente. Se adicionar os endereços ponto a site depois de criar uma VPN site a site, você precisará atualizar as rotas manualmente. Os detalhes sobre como fazer isso variam de acordo com o gateway, e não são descritos aqui. Você não pode ter o BGP configurado com uma conexão VPN site a site.

Não há nenhuma configuração adicional necessária para que o recurso de integração VNet regional alcance por meio de sua VNet e para o local. Você simplesmente precisa conectar sua VNet ao local usando o ExpressRoute ou uma VPN site a site. 

> [!NOTE]
> O recurso de integração VNet do gateway necessário não integra um aplicativo a uma VNet que tem um gateway de ExpressRoute. Mesmo que o gateway de ExpressRoute esteja configurado no [modo][VPNERCoex] de coexistência, a integração VNet não funciona. Se você precisar acessar recursos por meio de uma conexão do ExpressRoute, poderá usar o recurso de integração VNet regional ou um [ambiente do serviço de aplicativo][ASE], que é executado em sua VNet. 
> 
> 

## <a name="peering"></a>Emparelhamento
Se você estiver usando o emparelhamento com a integração VNet regional, não será necessário fazer nenhuma configuração adicional. 

Se estiver usando a integração VNet necessária do gateway com o emparelhamento, você precisará configurar alguns itens adicionais. Para configurar o emparelhamento para funcionar com o aplicativo:

1. Adicione que uma conexão de emparelhamento na rede virtual à qual o aplicativo se conecta. Ao adicionar a conexão de emparelhamento, habilite **Permitir acesso à rede virtual** e marque **Permitir tráfego encaminhado** e **Permitir trânsito de gateway**.
1. Adicione uma conexão de emparelhamento na rede virtual que está sendo emparelhada à rede virtual à qual você está conectado. Ao adicionar a conexão de emparelhamento à VNet de destino, habilite **Permitir acesso à rede virtual** e marque **Permitir tráfego encaminhado** e **Permitir gateways remotos**.
1. Vá para o Plano do Serviço de Aplicativo > Rede > Interface do usuário da Integração VNet no portal.  Selecione a VNet à qual o aplicativo se conecta. Na seção de roteamento, adicione o intervalo de endereços da VNet que está emparelhada com a VNet à qual o aplicativo está conectado.  


## <a name="pricing-details"></a>Detalhes do preço
O recurso de integração VNet regional não tem nenhum custo adicional para uso além dos encargos do tipo de preço ASP.

Há três encargos relacionados ao uso do recurso de integração VNet exigido pelo gateway:

* Cobranças do tipo de preço do ASP – seus aplicativos precisam estar em um plano do serviço de aplicativo Standard, Premium ou PremiumV2. Você pode ver mais detalhes sobre esses custos aqui: [Preço do serviço de aplicativo][ASPricing]. 
* Custos de transferência de dados – há um encargo para a saída de dados, mesmo que a VNet esteja na mesma data center. Esses encargos são descritos em [transferência de dados detalhes de preços][DataPricing]. 
* Custos de gateway de VPN – há um custo para o gateway de VNet que é necessário para a VPN ponto a site. Os detalhes estão na página de [preços do gateway de VPN][VNETPricing] .


## <a name="troubleshooting"></a>Solução de problemas
Embora o recurso seja fácil de configurar, isso não significa que sua experiência estará livre de problemas. Se você encontrar problemas ao acessar o ponto de extremidade desejado, há utilitários que você pode usar para testar a conectividade do console do aplicativo. Há dois consoles que você pode usar. Uma é o console do Kudu e a outra é o console no portal do Azure. Para acessar o console do Kudu do aplicativo, vá para Ferramentas -> Kudu. Isso é o mesmo que ir para [nomedosite].scm.azurewebsites.net. Depois de aberto, vá para a guia do console Depuração. Para obter a console hospedado no portal do Azure, em seu aplicativo, vá para Ferramentas -> Console. 

#### <a name="tools"></a>Ferramentas
As ferramentas **ping**, **nslookup** e **tracert** não funcionarão no console devido a restrições de segurança. Para compensar essa ausência, duas ferramentas separadas foram adicionadas. Para testar a funcionalidade do DNS, adicionamos uma ferramenta chamada nameresolver.exe. A sintaxe é:

    nameresolver.exe hostname [optional: DNS Server]

Você pode usar **nameresolver** para verificar os nomes de host de que seu aplicativo depende. Dessa forma, você pode testar se não há nada mal configurado no DNS ou se não tem acesso ao seu servidor DNS. Você pode ver o servidor DNS que seu aplicativo usará no console examinando as variáveis de ambiente WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

A próxima ferramenta permite testar a conectividade do TCP de uma combinação de host e porta. Essa ferramenta é chamada **tcpping** e a sintaxe é:

    tcpping.exe hostname [optional: port]

O utilitário **tcpping** informa se você pode acessar um host específico e uma porta. Ele só pode mostrar êxito se: houver um aplicativo escutando na combinação de porta e host, e houver acesso de rede de seu aplicativo para o host e porta especificados.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Depurar o acesso a recursos hospedados por VNet
Há várias coisas que podem impedir que seu aplicativo alcance um host e uma porta específicos. Na maioria das vezes, é uma dentre três coisas:

* **Há um firewall no caminho.** Se houver um firewall no caminho, você atingirá o tempo limite de TCP. O tempo limite de TCP é 21 segundos neste caso. Use a ferramenta **tcpping** para testar a conectividade. Tempos limite de TCP podem ocorrer por muitos motivos, além de firewalls, mas comece com isso. 
* **O DNS não está acessível.** O tempo limite do DNS é de três segundos por servidor DNS. Se você tiver dois servidores DNS, o tempo limite será de seis segundos. Use nameresolver para ver se o DNS está funcionando. Lembre-se de que você não pode usar nslookup, pois ele não usa o DNS com o qual sua VNet está configurada. Se estiver inacessível, você pode ter um firewall ou NSG bloqueando o acesso ao DNS ou pode estar inoperante.

Se esses itens não responderem a seus problemas, veja primeiro algo como: 

**Integração de VNet regional**
* seu destino é um endereço RFC 1918
* Há um NSG bloqueando a saída de sua sub-rede de integração
* Se estiver entrando no ExpressRoute ou em uma VPN, o gateway local será configurado para rotear o tráfego de volta para o Azure? Se você puder acessar pontos de extremidade em sua VNet, mas não local, isso será bom para verificar.

**Integração VNet necessária do gateway**
* é o intervalo de endereços de ponto a site nos intervalos RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* O Gateway é mostrado como estando em execução no Portal? Se o gateway estiver inativo, ative-o.
* Os certificados são mostrados como estando em sincronia ou você suspeita que a configuração de rede foi alterada?  Se os certificados estiverem fora de sincronia ou se você suspeitar de que houve uma alteração na configuração da VNet que não foi sincronizada com suas ASPs, pressione "sincronizar rede".
* Se estiver entrando no ExpressRoute ou em uma VPN, o gateway local será configurado para rotear o tráfego de volta para o Azure? Se você puder acessar pontos de extremidade em sua VNet, mas não local, isso será bom para verificar.

A depuração de problemas de rede é um desafio porque não é possível ver o que está bloqueando o acesso a uma combinação de hosts específicos: porta. Algumas das causas incluem:

* você tem um firewall no seu host que impede o acesso à porta do aplicativo usando o intervalo de IP ponto a site. O cruzamento de sub-redes geralmente exige acesso Público.
* o host de destino está inoperante
* seu aplicativo está inoperante
* você tinha o IP ou nome de host incorreto
* seu aplicativo está escutando em uma porta diferente da que você esperava. Você pode corresponder a sua ID de processo com a porta de escuta usando "netstat -aon" no host do ponto de extremidade. 
* os grupos de segurança de rede estão configurados de modo a impedir o acesso ao host do aplicativo e à porta do intervalo de IP ponto a site.

Lembre-se de que você não sabe qual endereço seu aplicativo realmente usará. Pode ser qualquer endereço na sub-rede de integração ou intervalo de endereços de ponto a site, portanto, você precisa permitir o acesso de todo o intervalo de endereços. 

As etapas de depuração adicionais incluem:

* conecte-se a uma VM na sua VNet e tente acessar o host:porta do recurso de lá. Para testar o acesso TCP, use o comando do PowerShell **test-netconnection**. A sintaxe é:

      test-netconnection hostname [optional: -Port]

* abrir um aplicativo em uma VM e testar o acesso ao host e à porta do console a partir do seu aplicativo usando o **tcpping**

#### <a name="on-premises-resources"></a>Recursos locais ####

Se o aplicativo não puder acessar um recurso local, verifique se é possível acessar o recurso da sua VNet. Use o comando do PowerShell **test-netconnection** para verificar se há acesso TCP. Se sua VM não conseguir acessar o recurso local, sua conexão VPN ou ExpressRoute poderá não estar configurada corretamente.

Se a VM hospedada na VNet puder acessar seu sistema local, mas seu aplicativo não, isso provavelmente ocorrerá devido a um dos seguintes motivos:

* suas rotas não estão configuradas com a sub-rede ou intervalos de endereços de ponto a site em seu gateway local
* seus grupos de segurança de rede estão bloqueando o acesso para o intervalo de IP Ponto a Site
* os firewalls locais estão bloqueando o tráfego do intervalo de IP Ponto a Site
* Você está tentando acessar um endereço não RFC 1918 usando o recurso de integração VNet regional


## <a name="powershell-automation"></a>Automação do PowerShell

Você pode integrar o Serviço de Aplicativo com uma Rede Virtual do Azure usando o PowerShell. Para um script pronto para uso, consulte [Conectar um aplicativo do Azure no Serviço de Aplicativo do Azure a uma Rede Virtual do Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
