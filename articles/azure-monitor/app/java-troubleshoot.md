---
title: Solucionar problemas do Application Insights em um projeto Web Java
description: 'Guia de solução de problemas: monitoramento em tempo real aplicativos Java com o Application Insights.'
ms.topic: conceptual
ms.date: 03/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: df1f58418452ecafacddd013ab815ebca5cb8a35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097543"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java-sdk"></a>Solução de problemas e perguntas e respostas do SDK do Application Insights para Java

> [!IMPORTANT]
> A abordagem recomendada para monitorar aplicativos Java é usar a instrumentação automática sem alterar o código. Siga as diretrizes para o [agente do Application Insights para Java 3.0](./java-in-process-agent.md).

Dúvidas ou problemas com o [Azure Application Insights em Java][java]? Aqui estão algumas dicas.

## <a name="build-errors"></a>Erros de compilação
**No Eclipse ou Intellij Idea, ao adicionar o Application Insights SDK via Maven ou Gradle, eu recebo erros de validação de soma ou compilação.**

* Se o elemento `<version>` de dependência estiver usando um padrão com caracteres curinga (ex.: Maven `<version>[2.0,)</version>` ou Gradle `version:'2.+'`), tente definir uma versão específica, como `2.6.2`.

## <a name="no-data"></a>Sem dados
**Adicionei o Application Insights com êxito e executei meu aplicativo, mas nunca vi dados no portal.**

* Espere um minuto e clique em Atualizar. Os gráficos são atualizados periodicamente, mas você também pode atualizá-los manualmente. O intervalo de atualização depende do intervalo de tempo do gráfico.
* Verifique se você tem uma chave de instrumentação definida no arquivo ApplicationInsights.xml (na pasta de recursos em seu projeto) ou configurada como variável Ambiente.
* Verifique se não há um nó `<DisableTelemetry>true</DisableTelemetry>` no arquivo xml.
* Em seu firewall, talvez você precise abrir as portas TCP 80 e 443 para o tráfego de saída de dc.services.visualstudio.com. Consulte a [lista completa de exceções do firewall](./ip-addresses.md)
* No painel inicial do Microsoft Azure, veja o mapa de status de serviço. Se houver indicações de alerta, espere até que elas tenham voltado a OK; então, feche e abra novamente a folha do Application Insights de seu aplicativo.
* [Ative o log](#debug-data-from-the-sdk) adicionando um elemento `<SDKLogger />` sob o nó raiz no arquivo ApplicationInsights.xml (na pasta de recursos do projeto) e verifique se há entradas precedidas com AI: INFO/AVISO/ERRO para ver se há algum log suspeito. 
* Certifique-se de que o arquivo ApplicationInsights.xml correto foi carregado com êxito pelo SDK do Java, examinando as mensagens de saída do console para uma instrução "Arquivo de configuração foi descoberto com êxito".
* Se não for encontrado no arquivo de configuração, verifique as mensagens de saída para ver onde o arquivo de configuração está sendo procurado e certifique-se de que o ApplicationInsights.xml seja localizado em um desses locais de pesquisa. Como regra geral, você pode colocar o arquivo de configuração perto dos JARs do SDK do Application Insights. Por exemplo: no Tomcat, isso poderia significar que a pasta WEB-INF/classes. Durante o desenvolvimento, você pode colocar ApplicationInsights.xml na pasta de recursos de seu projeto Web.
* Também consulte a [página de problemas no GitHub](https://github.com/microsoft/ApplicationInsights-Java/issues) para problemas conhecidos com o SDK.
* Certifique-se de usar a mesma versão do Application Insights principal, web, agente e log appenders para evitar quaisquer problemas de conflito de versão.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Eu costumava ver os dados, mas eles foram interrompidos
* Você atingiu sua cota mensal de pontos de dados? Abra Configurações/Cota e Preços para descobrir isso. Nesse caso, você pode atualizar seu plano ou, então, pagar uma capacidade adicional. Consulte o [esquema de preços](https://azure.microsoft.com/pricing/details/application-insights/).
* Você atualizou recentemente o SDK? Certifique-se de que apenas os jars SDK exclusivo estão presentes no diretório do projeto. Não deve haver duas versões diferentes do SDK presente.
* Você está analisando o recurso AI correto? Corresponda a iKey do seu aplicativo para o recurso em que você está esperando telemetria. Eles devem ser iguais.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Não vejo todos os dados que eu esperava
* Abra a página Uso e custos estimados e verifique se a [amostragem](./sampling.md) está funcionando. (Transmissão de 100% significa que a amostragem não está funcionando.) O serviço Application Insights pode ser definido para aceitar apenas uma fração da telemetria recebida do aplicativo. Isso o ajuda a se manter dentro de sua cota mensal de telemetria.
* Você está com a amostragem do SDK ativada? Se sim, os dados seriam amostrados na taxa especificada para todos os tipos aplicáveis.
* Você está executando uma versão mais antiga do SDK do Java? Começando com a versão 2.0.1, apresentamos mecanismo de tolerância para tratar intermitentes de rede e falhas de back-end, bem como a persistência de dados em unidades locais.
* Está tendo limitação devido à excessiva Telemetria? Se você ativar o log de informações, você verá um log de mensagem "O aplicativo está limitado". Nosso limite atual é a telemetria de 32 k itens/segundo.

### <a name="java-agent-cannot-capture-dependency-data"></a>Agente de Java não é possível capturar dados de dependência
* Você configurou o agente de Java seguindo [Configurar Agente de Java](java-agent.md) ?
* Verifique se o jar do agente java e o arquivo AI-Agent.xml são colocados na mesma pasta.
* Certifique-se de que a dependência que você está tentando coletar automaticamente tem suporte para coleta automática. No momento só há suporte para MySQL, MsSQL, Oracle DB e coleção de dependência Azure Cache para Redis.

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

**Or**

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
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Iniciador do Spring Boot

Para habilitar o log do SDK com aplicativos Spring Boot usando o Iniciador do Spring Boot do Application Insights, adicione o seguinte ao arquivo `application.properties`:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

ou para impressão com o erro padrão:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Agente de Java

Para habilitar o log do Agente da JVM, atualize o [arquivo AI-Agent.xml](java-agent.md):

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Propriedades de linha de comando do Java
_Desde a versão 2.4.0_

Para habilitar o log usando opções de linha de comando sem alterar os arquivos de configuração:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

ou para impressão com o erro padrão:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>A tela inicial do Azure
**Estou examinando [o portal do Azure](https://portal.azure.com). O mapa informa algo sobre meu aplicativo?**

Não, ele mostra a integridade dos servidores do Azure em todo o mundo.

*No painel inicial do Azure (tela inicial), como localizar dados sobre meu aplicativo?*

Supondo que você tenha [configurado seu aplicativo para o Application Insights][java], clique em Procurar, selecione Application Insights e selecione o recurso de aplicativo criado para seu aplicativo. Para acessar essa opção mais rapidamente no futuro, você pode fixar o aplicativo no painel inicial.

## <a name="intranet-servers"></a>Servidores de intranet
**Posso monitorar um servidor em minha intranet?**

Sim, desde que o servidor possa enviar telemetria para o portal do Application Insights pela Internet pública.

Talvez seja necessário [abrir algumas portas de saída no firewall do servidor](./ip-addresses.md#outgoing-ports) para permitir que o SDK envie dados para o portal.

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
* [Relatar Problema no GitHub](https://github.com/microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[data]: ./data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ./platforms.md
[track]: ./api-custom-events-metrics.md
[usage]: javascript.md

