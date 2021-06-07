---
title: Configurar aplicativos Java
description: Saiba como configurar aplicativos Java para execução no Azure App Service. Este artigo mostra as tarefas de configuração mais comuns.
keywords: serviço de aplicativo do Azure, aplicativo Web, Windows, OSS, Java, Tomcat, JBoss
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: cc532c5ac6babb8378860ac5049e931cc7657932
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629250"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Configurar um aplicativo Java para o Serviço de Aplicativo do Azure

Azure App serviço permite que os desenvolvedores de Java compilem, implantem e dimensionem rapidamente seus aplicativos Web Java SE, Tomcat e JBoss em um serviço totalmente gerenciado. Implante aplicativos com plug-ins do Maven, na linha de comando ou em editores como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece os principais conceitos e instruções para desenvolvedores Java usando o serviço de aplicativo. Se você nunca usou Azure App serviço, leia primeiro o [início rápido do Java](quickstart-java.md) . As perguntas gerais sobre como usar o serviço de aplicativo que não são específicas do desenvolvimento do Java são respondidas nas [perguntas frequentes do serviço de aplicativo](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Implantação do aplicativo

Você pode usar o [plug-in do aplicativo Web do Azure para Maven](https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) para implantar seus arquivos. War ou. jar. A implantação com IDEs populares também tem suporte com o [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) ou [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

Caso contrário, o método de implantação dependerá de seu tipo de arquivo:

### <a name="java-se"></a>Java SE

Para implantar arquivos. jar em Java SE, use o `/api/zipdeploy/` ponto de extremidade do site kudu. Para obter mais informações sobre essa API, confira [essa documentação](./deploy-zip.md#rest).

### <a name="tomcat"></a>Tomcat

Para implantar arquivos .war para Tomcat, use o ponto de extremidade `/api/wardeploy/` para executar POST de seu arquivo morto. Para obter mais informações sobre essa API, confira [essa documentação](./deploy-zip.md#deploy-war-file).

::: zone pivot="platform-linux"

### <a name="jboss-eap"></a>JBoss EAP

Para implantar arquivos. War no JBoss, use o `/api/wardeploy/` ponto de extremidade para postar o arquivo morto. Para obter mais informações sobre essa API, confira [essa documentação](./deploy-zip.md#deploy-war-file).

Para implantar arquivos. ear, [use o FTP](deploy-ftp.md).

::: zone-end

Não implante seu .war ou .jar usando FTP. A ferramenta FTP foi projetada para carregar os scripts de inicialização, dependências ou outros arquivos de runtime. Não é a opção ideal para a implantação de aplicativos Web.

## <a name="logging-and-debugging-apps"></a>Aplicativos de registro em log e depuração

Relatórios de desempenho, visualizações de tráfego e verificações de integridade estão disponíveis para cada aplicativo por meio do portal do Azure. Para obter mais informações, confira [Visão geral de diagnóstico do Serviço de Aplicativo do Azure](overview-diagnostics.md).

### <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

Para obter mais informações, confira [Logs de fluxo no Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

::: zone pivot="platform-linux"

### <a name="ssh-console-access"></a>Acesso ao console SSH

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="troubleshooting-tools"></a>Ferramentas de solução de problemas

As imagens Java internas são baseadas no sistema operacional [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html). Use o gerenciador de pacotes `apk` para instalar quaisquer ferramentas ou comandos de solução de problemas.

::: zone-end

### <a name="flight-recorder"></a>Flight Recorder

Todos os tempos de execução do Java no serviço de aplicativo usando o azul JVMs vêm com o gravador de vôo Zulu. Você pode usar isso para registrar eventos da JVM, do sistema e do aplicativo e solucionar problemas em seus aplicativos Java.

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>Gravação programada

Para fazer uma gravação cronometrada, você precisará do PID (ID do processo) do aplicativo Java. Para localizar o PID, abra um navegador para o site do SCM do seu aplicativo Web em https://<your-site-Name>. scm.azurewebsites.net/ProcessExplorer/. Esta página mostra os processos em execução em seu aplicativo Web. Localize o processo chamado "Java" na tabela e copie o PID correspondente (ID do processo).

Em seguida, abra o **console de depuração** na barra de ferramentas superior do site do SCM e execute o comando a seguir. Substitua `<pid>` pela ID do processo que você copiou anteriormente. Esse comando iniciará uma gravação do criador de perfil de 30 segundos de seu aplicativo Java e gerará um arquivo chamado `timed_recording_example.jfr` no `D:\home` diretório.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

Use SSH em seu serviço de aplicativo e execute o `jcmd` comando para ver uma lista de todos os processos Java em execução. Além do próprio jcmd, você deve ver seu aplicativo Java em execução com um número de pid (ID do processo).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Execute o comando abaixo para iniciar uma gravação de 30 segundos da JVM. Isso criará o perfil da JVM e um arquivo JFR chamado *jfr_example.jfr* no diretório inicial. (Substitua 116 pelo pid do seu aplicativo Java.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Durante o intervalo de 30 segundos, é possível validar a gravação executando `jcmd 116 JFR.check`. Isso mostrará todas as gravações para o processo Java fornecido.

#### <a name="continuous-recording"></a>Gravação contínua

Você pode usar o Zulu Flight Recorder para criar um perfil contínuo de seu aplicativo Java com impacto mínimo no desempenho do tempo de execução ([origem](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Para fazer isso, execute o comando da CLI do Azure a seguir para criar uma Configuração de Aplicativo chamada JAVA_OPTS com a configuração necessária. O conteúdo da Configuração de Aplicativo JAVA_OPTS é passado para o comando `java` quando o aplicativo é iniciado.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Após o início da gravação, você pode despejar os dados da gravação atual a qualquer momento usando o comando `JFR.dump`.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

::: zone-end

#### <a name="analyze-jfr-files"></a>Analisar `.jfr` arquivos

Use o [FTPS](deploy-ftp.md) para baixar o arquivo JFR no seu computador local. Para analisar o arquivo JFR, baixe e instale o [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Para obter instruções sobre o Zulu Mission Control, confira a [documentação do Azul](https://docs.azul.com/zmc/) e as [instruções de instalação](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

### <a name="app-logging"></a>Registro em log do aplicativo

::: zone pivot="platform-windows"

Habilite o [registro em log de aplicativos](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) por meio do portal do Azure ou da [CLI do Azure](/cli/azure/webapp/log#az-webapp-log-config) para configurar o Serviço de Aplicativo do Azure para gravar a saída do console padrão do aplicativo e os fluxos de erro do console padrão no sistema de arquivos local ou no Armazenamento de Blobs do Azure. O registro em log na instância do sistema de arquivos do Serviço de Aplicativo local será desabilitado 12 horas após ser configurado. Se você precisar de uma retenção mais longa, configure o aplicativo para gravar a saída em um contêiner do armazenamento de blobs. Seus logs de aplicativos Java e Tomcat podem ser encontrados no diretório */home/LogFiles/Application/* .

::: zone-end
::: zone pivot="platform-linux"

Habilite o [registro em log de aplicativos](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) por meio do portal do Azure ou da [CLI do Azure](/cli/azure/webapp/log#az-webapp-log-config) para configurar o Serviço de Aplicativo do Azure para gravar a saída do console padrão do aplicativo e os fluxos de erro do console padrão no sistema de arquivos local ou no Armazenamento de Blobs do Azure. Se você precisar de uma retenção mais longa, configure o aplicativo para gravar a saída em um contêiner do armazenamento de blobs. Seus logs de aplicativos Java e Tomcat podem ser encontrados no diretório */home/LogFiles/Application/* .

O registro em log do Armazenamento de Blobs do Azure para Serviços de Aplicativo com base no Linux pode ser configurado apenas usando o [Azure Monitor (versão prévia)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) 

::: zone-end

Se o aplicativo usar [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para rastreamento, será possível encaminhá-los para revisão no Azure Application Insights usando as instruções de configuração de estrutura de registros em [Explorar os logs de rastreamento de Java no Application Insights](../azure-monitor/app/java-trace-logs.md).

## <a name="customization-and-tuning"></a>Personalização e ajuste

O Serviço de Aplicativo do Azure para Linux é compatível com o ajuste e a personalização de caixas por meio do portal e da CLI do Azure. Examine os seguintes artigos para saber mais sobre a configuração de aplicativos Web específica para não Java:

- [Definir as configurações do aplicativo](configure-common.md#configure-app-settings)
- [Configurar um domínio personalizado](app-service-web-tutorial-custom-domain.md)
- [Configurar associações SSL](configure-ssl-bindings.md)
- [Adicionar uma CDN](../cdn/cdn-add-to-web-app.md)
- [Configurar o site do Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)


### <a name="set-java-runtime-options"></a>Definir opções de runtime do Java

Para definir a memória alocada ou outras opções de tempo de execução da JVM, crie uma [configuração de aplicativo](configure-common.md#configure-app-settings) chamada `JAVA_OPTS` com as opções. O serviço de aplicativo passa essa configuração como uma variável de ambiente para o tempo de execução Java quando ele é iniciado.

No portal do Azure, em **Configurações do aplicativo** para o aplicativo Web, crie uma configuração de aplicativo denominada `JAVA_OPTS` que inclui as configurações adicionais, como `-Xms512m -Xmx1204m`.

Para definir a configuração de aplicativo do plug-in do Maven, adicione marcas de configuração/valor à seção de plug-in do Azure. O seguinte exemplo define um tamanho de heap de Java específico mínimo e máximo:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Os desenvolvedores que executam um único aplicativo com um slot de implantação no Plano do Serviço de Aplicativo podem usar as seguintes opções:

- Instâncias B1 e S1: `-Xms1024m -Xmx1024m`
- Instâncias B2 e S2: `-Xms3072m -Xmx3072m`
- Instâncias B3 e S3: `-Xms6144m -Xmx6144m`

Ao ajustar as configurações de heap do aplicativo, examine os detalhes do Plano do Serviço de Aplicativo e considere os vários aplicativos e slots de implantação necessários para encontrar a alocação de memória ideal.

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

### <a name="pre-compile-jsp-files"></a>Pré-compilar arquivos JSP

Para melhorar o desempenho dos aplicativos Tomcat, você pode compilar seus arquivos JSP antes de implantar no Serviço de Aplicativo. Você pode usar o [plug-in do Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornecido pelo Apache Sling ou usando este [arquivo de compilação Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Aplicativos seguros

Aplicativos Java em execução no serviço de aplicativo têm o mesmo conjunto de [práticas recomendadas de segurança](../security/fundamentals/paas-applications-using-app-services.md) que outros aplicativos.

### <a name="authenticate-users-easy-auth"></a>Autenticar usuários (autenticação fácil)

Configure a autenticação de aplicativo no portal do Azure com a opção **Autenticação e Autorização**. Em seguida, será possível habilitar a autenticação usando o Azure Active Directory ou os logons de redes sociais, como Facebook, Google ou GitHub. A configuração do portal do Azure só funciona ao configurar um único provedor de autenticação. Para saber mais, confira [Configurar o aplicativo do Serviço de Aplicativo para usar o logon do Azure Active Directory](configure-authentication-provider-aad.md) e os artigos relacionados para outros provedores de identidade. Se você precisar habilitar vários provedores de entrada, siga as instruções no artigo [Personalizar a autenticação do Serviço de Aplicativo](app-service-authentication-how-to.md).

#### <a name="java-se"></a>Java SE

Os desenvolvedores do Spring Boot podem usar o [inicializador do Spring Boot do Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory) para proteger aplicativos usando APIs e anotações conhecidas do Spring Security. Lembre-se de aumentar o tamanho máximo do cabeçalho no arquivo *application.properties*. Sugerimos um valor igual a `16384`.

#### <a name="tomcat"></a>Tomcat

Seu aplicativo Tomcat pode acessar as declarações do usuário diretamente do servlet, convertendo o objeto Principal em um objeto Map. O objeto Map mapeia cada tipo de declaração para uma coleção de declarações para esse tipo. No código abaixo, `request` é uma instância de `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Agora você pode inspecionar o objeto `Map` para qualquer declaração específica. Por exemplo, o trecho de código a seguir itera todos os tipos de declaração e imprime o conteúdo de cada coleção.

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

Para desconectar os usuários, use o caminho `/.auth/ext/logout`. Para executar outras ações, confira a documentação em [Uso de autenticação e autorização do Serviço de Aplicativo](./app-service-authentication-how-to.md). Também há documentação oficial na interface [HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) do Tomcat e seus métodos. Os seguintes métodos de servlet também são hidratados com base na sua configuração do Serviço de Aplicativo:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Para desabilitar esse recurso, crie uma Configuração de Aplicativo chamada `WEBSITE_AUTH_SKIP_PRINCIPAL` com um valor de `1`. Para desabilitar todos os filtros de servlet adicionados pelo Serviço de Aplicativo, crie uma configuração chamada `WEBSITE_SKIP_FILTERS` com um valor `1`.

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções em [Proteger um nome DNS personalizado com uma associação SSL no Serviço de Aplicativo do Azure](configure-ssl-bindings.md) para carregar um certificado SSL existente e associá-lo ao nome de domínio do aplicativo. Por padrão, o aplicativo ainda permitirá conexões HTTP – siga as etapas específicas no tutorial para impor o SSL e o TLS.

### <a name="use-keyvault-references"></a>Usar referências de Key Vault

O [Azure KeyVault](../key-vault/general/overview.md) fornece gerenciamento secreto centralizado com políticas de acesso e histórico de auditoria. Você pode armazenar segredos (como senhas ou cadeias de conexão) no KeyVault e acessar esses segredos no seu aplicativo por meio de variáveis de ambiente.

Primeiro, siga as instruções para [conceder ao seu aplicativo acesso ao Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e [fazer uma referência do KeyVault ao seu segredo em uma Configuração de Aplicativo](app-service-key-vault-references.md#reference-syntax). Você pode validar que a referência seja resolvida no segredo imprimindo a variável de ambiente enquanto acessa remotamente o terminal do Serviço de Aplicativo.

Para injetar esses segredos em seu arquivo de configuração Spring ou Tomcat, use a sintaxe de injeção de variável de ambiente (`${MY_ENV_VAR}`). Para arquivos de configuração do Spring, confira esta documentação sobre [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>Usar o repositório de chaves Java

Por padrão, todos os certificados públicos ou privados [carregados no Serviço de Aplicativo do Linux](configure-ssl-certificate.md) serão carregados nos respectivos Repositório de Chaves Java quando o contêiner for iniciado. Após carregar seu certificado, você precisará reiniciar o Serviço de Aplicativo para que ele seja carregado no Repositório de Chaves Java. Os certificados públicos são carregados no Repositório de Chaves em `$JAVA_HOME/jre/lib/security/cacerts` e os certificados privados são armazenados em `$JAVA_HOME/lib/security/client.jks`.

A configuração adicional pode ser necessária para criptografar sua conexão JDBC com certificados no Repositório de Chaves Java. Confira a documentação para o driver JDBC escolhido.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>Inicializar o repositório de chaves Java

Para inicializar o objeto `import java.security.KeyStore`, carregue o arquivo do repositório de chaves com a senha. A senha padrão para ambos os repositórios de chaves é "changeit".

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>Carregar manualmente o repositório de chaves

Você pode carregar certificados manualmente no repositório de chaves. Crie uma configuração de aplicativo, `SKIP_JAVA_KEYSTORE_LOAD`, com um valor de `1` para desabilitar o Serviço de Aplicativo do carregamento dos certificados no repositório de chaves automaticamente. Todos os certificados públicos carregados no Serviço de Aplicativo por meio do portal do Azure são armazenados em `/var/ssl/certs/`. Os certificados particulares são armazenados em `/var/ssl/private/`.

Você pode interagir ou depurar a Java Key Tool [abrindo uma conexão SSH](configure-linux-open-ssh-session.md) no seu Serviço de Aplicativo e executando o comando `keytool`. Confira a [documentação da Key Tool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) para obter uma lista de comandos. Para obter mais informações sobre a API KeyStore, confira [a documentação oficial](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

::: zone-end

## <a name="configure-apm-platforms"></a>Configurar plataformas APM

Esta seção mostra como conectar aplicativos Java implantados no Serviço de Aplicativo do Azure no Linux às APM (plataformas de monitoramento de desempenho) NewRelic e AppDynamics.

### <a name="configure-new-relic"></a>Configurar o New Relic

::: zone pivot="platform-windows"

1. Criar uma conta de NewRelic em [NewRelic.com](https://newrelic.com/signup)
2. Baixe o agente de Java do NewRelic; ele terá um nome de arquivo semelhante a *newrelic-java-x.x.x.zip*.
3. Copie sua chave de licença, você precisará dela para configurar o agente mais tarde.
4. [SSH em sua instância do Serviço de Aplicativo](configure-linux-open-ssh-session.md) e crie um diretório */home/site/wwwroot/apm*.
5. Carregue os arquivos do agente NewRelic Java descompactados em um diretório em */home/site/wwwroot/apm*. Os arquivos do seu agente devem estar em */home/site/wwwroot/apm/newrelic*.
6. Modifique o arquivo YAML em */home/site/wwwroot/apm/newrelic/newrelic.yml* e substitua o valor de licença de espaço reservado pela sua chave de licença.
7. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.

    - Para aplicativos **Java se** , crie uma variável de ambiente chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Para o **tomcat**, crie uma variável de ambiente chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end
::: zone pivot="platform-linux"

1. Criar uma conta de NewRelic em [NewRelic.com](https://newrelic.com/signup)
2. Baixe o agente de Java do NewRelic; ele terá um nome de arquivo semelhante a *newrelic-java-x.x.x.zip*.
3. Copie sua chave de licença, você precisará dela para configurar o agente mais tarde.
4. [SSH em sua instância do Serviço de Aplicativo](configure-linux-open-ssh-session.md) e crie um diretório */home/site/wwwroot/apm*.
5. Carregue os arquivos do agente NewRelic Java descompactados em um diretório em */home/site/wwwroot/apm*. Os arquivos do seu agente devem estar em */home/site/wwwroot/apm/newrelic*.
6. Modifique o arquivo YAML em */home/site/wwwroot/apm/newrelic/newrelic.yml* e substitua o valor de licença de espaço reservado pela sua chave de licença.
7. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
   
    - Para aplicativos **Java se** , crie uma variável de ambiente chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Para o **tomcat**, crie uma variável de ambiente chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end

>  Se você já tiver uma variável de ambiente para `JAVA_OPTS` ou `CATALINA_OPTS`, acrescente o `-javaagent:/...` opção ao final do valor atual.

### <a name="configure-appdynamics"></a>Configurar o AppDynamics

::: zone pivot="platform-windows"

1. Criar uma conta do AppDynamics em [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Baixe o agente de Java do site do AppDynamics, o nome do arquivo será semelhante a *AppServerAgent-x.x.x.xxxxx.zip*
3. Use o [console do kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) para criar um novo diretório */Home/site/wwwroot/APM*.
4. Carregue os arquivos do agente de Java descompactados em um diretório em */home/site/wwwroot/apm*. Os arquivos do seu agente devem estar em */home/site/wwwroot/apm/appdynamics*.
5. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.

   - Para aplicativos **Java se** , crie uma variável de ambiente chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` em que `<app-name>` é o nome do serviço de aplicativo.
   - Para aplicativos **tomcat** , crie uma variável de ambiente chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` em que `<app-name>` é o nome do serviço de aplicativo.

::: zone-end
::: zone pivot="platform-linux"

1. Criar uma conta do AppDynamics em [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Baixe o agente de Java do site do AppDynamics, o nome do arquivo será semelhante a *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH em sua instância do Serviço de Aplicativo](configure-linux-open-ssh-session.md) e crie um diretório */home/site/wwwroot/apm*.
4. Carregue os arquivos do agente de Java descompactados em um diretório em */home/site/wwwroot/apm*. Os arquivos do seu agente devem estar em */home/site/wwwroot/apm/appdynamics*.
5. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.

   - Para aplicativos **Java se** , crie uma variável de ambiente chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` em que `<app-name>` é o nome do serviço de aplicativo.
   - Para aplicativos **tomcat** , crie uma variável de ambiente chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` em que `<app-name>` é o nome do serviço de aplicativo.

::: zone-end

> [!NOTE]
>  Se você já tiver uma variável de ambiente para `JAVA_OPTS` ou `CATALINA_OPTS`, acrescente o `-javaagent:/...` opção ao final do valor atual.

## <a name="configure-data-sources"></a>Configurar fonte de dados

### <a name="java-se"></a>Java SE

Para conectar-se a fontes de dados nos aplicativos Spring Boot, sugerimos criar cadeias de conexão e injetá-las no arquivo *application.properties*.

1. Na seção "Configuração" da página Serviço de Aplicativo, defina um nome para a cadeia de caracteres, cole sua cadeia de conexão JDBC no campo de valor e defina o tipo como "Personalizado". Opcionalmente, defina essa cadeia de conexão como configuração de slot.

    Essa cadeia de conexão é acessível ao nosso aplicativo como uma variável de ambiente nomeada `CUSTOMCONNSTR_<your-string-name>`. Por exemplo, a cadeia de conexão que criamos acima será nomeada `CUSTOMCONNSTR_exampledb`.

2. No arquivo *application.properties*, faça referência a essa cadeia de conexão com o nome da variável de ambiente. Para o nosso exemplo, usaríamos o seguinte:

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Confira a [documentação do Spring Boot sobre acesso a dados](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) e [configurações externalizadas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) para obter mais informações sobre este tópico.

::: zone pivot="platform-windows"

### <a name="tomcat"></a>Tomcat

Essas instruções se aplicam a todas as conexões de banco de dados. Você precisará preencher os espaços reservados com nome de classe do driver do banco de dados escolhido e o arquivo JAR. É fornecida uma tabela com nomes de classe e downloads de driver para bancos de dados comuns.

| Banco de dados   | Nome de Classe do Driver                             | Driver JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Download](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Baixar](https://dev.mysql.com/downloads/connector/j/) (Selecione "Independente de Plataforma") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Download](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Para configurar o Tomcat para usar a JDBC (Conectividade de Banco de Dados Java) ou a JPA (API de Persistência Java), primeiro personalize a variável de ambiente `CATALINA_OPTS` lida em Tomcat na inicialização do backup. Defina esses valores por meio de uma configuração de aplicativo no [plug-in Maven do Serviço de Aplicativo](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Ou defina as variáveis de ambiente na página **Configuração** > **Configurações de Aplicativo** no portal do Azure.

Em seguida, determine se a fonte de dados deve estar disponível para um aplicativo ou para todos os aplicativos em execução no servlet do Tomcat.

#### <a name="application-level-data-sources"></a>Fontes de dados no nível do aplicativo

1. Crie um arquivo *context.xml* no diretório *META-INF/* do seu projeto. Crie o diretório *META-INF/* se ele não existir.

2. Em *context.xml*, adicione um elemento `Context` para vincular a fonte de dados a um endereço JNDI. Substitua o espaço reservado `driverClassName` pelo nome de classe do seu driver da tabela acima.

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

3. Atualize o *web.xml* do aplicativo para usar a fonte de dados no aplicativo.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Finalizar a configuração

Por fim, colocaremos os JARs do driver no classpath do Tomcat e reiniciaremos o serviço de aplicativo. Verifique se os arquivos do driver JDBC estão disponíveis para o carregador de classes do Tomcat colocando-os no diretório */home/tomcat/lib*. (Crie esse diretório se ele ainda não existir.) Para carregar esses arquivos na instância do Serviço de Aplicativo, siga estas etapas:

1. No [Cloud Shell](https://shell.azure.com), instale a extensão do aplicativo da Web:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Execute o seguinte comando de CLI para criar um túnel SSH do sistema local para o Serviço de Aplicativo:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Faça a conexão da porta de túnel local com o cliente SFTP e carregue os arquivos na pasta */home/tomcat/lib*.

Como alternativa, você pode usar um cliente de FTP para carregar o driver JDBC. Siga estas [instruções para obter suas credenciais FTP](deploy-configure-credentials.md).

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="tomcat"></a>Tomcat

Essas instruções se aplicam a todas as conexões de banco de dados. Você precisará preencher os espaços reservados com nome de classe do driver do banco de dados escolhido e o arquivo JAR. É fornecida uma tabela com nomes de classe e downloads de driver para bancos de dados comuns.

| Banco de dados   | Nome de Classe do Driver                             | Driver JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Download](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Baixar](https://dev.mysql.com/downloads/connector/j/) (Selecione "Independente de Plataforma") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Download](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Para configurar o Tomcat para usar a JDBC (Conectividade de Banco de Dados Java) ou a JPA (API de Persistência Java), primeiro personalize a variável de ambiente `CATALINA_OPTS` lida em Tomcat na inicialização do backup. Defina esses valores por meio de uma configuração de aplicativo no [plug-in Maven do Serviço de Aplicativo](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Ou defina as variáveis de ambiente na página **Configuração** > **Configurações de Aplicativo** no portal do Azure.

Em seguida, determine se a fonte de dados deve estar disponível para um aplicativo ou para todos os aplicativos em execução no servlet do Tomcat.

#### <a name="application-level-data-sources"></a>Fontes de dados no nível do aplicativo

1. Crie um arquivo *context.xml* no diretório *META-INF/* do seu projeto. Crie o diretório *META-INF/* se ele não existir.

2. Em *context.xml*, adicione um elemento `Context` para vincular a fonte de dados a um endereço JNDI. Substitua o espaço reservado `driverClassName` pelo nome de classe do seu driver da tabela acima.

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

3. Atualize o *web.xml* do aplicativo para usar a fonte de dados no aplicativo.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Recursos compartilhados no nível do servidor

A adição de uma fonte de dados compartilhada no nível do servidor exigirá a edição do server.xml do Tomcat. Primeiro, carregue um [script de inicialização](faq-app-service-linux.md#built-in-images) e defina o caminho para o script em **Configuração** > **Comando de Inicialização**. Você pode carregar o script de inicialização usando o [FTP](deploy-ftp.md).

O script de inicialização fará uma [transformação de xsl](https://www.w3schools.com/xml/xsl_intro.asp) no arquivo server.xml e produzirá o arquivo xml resultante para `/usr/local/tomcat/conf/server.xml`. O script de inicialização deve instalar o libxslt via apk. O arquivo xsl e o script de inicialização podem ser carregados via FTP. Confira abaixo um exemplo de script de inicialização.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Um exemplo de arquivo xsl é fornecido abaixo. O arquivo xsl de exemplo adiciona um novo nó do conector ao server.xml do Tomcat.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Finalizar a configuração

Por fim, coloque os JARs do driver no classpath do Tomcat e reinicie o Serviço de Aplicativo.

1. Verifique se os arquivos do driver JDBC estão disponíveis para o carregador de classes do Tomcat colocando-os no diretório */home/tomcat/lib*. (Crie esse diretório se ele ainda não existir.) Para carregar esses arquivos na instância do Serviço de Aplicativo, siga estas etapas:

    1. No [Cloud Shell](https://shell.azure.com), instale a extensão do aplicativo da Web:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Execute o seguinte comando de CLI para criar um túnel SSH do sistema local para o Serviço de Aplicativo:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Faça a conexão da porta de túnel local com o cliente SFTP e carregue os arquivos na pasta */home/tomcat/lib*.

    Como alternativa, você pode usar um cliente de FTP para carregar o driver JDBC. Siga estas [instruções para obter suas credenciais FTP](deploy-configure-credentials.md).

2. Se você tiver uma fonte de dados no nível do servidor, reinicie o aplicativo Linux Serviço de Aplicativo. O Tomcat redefinirá `CATALINA_BASE` como `/home/tomcat` e usará a configuração atualizada.

### <a name="jboss-eap"></a>JBoss EAP

Há três etapas principais ao [registrar uma fonte de dados com o JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management): carregando o driver JDBC, adicionando o driver JDBC como um módulo e registrando o módulo. O serviço de aplicativo é um serviço de hospedagem sem monitoração de estado, portanto, os comandos de configuração para adicionar e registrar o módulo de fonte de dados devem ser inseridos em script e aplicados conforme o contêiner é iniciado.

1. Obtenha o driver JDBC do seu banco de dados. 
2. Crie um arquivo de definição de módulo XML para o driver JDBC. O exemplo mostrado abaixo é uma definição de módulo para PostgreSQL.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.postgres">
        <resources>
        <!-- ***** IMPORTANT : REPLACE THIS PLACEHOLDER *******-->
        <resource-root path="/home/site/deployments/tools/postgresql-42.2.12.jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. Coloque os comandos da CLI do JBoss em um arquivo chamado `jboss-cli-commands.cli` . Os comandos do JBoss devem adicionar o módulo e registrá-lo como uma fonte de dados. O exemplo a seguir mostra os comandos da CLI do JBoss para PostgreSQL.

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. Crie um script de inicialização `startup_script.sh` que chama os comandos da CLI do JBoss. O exemplo a seguir mostra como chamar seu `jboss-cli-commands.cli` . Posteriormente, você configrerá o serviço de aplicativo para executar esse script quando o contêiner for iniciado. 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. Usando um cliente FTP de sua escolha, carregue seu driver JDBC, `jboss-cli-commands.cli` , `startup_script.sh` , e a definição do módulo para `/site/deployments/tools/` .
2. Configure seu site para ser executado `startup_script.sh` quando o contêiner for iniciado. No portal do Azure, navegue até **configuração**  >  **configurações gerais**  >  **comando de inicialização**. Defina o campo de comando de inicialização como `/home/site/deployments/tools/startup_script.sh` . **Salve** suas alterações.

Para confirmar que a fonte de fontes foi adicionada ao servidor JBoss, use SSH em seu webapp e execute `$JBOSS_HOME/bin/jboss-cli.sh --connect` . Quando você estiver conectado ao JBoss, execute o `/subsystem=datasources:read-resource` para imprimir uma lista de fontes de dados.

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>Escolhendo uma versão de tempo de execução Java

O serviço de aplicativo permite que os usuários escolham a versão principal da JVM, como Java 8 ou Java 11, bem como a versão secundária, como 1.8.0 _232 ou 11.0.5. Você também pode optar por fazer com que a versão secundária seja atualizada automaticamente conforme novas versões secundárias se tornarem disponíveis. Na maioria dos casos, os sites de produção devem usar versões secundárias fixadas. Isso impedirá interrupções de unnanticipated durante uma atualização automática de versão secundária.

Se você optar por fixar a versão secundária, será necessário atualizar periodicamente a versão secundária da JVM no site. Para garantir que seu aplicativo seja executado na versão secundária mais recente, crie um slot de preparo e aumente a versão secundária no site de preparo. Depois de confirmar que o aplicativo é executado corretamente na nova versão secundária, você pode trocar os slots de preparo e de produção.

## <a name="jboss-eap-hardware-options"></a>Opções de hardware de EAP JBoss

O protocolo JBoss só está disponível nas opções de hardware Premium e isolado. Os clientes que criaram um site do JBoss EAP em uma camada gratuita, compartilhada, básica ou Standard durante a visualização pública devem escalar verticalmente para a camada de hardware Premium ou isolada para evitar um comportamento inesperado.

## <a name="java-runtime-statement-of-support"></a>Declaração de suporte do runtime do Java

### <a name="jdk-versions-and-maintenance"></a>Versões e manutenção do JDK

O JDK (Java Development Kit) com suporte do Azure é o [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornecido pela [Azul Systems](https://www.azul.com/). Builds do Azul Zulu Enterprise do OpenJDK são uma distribuição sem custo, multiplataforma e pronta para produção do OpenJDK para Azure e Azure Stack da Microsoft e da Azul Systems. Eles contêm todos os componentes para criar e executar aplicativos Java SE. Você pode instalar o JDK a partir da [Instalação do Java JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support).

As atualizações da versão principal serão fornecidas por meio de novas opções de tempo de execução no serviço Azure App. Os clientes podem atualizar para essas versões mais recentes do Java configurando a implantação do Serviço de Aplicativo. Além disso, são responsáveis por testar e garantir que a atualização da versão principal atenda às suas necessidades.

Os JDKs com suporte são corrigidos automaticamente a cada trimestre em janeiro, abril, julho e outubro de cada ano. Para saber mais sobre Java no Azure, confira [este documento de suporte](/azure/developer/java/fundamentals/java-jdk-long-term-support).

### <a name="security-updates"></a>Atualizações de segurança

Os patches e as correções para grandes vulnerabilidades de segurança serão liberados assim que forem disponibilizados pela Azul Systems. Uma "grande" vulnerabilidade é definida por uma pontuação básica de 9,0 ou mais na [versão 2 do NIST Common Vulnerability Scoring System](https://nvd.nist.gov/vuln-metrics/cvss).

O Tomcat 8,0 atingiu [o fim da vida útil (EOL) a partir de 30 de setembro de 2018](https://tomcat.apache.org/tomcat-80-eol.html). Embora o tempo de execução ainda esteja disponível no serviço Azure App, o Azure não aplicará atualizações de segurança ao Tomcat 8,0. Se possível, migre seus aplicativos para o Tomcat 8,5 ou 9,0. O Tomcat 8,5 e o 9,0 estão disponíveis no serviço Azure App. Consulte o [site oficial do Tomcat](https://tomcat.apache.org/whichversion.html) para obter mais informações. 

### <a name="deprecation-and-retirement"></a>Reprovação e desativação

Se um runtime do Java com suporte for desativado, os desenvolvedores do Azure que usam o runtime afetado receberão um aviso de reprovação pelo menos seis meses antes de o runtime ser desativado.


### <a name="local-development"></a>Desenvolvimento local

Os desenvolvedores podem baixar a Production Edition do Azul Zulu Enterprise JDK para desenvolvimento local do [site de download da Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Suporte ao desenvolvimento

O suporte ao produto para o [azul Zulu com suporte do Azure](https://www.azul.com/downloads/azure-only/zulu/) está disponível por meio da Microsoft ao desenvolver para o azure ou [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) com um [plano de suporte qualificado do Azure](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Próximas etapas

Acesse a central para [Desenvolvedores do Azure para Java](/java/azure/) para conferir inícios rápidos, tutoriais e documentação de referência do Java.

Dúvidas gerais sobre como usar o Serviço de Aplicativo para Linux que não são específicas do desenvolvimento em Java são respondidas nas [Perguntas frequentes do Serviço de Aplicativo no Linux](faq-app-service-linux.md).
