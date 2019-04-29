---
title: Considerações de rede com um Ambiente do Serviço de Aplicativo – Azure
description: Explica o tráfego de rede do ASE e como definir NSGs e UDRs com seu ASE
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 73175b326c25d5d9a78155d0d9d888b655da1bfd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61226624"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Considerações sobre a rede para um Ambiente do Serviço de Aplicativo #

## <a name="overview"></a>Visão geral ##

 O [Ambiente do Serviço de Aplicativo][Intro] do Azure é uma implantação do Serviço de Aplicativo do Azure em uma sub-rede da sua VNet (VNet) do Azure. Há dois tipos de implantação para um ASE (ambiente de Serviço de Aplicativo):

- **ASE externo**: expõe os aplicativos ASE hospedados em um endereço IP acessado pela Internet. Para saber mais, confira [Criar um ASE externo][MakeExternalASE].
- **ILB ASE**: expõe os aplicativos hospedados do ASE em um endereço IP dentro da sua VNet. O ponto de extremidade interno é um ILB (balanceador de carga interno) e, por isso, ele é chamado de ASE do ILB. Para saber mais, confira [Criar e usar um ASE ILB][MakeILBASE].

Há duas versões do Ambiente do Serviço de Aplicativo: ASEv1 e ASEv2. Para saber mais sobre o ASEv1, consulte [Introdução ao Ambiente do Serviço de Aplicativo v1][ASEv1Intro]. O ASEv1 pode ser implantado em uma VNet clássica ou do Resource Manager. Um ASEv2 só pode ser implantado em uma VNet do Resource Manager.

Todas as chamadas de um ASE que vão para a Internet deixam a rede virtual por meio de um VIP atribuído ao ASE. O IP público desse VIP é o IP de origem de todas as chamadas do ASE que vão para a Internet. Se os aplicativos em seu ASE fizerem chamadas a recursos na sua VNet ou por uma VPN, o IP de origem será um dos IPs na sub-rede usada pelo ASE. Como o ASE é na VNet, também pode acessar recursos na VNet sem nenhuma configuração adicional. Se a VNet estiver conectada à sua rede local, os aplicativos no ASE também terão acesso aos recursos de lá sem configuração adicional.

![ASE externo][1] 

Se você tiver um ASE externo, o VIP público também será o ponto de extremidade para o qual seus aplicativos ASE resolvem:

* HTTP/S. 
* FTP/S. 
* Implantação da Web.
* Depuração remota.

![ILB ASE][2]

Se você tiver um ASE ILB, o endereço do ILB será o ponto de extremidade para HTTP/S, FTP/S, implantação da Web e depuração remota.

As portas de acesso normais do aplicativo são:

| Uso | Da | Para |
|----------|---------|-------------|
|  HTTP/HTTPS  | Configurável pelo usuário |  80, 443 |
|  FTP/FTPS    | Configurável pelo usuário |  21, 990, 10001-10020 |
|  Depuração remota no Visual Studio  |  Configurável pelo usuário |  4020, 4022, 4024 |
|  Implantar serviço Web | Configurável pelo usuário | 8172 |

Isso será verdadeiro se você estiver em um ASE externo ou em um ASE ILB. Se você estiver em um ASE externo, chegará a essas portas no VIP público. Se você estiver em um ASE ILB, chegará a essas portas no ILB. Se você bloquear a porta 443, poderá haver um efeito sobre alguns recursos expostos no portal. Para saber mais, confira [Dependências do portal](#portaldep).

## <a name="ase-subnet-size"></a>Tamanho da sub-rede ASE ##

O tamanho da sub-rede usada para hospedar um ASE não pode ser alterado depois da implantação do ASE.  O ASE usa um endereço para cada função de infraestrutura, bem como para cada instância de plano do serviço de aplicativo isolado.  Além disso, existem 5 endereços usados pela rede do Azure para cada sub-rede criada.  Um ASE sem nenhum plano do serviço de aplicativo usará 12 endereços antes de criar um aplicativo.  Se for um ASE ILB, ele usará 13 endereços antes de você criar um aplicativo no ASE. Conforme você expande seu ASE, funções de infraestrutura são adicionadas a cada múltiplo de 15 e de 20 de suas instâncias do Plano do Serviço de Aplicativo.

   > [!NOTE]
   > Nada mais pode existir na sub-rede além do ASE. Escolha um espaço de endereço que possibilite crescimento futuro. Você não poderá alterar essa configuração mais tarde. É recomendável um tamanho de `/24` com 256 endereços.

Quando você escala ou reduz verticalmente, são adicionadas novas funções de tamanho apropriado e, em seguida, suas cargas de trabalho são migradas do tamanho atual para o tamanho de destino. As VMs originais são removidas somente depois que seus aplicativos são migrados. Isso significa que, se você tivesse um ASE com 100 instâncias de ASP, haveria um período em que você precisaria duplicar o número de VMs.  É por esse motivo que recomendamos o uso de um “/ 24” para acomodar as alterações que poderão ser necessárias.  

## <a name="ase-dependencies"></a>Dependências do ASE ##

### <a name="ase-inbound-dependencies"></a>Dependências de entrada do ASE ###

As dependências de acesso de entrada do ASE são:

| Uso | Da | Para |
|-----|------|----|
| Gerenciamento | Endereços de gerenciamento do Serviço de Aplicativo | Sub-rede ASE: 454, 455 |
|  Comunicação interna ASE | Sub-rede ASE: Todas as portas | Sub-rede ASE: Todas as portas
|  Permitir a entrada do Azure Load Balancer | Azure Load Balancer | Sub-rede ASE: Todas as portas
|  Endereços IP atribuídos ao aplicativo | Endereços atribuído de aplicativo | Sub-rede ASE: Todas as portas

O tráfego de gerenciamento de entrada fornece o comando e o controle do ASE, além de monitoramento do sistema. Os endereços de origem desse tráfego são listados no documento [Endereços de gerenciamento do ASE endereços][ASEManagement]. A configuração de segurança de rede deve permitir o acesso de todos os IPs nas portas 454 e 455. Se você bloquear o acesso desses endereços, seu ASE deixará de ser íntegro e será suspenso.

Dentro da sub-rede ASE há muitas portas usadas para a comunicação interna do componente e elas podem mudar.  Isso exige que todas as portas na sub-rede ASE estejam acessíveis por meio da sub-rede ASE. 

Para a comunicação entre o balanceador de carga do Azure e a sub-rede do ASE, as portas mínimas que precisam ser abertas são 454 e 455 de 16001. A porta 16001 é usada para tráfego de keep alive entre o balanceador de carga e o ASE. Se você estiver usando um ASE ILB, poderá restringir o tráfego somente para as portas 454, 455, 16001.  Se você estiver usando um ASE externo, então precisará levar em conta as portas de acesso do aplicativo normal.  Se você estiver usando endereços atribuídos de aplicativo, será necessário abri-los para todas as portas.  Quando um endereço é atribuído a um aplicativo específico, o balanceador de carga usa as portas que não são conhecidas com antecedência para enviar o tráfego HTTP e HTTPS para o ASE.

Se você estiver usando os endereços IP atribuídos pelo aplicativo, será necessário permitir o tráfego dos IPs atribuídos a seus aplicativos à sub-rede do ASE.

O tráfego TCP que chega nas portas 454 e 455 deve voltar do mesmo VIP ou você terá um problema de roteamento assimétrico. 

### <a name="ase-outbound-dependencies"></a>Dependências de saída do ASE ###

Para acesso de saída, um ASE depende de vários sistemas externos. Muitas dessas dependências do sistema são definidas com nomes DNS e não são mapeadas para um conjunto fixo de endereços IP. Assim, o ASE requer acesso de saída da sub-rede do ASE para todos os IPs externos em uma variedade de portas. 

A lista completa das dependências de saída está no documento que descreve [Como bloquear o tráfego de saída do Ambiente do Serviço de Aplicativo](./firewall-integration.md). Se perder o acesso a suas dependências, o ASE deixará de funcionar. Quando isso acontece por tempo suficiente, o ASE fica suspenso. 

### <a name="customer-dns"></a>DNS do cliente ###

Se a VNet for configurada com um servidor DNS definido pelo cliente, as cargas de trabalho de locatário usarão esse servidor DNS. O ASE ainda precisa se comunicar com o DNS do Azure para fins de gerenciamento. 

Se a VNet for configurada com um DNS de cliente no outro lado de uma VPN, o servidor DNS deverá poder ser acessado da sub-rede que contém o ASE.

Para testar a resolução do seu aplicativo Web, você pode usar o comando de console *nameresolver*. Vá até a janela de depuração no site do SCM de seu aplicativo ou vá até o aplicativo no portal e selecione o console. No prompt de shell, você pode emitir o comando *nameresolver* junto com o endereço que deseja pesquisar. O resultado obtido é o mesmo que o seu aplicativo obteria ao fazer a mesma pesquisa. Se usar nslookup, você fará uma pesquisa usando o DNS do Azure.

Se alterar a configuração de DNS da VNet em que seu ASE se encontra, você precisará reiniciar o ASE. Para evitar a reinicialização do ASE, é altamente recomendável que você defina as configurações de DNS da VNet antes de criar o ASE.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Dependências do portal ##

Além das dependências funcionais do ASE, há alguns itens adicionais relacionados à experiência do portal. Alguns dos recursos no portal do Azure dependem do acesso direto ao _site do SCM_. Para cada aplicativo no Serviço de Aplicativo do Azure, há duas URLs. A primeira URL é para acessar seu aplicativo. A segunda URL é para acessar o site do SCM, que também é chamado de _console Kudu_. Recursos que usam o site do SCM incluem:

-   Trabalhos da Web
-   Funções
-   Streaming de log
-   Kudu
-   Extensões
-   Gerenciador de Processos
-   Console

Quando você usa uma ASE ILB, o site SCM não é acessível pela Internet de fora da VNet. Quando o aplicativo estiver hospedado em um ILB ASE, algumas funcionalidades não funcionarão no portal.  

Muitos desses recursos que dependem do site do SCM também estão disponíveis diretamente no console Kudu. Você pode se conectar a ele diretamente, em vez de por meio do portal. Se o seu aplicativo estiver hospedado em um ASE ILB, use suas credenciais de publicação para entrar. A URL para acessar o site do SCM de um aplicativo hospedado em um ASE ILB tem o seguinte formato: 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

Se seu ASE ILB for o nome de domínio *contoso.net* e o nome do aplicativo for *testapp*, o aplicativo será alcançado em *testapp.contoso.net*. O site SCM que o acompanha é alcançado em *testapp.scm.contoso.net*.

## <a name="functions-and-web-jobs"></a>Funções e trabalhos Web ##

As Funções e os trabalhos Web dependem do site do SCM, mas há suporte para eles para uso no portal, mesmo que os aplicativos estiverem em um ILB ASE, desde que o navegador possa acessar o site do SCM.  Se você estiver usando um certificado autoassinado com o ILB ASE, precisará habilitar o navegador para que ele confie no certificado.  Para o IE e o Microsoft Edge, isso significa que o certificado deve estar no repositório de confiança do computador.  Se você estiver usando o Chrome, isso significa que você aceitou o certificado no navegador anteriormente supostamente visitando o site do SCM diretamente.  A melhor solução é usar um certificado comercial que está na cadeia de confiança do navegador.  

## <a name="ase-ip-addresses"></a>Endereços IP do ASE ##

Um ASE tem alguns endereços IP para reconhecer. Eles são:

- **Endereço IP público de entrada:** usado para o tráfego de aplicativo em um ASE externo e o tráfego de gerenciamento em um ASE externo e em um ASE ILB.
- **IP público de saída**: usado como o IP “de” das conexões de saída do ASE que saem da VNet, que não são roteadas por uma VPN.
- **Endereço IP do ILB**: se você usa um ASE ILB.
- **Endereços SSL com base em IP atribuídos ao aplicativo**: possível somente com um ASE externo e quando o SSL baseado em IP está configurado.

Todos esses endereços IP ficam visíveis em um ASEv2 no portal do Azure, na interface do usuário do ASE. Se você tiver um ASE ILB, o IP para o ILB estará listado.

   > [!NOTE]
   > Esses endereços IP não serão alterados desde que o ASE permanece em execução.  Se o ASE for suspenso e restaurado, os endereços usados por ele serão alterados. Normalmente, um ASE será suspenso se você bloquear o acesso do gerenciamento de entrada ou bloquear o acesso a uma dependência do ASE. 

![Endereços IP][3]

### <a name="app-assigned-ip-addresses"></a>Endereços IP atribuídos ao aplicativo ###

Com um ASE externo, você pode atribuir endereços IP a aplicativos individuais. Você não pode fazer isso com um ASE ILB. Para saber mais sobre como configurar seu aplicativo para que tenha seu próprio endereço IP, confira [Associar um certificado SSL personalizado existente ao Serviço de Aplicativo do Azure](../app-service-web-tutorial-custom-ssl.md).

Quando um aplicativo tem seu próprio endereço SSL com base em IP, o ASE reserva duas portas para mapear para esse endereço IP. Uma porta é para o tráfego HTTP e a outra porta é para HTTPS. Essas portas estão listadas na interface de usuário do ASE na seção de endereços IP. O tráfego deverá ser capaz de alcançar essas portas do VIP ou os aplicativos ficarão inacessíveis. É importante lembrar-se desse requisito ao configurar NSGs (grupos de segurança de rede).

## <a name="network-security-groups"></a>Grupos de segurança de rede ##

Os [Grupos de Segurança de Rede][NSGs] permitem controlar o acesso de rede em uma VNet. Quando você usa o portal, há uma regra de negação implícita a prioridade mais baixa para negar tudo. O que você cria são suas regras de permissão.

Em um ASE, você não tem acesso às VMs usadas para hospedar o ASE em si. Elas estão em uma assinatura gerenciada pela Microsoft. Se você quiser restringir o acesso aos aplicativos no ASE, defina NSGs na sub-rede do ASE. Ao fazer isso, preste muita atenção às dependências do ASE. Se você bloquear qualquer dependência, o ASE deixará de funcionar.

Os NSGs podem ser configurados por meio do portal do Azure ou por meio do PowerShell. As informações aqui mostram o portal do Azure. Crie e gerencie os NSGs no portal como um recurso de nível superior em **Rede**.

Quando os requisitos de entrada e saída são levados em conta, os NSGs devem ser semelhantes aos NSGs mostrados neste exemplo. O intervalo de endereços da VNet é _192.168.250.0/23_ e a sub-rede em que o ASE está é _192.168.251.128/25_.

Os primeiro dois requisitos de entrada para que o ASE funcione são mostrados no alto da lista neste exemplo. Eles habilitam o gerenciamento do ASE e permitem que o ASE se comunique com ele mesmo. As outras entradas todas podem ser configuradas pelo locatário e podem controlar o acesso de rede aos aplicativos hospedados pelo ASE. 

![Regras de segurança de entrada][4]

Uma regra padrão permite que os IPs na VNet comuniquem-e com a sub-rede do ASE. Outra regra padrão permite que o balanceador de carga, também conhecido como o VIP público, comunique-se com o ASE. Para ver as regras padrão, selecione **Regras padrão** ao lado do ícone **Adicionar**. Se você colocar uma regra para negar tudo após as regras do NSG mostradas, você impedirá o tráfego entre o VIP e o ASE. Para evitar o tráfego proveniente de dentro da VNet, adicione suas próprias regras para permitir a entrada. Usar uma fonte igual ao AzureLoadBalancer com um destino de **Qualquer** e um intervalo de portas de **\***. Como a regra NSG é aplicada à sub-rede do ASE, você não precisa ser específico quanto ao destino.

Se você tiver atribuído um endereço IP ao seu aplicativo, mantenha as portas abertas. Para ver as portas, selecione **Ambiente de Serviço de Aplicativo** > **Endereços IP**.  

Todos os itens mostrados nas regras de saída a seguir são necessários, exceto pelo último item. Isso permite o acesso de rede às dependências do ASE que foram observadas anteriormente neste artigo. Se você bloquear qualquer uma delas, o ASE deixará de funcionar. O último item na lista permite que seu ASE se comunique com outros recursos em sua VNet.

![Regras de segurança de saída][5]

Depois que seus NSGs estiverem definidos, atribua-os à sub-rede em que está seu ASE. Caso não se lembre da sub-rede ou Vnet ASE, você poderá visualizá-los a partir da página do portal do ASE. Para atribuir o NSG à sua sub-rede, vá para a sub-rede da interface do usuário e selecione o NSG.

## <a name="routes"></a>Rotas ##

O túnel forçado é quando você define rotas na rede virtual para que o tráfego de saída não vá diretamente para a Internet, mas em outro lugar, como um gateway do ExpressRoute ou uma solução de virtualização.  Se for necessário configurar o ASE dessa maneira, leia o documento em [Configurar o ambiente do Serviço de Aplicativo com túnel forçado][forcedtunnel].  Esse documento informará as opções disponíveis para trabalhar com o ExpressRoute e o túnel forçado.

Quando você cria um ASE no portal, também criamos um conjunto de tabelas de rotas na sub-rede que é criada com o ASE.  Essas rotas simplesmente informam para enviar o tráfego de saída diretamente para a Internet.  
Para criar as mesmas rotas manualmente, siga estas etapas:

1. Vá para o portal do Azure. Selecione **Rede** > **Tabelas de Rota**.

2. Crie uma nova tabela de rota na mesma região da sua VNet.

3. Dentro da interface do usuário da tabela de rota, selecione **Rotas** > **Adicionar**.

4. Defina o **Tipo do próximo salto** como **Internet** e o **Prefixo de endereço** como **0.0.0.0/0**. Clique em **Salvar**.

    Então você verá algo semelhante ao que se segue:

    ![Rotas funcionais][6]

5. Depois de criar a nova tabela de rota, vá para a sub-rede que contém seu ASE. Selecione sua tabela de rota na lista do portal. Depois de salvar a alteração, você deve ver os NSGs e as rotas anotadas com a sua sub-rede.

    ![NSGs e rotas][7]

## <a name="service-endpoints"></a>Pontos de extremidade de serviço ##

Os Pontos de Extremidade de Serviço permitem restringir o acesso aos serviços de vários locatários para um conjunto de sub-redes e redes virtuais do Azure. Você pode saber mais sobre os Pontos de Extremidade de Serviço na documentação [Pontos de Extremidade de Serviço de Rede Virtual][serviceendpoints]. 

Quando você habilita Pontos de Extremidade de Serviço em um recurso, existem rotas criadas com prioridade mais alta que todas as outras rotas. Se você usar Pontos de Extremidade de Serviço com um ASE em túnel forçado, o SQL do Azure e o tráfego de gerenciamento do Armazenamento do Azure não é forçado em túnel. 

Quando os Pontos de Extremidade de Serviço estão habilitados em uma sub-rede com uma instância do SQL do Azure, todas as instâncias do SQL do Azure conectadas à sub-rede devem ter os Pontos de Extremidade de Serviço habilitados. Se quiser acessar várias instâncias do SQL do Azure da mesma sub-rede, você não pode habilitar os Pontos de Extremidade de Serviço em uma instância do SQL do Azure e não em outra. O Armazenamento do Azure não possuem o mesmo comportamento do SQL do Azure. Ao habilitar os Pontos de Extremidade de Serviço com o Armazenamento do Azure, você bloqueia o acesso a esse recurso de sua sub-rede, mas ainda pode acessar outras contas de Armazenamento do Azure, mesmo se elas não tiverem os Pontos de Extremidade de Serviço habilitados.  

![Pontos de extremidade de serviço][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
