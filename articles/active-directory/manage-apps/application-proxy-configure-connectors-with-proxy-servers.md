---
title: Trabalhar com servidores proxy locais existentes e o Azure AD | Microsoft Docs
description: Cobre como trabalhar com os servidores proxy locais existentes.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e4b073a63b5b6bec565aed67bcaec7ed014261b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807866"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Trabalhar com servidores proxy locais existentes

Este artigo explica como configurar conectores de Proxy de aplicativo do Azure Active Directory (AD do Azure) para trabalhar com servidores de proxy de saída. Ele destina-se aos clientes com ambientes de rede que já têm proxies.

Começamos analisando este principais cenários de implantação:

* Configure os conectores para ignorar os proxies de saída locais.
* Configure os conectores para usar um proxy de saída para acessar o Proxy do Aplicativo do Azure AD.

Para saber mais sobre como funcionam os conectores, consulte [Noções básicas sobre conectores de proxy de aplicativo do Azure AD](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Ignorar proxies de saída

Conectores têm componentes subjacentes do sistema operacional que fazem solicitações de saída. Esses componentes automaticamente tentam localizar um servidor proxy na rede usando o WPAD (Serviço de Descoberta Automática de Proxy da Web).

Os componentes do SO tentam localizar um servidor proxy realizando uma pesquisa de DNS para wpad.domainsuffix. Se a pesquisa for resolvida no DNS, uma solicitação HTTP será feita para o endereço IP de wpad.dat. Essa solicitação se tornará o script de configuração do proxy em seu ambiente. O conector usará o script para selecionar um servidor proxy de saída. No entanto, o tráfego do conector ainda pode continuar interrompido devido às definições de configuração adicionais necessárias no proxy.

É possível configurar o conector para ignorar o proxy local de modo a garantir que ele use uma conectividade direta com os serviços do Azure. Isso é o que recomendamos (se sua política de rede permitir), porque significa que você terá uma menor configuração para manter.

Para desabilitar o uso do proxy de saída para o conector, edite o arquivo C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e adicione a seção *system.net* mostrada neste exemplo de código:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Para garantir que o serviço Atualizador do Conector também ignore o proxy, faça uma alteração semelhante no arquivo ApplicationProxyConnectorUpdaterService.exe.config file. O arquivo está localizado em C:\Program Files\Microsoft AAD App Proxy Connector Updater.

Faça cópias dos arquivos originais, caso você precise voltar para os arquivos .config padrão.

## <a name="use-the-outbound-proxy-server"></a>Usar o servidor proxy de saída

Como mencionado antes, alguns ambientes requerem que todo o tráfego de saída passe por um proxy de saída, sem exceção. Consequentemente, ignorar o proxy não é uma opção.

Você pode configurar que o tráfego do conector passe pelo proxy de saída, como mostrado no diagrama a seguir:

 ![Configurar o tráfego do conector para passar por um proxy de saída para o Proxy do Aplicativo do Azure AD](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Como consequência de ter apenas o tráfego de saída, não há necessidade de configurar o acesso de entrada por meio de firewalls.

> [!NOTE]
> O Proxy de Aplicativo não dá suporte à autenticação para outros proxies. As contas de serviço de rede do atualizador/conector devem ser capazes de se conectarem ao proxy sem serem desafiadas para autenticação.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Etapa 1: Configurar o conector e serviços relacionados para passar pelo proxy de saída

Se a WPAD estiver habilitada no ambiente e corretamente configurada, o conector descobrirá automaticamente o servidor proxy de saída e tentará usá-lo. No entanto, você pode configurar explicitamente o conector para passar por um proxy de saída.

Para fazer isso, edite o arquivo C: \ Arquivos de Programas \ Microsoft AAD App Proxy Connector \ ApplicationProxyConnectorService.exe.config e adicione a seção *system.net* mostrada neste exemplo de código. Altere *proxyserver:8080* para refletir o nome do servidor proxy local ou o endereço IP e a porta na qual ele está escutando. O valor deve ter o prefixo http://, mesmo se você estiver usando um endereço IP.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Em seguida, configure o serviço Atualizador do Conector para usar o proxy fazendo uma alteração semelhante no arquivo C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Etapa 2: Configurar o proxy para permitir o tráfego a partir do conector e que os serviços relacionados passem por ele

Há quatro aspectos a considerar no proxy de saída:

* Regras de saída do proxy
* Autenticação do proxy
* Portas do proxy
* Inspeção SSL

#### <a name="proxy-outbound-rules"></a>Regras de saída do proxy

Permita o acesso às seguintes URLs:

| URL | Como ele é usado |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Comunicação entre o conector e o serviço de nuvem do Proxy de Aplicativo |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | O Azure usa essas URLs para verificar os certificados |
| login.windows.net<br>login.microsoftonline.com | O conector usa essas URLs durante o processo de registro. |

Se seu firewall ou proxy permite que você configure listas de permissões de DNS, você pode permitir conexões com \*. msappproxy.net e \*. servicebus.windows.net. Caso contrário, você precisará permitir o acesso aos [intervalos IP do Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Os intervalos de IP são atualizados a cada semana.

Se você não puder permitir a conectividade pelo FQDN e precisar especificar intervalos IP, use estas opções:

* permitir o acesso de saída do conector para todos os destinos.
* Permitir o acesso de saída do conector para os [intervalos de IP do datacenter do Azure](https://www.microsoft.com//download/details.aspx?id=41653). O desafio de usar a lista de intervalos IP do datacenter do Azure é que ela é atualizada semanalmente. Você precisa implantar um processo para garantir que as regras de acesso sejam atualizadas de acordo. Usar apenas um subconjunto dos endereços IP pode interromper sua configuração.

#### <a name="proxy-authentication"></a>Autenticação do proxy

No momento, não há suporte à autenticação do proxy. Nossa recomendação atual é permitir acesso anônimo ao conector aos destinos da Internet.

#### <a name="proxy-ports"></a>Portas do proxy

O conector faz conexões de saída baseadas em SSL usando o método CONNECT. Basicamente, esse método configura um túnel pelo proxy de saída. Configure o servidor proxy para permitir o túnel para as portas 443 e 80.

> [!NOTE]
> Quando o Barramento de Serviço for executado por HTTPS, ele usará a porta 443. No entanto, por padrão, o Barramento de Serviço tenta as conexões TCP diretas e voltará para o HTTPS somente se a conectividade direta falhar.

#### <a name="ssl-inspection"></a>Inspeção SSL

Não use a inspeção SSL para o tráfego do conector, pois isso causa problemas no tráfego dele. O conector usa um certificado para autenticar o serviço de Proxy de Aplicativo e esse certificado pode ser perdido durante a inspeção SSL.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Solucionar problemas do proxy do conector e problemas de conectividade do serviço

Agora, você deve ver todo o tráfego fluindo pelo proxy. Se você tiver problemas, as seguintes informações de solução de problemas deverão ajudar.

A melhor maneira de identificar e solucionar os problemas de conectividade do conector é usar uma captura da rede ao iniciar o serviço do conector. Aqui estão algumas dicas rápidas sobre capturar e filtrar os rastreamentos de rede.

Você pode usar a ferramenta de monitoramento de sua preferência. Para este artigo, usamos o Microsoft Message Analyzer. Você pode [baixá-lo na Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Os exemplos a seguir são específicos para o Message Analyzer, mas os princípios podem ser aplicados a qualquer ferramenta de análise.

### <a name="take-a-capture-of-connector-traffic"></a>Fazer uma captura do tráfego do conector

Para a solução de problemas inicial, execute as seguintes etapas:

1. Em services.msc, pare o serviço Conector do Proxy de Aplicativo do Azure AD.

   ![Serviço Conector do Proxy de Aplicativo do Azure AD em services.msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Execute o Message Analyzer como um administrador.
1. Selecione **Iniciar rastreamento local**.
1. Inicie o serviço Conector do Proxy de Aplicativo do Azure AD.
1. Pare a captura de rede.

   ![Captura de tela mostra o botão de captura de rede de parada](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Verifique se o tráfego do conector ignorou proxies de saída

Se você configurou o conector de Proxy de Aplicativo para ignorar os servidores proxy e conectar-se diretamente ao serviço de Proxy de Aplicativo, você desejará pesquisar na captura de rede para tentativas de conexão TCP com falha.

Use o filtro do Message Analyzer para identificar essas tentativas. Insira `property.TCPSynRetransmit` na caixa de filtro e selecione **Aplicar**.

Um pacote SYN é o primeiro pacote enviado para estabelecer uma conexão TCP. Se o pacote não retornar uma resposta, o SYN fará uma nova tentativa. Você pode usar o filtro anterior para ver quaisquer SYNs retransmitidos. Assim, você pode verificar se eles correspondem ao tráfego relacionado a algum conector.

Se você espera que o conector faça conexões diretas com os serviços do Azure, as respostas SynRetransmit na porta 443 serão uma clara indicação de que há um problema de rede ou firewall.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Verifique se o tráfego do conector usa os proxies de saída

Se tiver configurado seu tráfego de conector de Proxy de Aplicativo para percorrer os servidores proxy, você desejará pesquisar conexões https com falha para o proxy.

Para filtrar a captura de rede para essas tentativas de conexão, insira `(https.Request or https.Response) and tcp.port==8080` no filtro do Message Analyzer, substituindo 8080 pela sua porta de serviço de proxy. Selecione **Aplicar** para ver os resultados do filtro.

O filtro anterior mostra apenas as solicitações e respostas HTTPs para/da porta do proxy. Você está procurando as solicitações CONNECT que mostram a comunicação com o servidor proxy. No caso de êxito, você obterá uma resposta HTTP OK (200).

Caso sejam exibidos outros códigos de resposta, como 407 ou 502, isso indica que o proxy está exigindo autenticação ou não está permitindo o tráfego por algum outro motivo. Neste ponto, você interage com a equipe de suporte do servidor proxy.

## <a name="next-steps"></a>Próximas etapas

* [Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-connectors.md)
* Se você tiver problemas de conectividade do conector, faça sua pergunta no [fórum do Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) ou crie um tíquete com nossa equipe de suporte.
