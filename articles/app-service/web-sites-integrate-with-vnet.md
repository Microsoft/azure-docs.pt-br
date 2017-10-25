---
title: Integrar um aplicativo a uma Rede Virtual do Azure
description: "Mostra como conectar um aplicativo do Azure no Serviço de Aplicativo do Azure a uma rede virtual do Azure nova ou existente"
services: app-service
documentationcenter: 
author: ccompy
manager: erikre
editor: cephalin
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: ccompy
ms.openlocfilehash: 72ff0c13319218f8ef91aff9208772fcb0fd9459
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar seu aplicativo Web a uma Rede Virtual do Azure
Este documento descreve o recurso de visualização de integração de rede virtual do Serviço de Aplicativo do Azure e mostra como configurá-lo com os aplicativos no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Se você estiver familiarizado com VNets (Redes Virtuais do Azure), isso é um recurso que permite colocar muitos dos recursos do Azure em uma rede que não pode ser roteada pela Internet, cujo acesso você pode controlar. Essas redes podem ser conectadas às redes locais usando uma variedade de tecnologias de VPN. Para saber mais sobre redes virtuais do Azure, confira [Visão geral da Rede Virtual do Azure][VNETOverview]. 

O Serviço de Aplicativo do Azure tem duas formas. 

1. Os sistemas de vários locatários que dão suporte a toda a gama de planos de preço.
2. O recurso premium do ASE (Ambiente do Serviço de Aplicativo) que implanta em sua VNet. 

Este documento passa pela Integração VNet e não pelo Ambiente de Serviço de Aplicativo. Se você quiser saber mais sobre o recurso ASE, comece com as informações fornecidas aqui: [Introdução ao Ambiente de Serviço de Aplicativo][ASEintro].

A integração VNet concede ao seu aplicativo Web acesso a recursos da sua rede virtual, mas não concede acesso privado ao seu aplicativo Web por meio da rede virtual. Acesso ao site privado significa tornar seu aplicativo acessível somente de uma rede privada, como de dentro de uma rede virtual do Azure. O acesso de site privado só está disponível com um ASE configurado com um ILB (Balanceador de carga interno). Para obter detalhes sobre como usar um ASE com ILB, comece com este artigo: [Criação e uso de um ASE com ILB][ILBASE]. 

Um cenário comum de uso da Integração VNet é a habilitação do acesso de seu aplicativo Web a um banco de dados ou serviços Web em execução em uma máquina virtual em sua rede virtual do Azure. Com a integração VNet, você não precisa expor um ponto de extremidade público para aplicativos em sua VM, mas pode usar, em vez disso, endereços roteáveis privados fora da Internet. 

O recurso de integração VNet:

* requer um plano de preços Standard, Premium ou Isolado 
* funciona com VNet clássica ou do Gerenciador de Recursos 
* dá suporte a TCP e UDP
* funciona com aplicativos Web, móveis e de API
* permite que um aplicativo se conecte somente a uma VNet por vez
* permite a integração de até cinco VNets com um Plano do Serviço de Aplicativo 
* permite que a mesma VNet seja usada por vários aplicativos em um Plano do Serviço de Aplicativo
* dá suporte a um SLA de 99,9% devido ao SLA no Gateway de VNet

Há algumas coisas a que a integração VNet não dá suporte, incluindo:

* montagem de unidade
* integração ao AD 
* NetBios
* acesso a site privado

### <a name="getting-started"></a>Introdução
Veja aqui algumas coisas para se ter em mente antes de conectar seu aplicativo Web a uma rede virtual:

* A integração VNet só funciona com aplicativos em um plano de preços **Standard**, **Premium** ou **Isolado**. Se você habilitar o recurso e dimensionar seu Plano do Serviço de Aplicativo para um plano de preços sem suporte, seus aplicativos perderão suas conexões com as VNets que estão usando. 
* Se a sua rede virtual de destino já existe, ela deve ter a VPN ponto a site habilitada com um gateway de roteamento dinâmico antes que possa ser conectada a um aplicativo. Se o gateway estiver configurado com roteamento Estático, não será possível habilitar a opção ponto a site da VPN (Rede virtual privada).
* A VNet deve estar na mesma assinatura que o ASP (Plano do Serviço de Aplicativo). 
* Os aplicativos que se integram a uma VNet usam o DNS especificado daquela VNet.
* Por padrão, os aplicativos integrados apenas rotearão o tráfego em sua VNet baseados nas rotas definidas em sua VNet. 

## <a name="enabling-vnet-integration"></a>Habilitar a integração VNet

Você tem a opção de conectar seu aplicativo a uma rede virtual nova ou existente. Se você criar uma nova rede como parte a sua integração, além de criar apenas uma VNET, um gateway de roteamento dinâmico será pré-configurado para você e a VPN Ponto a Site será habilitada. 

> [!NOTE]
> A configuração de uma nova integração da rede virtual pode levar vários minutos. 
> 
> 

Para habilitar a integração VNet, abra as Configurações do aplicativo configurações e selecione Rede. A interface do usuário oferece três opções de rede. Este guia só aborda a integração VNet, mas as Conexões Híbridas e Ambientes de Serviço de Aplicativo são discutidos neste documento. 

Se seu aplicativo não estiver no plano de preços correto, a interface do usuário permitirá que você escalone seu plano para o plano de preços mais alto que queira escolher.

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Habilitar a integração VNet com uma VNet já existente
A interface de usuário de Integração VNet permite que você selecione em uma lista de suas VNets. As VNets Clássicas são identificadas com a palavra "Clássica" entre parênteses ao lado do nome da VNet. A lista é classificada de modo que as VNets do Gerenciador de Recursos estarão listadas primeiro. Na imagem abaixo, você pode ver que somente uma VNet pode ser selecionada. Há vários motivos para uma VNet ficar esmaecida, incluindo:

* a VNet está em outra assinatura à qual sua conta tem acesso
* a VNet não tem um Ponto a Site habilitado
* a VNet não tem um gateway de roteamento dinâmico

![][2]

Para habilitar a integração, simplesmente clique na VNet com a qual você quer integrar. Depois de selecionar a VNet, o aplicativo é reiniciado automaticamente para que as alterações entrem em vigor. 

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Habilitar Ponto a Site em uma VNet Clássica
Se sua VNet não tiver um gateway ou Ponto a Site, você precisará configurar isso primeiro. Para fazer isso para uma VNet Clássica, acesse o [Portal do Azure][AzurePortal] e exiba a lista de Redes Virtuais (clássicas). Em seguida, clique na rede com que você deseja integrar e clique na caixa grande em Essentials chamada Conexões VPN. A partir daqui, você pode criar sua VPN ponto a site e até mesmo fazê-la criar um gateway. Depois de passar pelo ponto a site com a experiência de criação do gateway, demora cerca de 30 minutos até que ele fique pronto. 

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Habilitar Ponto a Site em uma VNet do Gerenciador de Recursos
Para configurar uma VNet do Gerenciador de Recursos com um gateway e Ponto a Site, você pode usar o PowerShell, conforme documentado em [Configurar uma conexão Ponto a Site com uma rede virtual usando PowerShell][V2VNETP2S] ou usar o Portal do Azure, conforme documentado em [Configurar uma conexão Ponto a Site para uma rede virtual usando o Portal do Azure][V2VNETPortal]. A interface do usuário para executar esse recurso ainda não está disponível. Observe que você não precisa criar certificados para a configuração Ponto a Site. Isso é automaticamente configurado quando você conecta seu WebApp à VNet. 

### <a name="creating-a-pre-configured-vnet"></a>Criar uma VNet pré-configurada
Se você quiser criar uma nova VNet que esteja configurada com um gateway e um Ponto a Site, a interface do usuário de rede do Serviço de Aplicativo tem a capacidade de fazer isso, mas somente para VNet do Gerenciador de Recursos. Se você quiser criar uma VNet Clássica com um gateway e um Ponto a Site, precisará fazer isso manualmente por meio da interface do usuário de rede. 

Para criar uma VNet do Gerenciador de Recursos por meio da interface do usuário de Integração VNet, basta selecionar **Criar Nova Rede Virtual** e fornecer:

* Nome da VNET
* Bloco de endereço da VNET
* Nome da sub-rede
* Bloco de endereço da sub-rede
* Bloco de endereço do Gateway
* Bloco de endereço Ponto a site

Se você quiser que essa VNet se conecte a qualquer outra rede, evite escolher um espaço de endereço IP que se sobreponha a essas redes. 

> [!NOTE]
> A criação da VNet do Gerenciador de Recursos com um gateway leva cerca de 30 minutos e atualmente não integra a VNet ao seu aplicativo. Após a criação da VNet com o gateway, você precisa voltar para a interface do usuário de Integração VNet de seu aplicativo e selecionar sua nova VNet.
> 
> 

![][3]

As VNets do Azure normalmente são criadas dentro de endereços de rede privada. Por padrão, o recurso Integração VNet roteará o tráfego destinado aos intervalos de endereços IP em sua VNet. Os intervalos de endereços IP privados são:

* 10.0.0.0/8 - esse é o mesmo que 10.0.0.0 - 10.255.255.255
* 172.16.0.0/12 - é o mesmo que 172.16.0.0 - 172.31.255.255 
* 192.168.0.0/16 - é o mesmo que 192.168.0.0 - 192.168.255.255

O espaço de endereço de VNet precisa ser especificado na notação CIDR. Se você não estiver familiarizado com notação CIDR, é um método para especificar blocos de endereço usando um endereço IP e um número inteiro que representa a máscara de rede. Como uma referência rápida, considere que 10.1.0.0/24 seria 256 endereços e 10.1.0.0/25 seria 128 endereços. Um endereço IPv4 com um /32 seria apenas 1 endereço. 

Se você definir as informações do servidor DNS aqui, elas serão definidas para sua VNet. Após a criação da VNet, você poderá editar essas informações nas experiências de usuário da VNet. Se você alterar o DNS da VNet, será necessário executar uma operação de Sincronização de Rede.

Quando você cria uma VNet Clássica usando a interface de usuário de integração VNet, ela cria uma VNet no mesmo grupo de recursos do seu aplicativo. 

## <a name="how-the-system-works"></a>Como o sistema funciona
De modo oculto este recurso se baseia na tecnologia VPN Ponto a site para se conectar ao aplicativo à sua VNet. Aplicativos no Serviço de Aplicativo do Azure têm uma arquitetura de sistema multilocatário, que impede o provisionamento de um aplicativo diretamente em uma VNet, como ocorre com máquinas virtuais. Ao desenvolver uma tecnologia ponto a site, limitamos o acesso à rede apenas à máquina virtual que está hospedando o aplicativo. O acesso à rede é ainda mais limitado nestes hosts do aplicativo, de modo que seus aplicativos poderão acessar apenas as redes que você configurar para eles acessarem. 

![][4]

Se você ainda não configurou um servidor DNS com sua rede virtual, será necessário usar endereços IP para acessar o recurso na VNet. Ao usar endereços IP, lembre-se de que a grande vantagem desse recurso é que ele permite que você use os endereços privados em sua rede privada. Se você configurar seu aplicativo para usar endereços IP públicos em uma das suas VMs, não estará usando o recurso Integração VNet e estará se comunicando pela internet.

## <a name="managing-the-vnet-integrations"></a>Gerenciar as integrações VNet
A capacidade de se conectar e desconectar de uma VNet está no nível dos aplicativos. As operações que podem afetar a integração VNet entre vários aplicativos estão no nível do ASP. Você pode obter detalhes sobre sua VNet na interface de usuário que é mostrada no nível de aplicativo. A maioria dessas informações também é mostrada no nível do ASP. 

![][5]

Na página Status do Recurso de Rede, você pode ver se seu aplicativo está conectado à sua VNet. Se o gateway da VNet estiver inativo por qualquer motivo, ele aparecerá como não conectado. 

As informações que você tem no aplicativo na interface de usuário da Integração VNet no nível de aplicativo são as mesmas detalhadas originadas no ASP. Estes são os itens:

* Nome da VNet – Esse link abre a interface do usuário da rede
* Local – Reflete o local de sua VNet. É possível fazer a integração a uma VNet em outro local.
* Status do certificado – Há certificados usados para proteger a conexão VPN entre o aplicativo e a VNet. Isso é um teste para garantir que eles estejam em sincronia.
* Status do gateway – se os gateways ficarem inativos por algum motivo, seu aplicativo não poderá acessar recursos na VNet. 
* Espaço de endereço da VNet – é o espaço de endereço IP para sua VNet. 
* Espaço de endereço ponto a site – é o espaço de endereço IP ponto a site para sua VNet. Seu aplicativo mostra a comunicação como proveniente de um dos IPs nesse espaço de endereço. 
* Espaço de endereço site a site – Você pode usar as VPNs site a site para conectar sua VNet a seus recursos locais ou a outras VNet. Se estiver configurado, os intervalos de IP definidos com aquela conexão VPN serão exibidos aqui.
* Servidores DNS – Se você tiver servidores DNS configurados com sua VNet, eles serão listados aqui.
* IPs roteados para a VNet – Há uma lista de endereços IP para os quais sua VNet definiu o roteamento, e esses endereços são exibidos aqui. 

A única operação que você pode executar no modo de exibição de aplicativo da Integração VNet é desconectar-se da VNet à qual seu aplicativo está atualmente conectado. Para fazer isso, simplesmente clique em Desconectar na parte superior. Essa ação não altera sua VNet. A VNet e sua configuração, incluindo os gateways, permanece inalterada. Se você quiser excluir sua VNet, precisa primeiro excluir os recursos nela, incluindo os gateways. 

O modo de exibição do Plano de Serviço de Aplicativo tem algumas operações adicionais. Ele também acesso diferente do aplicativo. Para acessar a interface do usuário da rede do ASP basta abrir sua interface do usuário do ASP e rolar para baixo. Há um elemento de interface do usuário chamado Status do Recurso de Rede. Ele fornece alguns pequenos detalhes sobre a integração VNet. Clicar nessa interface do usuário abre a interface do usuário Status de Recurso de Rede. Se você clicar em "Clique aqui para gerenciar", abrirá a interface do usuário que lista as integrações de VNet nesse ASP.

![][6]

É bom lembrar do local do ASP ao examinar os locais das VNets com que você estiver integrando. Quando a VNet está em outro local, a probabilidade de perceber os problemas de latência é muito menor. 

As VNets integradas são um lembrete de quantas VNets estão integradas com seus aplicativos no ASP e quantas você pode ter. 

Para ver detalhes adicionados em cada VNet, basta clicar na VNet que lhe interessa. Além dos detalhes que foram observados anteriormente, você também verá uma lista dos aplicativos nesse ASP que estão usando aquela VNet. 

Em relação a ações, existem duas ações primárias. A primeira é a capacidade de adicionar rotas que direcionam o tráfego que sai de seu aplicativo para sua VNet. A segunda ação é a capacidade de sincronizar certificados e informações de rede.

![][7]

**Roteamento** Conforme observado anteriormente, as rotas que são definidas em sua VNet são usadas para direcionar o tráfego para sua VNet vindo de seu aplicativo. No entanto, há alguns usos, como quando os clientes querem enviar o tráfego de saída adicional de um aplicativo para a VNet, e esse recurso é fornecido para eles. O que acontece com o tráfego depois depende de como o cliente configura sua VNet. 

**Certificados** O status do certificado reflete uma verificação de que está sendo executada pelo Serviço de Aplicativo para validar se os certificados que estamos usando para a conexão VPN ainda estão válidos. Ao habilitar a Integração VNet, se essa for a primeira integração com a VNet de qualquer aplicativos nesse ASP, há uma troca de certificados necessária para garantir a segurança da conexão. Juntamente com os certificados, obtemos a configuração do DNS, as rotas e outros itens semelhantes que descrevem a rede.
Se esses certificados ou informações de rede forem alterados, você precisará clicar em "Sincronizar Rede". **Observação**: quando você clica em "Sincronização Rede", pode causar uma breve interrupção na conectividade entre o aplicativo e a VNet. Embora seu aplicativo não seja reiniciado, a perda de conectividade pode fazer com que seu site não funcione corretamente. 

## <a name="accessing-on-premises-resources"></a>Como acessar recursos locais
Um dos benefícios do recurso Integração VNet é que, se a VNet estiver conectada à rede local com uma VPN site a site, seus aplicativos poderão ter acesso aos recursos locais usando seu aplicativo. Para isso funcionar, talvez seja necessário atualizar o gateway de VPN local com as rotas para o intervalo de IP ponto a site. Quando o VPN site a site é configurado pela primeira vez, os scripts usados para configurá-lo devem configurar as rotas, incluindo a VPN ponto a site. Se você adicionar a VPN Ponto a Site depois de criar a VPN Site a Site, precisará atualizar as rotas manualmente. Os detalhes sobre como fazer isso variam de acordo com o gateway, e não são descritos aqui. 

> [!NOTE]
> O recurso de Integração VNet não integra um aplicativo com uma rede VNet que tem um Gateway ExpressRoute. Mesmo que o Gateway ExpressRoute esteja configurado no [modo de coexistência][VPNERCoex], a Integração VNet não funcionará. Se você precisar acessar recursos usando uma conexão ExpressRoute, poderá usar um [Ambiente de Serviço de Aplicativo][ASE] que seja executado em sua VNet.
> 
> 

## <a name="pricing-details"></a>Detalhes de preço
Existem algumas nuances de preço que você deve levar em conta ao usar o recurso Integração VNet. Há três cobranças relacionadas ao uso desse recurso:

* Requisitos de tipo de preço do ASP
* Custos de transferência de dados
* Custos de gateway de VPN.

Para que seus aplicativos possam usar o recurso, eles precisam estar em um Plano de Serviço de Aplicativo Standard ou Premium. Você pode ver mais detalhes sobre esses custos aqui: [Preço do Serviço de Aplicativo][ASPricing]. 

Devido à maneira como as VPNs Ponto a Site são tratadas, você sempre será cobrado por dados de saída pela conexão da Integração VNet, mesmo que ela não esteja no mesmo data center. Para ver quais são essas cobranças, dê uma olhada aqui: [Detalhes de Preço da Transferência de Dados][DataPricing]. 

O último item é o custo dos gateways de VNet. Se você não precisa dos gateways para algo como VPNs Site a Site, está pagando para que eles deem suporte ao recurso Integração VNet. Há detalhes sobre esses custos aqui: [Preço de Gateway de VPN][VNETPricing]. 

## <a name="troubleshooting"></a>Solucionar problemas
Embora o recurso seja fácil de configurar, isso não significa que sua experiência estará livre de problemas. Se você encontrar problemas ao acessar o ponto de extremidade desejado, há utilitários que você pode usar para testar a conectividade do console do aplicativo. Há duas experiências de console que você pode usar. Uma é o console Kudu e a outra é o console que você pode acessar no Portal do Azure. Para acessar o console do Kudu em seu aplicativo, vá para Ferramentas -> Kudu. Isso é o mesmo que ir para [nomedosite].scm.azurewebsites.net. Depois de aberto, vá para a guia Console de depuração. Para obter a console hospedado no portal do Azure, em seu aplicativo, vá para Ferramentas -> Console. 

#### <a name="tools"></a>Ferramentas
As ferramentas ping, nslookup e tracert não funcionarão no console devido a restrições de segurança. Para compensar essa ausência, duas ferramentas separadas foram adicionadas. Para testar a funcionalidade do DNS, adicionamos uma ferramenta chamada nameresolver.exe. A sintaxe do é:

    nameresolver.exe hostname [optional: DNS Server]

Você pode usar a nameresolver para verificar os nomes de host de que seu aplicativo depende. Dessa forma, você pode testar se não há nada mal configurado no DNS ou se não tem acesso ao seu servidor DNS.

A próxima ferramenta permite testar a conectividade do TCP de uma combinação de host e porta. Essa ferramenta é chamada tcpping.exe e a sintaxe é:

    tcpping.exe hostname [optional: port]

O utilitário tcpping informa se você pode acessar um host específico e uma porta. Ele só pode mostrar êxito se: houver um aplicativo escutando na combinação de porta e host, e houver acesso de rede de seu aplicativo para o host e porta especificados.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Depurar o acesso a recursos hospedados por VNet
Há várias coisas que podem impedir que seu aplicativo alcance um host e uma porta específicos. Na maioria das vezes, é uma dentre três coisas:

* **Há um firewall no caminho** Se houver um firewall no caminho, você atingirá o tempo limite de TCP. Nesse caso, de 21 segundos. Use a ferramenta tcpping para testar a conectividade. Tempos limite de TCP podem ocorrer por muitos motivos, além de firewalls, mas comece com isso. 
* **DNS não está acessível** O tempo limite do DNS é de três segundos por servidor DNS. Se você tiver dois servidores DNS, o tempo limite será de seis segundos. Use nameresolver para ver se o DNS está funcionando. Lembre-se de que você não pode usar nslookup, pois ele não usa o DNS com o qual sua VNet está configurada.
* **Intervalo de IP P2S inválido** O intervalo de IP Ponto a Site deve estar nos intervalos de IP privados RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Se o intervalo usar IPs fora disso, as coisas não funcionarão. 

Se esses itens não resolverem seu problema, procure por coisas simples primeiro, como: 

* O Gateway é mostrado como estando em execução no Portal?
* Os certificados são mostrados como sincronizados?
* Alguém alterou a configuração de rede sem fazer uma “Sincronização de Rede” nos ASPs afetados? 

Se o gateway estiver inativo, ative-o. Se os certificados estiverem fora de sincronia, vá para a exibição ASP da integração VNet e pressione "Sincronizar Rede". Se você suspeitar de que houve uma alteração na configuração da VNet e ela não foi sincronizada com seus ASPs, vá para o modo de exibição ASP da Integração VNet e pressione "Sincronizar Rede". Lembrete: isso paralisará momentaneamente a conexão com sua VNet e com seus aplicativos. 

Se tudo estiver bem, você precisará se aprofundar um pouco mais:

* Há outros aplicativos que usam a Integração VNet para acessar recursos na mesma VNet? 
* Você consegue ir ao console do aplicativo e usar tcpping para acessar outros recursos na sua VNet? 

Se alguma das opções acima é verdadeira, a Integração VNet está boa e o problema está em outro lugar. Essa hipótese é mais complicada, já que não há uma maneira simples de ver por que você não consegue acessar o host:porta. Algumas das causas incluem:

* você tem um firewall no seu host que impede o acesso à porta do aplicativo usando o intervalo de IP ponto a site. O cruzamento de sub-redes geralmente exige acesso Público.
* o host de destino está inoperante
* seu aplicativo está inoperante
* você tinha o IP ou nome de host incorreto
* seu aplicativo está escutando em uma porta diferente da que você esperava. Você pode verificar isso indo até o host e usando "netstat - aon" no prompt de comando. Isso mostra qual ID de processo está escutando em qual porta. 
* os grupos de segurança de rede estão configurados de modo a impedir o acesso ao host do aplicativo e à porta do intervalo de IP ponto a site.

Lembre-se de que você não sabe quais IPs no intervalo de IP ponto a site seu aplicativo usará; portanto, é necessário permitir o acesso a todo o intervalo. 

As etapas de depuração adicionais incluem:

* fazer logon em outra VM na VNet e tentar acessar o host:porta do recurso a partir daí. Existem alguns utilitários de ping do TCP que você pode usar para essa finalidade, ou até mesmo usar o telnet, se for o caso. O objetivo aqui é somente determinar se há conectividade de outra máquina virtual. 
* colocar um aplicativo em outra VM e testar o acesso àquele host e porta usando o console do aplicativo

#### <a name="on-premises-resources"></a>Recursos locais ####
Se o aplicativo não puder acessar os recursos locais, a primeira medida a adotar será verificar se você consegue acessar um recurso na VNet. Se isso funcionar, tente acessar o aplicativo local de uma VM na VNet. Você pode usar o telnet ou um utilitário de ping de TCP. Se sua VM não conseguir acessar o recurso local, verifique se a conexão de VPN site a site está funcionando. Se estiver funcionando, verifique os mesmos pontos observados anteriormente, bem como a configuração e o status do gateway local. 

Agora, se sua VM hospedada na VNet puder acessar seu sistema local, mas seu aplicativo não, provavelmente, isso ocorre devido a um dos seguintes motivos:

* suas rotas não estão configuradas com os intervalos de IP ponto a site em seu gateway local
* seus grupos de segurança de rede estão bloqueando o acesso para o intervalo de IP Ponto a Site
* os firewalls locais estão bloqueando o tráfego do intervalo de IP Ponto a Site
* você tem uma UDR (Rota Definida pelo Usuário) em sua VNet que impede que o tráfego baseado em Ponto a Site acesse sua rede local

## <a name="hybrid-connections-and-app-service-environments"></a>Conexões híbridas e ambientes de serviço de aplicativo
Há três recursos que habilitam o acesso a recursos hospedados em VNet. Eles são:

* Integração VNet
* Conexões Híbridas
* Ambientes de Serviço de Aplicativo

As conexões híbridas exigem que você instale um agente de retransmissão chamado HCM (gerente de conexões híbridas) na sua rede. O HCM precisa ser capaz de se conectar ao Azure e também a seu aplicativo. Essa solução é especialmente boa usando uma rede remota, como sua rede local ou mesmo outra rede hospedada em nuvem, já que não exige um ponto de extremidade acessível pela Internet. O HCM só é executado no Windows e você pode ter até cinco instâncias em execução para fornecer alta disponibilidade. No entanto, as conexões híbridas só dão suporte a TCP e cada ponto de extremidade de HC tem que corresponder a uma combinação de host:porta específica. 

O recurso Ambiente de Serviço de Aplicativo permite a execução de uma instância do Serviço de Aplicativo do Azure em sua VNet. Isso permite que seus aplicativos acessem os recursos de sua VNet sem etapas adicionais. Alguns dos outros benefícios de um Ambiente de Serviço de Aplicativo é que você pode usar trabalhos com base em Dv2 com 14 GB de RAM. Outra vantagem é que você pode escalonar o sistema para atender às suas necessidades. Ao contrário dos ambientes multilocatários nos quais o ASP é limitado a 20 instâncias, em um ASE, você pode escalar verticalmente até 100 instâncias do ASP. Uma das coisas que você obtém com um ASE e que não obtém com a Integração VNet é que um Ambiente de Serviço de Aplicativo poder funcionar com uma VPN ExpressRoute. 

Embora haja sobreposição de caso de uso, nenhum desses recursos pode substituir o outro. Saber qual recurso usar depende de suas necessidades. Por exemplo:

* Se você for um desenvolvedor e quiser simplesmente executar um site no Azure que acesse o banco de dados na estação de trabalho na sua mesa, a coisa mais fácil de usar é o Conexões Híbridas. 
* Se você tem uma grande organização que deseja colocar um grande número de propriedades da Web na nuvem pública e gerenciá-las em sua própria rede, a melhor opção é o Ambiente de Serviço de Aplicativo. 
* Se você tiver uma quantidade de aplicativos hospedados no Serviço de Aplicativo e simplesmente quiser acessar recursos na VNet, a melhor opção será a Integração VNet. 

Além dos casos de uso, há alguns aspectos relativos à simplicidade. Se sua VNet já estiver conectada à sua rede local, o uso da Integração VNet ou de um Ambiente de Serviço de Aplicativo será uma maneira fácil de consumir recursos locais. Por outro lado, se sua VNet não estiver conectada à sua rede local, será muito mais trabalhoso configurar uma VPN site a site com a VNet do que instalar o HCM. 

Além das diferenças funcionais, há também diferenças de preço. O recurso Ambiente de Serviço de Aplicativo é uma oferta do serviço Premium, mas oferece a maioria das possibilidades de configuração de rede, além de outros recursos incríveis. A Integração VNet pode ser usada com ASPs Standard ou Premium e é perfeita para consumir com segurança recursos em sua VNet do Serviço de Aplicativo multilocatário. O Conexões Híbridas atualmente depende de um conta BizTalk, com preços que variam de gratuito a mais caros baseado na quantidade necessária. Quando se trata de trabalhar em várias redes, no entanto, não há nenhum outro recurso como Conexões Híbridas, que pode permitir que você acesse recursos em mais de 100 redes separadas. 

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
