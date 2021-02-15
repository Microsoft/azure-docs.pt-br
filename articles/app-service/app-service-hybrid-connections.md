---
title: Conexões Híbridas
description: Saiba como criar e usar conexões híbridas no serviço de Azure App para acessar recursos em redes diferentes.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 02/05/2020
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 1b3fc4a254c1157f2c2336e6360ba7621f31364d
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594224"
---
# <a name="azure-app-service-hybrid-connections"></a>Conexões Híbridas do Serviço de Aplicativo do Azure

Conexões Híbridas é um serviço no Azure e uma funcionalidade no Serviço de Aplicativo do Azure. Como um serviço, ele tem uso e recursos além daqueles usados no Serviço de Aplicativo do Azure. Para saber mais sobre Conexões Híbridas e seu uso fora do Serviço de Aplicativo do Azure, confira [Conexões Híbridas da Retransmissão do Azure][HCService].

No serviço de aplicativo, Conexões Híbridas pode ser usado para acessar recursos do aplicativo em qualquer rede que possa fazer chamadas de saída para o Azure pela porta 443. Conexões Híbridas fornece acesso de seu aplicativo para um ponto de extremidade TCP e não permite uma nova maneira de acessar seu aplicativo. Conforme usado no Serviço de Aplicativo, cada conexão híbrida se correlaciona com uma única combinação de host e de porta TCP. Isso permite que seus aplicativos acessem recursos em qualquer sistema operacional, desde que seja um ponto de extremidade TCP. O recurso Conexões Híbridas não sabe e nem se importa com o protocolo de aplicativo ou o que você está acessando. Ele simplesmente fornece acesso à rede.  

## <a name="how-it-works"></a>Como ele funciona ##
Conexões Híbridas requer que um agente de retransmissão seja implantado onde ele possa alcançar o ponto de extremidade desejado, bem como o Azure. O agente de retransmissão, Gerenciador de Conexões Híbridas (HCM), chama a retransmissão do Azure pela porta 443. No site do aplicativo Web, a infraestrutura do serviço de aplicativo também se conecta à retransmissão do Azure em nome do seu aplicativo. Por meio das conexões Unidas, seu aplicativo é capaz de acessar o ponto de extremidade desejado. A conexão usa o protocolo TLS 1.2 para segurança e as chaves de SAS (Assinatura de Acesso Compartilhado) para autenticação e autorização.    

![Diagrama de fluxo de alto nível de Conexão híbrida][1]

Quando seu aplicativo faz uma solicitação DNS que corresponde a um ponto de extremidade de Conexão Híbrida configurado, o tráfego TCP de saída será redirecionado por meio da Conexão Híbrida.  

> [!NOTE]
> Isso significa que você deve tentar sempre usar um nome DNS para sua Conexão Híbrida. Se em vez disso o ponto de extremidade usar um endereço IP, alguns softwares cliente não farão uma pesquisa de DNS. 
>

### <a name="app-service-hybrid-connection-benefits"></a>Benefícios da Conexão Híbrida do Serviço de Aplicativo ###

Há inúmeros benefícios para a funcionalidade de Conexões Híbridas, incluindo:

- Os aplicativos podem acessar sistemas e serviços locais com segurança.
- O recurso não requer um ponto de extremidade acessível pela Internet.
- É rápido e fácil de configurar. Nenhum gateway é necessário
- Cada Conexão Híbrida corresponde a uma única combinação de host:porta, que é útil para segurança.
- Ele geralmente não exigem aberturas do firewall. As conexões são todas de saídas por meio de portas padrão da Web.
- Como o recurso está no nível de rede, ele independente da linguagem usada pelo seu aplicativo e da tecnologia usada pelo ponto de extremidade.
- Ele pode ser usado para fornecer acesso em várias redes de um único aplicativo. 
- Há suporte no GA para aplicativos do Windows e aplicativos do Linux. Não há suporte para aplicativos de contêiner do Windows.

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Coisas que você não pode fazer com as Conexões Híbridas ###

Coisas que você não pode fazer com conexões híbridas incluem:

- Monte uma unidade.
- Use UDP.
- Acesse serviços baseados em TCP que usam portas dinâmicas, como o Modo Passivo FTP ou o Modo Passivo Estendido.
- Suporte LDAP, porque pode exigir UDP.
- Suporte ao Active Directory, porque você não pode ingressar no domínio de um funcionário do Serviço de Aplicativo. 

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Adicionar e criar Conexões Híbridas em seu aplicativo ##

Para criar uma Conexão Híbrida, acesse o [Portal do Azure][portal] e selecione seu aplicativo. Selecione **rede**  >  **configurar seus pontos de extremidade de conexão híbrida**. Aqui você pode ver as conexões híbridas que são configuradas para seu aplicativo.  

![Captura de tela da lista de Conexões Híbridas][2]

Para adicionar uma nova conexão híbrida, selecione **[+] Adicionar conexão híbrida**.  Você verá uma lista das conexões híbridas que você já criou. Para adicionar uma ou mais delas ao seu aplicativo, selecione as que você deseja e selecione **Adicionar Conexão Híbrida selecionada**.  

![Captura de tela do portal de Conexões Híbridas][3]

Se desejar criar uma nova Conexão Híbrida, clique em **Criar nova conexão híbrida**. Especifique: 

- Nome da Conexão híbrida.
- Nome do host do ponto de extremidade.
- Porta do ponto de extremidade.
- O namespace do Barramento de Serviço que você deseja usar.

![Captura de tela da caixa de diálogo Criar nova conexão híbrida][4]

Cada Conexão Híbrida está associada a um namespace do Barramento de Serviço e cada namespace do Barramento de Serviço está em uma região do Azure. É importante tentar usar um namespace do Barramento de Serviço na mesma região do seu aplicativo para evitar a latência de rede induzida.

Se você desejar remover sua Conexão Híbrida do seu aplicativo, clique com o botão direito do mouse nele e selecione **Desconectar**.  

Depois que uma Conexão Híbrida é adicionada ao seu aplicativo, basta clicar nela para ver seus detalhes. 

![Captura de tela dos detalhes das Conexões Híbridas][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Criar uma Conexão Híbrida no portal da Retransmissão do Azure ###

Além da experiência do portal de dentro do seu aplicativo, você também pode criar Conexões Híbridas no portal da Retransmissão do Azure. Para uma Conexão Híbrida ser usada pelo Serviço de Aplicativo, ele deverá:

* Exigir autorização do cliente.
* Ter um item de metadados denominado ponto de extremidade que contém a combinação de host:porta como o valor.

## <a name="hybrid-connections-and-app-service-plans"></a>Conexões Híbridas e Planos do Serviço de Aplicativo ##

As conexões híbridas do serviço de aplicativos estão disponíveis apenas nos SKUs de preços Básico, Padrão, Premium e Isolado. Há limites vinculados ao plano de preços.  

| Plano de preços | Número de Conexões Híbridas utilizáveis no plano |
|----|----|
| Básico | 5 por plano |
| Standard | 25 por plano |
| PremiumV2 | 200 por aplicativo |
| Isolado | 200 por aplicativo |

A UI do plano de serviço do aplicativo mostra quantas conexões híbridas estão sendo usadas e por quais aplicativos.  

![Captura de tela das propriedades do Plano do Serviço de Aplicativo][6]

Selecione a Conexão Híbrida para ver os detalhes. Você pode ver todas as informações exibidas no modo de exibição do aplicativo. Você também pode ver quantos outros aplicativos no mesmo plano estão usando essa Conexão Híbrida.

Há um limite no número de pontos de extremidade de Conexão Híbrida que pode ser usado em um Plano do Serviço de Aplicativo. Cada Conexão Híbrida usada, no entanto, pode ser usado em qualquer quantidade de aplicativos no plano. Por exemplo, uma única Conexão Híbrida usada em cinco aplicativos separados em um Plano do Serviço de Aplicativo conta como uma Conexão Híbrida.

### <a name="pricing"></a>Preços ###

Além de haver um requisito de SKU do plano de serviço de aplicativo, há um custo adicional para o uso de conexões híbridas. Há uma taxa para cada ouvinte usado por uma conexão híbrida. O ouvinte é o Gerenciador de Conexão híbrida. Se você tivesse cinco conexões híbridas suportadas por dois gerentes de conexão híbridos, seriam 10 ouvintes. Para obter mais informações, consulte [preços do Barramento de Serviço][sbpricing].

## <a name="hybrid-connection-manager"></a>Gerenciador de Conexão Híbrida ##

O recurso Conexões Híbridas exige um agente de retransmissão na rede que hospeda o seu ponto de extremidade de Conexão Híbrida. Esse agente de retransmissão é chamado de HCM (Gerenciador de Conexão Híbrida). Parabaixar o HCM, no seu aplicativo no [portal do Azure][portal], selecione **Rede** > **Configurar seus pontos de extremidade de Conexões Híbridas**.  

Essa ferramenta é executada no Windows Server 2012 e versões posteriores. O HCM é executado como um serviço e conecta-se de saída para retransmissão do Azure na porta 443.  

Depois de instalar o HCM, você pode executar HybridConnectionManagerUi.exe para usar a interface do usuário da ferramenta. Esse arquivo está no diretório de instalação do Gerenciador de Conexões Híbridas. No Windows 10, você também pode procurar por *Interface do usuário do Gerenciador de Conexões Híbridas* na sua caixa de pesquisa.  

![Captura de tela do Gerenciador de Conexões Híbridas][7]

Ao iniciar a interface do usuário do HCM, a primeira coisa que você vê é uma tabela que lista todas as Conexões Híbridas configuradas com esta instância do HCM. Se você quiser fazer alterações, faça autenticação no Azure primeiro. 

Para adicionar uma ou mais Conexões Híbridas a seu HCM:

1. Inicie a interface do usuário do HCM.
2. Selecione **Configurar outra Conexão Híbrida**.
![Captura de tela de Configurar novas Conexões Híbridas][8]

1. Entre com sua conta do Azure para colocar seu Conexões Híbridas disponível com suas assinaturas. A HCM não continua a usar sua conta do Azure além disso. 
1. Escolha uma assinatura.
1. Selecione as Conexões Híbridas que você deseja que o HCM retransmita.
![Captura de tela de Conexões Híbridas][9]

1. Selecione **Salvar**.

Agora você pode ver as Conexões Híbridas adicionadas. Você também pode selecionar a Conexão híbrida configurada para ver os detalhes.

![Captura de tela de detalhes de Conexão Híbrida][10]

Para dar suporte às Conexões Híbridas com que ele está configurado, o HCM requer:

- Acesso TCP ao Azure pela porta 443.
- Acesso TCP ao ponto de extremidade da Conexão Híbrida.
- A capacidade de fazer pesquisas de DNS no host do ponto de extremidade e no namespace do Barramento de Serviço.

> [!NOTE]
> A Retransmissão do Azure depende de soquetes da Web para ter conectividade. Essa funcionalidade só está disponível no Windows Server 2012 ou versão posterior. Por isso, o HCM não tem suporte em nada anterior ao Windows Server 2012.
>

### <a name="redundancy"></a>Redundância ###

Cada HCM pode ser compatível com várias Conexões Híbridas. Além disso, qualquer Conexão Híbrida determinada pode ser compatível com vários HCMs. O comportamento padrão é direcionar o tráfego pelos HCMs configurados para qualquer ponto de extremidade. Se desejar alta disponibilidade nas Conexões Híbridas da sua rede, execute vários HCMs em computadores separados. O algoritmo de distribuição de carga usado pelo serviço de retransmissão para distribuir o tráfego para os HCMs é uma atribuição aleatória. 

### <a name="manually-add-a-hybrid-connection"></a>Adicionar uma Conexão Híbrida manualmente ###

Para permitir que alguém fora da sua assinatura hospede uma instância HCM para uma determinada Conexão Híbrida, compartilhe a cadeia de conexão de gateway para a Conexão Híbrida. Você pode ver a cadeia de conexão do gateway nas propriedades da Conexão Híbrida no [Portal do Azure][portal]. Para usar essa cadeia de caracteres, selecione **Inserir Manualmente** no HCM e cole-o na cadeia de conexão de gateway.

![Adicionar uma Conexão Híbrida manualmente][11]

### <a name="upgrade"></a>Atualizar ###

Há atualizações periódicas no Gerenciador de Conexão Híbrida para corrigir problemas ou fornecer melhorias. Quando as atualizações são lançadas, um pop-up aparecerá na interface do usuário do HCM. A aplicação da atualização aplicará as mudanças e reiniciará o HCM. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Adicionando uma conexão híbrida ao seu aplicativo programaticamente ##

Há CLI do Azure suporte para Conexões Híbridas. Os comandos fornecidos operam no aplicativo e no nível do plano do serviço de aplicativo.  Os comandos no nível do aplicativo são:

```azurecli
az webapp hybrid-connection

Group
    az webapp hybrid-connection : Methods that list, add and remove hybrid-connections from webapps.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a hybrid-connection to a webapp.
    list   : List the hybrid-connections on a webapp.
    remove : Remove a hybrid-connection from a webapp.
```

Os comandos do plano do serviço de aplicativo permitem que você defina qual chave uma determinada conexão híbrida usará. Há duas chaves definidas em cada conexão híbrida, uma primária e uma secundária. Você pode optar por usar a chave primária ou secundária com os comandos abaixo. Isso permite que você alterne as chaves para quando quiser regenerar as chaves periodicamente. 

```azurecli
az appservice hybrid-connection --help

Group
    az appservice hybrid-connection : A method that sets the key a hybrid-connection uses.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    set-key : Set the key that all apps in an appservice plan use to connect to the hybrid-
                connections in that appservice plan.
```

## <a name="secure-your-hybrid-connections"></a>Proteger seu Conexões Híbridas ##

Uma conexão híbrida existente pode ser adicionada a outros aplicativos Web do serviço de aplicativo por qualquer usuário que tenha permissões suficientes na retransmissão do barramento de serviço do Azure subjacente. Isso significa que, se você precisar impedir que outras pessoas reutilizem essa mesma conexão híbrida (por exemplo, quando o recurso de destino é um serviço que não tem nenhuma medida de segurança adicional em vigor para impedir o acesso não autorizado), você deve bloquear o acesso à retransmissão do barramento de serviço do Azure.

Qualquer pessoa com `Reader` acesso à retransmissão poderá _Ver_ a conexão híbrida ao tentar adicioná-la ao aplicativo Web na portal do Azure, mas elas não poderão _adicioná_ -la, pois elas não têm as permissões para recuperar a cadeia de conexão que é usada para estabelecer a conexão de retransmissão. Para adicionar a conexão híbrida com êxito, elas devem ter a `listKeys` permissão ( `Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action` ). A `Contributor` função ou qualquer outra função que inclua essa permissão na retransmissão permitirá que os usuários usem a conexão híbrida e o adicionem aos seus próprios aplicativos Web.

## <a name="troubleshooting"></a>Solução de problemas ##

O status “Conectado” significa que pelo menos um HCM está configurado com essa Conexão Híbrida e é capaz de alcançar o Azure. Se o status da sua Conexão Híbrida não indica **Conectado**, sua Conexão Híbrida não está configurada em nenhum HCM com acesso ao Azure. Quando a HCM mostra **não conectado** , há algumas coisas a serem verificadas:

* O seu host tem acesso de saída ao Azure na porta 443? Você pode testar a partir do seu host HCM usando o comando do PowerShell *Test-NetConnection-P Port* 
* Seu HCM pode estar em um estado inadequado? Tente reiniciar o serviço local "serviço de Gerenciador de Conexões Híbridas do Azure".

Se seu status diz **conectado** , mas seu aplicativo não pode acessar o ponto de extremidade, então:

* Verifique se você está usando um nome DNS em sua conexão híbrida. Se você usar um endereço IP, a pesquisa de DNS de cliente necessária pode não acontecer. Se o cliente em execução no seu aplicativo Web não fizer uma pesquisa de DNS, a conexão híbrida não funcionará
* Verifique se o nome DNS usado em sua conexão híbrida pode ser resolvido a partir do host HCM. Verifique a resolução usando *nslookup EndpointDNSname* , em que EndpointDNSname é uma correspondência exata para o que é usado em sua definição de conexão híbrida.
* teste o acesso de seu host HCM ao seu ponto de extremidade usando o comando do PowerShell *Test-NetConnection EndpointDNSname-P Port*  se você não conseguir acessar o ponto de extremidade do seu host HCM, verifique os firewalls entre os dois hosts, incluindo quaisquer firewalls baseados em host no host de destino.

No serviço de aplicativo, a ferramenta de linha de comando **tcpping** pode ser chamada no console de ferramentas avançadas (kudu). Essa ferramenta pode informar se você tem acesso a um ponto de extremidade TCP, mas ela não diz se você tem acesso a um ponto de extremidade de Conexão Híbrida. Quando você usar a ferramenta no console em relação a um ponto de extremidade de Conexão Híbrida, apenas confirmará que ele usa uma combinação host:porta.  

Se você tiver um cliente de linha de comando para seu ponto de extremidade, poderá testar a conectividade no console do aplicativo. Por exemplo, você pode testar o acesso a pontos de extremidade do servidor Web usando a ondulação.


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
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: /azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: /azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
