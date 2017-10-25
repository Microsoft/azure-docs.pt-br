---
title: "Conexões Híbridas do Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Como criar e usar Conexões Híbridas para acessar recursos em redes diferentes"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2017
ms.author: ccompy
ms.openlocfilehash: fef9e7b280387934cb093f51b4c8aa134a3b87e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Conexões Híbridas do Serviço de Aplicativo do Azure #

## <a name="overview"></a>Visão geral ##

Conexões Híbridas é um serviço no Azure e uma funcionalidade no Serviço de Aplicativo do Azure.  Como um serviço, ele tem uso e recursos além daqueles usados no Serviço de Aplicativo do Azure.  Para saber mais sobre Conexões Híbridas e seu uso fora do Serviço de Aplicativo do Azure, você pode começar aqui, [Conexões Híbridas de Retransmissão do Azure][HCService]

No Serviço de Aplicativo do Azure, as conexões híbridas podem ser usadas para acessar recursos do aplicativo em outras redes. Ele fornece acesso DE seu aplicativo PARA um ponto de extremidade do aplicativo.  Ele não permite que uma funcionalidade alternativa acesse seu aplicativo.  Conforme usado no Serviço de Aplicativo, cada conexão híbrida se correlaciona com uma única combinação de host e a porta TCP.  Isso significa que o ponto de extremidade de conexão híbrida pode estar em qualquer sistema operacional e em qualquer aplicativo, desde que você esteja atingindo uma porta de escuta TCP. As conexões híbridas não sabem e nem se importam qual é o protocolo de aplicativo ou o que você está acessando.  Ele simplesmente fornece acesso à rede.  


## <a name="how-it-works"></a>Como ele funciona ##
A funcionalidade de conexões híbridas consiste em duas chamadas de saída para Retransmissão do Barramento de Serviço.  Há uma conexão de uma biblioteca no host em que o aplicativo é executado no Serviço de Aplicativo e, em seguida, há uma conexão do HCM (Gerenciador de Conexão Híbrida) à Retransmissão do Barramento de Serviço.  O HCM é um serviço de retransmissão que você implanta dentro da hospedagem de rede 

Por meio de duas conexões unidas, seu aplicativo tem um túnel TCP para uma combinação fixa host:porta no outro lado do HCM.  A conexão usa a TLS 1.2 para segurança e chaves SAS para autenticação/autorização.    

![][1]

Quando seu aplicativo faz uma solicitação DNS que corresponde a um ponto de extremidade de Conexão Híbrida configurado, o tráfego TCP de saída será redirecionado pela conexão híbrida.  

> [!NOTE]
> Isso significa que você deve tentar sempre usar um nome DNS para sua Conexão Híbrida.  Se em vez disso o ponto de extremidade usar um endereço IP, alguns softwares cliente não farão uma pesquisa de DNS.
>
>

Há dois tipos de conexões híbridas: as novas conexões híbridas, que são oferecidas como um serviço de Retransmissão do Azure, e as Conexões Híbridas do BizTalk, que são mais antigas.  As Conexões híbridas do BizTalk mais antigas são denominadas Conexões Híbridas Clássicas no portal.  Há mais informações posteriormente neste documento sobre elas.

### <a name="app-service-hybrid-connection-benefits"></a>Benefícios das conexões híbridas do Serviço de Aplicativo ###

Há uma série de benefícios para a funcionalidade de conexões híbridas, incluindo

- Os aplicativos podem acessar serviços e sistemas locais com segurança
- a funcionalidade não requer um ponto de extremidade acessível da Internet
- é rápido e fácil de configurar  
- cada conexão híbrida corresponde a uma única combinação de host: porta, o que é um aspecto de segurança excelente
- ele normalmente não exige buracos de firewall, já que a saída de todas as conexões ocorre por portas da Web padrão
- já que a funcionalidade é no nível de rede, isso também significa que ele independe do idioma usado pelo seu aplicativo e da tecnologia usada pelo ponto de extremidade
- ele pode ser usado para fornecer acesso em várias redes de um único aplicativo 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Coisas que você não pode fazer com as Conexões Híbridas ###

Há algumas coisas que você não pode fazer com as conexões híbridas e elas incluem:

- montagem de unidade
- usar UDP
- acessar serviços baseados em TCP que usam portas dinâmicas como um Modo passivo de FTP ou Modo passivo estendido
- suporte a LDAP, já que isso às vezes requer UDP
- suporte ao Active Directory

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>Adicionar e criar uma Conexão Híbrida em seu aplicativo ##

As Conexões Híbridas podem ser criadas por meio do portal do aplicativo ou do portal do serviço de Conexão Híbrida.  É altamente recomendável que você use o portal do aplicativo para criar as Conexões Híbridas que você deseja usar com seu aplicativo.  Para criar uma Conexão Híbrida, vá para o [Portal do Azure][portal] e acesse a interface do usuário para seu aplicativo.  Selecione **Rede > Configurar seus pontos de extremidade de conexão híbrida**.  Daqui, você pode ver as Conexões Híbridas configuradas com seu aplicativo  

![][2]

Para adicionar uma nova Conexão híbrida, clique em Adicionar Conexão Híbrida.  A interface do usuário lista as Conexões Híbridas que você já criou.  Para adicionar uma ou mais delas ao seu aplicativo, clique naqueles que deseja e pressione **Adicionar a conexão híbrida selecionada**.  

![][3]

Se você quiser criar uma nova Conexão Híbrida, clique em **criar nova conexão híbrida**.  Aqui você especifica o: 

- nome do ponto de extremidade
- nome do host do ponto de extremidade
- porta do ponto de extremidade
- namespace do barramento de serviço que você deseja usar

![][4]

Cada conexão híbrida é associada a um namespace do barramento de serviço e cada namespace do barramento de serviço está em uma região do Azure.  É importante tentar usar um namespace do barramento de serviço na mesma região do seu aplicativo para evitar latência de rede induzida.

Se você quiser remover sua conexão híbrida do seu aplicativo, clique com o botão direito do mouse nele e selecione **Desconectar**.  

Depois que uma conexão híbrida é adicionada ao seu aplicativo Web, você pode apenas clicar sobre ela para ver seus detalhes.  

![][5]

## <a name="hybrid-connections-and-app-service-plans"></a>Conexões Híbridas e Planos de Serviço de Aplicativo ##

As únicas conexões híbridas que você pode estabelecer agora são as novas conexões híbridas.  Eles só estão disponíveis em SKUs de preço Básico, Standard, Premium e Isolado.  Há limites vinculados ao plano de preços.  

| Plano de Preços | Número de conexões híbridas utilizáveis no plano |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolado | 200 |

Já que há restrições do Plano do Serviço de Aplicativo, também há uma interface do usuário no Plano do Serviço de Aplicativo que mostra quantas conexões híbridas estão sendo usadas e por quais aplicativos.  

![][6]

Assim como ocorre com o modo de exibição do aplicativo, você pode ver detalhes sobre sua conexão híbrida clicando nela.  Nas propriedades mostradas aqui, você pode ver todas as informações que você tinha no modo de exibição do aplicativo, mas também pode ver quantos outros aplicativos no mesmo Plano do Serviço de Aplicativo estão usando essa conexão híbrida.

![][7]

Embora haja um limite no número de pontos de extremidade de conexão híbrida que podem ser usados em um Plano do Serviço de Aplicativo, cada conexão híbrida usada pode ser usada entre qualquer número de aplicativos nesse Plano do Serviço de Aplicativo.  Isso quer dizer que, se eu tivesse uma conexão híbrida que usasse em cinco aplicativos separados no meu Plano do Serviço de Aplicativo, trataria-se ainda de apenas uma conexão híbrida.

Há um custo adicional para conexões híbridas que vai além de apenas serem utilizáveis em um SKU Básico, Standard, Premium ou Isolado.  Para obter detalhes sobre preços de conexão híbrida, veja aqui: [Preços do Barramento de Serviço][sbpricing].

## <a name="hybrid-connection-manager"></a>Gerenciador de Conexão Híbrida ##

Para que as conexões híbridas funcionem, você precisa de um agente de retransmissão na rede que hospede o seu ponto de extremidade de conexão híbrida.  Esse agente de retransmissão é chamado de HCM (Gerenciador de Conexão Híbrida).  Essa ferramenta pode ser baixada da interface do usuário **Rede > Configurar seus pontos de extremidade de conexão híbrida** disponível no seu aplicativo no [Portal do Azure][portal].  

Essa ferramenta é executada no Windows Server 2008 R2 e versões posteriores do Windows.  Uma vez instalado, o HCM executa como um serviço.  Esse serviço se conecta à retransmissão do Barramento de Serviço do Azure com base nos pontos de extremidade configurados.  As saída das conexões provenientes do HCM destinam-se às portas 80 e 443.    

A HCM tem uma interface do usuário para configurá-la.  Após o HCM ser instalado, você poderá exibir a interface do usuário executando o HybridConnectionManagerUi.exe que fica no diretório de instalação do Gerenciador de Conexão Híbrida.  Ela também pode ser facilmente acessada no Windows 10 digitando *Interface do usuário do Gerenciador de Conexões Híbridas* na caixa de pesquisa.  

Quando a interface do usuário do HCM é iniciada, a primeira coisa que você vê é uma tabela que lista todas as conexões híbridas configuradas com esta instância do HCM.  Se você quiser fazer alterações, você precisará autenticar com o Azure. 

Para adicionar uma ou mais Conexões Híbridas a seu HCM:

1. Inicie a interface do usuário do HCM
1. Clique em configurar outra conexão híbrida ![][8]

1. Entre com sua conta do Azure
1. Escolha uma assinatura
1. Clique nas conexões híbridas que você deseja que este HCM retransmita ![][9]

1. Clique em Salvar

Neste ponto, você verá as conexões híbridas que você adicionou.  Você também pode clicar na conexão híbrida configurada e ver detalhes sobre ela.

![][10]

Para que o HCM possa dar suporte às conexões híbridas com as quais ele é configurado, ele precisará de:

- Acesso TCP ao Azure nas portas 80 e 443
- Acesso TCP ao ponto de extremidade de conexão híbrida
- capacidade de fazer pesquisas de DNS no host do ponto de extremidade e no namespace do Barramento de Serviço do Azure

O HCM dá suporte tanto às novas conexões híbridas quanto às conexões híbridas do BizTalk mais antigas.

### <a name="redundancy"></a>Redundância ###

Cada HCM pode dar suporte a várias conexões híbridas.  Além disso, qualquer determinada conexão híbrida pode ter suporte de vários HCMs.  O comportamento padrão é transferir o tráfego pelo método round robin pelos HCMs configurados para qualquer determinado ponto de extremidade.  Se desejar alta disponibilidade nas conexões híbridas da rede, basta instanciar vários HCMs em computadores separados. 

### <a name="manually-adding-a-hybrid-connection"></a>Adicionar uma conexão híbrida manualmente ###

Se desejar que alguém fora da sua assinatura hospede uma instância HCM para uma determinada conexão híbrida, você poderá compartilhar com eles a cadeia de conexão de gateway para a conexão híbrida.  Você pode ver isso nas propriedades de uma conexão híbrida no [Portal do Azure][portal]. Para usar essa cadeia de caracteres, clique no botão **Configurar manualmente** no HCM e cole-o na cadeia de conexão de gateway.


## <a name="troubleshooting"></a>Solucionar problemas ##

Quando sua conexão híbrida é configurada com um aplicativo em execução e há pelo menos um HCM com essa conexão híbrida configurada, o status indicará **Conectado** no portal.  Se ele não informa **Conectado**, isso significa que seu aplicativo está inativo ou o HCM não pode realizar uma conexão de saída para o Azure nas portas 80 ou 443.  

O principal motivo pelo qual os clientes não podem se conectar ao ponto de extremidade é porque o ponto de extremidade foi especificado usando um endereço IP em vez de um nome DNS.  Se seu aplicativo não puder acessar o ponto de extremidade desejado e você tiver usado um endereço IP, mude e passe a usar um nome DNS válido no host em que o HCM está em execução.  Outros itens a serem verificados são que o nome DNS seja resolvido corretamente no host em que a HCM está em execução e que haja conectividade do host em que a HCM está em execução para o ponto de extremidade de conexão híbrida.  

Há uma ferramenta no Serviço de Aplicativo que pode ser invocada por meio do console, chamada tcpping.  Essa ferramenta pode informar se você tem acesso a um ponto de extremidade TCP, mas não lhe diz se você tem acesso a um ponto de extremidade de conexão híbrida.  Quando usado no console em relação a um ponto de extremidade de conexão híbrida, um ping bem-sucedido apenas informará que você tem uma conexão híbrida configurada para o aplicativo que usa essa combinação de host:porta.  

## <a name="biztalk-hybrid-connections"></a>Conexões Híbridas do BizTalk ##

A funcionalidade Conexões Híbridas antiga do BizTalk foi encerrada no que se refere a criações futuras de Conexões Híbridas do BizTalk.  Você pode continuar usando as Conexões Híbridas do BizTalk preexistentes com seus aplicativos, mas deve migrar para o novo serviço.  Entre os benefícios no novo serviço em relação à versão do BizTalk, estão:

- nenhuma conta adicional do BizTalk é necessária
- O TLS é 1.2 em vez do 1.0, o qual é usado nas Conexões Híbridas do BizTalk
- A comunicação é pelas portas 80 e 443 e usa um nome DNS para acessar o Azure, em vez de usar endereços IP e uma variedade de outras portas.  

Para adicionar uma Conexão Híbrida do BizTalk ao seu aplicativo, vá para seu aplicativo no [Portal do Azure][portal] e clique em **Rede > Configurar seus pontos de extremidade de conexão híbrida**.  Na tabela de conexões híbridas clássicas, clique em **Adicionar conexão híbrida clássica**.  Daqui em diante, você verá uma lista das conexões híbridas do BizTalk.  


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/

