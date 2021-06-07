---
title: Trabalhar com servidores proxy locais existentes e Azure Active Directory
description: Aborda como trabalhar com servidores proxy locais existentes com o Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/07/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: 09a257c4b80fd796ac4e1e8203f00857d2d95eaf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259110"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Trabalhar com servidores proxy locais existentes

Este artigo explica como configurar conectores de Proxy de aplicativo do Azure Active Directory (AD do Azure) para trabalhar com servidores de proxy de saída. Ele destina-se aos clientes com ambientes de rede que já têm proxies.

Começamos analisando este principais cenários de implantação:

* Configure os conectores para ignorar os proxies de saída locais.
* Configure os conectores para usar um proxy de saída para acessar o Proxy do Aplicativo do Azure AD.
* Configure usando um proxy entre o conector e o aplicativo de back-end.

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
* Inspeção TLS

#### <a name="proxy-outbound-rules"></a>Regras de saída do proxy

Permita o acesso às seguintes URLs:

| URL | Porta |  Como ele é usado |
| --- | --- | --- |
| &ast;.msappproxy.net<br>&ast;.servicebus.windows.net | 443/HTTPS | Comunicação entre o conector e o serviço de nuvem do Proxy de Aplicativo |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP | O conector usa essas URLs para verificar certificados. |
| login.windows.net<br>Secure.aadcdn.microsoftonline p.com<br>&ast;.microsoftonline.com<br>&ast;.microsoftonline-p.com<br>&ast;.msauth.net<br>&ast;.msauthimages.net<br>&ast;.msecnd.net<br>&ast;.msftauth.net<br>&ast;.msftauthimages.net<br>&ast;.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com | 443/HTTPS | O conector usa essas URLs durante o processo de registro. |
| ctldl.windowsupdate.com | 80/HTTP | O conector usa essa URL durante o processo de registro. |

Se o firewall ou o proxy permitir a configuração de listas de permissões de DNS, você poderá permitir conexões a \*.msappproxy.net e \*.servicebus.windows.net.

Se você não puder permitir a conectividade pelo FQDN e precisar especificar intervalos IP, use estas opções:

* permitir o acesso de saída do conector para todos os destinos.
* Permitir o acesso de saída do conector para os intervalos de IP do datacenter do Azure. O desafio de usar a lista de intervalos IP do datacenter do Azure é que ela é atualizada semanalmente. Você precisa implantar um processo para garantir que as regras de acesso sejam atualizadas de acordo. Usar apenas um subconjunto dos endereços IP pode interromper sua configuração. Para baixar os intervalos de IP mais recentes do Data Center do Azure, navegue até [https://download.microsoft.com](https://download.microsoft.com) e pesquise por "intervalos de IP e marcas de serviço do Azure". Certifique-se de selecionar a nuvem relevante. Por exemplo, os intervalos de IP de nuvem pública podem ser encontrados com "intervalos de IP do Azure e marcas de serviço – nuvem pública". A nuvem do governo dos EUA pode ser encontrada pesquisando "intervalos de IP do Azure e marcas de serviço – nuvem do governo dos EUA".

#### <a name="proxy-authentication"></a>Autenticação do proxy

No momento, não há suporte à autenticação do proxy. Nossa recomendação atual é permitir acesso anônimo ao conector aos destinos da Internet.

#### <a name="proxy-ports"></a>Portas do proxy

O conector faz conexões de saída baseadas em TLS usando o método CONNECT. Basicamente, esse método configura um túnel pelo proxy de saída. Configure o servidor proxy para permitir o túnel para as portas 443 e 80.

> [!NOTE]
> Quando o Barramento de Serviço for executado por HTTPS, ele usará a porta 443. No entanto, por padrão, o Barramento de Serviço tenta as conexões TCP diretas e voltará para o HTTPS somente se a conectividade direta falhar.

#### <a name="tls-inspection"></a>Inspeção TLS

Não use a inspeção TLS para o tráfego do conector, pois isso causa problemas no tráfego dele. O conector usa um certificado para autenticar o serviço de Proxy de Aplicativo e esse certificado pode ser perdido durante a inspeção TLS.

## <a name="configure-using-a-proxy-between-the-connector-and-backend-application"></a>Configure usando um proxy entre o conector e o aplicativo de back-end
Usar um proxy de encaminhamento para a comunicação em direção ao aplicativo de back-end pode ser um requisito especial em alguns ambientes.
Para habilitar isso, siga as próximas etapas:

### <a name="step-1-add-the-required-registry-value-to-the-server"></a>Etapa 1: adicionar o valor de Registro necessário ao servidor
1. Para habilitar o uso do proxy padrão, adicione o seguinte valor de Registro (DWORD) `UseDefaultProxyForBackendRequests = 1` à chave do Registro de configuração do Conector localizada em "HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft AAD App Proxy Connector".

### <a name="step-2-configure-the-proxy-server-manually-using-netsh-command"></a>Etapa 2: Configurar o servidor proxy manualmente usando o comando netsh
1.  Habilite a política de grupo Fazer configurações de proxy por computador. Isso é encontrado em: Configuração do Computador\Políticas\Modelos Administrativos\Componentes do Windows\Internet Explorer. Isso precisa ser definido, em vez de deixar essa política definida como "por usuário".
2.  Execute `gpupdate /force` no servidor ou reinicialize o servidor para garantir que ele use as configurações de política de grupo atualizadas.
3.  Inicie um prompt de comandos com privilégios elevados com direitos de administrador e insira `control inetcpl.cpl`.
4.  Defina as configurações de proxy necessárias. 

Essas configurações fazem com que o conector use o mesmo proxy de encaminhamento para a comunicação com o Azure e para o aplicativo de back-end. Se o conector para a comunicação do Azure não exigir nenhum proxy de encaminhamento ou exigir um proxy de encaminhamento diferente, você poderá configurá-lo modificando o arquivo ApplicationProxyConnectorService.exe.config, conforme descrito nas seções Ignorar proxies de saída ou Usar o servidor proxy de saída.

> [!NOTE]
> Há várias maneiras de configurar o proxy da Internet no sistema operacional. As configurações de proxy definidas por meio de NETSH WINHTTP (executar `NETSH WINHTTP SHOW PROXY` para verificar) substituem as configurações de proxy que você configurou na etapa 2. 

O serviço atualizador do conector também usará o proxy do computador. Esse comportamento pode ser alterado modificando o arquivo ApplicationProxyConnectorUpdaterService.exe.config.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Solucionar problemas do proxy do conector e problemas de conectividade do serviço

Agora, você deve ver todo o tráfego fluindo pelo proxy. Se você tiver problemas, as seguintes informações de solução de problemas deverão ajudar.

A melhor maneira de identificar e solucionar os problemas de conectividade do conector é usar uma captura da rede ao iniciar o serviço do conector. Aqui estão algumas dicas rápidas sobre capturar e filtrar os rastreamentos de rede.

Você pode usar a ferramenta de monitoramento de sua preferência. Para este artigo, usamos o Microsoft Message Analyzer.

> [!NOTE]
> O [MMA (analisador de mensagens da Microsoft) foi desativado](/openspecs/blog/ms-winintbloglp/dd98b93c-0a75-4eb0-b92e-e760c502394f) e seus pacotes de download foram removidos dos sites do Microsoft.com em novembro de 25 2019.  No momento, não há substituição da Microsoft para o Microsoft Message Analyzer em desenvolvimento no momento.  Para uma funcionalidade semelhante, considere o uso de uma ferramenta de analisador de protocolo de rede de terceiros, como o Wireshark.

Os exemplos a seguir são específicos para o Message Analyzer, mas os princípios podem ser aplicados a qualquer ferramenta de análise.

### <a name="take-a-capture-of-connector-traffic"></a>Fazer uma captura do tráfego do conector

Para a solução de problemas inicial, execute as seguintes etapas:

1. Em services.msc, pare o serviço Conector do Proxy de Aplicativo do Azure AD.

   ![Serviço Conector do Proxy de Aplicativo do Azure AD em services.msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Execute o Message Analyzer como um administrador.
1. Selecione **Iniciar rastreamento local**.
1. Inicie o serviço Conector do Proxy de Aplicativo do Azure AD.
1. Pare a captura de rede.

   ![Captura de tela mostrando o botão Parar captura de rede](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

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
* Se você tiver problemas de conectividade do conector, faça sua pergunta na [Página de P e R Microsoft para o Azure Active Directory](/answers/topics/azure-active-directory.html) ou crie um tíquete com nossa equipe de suporte.