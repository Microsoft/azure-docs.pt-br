---
title: Solução de problemas Azure Monitor Application Insights para Java
description: Saiba como solucionar problemas do agente Java para Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: f971466f25c2b7a4bd28e5b7eec6268f1b2e8b3d
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225567"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Guia de solução de problemas: Azure Monitor Application Insights para Java

Neste artigo, abordamos alguns dos problemas comuns que você pode enfrentar ao instrumentar um aplicativo Java usando o agente Java para Application Insights. Também abordaremos as etapas para resolver esses problemas. Application Insights é um recurso do serviço de plataforma Azure Monitor.

## <a name="check-the-self-diagnostic-log-file"></a>Verificar o arquivo de log de autodiagnóstico

Por padrão, o agente do Java 3,0 para Application Insights produz um arquivo de log chamado `applicationinsights.log` no mesmo diretório que contém o `applicationinsights-agent-3.0.2.jar` arquivo.

Esse arquivo de log é o primeiro lugar para verificar se há dicas para quaisquer problemas que você esteja enfrentando.

## <a name="jvm-fails-to-start"></a>Falha na inicialização da JVM

Se a JVM não iniciar com "erro ao abrir o arquivo zip ou o manifesto JAR ausente", tente baixar novamente o arquivo JAR do agente porque ele pode ter sido corrompido durante a transferência de arquivos.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Atualizar do SDK Application Insights Java 2. x

Se você já estiver usando o SDK Application Insights Java 2. x em seu aplicativo, você poderá continuar a usá-lo. O agente do Java 3,0 irá detectá-lo. Para obter mais informações, consulte [Atualizar do SDK do Java 2. x](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Atualizar de Application Insights visualização do Java 3,0

Se você estiver atualizando do agente de visualização do Java 3,0, revise todas as [Opções de configuração](./java-standalone-config.md) com cuidado. A estrutura JSON foi completamente alterada na versão GA (disponibilidade geral) de 3,0.

Essas alterações incluem:

-  O nome do arquivo de configuração foi alterado de `ApplicationInsights.json` para `applicationinsights.json` .
-  O `instrumentationSettings` nó não está mais presente. Todo o conteúdo em `instrumentationSettings` é movido para o nível raiz. 
-  Nós de configuração como `sampling` , `jmxMetrics` , `instrumentation` e `heartbeat` são movidos de `preview` para o nível raiz.

## <a name="some-logging-is-not-auto-collected"></a>Algum registro em log não é coletado automaticamente

O registro em log só será capturado se ele atender primeiro ao nível configurado para a estrutura de registro em log e, segundo, também atender ao nível configurado para Application Insights.

Por exemplo, se sua estrutura de registro em log estiver configurada para registrar `WARN` (e acima) do pacote `com.example` e Application insights estiver configurada para capturar `INFO` (e acima), Application insights só capturará `WARN` (e acima) do pacote `com.example` .

A melhor maneira de saber se uma determinada instrução de log atende ao limite configurado da estrutura de registro em log é confirmar que ela está aparecendo no log normal do aplicativo (por exemplo, arquivo ou console).

Observe também que, se um objeto de exceção for passado para o agente, a mensagem de log (e os detalhes do objeto de exceção) aparecerá na portal do Azure na `exceptions` tabela em vez da `traces` tabela.

Consulte a [configuração de log coletada automaticamente](./java-standalone-config.md#auto-collected-logging) para obter mais detalhes.

## <a name="import-ssl-certificates"></a>Importar certificados SSL

Esta seção ajuda você a solucionar problemas e possivelmente corrigir as exceções relacionadas a certificados SSL ao usar o agente Java.

Há dois caminhos diferentes abaixo para resolver esse problema:
* Se estiver usando um repositório de chaves Java padrão
* Se estiver usando um repositório de chaves Java personalizado

Se você não tiver certeza sobre qual caminho deve ser seguido, verifique se você tem um ARG JVM `-Djavax.net.ssl.trustStore=...` .
Se você _não_ tiver esse ARG de JVM, provavelmente estará usando o keystore padrão do Java.
Se você _tiver um_ ARG de JVM, você provavelmente está usando um keystore personalizado e a ARG JVM apontará para seu keystore personalizado.

### <a name="if-using-the-default-java-keystore"></a>Se estiver usando o repositório de chaves Java padrão:

Normalmente, o repositório de chaves Java padrão já terá todos os certificados raiz da autoridade de certificação. No entanto, pode haver algumas exceções, como o certificado de ponto de extremidade de ingestão pode ser assinado por um certificado raiz diferente. Portanto, recomendamos as três etapas a seguir para resolver esse problema:

1.  Verifique se o certificado raiz que foi usado para assinar o ponto de extremidade de Application Insights já está presente no keystore padrão. Os certificados de autoridade de certificação confiáveis, por padrão, são armazenados em `$JAVA_HOME/jre/lib/security/cacerts` . Para listar certificados em um repositório de chaves Java, use o seguinte comando:
    > `keytool -list -v -keystore $PATH_TO_KEYSTORE_FILE`
 
    Você pode redirecionar a saída para um arquivo temporário como este (será fácil pesquisar mais tarde)
    > `keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts > temp.txt`

2. Depois de ter a lista de certificados, siga estas [etapas](#steps-to-download-ssl-certificate) para baixar o certificado raiz que foi usado para assinar o ponto de extremidade de Application insights.

    Depois que o certificado for baixado, gere um hash SHA-1 no certificado usando o comando abaixo:
    > `keytool -printcert -v -file "your_downloaded_root_certificate.cer"`
 
    Copie o valor SHA-1 e verifique se esse valor está presente no arquivo "temp.txt" que você salvou anteriormente.  Se você não conseguir localizar o valor SHA-1 no arquivo temporário, isso indica que o certificado raiz baixado está ausente no repositório de chaves Java padrão.


3. Importe o certificado raiz para o armazenamento de chaves Java padrão usando o seguinte comando:
    >   `keytool -import -file "the cert file" -alias "some meaningful name" -keystore "path to cacerts file"`
 
    Nesse caso, será
 
    > `keytool -import -file "your downloaded root cert file" -alias "some meaningful name" $JAVA_HOME/jre/lib/security/cacerts`


### <a name="if-using-a-custom-java-keystore"></a>Se estiver usando um repositório de chaves Java personalizado:

Se você estiver usando um repositório de chaves Java personalizado, talvez seja necessário importar os certificados SSL (s) raiz do ponto de extremidade do Application Insights para ele.
Recomendamos as duas etapas a seguir para resolver esse problema:
1. Siga estas [etapas](#steps-to-download-ssl-certificate) para baixar o certificado raiz do ponto de extremidade Application insights.
2. Use o seguinte comando para importar o certificado SSL raiz para o repositório de chaves Java personalizado:
    > `keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name.cer" -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-ssl-certificate"></a>Etapas para baixar o certificado SSL

1.  Abra seu navegador favorito e vá para a `IngestionEndpoint` URL presente na cadeia de conexão que é usada para instrumentar seu aplicativo.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png" alt-text="Captura de tela que mostra uma cadeia de conexão Application Insights." lightbox="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png":::

2.  Selecione o ícone **Exibir informações do site** (bloquear) no navegador e, em seguida, selecione a opção **certificado** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Captura de tela da opção de certificado nas informações do site." lightbox="media/java-ipa/troubleshooting/certificate-icon-capture.png":::

3.  Em vez de baixar o certificado ' folha ', você deve baixar o certificado ' raiz ', conforme mostrado abaixo. Posteriormente, você precisa clicar no "caminho do certificado"-> selecionar o certificado raiz-> clique em ' Exibir certificado '. Isso exibirá um novo menu de certificado e você poderá baixar o certificado no menu novo.

    :::image type="content" source="media/java-ipa/troubleshooting/root-certificate-selection.png" alt-text="Captura de tela de como selecionar o certificado raiz." lightbox="media/java-ipa/troubleshooting/root-certificate-selection.png":::

4.  Vá para a guia **detalhes** e selecione **copiar para arquivo**.
5.  Selecione o botão **Avançar** , selecione **X. 509 codificado em base-64 (. CER)** e, em seguida, selecione **Avançar** novamente.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Captura de tela do assistente para exportação de certificados, com um formato selecionado." lightbox="media/java-ipa/troubleshooting/certificate-export-wizard.png":::

6.  Especifique o arquivo no qual você deseja salvar o certificado SSL. Em seguida, selecione **próxima**  >  **conclusão**. Você deve ver uma mensagem "a exportação foi bem-sucedida".

> [!WARNING]
> Você precisará repetir essas etapas para obter o novo certificado antes que o certificado atual expire. Você pode encontrar as informações de expiração na guia **detalhes** da caixa de diálogo **certificado** .
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Captura de tela que mostra os detalhes do certificado SSL." lightbox="media/java-ipa/troubleshooting/certificate-details.png":::
