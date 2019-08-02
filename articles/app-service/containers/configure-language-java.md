---
title: Configurar aplicativos Java do Linux – serviço de Azure App | Microsoft Docs
description: Saiba como configurar aplicativos Java em execução no Serviço de Aplicativo do Azure no Linux.
keywords: serviço de aplicativo do Azure, aplicativo Web, Linux, OSS, Java, Java EE, JEE, Java
services: app-service
author: bmitchell287
manager: douge
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/26/2019
ms.author: brendm
ms.custom: seodec18
ms.openlocfilehash: 1488dbdcc042b29880560e7255de96b8d0409779
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498499"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Configurar um aplicativo Java do Linux para o serviço Azure App

O Serviço de Aplicativo do Azure no Linux permite que desenvolvedores de Java criem, implantem e escalonem rapidamente os aplicativos Web empacotados do Tomcat ou do Java Standard Edition (SE) em um serviço baseado em Linux totalmente gerenciado. Implante aplicativos com o plug-ins do Maven na linha de comando ou em editores, como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece os principais conceitos e instruções para desenvolvedores de Java que usam um contêiner do Linux interno no serviço de aplicativo. Se você nunca usou Azure App serviço, siga o tutorial [início rápido do Java](quickstart-java.md) e [Java com PostgreSQL](tutorial-java-enterprise-postgresql-app.md) primeiro.

## <a name="deploying-your-app"></a>Implantação do aplicativo

Você pode usar o [plug-in do Maven para Azure app serviço](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) para implantar arquivos. jar e. War. A implantação com IDEs populares também tem suporte com [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) ou [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Caso contrário, o método de implantação dependerá do tipo de arquivo morto:

- Para implantar arquivos .war para Tomcat, use o ponto de extremidade `/api/wardeploy/` para executar POST de seu arquivo morto. Para obter mais informações sobre essa API, confira [essa documentação](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Para implantar arquivos .jar nas imagens do Java SE, use o ponto de extremidade `/api/zipdeploy/` do site do Kudu. Para obter mais informações sobre essa API, confira [essa documentação](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Não implante seu .war ou .jar usando FTP. A ferramenta FTP foi projetada para carregar os scripts de inicialização, dependências ou outros arquivos de tempo de execução. Não é a opção ideal para a implantação de aplicativos Web.

## <a name="logging-and-debugging-apps"></a>Aplicativos de registro em log e depuração

Relatórios de desempenho, visualizações de tráfego e verificações de integridade estão disponíveis para cada aplicativo por meio do portal do Azure. Para obter mais informações, consulte [visão geral do diagnóstico de serviço Azure app](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Acesso ao console SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Para saber mais, confira [Transmitir logs com a CLI do Azure](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Registro em log do aplicativo

Habilite o [registro em log de aplicativos](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) por meio do portal do Azure ou da [CLI do Azure](/cli/azure/webapp/log#az-webapp-log-config) para configurar o Serviço de Aplicativo do Azure para gravar a saída do console padrão do aplicativo e os fluxos de erro do console padrão no sistema de arquivos local ou no Armazenamento de Blobs do Azure. O registro em log na instância do sistema de arquivos do Serviço de Aplicativo local será desabilitado 12 horas após ser configurado. Se você precisar de uma retenção mais longa, configure o aplicativo para gravar a saída em um contêiner do armazenamento de blobs. Seus logs de aplicativo Java e Tomcat podem ser encontrados no diretório */Home/LogFiles/Application/*

Se o aplicativo usar [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para rastreamento, será possível encaminhá-los para revisão no Azure Application Insights usando as instruções de configuração de estrutura de registros em [Explorar os logs de rastreamento de Java no Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Ferramentas de solução de problemas

As imagens Java internas são baseadas no sistema operacional [alpineum Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . Use o `apk` Gerenciador de pacotes para instalar qualquer ferramenta ou comando de solução de problemas.

### <a name="flight-recorder"></a>Gravador de voo

Todas as imagens Java do Linux no serviço de aplicativo têm o Zulu Flight Recorder instalado para que você possa se conectar facilmente à JVM e iniciar uma gravação do criador de perfil ou gerar um despejo de heap.

#### <a name="timed-recording"></a>Gravação cronometrada

Para começar, use o ssh em seu serviço de aplicativo e `jcmd` execute o comando para ver uma lista de todos os processos Java em execução. Além do próprio jcmd, você deve ver seu aplicativo Java em execução com um PID (número de ID de processo).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Execute o comando a seguir para iniciar uma gravação de 30 segundos da JVM. Isso fará o perfil da JVM e criará um arquivo JFR chamado *jfr_example. JFR* no diretório base. (Substitua 116 pelo PID do seu aplicativo Java.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Durante o intervalo de 30 segundos, você pode validar se a gravação está ocorrendo `jcmd 116 JFR.check`executando. Isso mostrará todas as gravações para o processo Java fornecido.

#### <a name="continuous-recording"></a>Gravação contínua

Você pode usar o gravador de vôo Zulu para criar o perfil de seu aplicativo Java com impacto mínimo sobre o desempenho do tempo de execução ([origem](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Para fazer isso, execute o seguinte comando CLI do Azure para criar uma configuração de aplicativo chamada JAVA_OPTS com a configuração necessária. O conteúdo da configuração do aplicativo JAVA_OPTS é passado para o `java` comando quando seu aplicativo é iniciado.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Depois que a gravação for iniciada, você poderá despejar os dados de gravação atuais a `JFR.dump` qualquer momento usando o comando.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Para obter mais informações, consulte a [referência do comando Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analisando gravações

Use [FTPS](../deploy-ftp.md) para baixar o arquivo JFR para o computador local. Para analisar o arquivo JFR, baixe e instale o [controle de missão Zulu](https://www.azul.com/products/zulu-mission-control/). Para obter instruções sobre o controle de missão Zulu, consulte a [documentação azul](https://docs.azul.com/zmc/) e as [instruções de instalação](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Personalização e ajuste

O serviço de Azure App para Linux dá suporte ao ajuste e à personalização prontos para uso por meio do portal do Azure e da CLI. Examine os seguintes artigos para configuração de aplicativo Web não específica para Java:

- [Definir configurações de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Configurar um domínio personalizado](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Habilitar SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Adicionar uma CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurar o site kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Definir opções de tempo de execução do Java

Para definir a memória alocada ou outras opções de tempo de execução da JVM nos ambientes Tomcat e Java se, crie `JAVA_OPTS` uma [configuração de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) chamada com as opções. O Serviço de Aplicativo no Linux passa essa configuração como uma variável de ambiente para o tempo de execução do Java quando ele é iniciado.

No portal do Azure, em **Configurações do aplicativo** para o aplicativo Web, crie uma configuração de aplicativo denominada `JAVA_OPTS` que inclui as configurações adicionais, como `-Xms512m -Xmx1204m`.

Para definir a configuração do aplicativo do plug-in do Maven, adicione marcas de configuração/valor na seção plug-in do Azure. O exemplo a seguir define um tamanho de heap de Java mínimo e máximo específico:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Os desenvolvedores que executam um único aplicativo com um slot de implantação no Plano do Serviço de Aplicativo podem usar as seguintes opções:

- Instâncias B1 e S1:`-Xms1024m -Xmx1024m`
- Instâncias B2 e S2:`-Xms3072m -Xmx3072m`
- Instâncias B3 e S3:`-Xms6144m -Xmx6144m`

Ao ajustar as configurações de heap do aplicativo, examine os detalhes do Plano do Serviço de Aplicativo e considere os vários aplicativos e slots de implantação necessários para encontrar a alocação de memória ideal.

Se você estiver implantando um aplicativo JAR, ele deverá ser nomeado *app. jar* para que a imagem interna possa identificar corretamente seu aplicativo. (O plug-in do Maven faz isso renomeando automaticamente.) Se você não quiser renomear o JAR para *app. jar*, poderá carregar um script de shell com o comando para executar o Jar. Em seguida, Cole o caminho completo para esse script na caixa de texto [arquivo de inicialização](app-service-linux-faq.md#built-in-images) na seção configuração do Portal.

### <a name="turn-on-web-sockets"></a>Ativar os soquetes da Web

Ative o suporte para soquetes da Web no portal do Azure, nas **Configurações de aplicativo**. Será necessário reiniciar o aplicativo para a configuração entrar em vigor.

Ative o suporte para soquete da Web usando a CLI do Azure com o seguinte comando:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Depois, reinicie o aplicativo:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Definir a codificação de caractere padrão

No portal do Azure, em **Configurações do aplicativo** para o aplicativo Web, crie uma configuração de aplicativo denominada `JAVA_OPTS` com o valor `-Dfile.encoding=UTF-8`.

Como alternativa, é possível definir a configuração do aplicativo usando o plug-in do Maven do Serviço de Aplicativo. Adicione as marcas de nome e valor de configuração à configuração do plug-in:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Ajustar tempo limite de inicialização

Se seu aplicativo Java for particularmente grande, você deverá aumentar o limite de tempo de inicialização. Para fazer isso, crie uma configuração `WEBSITES_CONTAINER_START_TIME_LIMIT` de aplicativo e defina-a como o número de segundos que o serviço de aplicativo deve aguardar antes de atingir o tempo limite. O valor máximo é `1800` segundos.

### <a name="pre-compile-jsp-files"></a>Pré-compilar arquivos JSP

Para melhorar o desempenho de aplicativos Tomcat, você pode compilar seus arquivos JSP antes de implantar no serviço de aplicativo. Você pode usar o [plug-in do Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornecido pelo Apache sling ou usando esse [arquivo de compilação Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Aplicativos seguros

Os aplicativos Java em execução no Serviço de Aplicativo para Linux têm o mesmo conjunto de [melhores práticas de segurança](/azure/security/security-paas-applications-using-app-services) que outros aplicativos.

### <a name="authenticate-users-easy-auth"></a>Autenticar usuários (fácil autenticação)

Configure a autenticação de aplicativo no portal do Azure com a opção **autenticação e autorização** . Em seguida, será possível habilitar a autenticação usando o Azure Active Directory ou os logons de redes sociais, como Facebook, Google ou GitHub. A configuração do portal do Azure só funciona ao configurar um único provedor de autenticação. Para saber mais, confira [Configurar o aplicativo do Serviço de Aplicativo para usar o logon do Azure Active Directory](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) e os artigos relacionados para outros provedores de identidade. Se você precisar habilitar vários provedores de entrada, siga as instruções no artigo [Personalizar a autenticação do Serviço de Aplicativo](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

#### <a name="tomcat-and-wildfly"></a>Tomcat e Wildfly

Seu aplicativo Tomcat ou Wildfly pode acessar as declarações do usuário diretamente do servlet, convertendo o objeto principal em um objeto Map. O objeto MAP mapeará cada tipo de declaração para uma coleção de declarações para esse tipo. No código a seguir, `request` é uma instância do `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Agora você pode inspecionar `Map` o objeto para qualquer declaração específica. Por exemplo, o trecho de código a seguir itera em todos os tipos de declaração e imprime o conteúdo de cada coleção.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Para desconectar os usuários, use `/.auth/ext/logout` o caminho. Para executar outras ações, consulte a documentação sobre o [uso de autenticação e autorização do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). Também há documentação oficial na [interface HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) do Tomcat e seus métodos. Os seguintes métodos servlets também são alimentados com base na configuração do serviço de aplicativo:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Para desabilitar esse recurso, crie uma configuração de aplicativo `WEBSITE_AUTH_SKIP_PRINCIPAL` chamada com um valor `1`de. Para desabilitar todos os filtros de servlet adicionados pelo serviço de aplicativo, crie `WEBSITE_SKIP_FILTERS` uma configuração chamada com `1`um valor de.

#### <a name="spring-boot"></a>Spring Boot

Os desenvolvedores do Spring Boot podem usar o [inicializador do Spring Boot do Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) para proteger aplicativos usando APIs e anotações conhecidas do Spring Security. Certifique-se de aumentar o tamanho máximo do cabeçalho em seu arquivo *Application. Properties* . Sugerimos um valor igual a `16384`.

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções em [Associar um certificado SSL personalizado existente](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para carregar um certificado SSL existente e associá-lo ao nome de domínio do aplicativo. Por padrão, o aplicativo ainda permitirá conexões HTTP – siga as etapas específicas no tutorial para impor o SSL e o TLS.

### <a name="use-keyvault-references"></a>Usar referências do keyvault

O [Azure keyvault](../../key-vault/key-vault-overview.md) fornece gerenciamento de segredos centralizado com políticas de acesso e histórico de auditoria. Você pode armazenar segredos (como senhas ou cadeias de conexão) no keyvault e acessar esses segredos em seu aplicativo por meio de variáveis de ambiente.

Primeiro, siga as instruções para [conceder acesso ao aplicativo para Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e [fazer uma referência de keyvault para seu segredo em uma configuração de aplicativo](../app-service-key-vault-references.md#reference-syntax). Você pode validar que a referência seja resolvida para o segredo imprimindo a variável de ambiente ao acessar remotamente o terminal do serviço de aplicativo.

Para injetar esses segredos em seu arquivo de configuração Spring ou Tomcat, use a sintaxe de`${MY_ENV_VAR}`injeção de variável de ambiente (). Para arquivos de configuração do Spring, consulte esta documentação sobre [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="configure-apm-platforms"></a>Configurar plataformas APM

Esta seção mostra como conectar aplicativos Java implantados no serviço de Azure App no Linux com as plataformas de monitoramento de desempenho de aplicativos NewRelic e AppDynamics (APM).

[Configurar novo Relic](#configure-new-relic)
[Configurar AppDynamics](#configure-appdynamics)

### <a name="configure-new-relic"></a>Configurar o New Relic

1. Criar uma conta de NewRelic em [NewRelic.com](https://newrelic.com/signup)
2. Baixe o agente Java de NewRelic, ele terá um nome de arquivo semelhante a *newrelic-Java-x. x. x. zip*.
3. Copie sua chave de licença, você precisará dela para configurar o agente mais tarde.
4. Use [SSH em sua instância do serviço de aplicativo](app-service-linux-ssh-support.md) e crie um novo diretório */Home/site/wwwroot/APM*.
5. Carregue os arquivos do agente Java NewRelic desempacotados em um diretório em */Home/site/wwwroot/APM*. Os arquivos do seu agente devem estar no */Home/site/wwwroot/APM/newrelic*.
6. Modifique o arquivo YAML em */Home/site/wwwroot/APM/newrelic/newrelic.yml* e substitua o valor da licença de espaço reservado por sua própria chave de licença.
7. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java SE**, crie uma variável de ambiente denominada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se você estiver usando **Tomcat**, crie uma variável de ambiente denominada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se você estiver usando o **WildFly**, consulte a nova documentação do Relic [aqui](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) para obter orientação sobre como instalar o agente Java e a configuração do JBoss.
    - Se você já tiver uma variável de ambiente para `JAVA_OPTS` ou `CATALINA_OPTS`, acrescente o `javaagent` opção ao final do valor atual.

### <a name="configure-appdynamics"></a>Configurar o AppDynamics

1. Criar uma conta do AppDynamics em [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Baixe o agente Java do site AppDynamics, o nome do arquivo será semelhante a *AppServerAgent-x. x. x. xxxxx. zip*
3. Use [SSH em sua instância do serviço de aplicativo](app-service-linux-ssh-support.md) e crie um novo diretório */Home/site/wwwroot/APM*.
4. Carregue os arquivos do agente Java em um diretório em */Home/site/wwwroot/APM*. Os arquivos do seu agente devem estar no */Home/site/wwwroot/APM/AppDynamics*.
5. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java SE**, crie uma variável de ambiente denominada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` quando `<app-name>` for o nome do seu Serviço de Aplicativo.
    - Se você estiver usando **Java SE**, crie uma variável de ambiente denominada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` quando `<app-name>` for o nome do seu Serviço de Aplicativo.
    - Se você estiver usando o **WildFly**, consulte a documentação do AppDynamics [aqui](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) para obter orientação sobre como instalar o agente Java e a configuração do JBoss.

## <a name="configure-jar-applications"></a>Configurar aplicativos JAR

### <a name="starting-jar-apps"></a>Iniciando aplicativos JAR

Por padrão, o serviço de aplicativo espera que seu aplicativo JAR seja nomeado *app. jar*. Se ele tiver esse nome, ele será executado automaticamente. Para usuários do Maven, você pode definir o nome do jar `<finalName>app</finalName>` incluindo `<build>` na seção de seu *pom. xml*. [Você pode fazer o mesmo no gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) definindo a `archiveFileName` propriedade.

Se você quiser usar um nome diferente para o JAR, também deverá fornecer o comando de [inicialização](app-service-linux-faq.md#built-in-images) que executa o arquivo jar. Por exemplo, `java -jar my-jar-app.jar`. Você pode definir o valor para o comando de inicialização no portal, em Configuração > configurações gerais ou com uma configuração de aplicativo denominada `STARTUP_COMMAND`.

### <a name="server-port"></a>Porta do servidor

O serviço de aplicativo Linux roteia solicitações de entrada para a porta 80, de modo que seu aplicativo também deve escutar na porta 80. Você pode fazer isso na configuração do aplicativo (como o arquivo *Application. Properties* da Spring) ou no comando de inicialização (por exemplo, `java -jar spring-app.jar --server.port=80`). Consulte a seguinte documentação para estruturas Java comuns:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Fontes de dados

### <a name="tomcat"></a>Tomcat

Essas instruções se aplicam a todas as conexões de banco de dados. Você precisará preencher os espaços reservados com nome de classe do driver do banco de dados escolhido e o arquivo JAR. É fornecida uma tabela com nomes de classe e downloads de driver para bancos de dados comuns.

| Banco de dados   | Nome de Classe do Driver                             | Driver JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Baixar](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Baixar](https://dev.mysql.com/downloads/connector/j/) (Selecione "Independente de Plataforma") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Baixar](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Para configurar o Tomcat para usar Java Database Connectivity (JDBC) ou a API de persistência Java (JPA), primeiro `CATALINA_OPTS` Personalize a variável de ambiente que é lida pelo Tomcat na inicialização. Defina esses valores por meio de uma configuração de aplicativo no [plug-in Maven do Serviço de Aplicativo](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Ou defina as variáveis de ambiente na página**configurações do aplicativo** de **configuração** > no portal do Azure.

Em seguida, determine se a fonte de dados deve estar disponível para um aplicativo ou para todos os aplicativos em execução no servlet do Tomcat.

#### <a name="application-level-data-sources"></a>Fontes de dados em nível de aplicativo

1. Crie um arquivo *Context. xml* no diretório *meta-inf/* do seu projeto. Crie o diretório *meta-inf/* se ele não existir.

2. Em *Context. xml*, adicione um `Context` elemento para vincular a fonte de dados a um endereço JNDI. Substitua o espaço reservado `driverClassName` pelo nome de classe do seu driver da tabela acima.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Atualize o *Web. xml* do seu aplicativo para usar a fonte de dados em seu aplicativo.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Recursos de nível de servidor compartilhados

1. Copie o conteúdo de */usr/local/Tomcat/conf* para */Home/Tomcat/conf* em sua instância do serviço de aplicativo do Linux usando SSH se você ainda não tiver uma configuração.

    ```bash
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Adicione um elemento de contexto em seu *Server. xml* dentro `<Server>` do elemento.

    ```xml
    <Server>
    ...
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ...
    </Server>
    ```

3. Atualize o *Web. xml* do seu aplicativo para usar a fonte de dados em seu aplicativo.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Finalizar a configuração

Por fim, coloque os JARs do driver no classpath do Tomcat e reinicie o serviço de aplicativo.

1. Certifique-se de que os arquivos do driver JDBC estejam disponíveis para o carregador de diretório do Tomcat colocando-os no */Home/Tomcat/lib* Directory. (Crie esse diretório se ele ainda não existir.) Para carregar esses arquivos na instância do Serviço de Aplicativo, siga estas etapas:

    1. No [Cloud Shell](https://shell.azure.com), instale a extensão webapp:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Execute o seguinte comando da CLI para criar um túnel SSH do seu sistema local para o serviço de aplicativo:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Conecte-se à porta de túnel local com seu cliente SFTP e carregue os arquivos na pasta */Home/Tomcat/lib*

    Como alternativa, você pode usar um cliente de FTP para carregar o driver JDBC. Siga estas [instruções para obter suas credenciais FTP](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Se você tiver uma fonte de dados no nível do servidor, reinicie o aplicativo Linux Serviço de Aplicativo. O Tomcat redefinirá `CATALINA_HOME` como `/home/tomcat/conf` e usará a configuração atualizada.

### <a name="spring-boot"></a>Spring Boot

Para se conectar a fontes de dados em aplicativos Spring boot, sugerimos criar cadeias de conexão e injeta-las em seu arquivo *Application. Properties* .

1. Na seção "configuração" da página Serviço de aplicativo, defina um nome para a cadeia de caracteres, Cole sua cadeia de conexão JDBC no campo valor e defina o tipo como "personalizado". Opcionalmente, você pode definir essa cadeia de conexão como configuração de slot.

    Essa cadeia de conexão é acessível ao nosso aplicativo como uma variável de `CUSTOMCONNSTR_<your-string-name>`ambiente chamada. Por exemplo, a cadeia de conexão que criamos acima será `CUSTOMCONNSTR_exampledb`nomeada.

2. No arquivo *Application. Properties* , referencie essa cadeia de conexão com o nome da variável de ambiente. Para nosso exemplo, usaremos o seguinte.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Consulte a [documentação do Spring boot sobre acesso a dados](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) e [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) para obter mais informações sobre este tópico.

## <a name="configure-java-ee-wildfly"></a>Configurar Java EE (WildFly)

> [!NOTE]
> O Java Enterprise Edition no serviço de aplicativo Linux está atualmente em versão prévia. Essa pilha **não** é recomendada para trabalho voltado para produção. informações sobre nossas pilhas do Java SE e Tomcat.

O serviço de Azure App no Linux permite que os desenvolvedores de Java compilem, implantem e dimensionem aplicativos Java Enterprise (Java EE) em um serviço totalmente gerenciado baseado em Linux.  O Java Enterprise Runtime Environment subjacente é o servidor de aplicativos [WildFly](https://wildfly.org/) de código aberto.

Essa seção contém os seguintes procedimentos:

- [Dimensionar com o serviço de aplicativo](#scale-with-app-service)
- [Personalizar a configuração do servidor de aplicativos](#customize-application-server-configuration)
- [Instalar módulos e dependências](#install-modules-and-dependencies)
- [Configurar fontes de dados](#configure-data-sources)
- [Habilitar provedores de mensagens](#enable-messaging-providers)
- [Configurar o cache de gerenciamento de sessão](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>Escalonar com o Serviço de Aplicativo

O servidor de aplicativos WildFly em execução no Serviço de Aplicativo no Linux é executado em modo autônomo, não em uma configuração de domínio. Quando você escala horizontalmente o Plano do Serviço de Aplicativo, cada instância de WildFly é configurada como um servidor autônomo.

Dimensione seu aplicativo vertical ou horizontalmente com [regras de dimensionamento](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) e [aumentando sua contagem de instâncias](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Personalizar a configuração do servidor de aplicativos

As instâncias do aplicativo Web são sem estado, portanto, cada nova instância iniciada deve ser configurada na inicialização para dar suporte à configuração WildFly necessária para o aplicativo.
Você pode escrever uma script Bash para chamar a CLI WildFly para inicialização:

- Principais fontes de dados
- Configurar provedores de mensagens
- Adicione outros módulos e dependências à configuração do servidor WildFly.

O script é executado quando o WildFly está em execução, mas antes de o aplicativo ser iniciado. O script deve usar a [CLI do JBoss](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) chamada de */opt/JBoss/wildfly/bin/JBoss-CLI.sh* para configurar o servidor de aplicativos com qualquer configuração ou alteração necessária depois que o servidor for iniciado.

Não use o modo interativo da CLI para configurar o WildFly. Em vez disso, é possível fornecer um script de comandos da CLI JBoss usando o comando `--file`, por exemplo:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Use o FTP para carregar o script de inicialização em um local em sua instância do serviço de aplicativo em seu diretório */Home* , como */Home/site/Deployments/Tools*. Para obter mais informações, consulte [implantar seu aplicativo no serviço de Azure App usando FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

Defina o campo **script de inicialização** no portal do Azure para o local do script do Shell de inicialização, por exemplo */Home/site/Deployments/Tools/Your-Startup-script.sh*.

Forneça [configurações](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) de aplicativo na configuração do aplicativo para passar variáveis de ambiente para uso no script. As configurações de aplicativo mantém cadeias de caracteres de conexão e outros segredos necessários para configurar seu aplicativo fora do controle de versão.

### <a name="install-modules-and-dependencies"></a>Instalar módulos e dependências

Para instalar os módulos e suas dependências no classpath do WildFly por meio da CLI do JBoss, você precisará criar os seguintes arquivos em seu próprio diretório. Alguns módulos e dependências talvez precisem de configuração adicional, como nomenclatura JNDI ou outra configuração específica de API; portanto, essa lista é um conjunto mínimo do que será necessário para configurar uma dependência na maioria dos casos.

- Um [descritor do módulo XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Esse arquivo XML define o nome, os atributos e as dependências do seu módulo. Esse [arquivo module.xml de exemplo](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) define um módulo Postgres, sua dependência do JDBC de arquivo JAR e outras dependências de módulo necessárias.
- Quaisquer dependências de arquivo JAR necessárias para seu módulo.
- Um script com seus comandos da CLI JBoss para configurar o novo módulo. Esse arquivo conterá seus comandos para serem executados pela CLI JBoss para configurar o servidor para usar a dependência. Para obter documentação sobre os comandos para adicionar módulos e provedores de mensagens, veja [este documento](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Um script de inicialização de Bash para chamar a CLI JBoss e executar o script na etapa anterior. Esse arquivo será executado quando a instância do Serviço de Aplicativo for reiniciada ou quando novas instâncias forem provisionadas durante uma expansão. Nesse script de inicialização, você pode executar quaisquer outras configurações para seu aplicativo conforme os comandos JBoss são passados para a CLI JBoss. No mínimo, esse arquivo pode ser um único comando para passar seu script de comando da CLI JBoss para a CLI JBoss:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Depois de ter os arquivos e o conteúdo do seu módulo, siga as etapas abaixo para adicionar o módulo ao servidor de aplicativos WildFly.

1. Use o FTP para carregar seus arquivos em um local em sua instância do serviço de aplicativo em seu diretório */Home* , como */Home/site/Deployments/Tools*. Para obter mais informações, consulte [implantar seu aplicativo no serviço de Azure App usando FTP/S](../deploy-ftp.md).
2. Na página**configurações gerais** de **configuração** > do portal do Azure, defina o campo **script de inicialização** como o local do script do Shell de inicialização, por exemplo, */Home/site/Deployments/Tools/Startup.sh*.
3. Reinicie a instância do serviço de aplicativo pressionando o botão **reiniciar** na seção **visão geral** do portal ou usando o CLI do Azure.

### <a name="configure-data-sources"></a>Configurar fontes de dados

Para configurar o WildFly/JBoss para acessar uma fonte de dados, use o processo geral descrito acima na seção "instalar módulos e dependências". A seção a seguir fornece detalhes específicos sobre esse processo para as fontes de dados PostgreSQL, MySQL e SQL Server.

Esta seção pressupõe que você já tem um aplicativo, uma instância do serviço de aplicativo e uma instância do serviço de banco de dados do Azure. As instruções a seguir referem-se ao nome do serviço de aplicativo, seu grupo de recursos e suas informações de conexão de banco de dados. Você pode encontrar essas informações no portal do Azure.

Se você preferir percorrer todo o processo desde o início usando um aplicativo de exemplo, consulte [o tutorial: Crie um aplicativo Web Java EE e postgres no Azure](tutorial-java-enterprise-postgresql-app.md).

As etapas a seguir explicam os requisitos para conectar seu serviço de aplicativo e banco de dados existentes.

1. Baixe o driver JDBC para [PostgreSQL](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/)ou [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server). Descompacte o arquivo baixado para obter o arquivo. jar do driver.

2. Crie um arquivo com um nome como *Module. xml* e adicione a marcação a seguir. Substitua o `<module name>` espaço reservado (incluindo os colchetes angulares `org.postgres` ) por para `com.mysql` PostgreSQL, para MySQL `com.microsoft` ou para SQL Server. Substitua `<JDBC .jar file path>` pelo nome do arquivo. jar da etapa anterior, incluindo o caminho completo para o local em que você colocará o arquivo em sua instância do serviço de aplicativo. Pode ser qualquer local no diretório */Home*

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

3. Crie um arquivo com um nome como *DataSource-Commands. CLI* e adicione o código a seguir. Substitua `<JDBC .jar file path>` pelo valor usado na etapa anterior. Substitua `<module file path>` pelo nome do arquivo e pelo caminho do serviço de aplicativo da etapa anterior, por exemplo, */Home/Module.xml*.

    **PostgreSQL**

    ```console
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker

    reload --use-current-server-config=true
    ```

    **MySQL**

    ```console
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)

    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter

    reload --use-current-server-config=true
    ```

    **SQL Server**

    ```console
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)

    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter

    reload --use-current-server-config=true
    ```

    Esse arquivo é executado pelo script de inicialização descrito na próxima etapa. Ele instala o driver JDBC como um módulo WildFly, cria a fonte de dados WildFly correspondente e recarrega o servidor para garantir que as alterações entrarão em vigor.

4. Crie um arquivo com um nome como *Startup.sh* e adicione o código a seguir. Substitua `<JBoss CLI script>` pelo nome do arquivo que você criou na etapa anterior. Certifique-se de incluir o caminho completo para o local em que você posicionará o arquivo em sua instância do serviço de aplicativo, por exemplo, */Home/DataSource-Commands.CLI*.

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. Use o FTP para carregar o arquivo JDBC. jar, o arquivo XML do módulo, o script da CLI do JBoss e o script de inicialização para sua instância do serviço de aplicativo. Coloque esses arquivos no local especificado nas etapas anteriores, como */Home*. Para obter mais informações sobre o FTP, consulte [implantar seu aplicativo no serviço de Azure App usando FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

6. Use o CLI do Azure para adicionar configurações ao serviço de aplicativo que contêm suas informações de conexão de banco de dados. Substitua `<resource group>` e`<webapp name>` pelos valores usados pelo serviço de aplicativo. Substitua `<database server name>`, `<database name>`, `<admin name>`e pelasinformaçõesdeconexãodobancodedados.`<admin password>` Você pode obter as informações do serviço de aplicativo e do banco de dados do portal do Azure.

    **PostgreSQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **SQL Server:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

    Os valores de DATABASE_CONNECTION_URL são diferentes para cada servidor de banco de dados e diferentes dos valores na portal do Azure. Os formatos de URL mostrados aqui (e nos trechos de código acima) são necessários para o uso do WildFly:

    * **PostgreSQL:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. Na portal do Azure, navegue até o serviço de aplicativo e localize a página**configurações gerais** de **configuração** > . Defina o campo **script de inicialização** como o nome e o local do script de inicialização, por exemplo, */Home/Startup.sh*.

Na próxima vez em que o serviço de aplicativo for reiniciado, ele executará o script de inicialização e executará as etapas de configuração necessárias. Para testar se essa configuração ocorre corretamente, você pode acessar o serviço de aplicativo usando o SSH e, em seguida, executar o script de inicialização por conta própria no prompt do bash. Você também pode examinar os logs do serviço de aplicativo. Para obter mais informações sobre essas opções, consulte [log and Debugging apps](#logging-and-debugging-apps).

Em seguida, você precisará atualizar a configuração do WildFly para seu aplicativo e reimplantá-lo. Use as seguintes etapas:

1. Abra o arquivo *src/main/resources/META-INF/persistence. xml* para seu aplicativo e `<jta-data-source>` localize o elemento. Substitua seu conteúdo, como mostrado aqui:

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

2. Recompile e reimplante seu aplicativo usando o seguinte comando no prompt do bash:

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. Reinicie a instância do serviço de aplicativo pressionando o botão **reiniciar** na seção **visão geral** do portal do Azure ou usando o CLI do Azure.

Sua instância do serviço de aplicativo agora está configurada para acessar seu banco de dados.

Para obter mais informações sobre como configurar a conectividade de banco de dados com o WildFly, consulte [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)ou [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898).

### <a name="enable-messaging-providers"></a>Habilitar provedores de mensagens

Para habilitar Beans controlado por mensagens usando o Barramento de Serviço como o mecanismo de mensagens:

1. Use a [biblioteca de mensagens do Apache QPId JMS](https://qpid.apache.org/proton/index.html). Inclua essa dependência em seu pom.xml (ou outro arquivo de build) para o aplicativo.

2. Crie os [recursos do Barramento de Serviço](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Crie um namespace do Barramento de Serviço do Azure, uma fila dentro desse namespace e uma Política de acesso compartilhada com funcionalidades de envio e recebimento.

3. Passe a chave da política de acesso compartilhada para seu código por meio da codificação de URL da chave primária de sua política ou [Use o SDK do Barramento de Serviço](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Siga as etapas descritas na seção Instalando Módulos e Dependências com seu descritor XML do módulo, dependências .jar, comandos da CLI JBoss e script de inicialização do provedor JMS. Além dos quatro arquivos, também será necessário criar um arquivo XML que define o nome JNDI para a fila JMS e para o tópico. Confira [este repositório](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) para arquivos de configuração de referência.

### <a name="configure-session-management-caching"></a>Configurar o cache de gerenciamento de sessão

Por padrão, o Serviço de Aplicativo no Linux usará cookies de afinidade de sessão para garantir que as solicitações de cliente com sessões existentes sejam roteadas para a mesma instância do seu aplicativo. Esse comportamento padrão não requer nenhuma configuração, mas tem algumas limitações:

- Se uma instância do aplicativo for reiniciada ou reduzida verticalmente, o estado de sessão do usuário no servidor de aplicativos será perdido.
- Se os aplicativos tiverem configurações de tempo limite de sessão longo ou um número fixo de usuários, poderá levar algum tempo para que novas instâncias com dimensionamento automático recebam carga, uma vez que apenas novas sessões serão roteadas a instâncias recém-iniciadas.

Você pode configurar o WildFly para usar um armazenamento de sessão externa, como o [cache do Azure para Redis](/azure/azure-cache-for-redis/). Você precisará [desabilitar a configuração de afinidade da instância arr existente](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) para desativar o roteamento baseado em cookie de sessão e permitir que o repositório de sessão WildFly configurado opere sem interferência.

## <a name="docker-containers"></a>Contêineres do Docker

Para usar o JDK do Zulu com suporte do Azure em seus contêineres, efetue pull e use as imagens predefinidas conforme documentado em [página de download do Azul Zulu Enterprise para Azure com suporte](https://www.azul.com/downloads/azure-only/zulu/) ou use os exemplos do `Dockerfile` dos [Repositório GitHub do Microsoft Java](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Declaração de suporte

### <a name="runtime-availability"></a>Disponibilidade de tempo de execução

O Serviço de Aplicativo para Linux dá suporte a dois tempos de execução para hospedagem gerenciada de aplicativos Web do Java:

- O [contêiner do servlet do Tomcat](https://tomcat.apache.org/) para execução de aplicativos empacotados como arquivos Web (WAR). As versões com suporte são 8.5 e 9.0.
- O ambiente de tempo de execução do Java SE para executar aplicativos empacotados como arquivos Java (JAR). As versões com suporte são Java 8 e 11.

### <a name="jdk-versions-and-maintenance"></a>Versões e manutenção do JDK

Builds do Azul Zulu Enterprise do OpenJDK são uma distribuição sem custo, multiplataforma e pronta para produção do OpenJDK para Azure e Azure Stack da Microsoft e da Azul Systems. Eles contêm todos os componentes para criar e executar aplicativos Java SE. Você pode instalar o JDK da [instalação do Java JDK](https://aka.ms/azure-jdks).

Os JDKs com suporte são corrigidos automaticamente a cada trimestre em janeiro, abril, julho e outubro de cada ano.

### <a name="security-updates"></a>Atualizações de segurança

Os patches e as correções para grandes vulnerabilidades de segurança serão liberados assim que forem disponibilizados pela Azul Systems. Uma "grande" vulnerabilidade é definida por uma pontuação básica de 9,0 ou mais na [versão 2 do NIST Common Vulnerability Scoring System](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Reprovação e desativação

Se um tempo de execução do Java com suporte for desativado, os desenvolvedores do Azure que usam o tempo de execução afetado receberão um aviso de reprovação pelo menos seis meses antes de o tempo de execução ser desativado.

## <a name="next-steps"></a>Próximas etapas

Acesse a central para [Desenvolvedores do Azure para Java](/java/azure/) para conferir inícios rápidos, tutoriais e documentação de referência do Java.

Dúvidas gerais sobre como usar o Serviço de Aplicativo para Linux que não são específicas do desenvolvimento em Java são respondidas nas [Perguntas frequentes do Serviço de Aplicativo no Linux](app-service-linux-faq.md).
