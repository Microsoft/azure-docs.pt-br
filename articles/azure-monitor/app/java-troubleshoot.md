---
title: Solucionar problemas do Application Insights em um projeto Web Java
description: 'Guia de solução de problemas: monitoramento em tempo real aplicativos Java com o Application Insights.'
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2018
ms.author: mbullwin
ms.openlocfilehash: eaade5f9ec9db7e8d224305147dafc264916d9c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60899492"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Solução de problemas e perguntas e respostas para o Application Insights para Java
Dúvidas ou problemas com o [Azure Application Insights em Java][java]? Aqui estão algumas dicas.

## <a name="build-errors"></a>Erros de compilação
**No Eclipse ou Intellij Idea, ao adicionar o Application Insights SDK via Maven ou Gradle, eu recebo erros de validação de soma ou compilação.**

* Se o `<version>` elemento de dependência estiver usando um padrão com caracteres curinga (por exemplo, (Maven) `<version>[2.0,)</version>` ou Gradle `version:'2.0.+'`), tente especificar uma versão específica em vez disso, como `2.0.1`. Veja as [notas de versão](https://github.com/Microsoft/ApplicationInsights-Java/releases) da versão mais recente.

## <a name="no-data"></a>Sem dados
**Adicionei o Application Insights com êxito e executei meu aplicativo, mas nunca vi dados no portal.**

* Espere um minuto e clique em Atualizar. Os gráficos são atualizados periodicamente, mas você também pode atualizá-los manualmente. O intervalo de atualização depende do intervalo de tempo do gráfico.
* Verifique se você tem uma chave de instrumentação definida no arquivo ApplicationInsights.xml (na pasta de recursos em seu projeto) ou configurada como variável Ambiente.
* Verifique se não há um nó `<DisableTelemetry>true</DisableTelemetry>` no arquivo xml.
* Em seu firewall, talvez você precise abrir as portas TCP 80 e 443 para o tráfego de saída de dc.services.visualstudio.com. Consulte a [lista completa de exceções do firewall](../../azure-monitor/app/ip-addresses.md)
* No painel inicial do Microsoft Azure, veja o mapa de status de serviço. Se houver indicações de alerta, espere até que elas tenham voltado a OK; então, feche e abra novamente a folha do Application Insights de seu aplicativo.
* Ative o log para a janela de console do IDE adicionando um elemento `<SDKLogger />` sob o nó raiz no arquivo ApplicationInsights.xml (na pasta de recursos em seu projeto) e verifique se há entradas precedidas com AI: INFO/WARN/ERROR para quaisquer logs suspeitos.
* Certifique-se de que o arquivo ApplicationInsights.xml correto foi carregado com êxito pelo SDK do Java, examinando as mensagens de saída do console para uma instrução "Arquivo de configuração foi descoberto com êxito".
* Se não for encontrado no arquivo de configuração, verifique as mensagens de saída para ver onde o arquivo de configuração está sendo procurado e certifique-se de que o ApplicationInsights.xml seja localizado em um desses locais de pesquisa. Como regra geral, você pode colocar o arquivo de configuração perto dos JARs do SDK do Application Insights. Por exemplo: no Tomcat, isso poderia significar que a pasta WEB-INF/classes. Durante o desenvolvimento, você pode colocar ApplicationInsights.xml na pasta de recursos de seu projeto Web.
* Também consulte a [página de problemas no GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues) para problemas conhecidos com o SDK.
* Certifique-se de usar a mesma versão do Application Insights principal, web, agente e log appenders para evitar quaisquer problemas de conflito de versão.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Eu costumava ver os dados, mas eles foram interrompidos
* Verifique o [blog de status](https://blogs.msdn.com/b/applicationinsights-status/).
* Você atingiu sua cota mensal de pontos de dados? Abra Configurações/Cota e Preços para descobrir. Nesse caso, você pode atualizar seu plano ou então pagar por capacidade adicional. Consulte o [esquema de preços](https://azure.microsoft.com/pricing/details/application-insights/).
* Você atualizou recentemente o SDK? Certifique-se de que apenas os jars SDK exclusivo estão presentes no diretório do projeto. Não deve haver duas versões diferentes do SDK presente.
* Você está analisando o recurso AI correto? Corresponda a iKey do seu aplicativo para o recurso em que você está esperando telemetria. Eles devem ser iguais.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Não vejo todos os dados que eu esperava
* Abra a página Uso e custos estimados e verifique se a [amostragem](../../azure-monitor/app/sampling.md) está funcionando. (Transmissão de 100% significa que a amostragem não está funcionando.) O serviço do Application Insights pode ser definido para aceitar apenas uma fração da telemetria que chega de seu aplicativo. Isso o ajuda a se manter dentro de sua cota mensal de telemetria.
* Você está com a amostragem do SDK ativada? Se sim, os dados seriam amostrados na taxa especificada para todos os tipos aplicáveis.
* Você está executando uma versão mais antiga do SDK do Java? Começando com a versão 2.0.1, apresentamos mecanismo de tolerância para tratar intermitentes de rede e falhas de back-end, bem como a persistência de dados em unidades locais.
* Está tendo limitação devido à excessiva Telemetria? Se você ativar o log de informações, você verá um log de mensagem "O aplicativo está limitado". Nosso limite atual é a telemetria de 32 k itens/segundo.

### <a name="java-agent-cannot-capture-dependency-data"></a>Agente de Java não é possível capturar dados de dependência
* Você configurou o agente de Java seguindo [Configurar Agente de Java](java-agent.md) ?
* Verifique se o jar do agente java e o arquivo AI-Agent.xml são colocados na mesma pasta.
* Certifique-se de que a dependência que você está tentando coletar automaticamente tem suporte para coleta automática. No momento só há suporte para MySQL, MsSQL, Oracle DB e coleção de dependência Azure Cache para Redis.
* Você está usando o JDK 1.7 ou 1.8? No momento não oferecemos suporte à coleção de dependência no JDK 9.

## <a name="no-usage-data"></a>Sem dados de uso
**Vejo dados sobre solicitações e tempos de resposta, mas não há dados de exibição de página, de navegador ou de usuário.**

Você configurou com êxito seu aplicativo para enviar telemetria do servidor. Agora, a próxima etapa é [configurar suas páginas da Web para enviar telemetria por meio do navegador da Web][usage].

Como alternativa, se o cliente for um aplicativo em um [telefone ou outro dispositivo][platforms], você poderá enviar telemetria por meio dele.

Use a mesma chave de instrumentação para configurar a telemetria de seu cliente e do servidor. Os dados serão exibidos no mesmo recurso do Application Insights, e você poderá correlacionar eventos do cliente e do servidor.


## <a name="disabling-telemetry"></a>Desabilitando a telemetria
**Como desabilitar a coleta da telemetria?**

No código:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Ou**

Atualize o arquivo ApplicationInsights.xml (na pasta de recursos em seu projeto). Adicione o seguinte sob o nó raiz:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Usando o método XML, você precisa reiniciar o aplicativo ao alterar o valor.

## <a name="changing-the-target"></a>Alterando o destino
**Como alterar o recurso do Azure ao qual meu projeto envia dados?**

* [Obtenha a chave de instrumentação do novo recurso.][java]
* Se você tiver adicionado o Application Insights a seu projeto usando o Kit de Ferramentas do Azure para Eclipse, clique com o botão direito do mouse em seu projeto Web, selecione **Azure**, **Configurar Application Insights** e altere a chave.
* Se você configurou a chave de instrumentação como variável de ambiente atualize o valor da variável de ambiente com iKey novo.
* Caso contrário, atualize a chave em ApplicationInsights.xml na pasta de recursos em seu projeto.

## <a name="debug-data-from-the-sdk"></a>Depurar dados do SDK

**Como descobrir o que o SDK está fazendo?**

Para saber mais sobre o que está acontecendo na API, adicione `<SDKLogger/>` ao nó raiz do arquivo de configuração Applicationinsights.xml.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

Você também pode instruir o agente para enviar a saída para um arquivo:

```XML
  <SDKLogger type="FILE">
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Iniciador do Spring Boot

Para habilitar o log do SDK com aplicativos do Spring Boot usando o iniciador do Spring Boot Application Insights, adicione o seguinte para o `application.properties` arquivo.:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Agente de Java

Habilitar o registro em log de agente do JVM update a [arquivo AI-Agent.xml](java-agent.md).

```xml
<AgentLogger type="FILE">
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

## <a name="the-azure-start-screen"></a>A tela inicial do Azure
**Estou examinando o [portal do Azure](https://portal.azure.com). O mapa me diz algo sobre meu aplicativo?**

Não, ele mostra a integridade dos servidores do Azure em todo o mundo.

*No painel inicial do Azure (tela inicial), como localizar dados sobre meu aplicativo?*

Supondo que você tenha [configurado seu aplicativo para o Application Insights][java], clique em Procurar, selecione Application Insights e selecione o recurso de aplicativo criado para seu aplicativo. Para acessar essa opção mais rapidamente no futuro, você pode fixar o aplicativo no painel inicial.

## <a name="intranet-servers"></a>Servidores de intranet
**Posso monitorar um servidor em minha intranet?**

Sim, desde que o servidor possa enviar telemetria para o portal do Application Insights pela Internet pública.

Em seu firewall, você terá que abrir as portas TCP 80 e 443 para tráfego de saída de dc.services.visualstudio.com e f5.services.visualstudio.com.

## <a name="data-retention"></a>Retenção de dados
**Por quanto tempo os dados são mantidos no portal? É seguro?**

Consulte [Privacidade e retenção de dados][data].

## <a name="debug-logging"></a>Registro em log de depuração
O Application Insights usa `org.apache.http`. Isso é realocado no jars do núcleo do Application Insights no namespace `com.microsoft.applicationinsights.core.dependencies.http`. Isso habilita o Application Insights a lidar com cenários em que diferentes versões do mesmo `org.apache.http` existem em uma base de código.

>[!NOTE]
>Se você habilitar o registro em log de nível DEBUG para todos os namespaces no aplicativo, isso será respeitado por todos os módulos em execução, incluindo `org.apache.http` renomeado como `com.microsoft.applicationinsights.core.dependencies.http`. O Application Insights não poderá aplicar a filtragem a essas chamadas, pois a chamada de log estará sendo feita pela biblioteca Apache. O registro em log do nível DEBUG gera uma quantidade considerável de dados de log e não é recomendado para instâncias de produção.


## <a name="next-steps"></a>Próximas etapas
**Configurei o Application Insights para meu aplicativo de servidor Java. O que mais posso fazer?**

* [Monitorar a disponibilidade de suas páginas da Web][availability]
* [Monitorar o uso da página da Web][usage]
* [Acompanhar o uso e diagnosticar problemas em seus aplicativos de dispositivos][platforms]
* [Escrever código para acompanhar o uso de seu aplicativo][track]
* [Capturar logs de diagnóstico][javalogs]

## <a name="get-help"></a>Obter ajuda
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Relatar Problema no GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

