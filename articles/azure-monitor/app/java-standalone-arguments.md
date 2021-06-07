---
title: Adicionando a JVM ARG-Azure Monitor Application Insights para Java
description: Como adicionar o ARG JVM que habilita Azure Monitor Application Insights para Java
ms.topic: conceptual
ms.date: 04/16/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: bd6ed2e5c848b39ad946209d5ea92fcf6ce3b438
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98625357"
---
# <a name="adding-the-jvm-arg---azure-monitor-application-insights-for-java"></a>Adicionando a JVM ARG-Azure Monitor Application Insights para Java



## <a name="azure-environments"></a>Ambientes do Azure

Configurar [serviços de aplicativos](../../app-service/configure-language-java.md#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Adicione o ARG de JVM `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` em algum lugar antes `-jar` , por exemplo:

```
java -javaagent:path/to/applicationinsights-agent-3.0.2.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Spring boot via ponto de entrada do Docker

Se você estiver usando o formulário *exec* , adicione o parâmetro `"-javaagent:path/to/applicationinsights-agent-3.0.2.jar"` à lista de parâmetros em algum lugar antes do `"-jar"` parâmetro, por exemplo:

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.2.jar", "-jar", "<myapp.jar>"]
```

Se você estiver usando o formulário do *shell* , adicione o ARG de JVM `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` em algum lugar antes `-jar` , por exemplo:

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.2.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat instalado via `apt-get` ou `yum`

Se você instalou o Tomcat via `apt-get` ou `yum` , você deve ter um arquivo `/etc/tomcat8/tomcat8.conf` .  Adicione esta linha ao final do arquivo:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.2.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat instalado por meio de download e descompactação

Se você instalou o Tomcat por meio de download e descompactação de [https://tomcat.apache.org](https://tomcat.apache.org) , você deve ter um arquivo `<tomcat>/bin/catalina.sh` .  Crie um novo arquivo no mesmo diretório chamado `<tomcat>/bin/setenv.sh` com o seguinte conteúdo:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.2.jar"
```

Se o arquivo `<tomcat>/bin/setenv.sh` já existir, modifique esse arquivo e adicione `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` ao `CATALINA_OPTS` .


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Executando o Tomcat a partir da linha de comando

Localize o arquivo `<tomcat>/bin/catalina.bat` .  Crie um novo arquivo no mesmo diretório chamado `<tomcat>/bin/setenv.bat` com o seguinte conteúdo:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.2.jar
```

As aspas não são necessárias, mas se você quiser incluí-las, o posicionamento adequado é:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.2.jar"
```

Se o arquivo `<tomcat>/bin/setenv.bat` já existir, basta modificar esse arquivo e adicionar `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` ao `CATALINA_OPTS` .

### <a name="running-tomcat-as-a-windows-service"></a>Executando o Tomcat como um serviço do Windows

Localize o arquivo `<tomcat>/bin/tomcat8w.exe` .  Execute esse executável e adicione `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` ao na `Java Options` `Java` guia.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Servidor autônomo

Adicione `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` à variável de `JAVA_OPTS` ambiente existente no arquivo `JBOSS_HOME/bin/standalone.conf` (Linux) ou `JBOSS_HOME/bin/standalone.conf.bat` (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.2.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Servidor de domínio

Adicionar `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` ao existente `jvm-options` no `JBOSS_HOME/domain/configuration/host.xml` :

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.2.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Se você estiver executando vários servidores gerenciados em um único host, será necessário adicionar `applicationinsights.agent.id` ao `system-properties` para cada `server` :

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

O `applicationinsights.agent.id` valor especificado deve ser exclusivo. Ele é usado para criar um subdiretório no diretório applicationinsights, pois cada processo da JVM precisa de sua própria configuração applicationinsights local e arquivo de log applicationinsights local. Além disso, se estiver relatando para o coletor central, o `applicationinsights.properties` arquivo será compartilhado por vários servidores gerenciados e, portanto, o especificado `applicationinsights.agent.id` será necessário para substituir a `agent.id` configuração nesse arquivo compartilhado. `applicationinsights.agent.rollup.id` pode ser especificado de forma semelhante no servidor `system-properties` se você precisar substituir a `agent.rollup.id` configuração por servidor gerenciado.


## <a name="jetty-9"></a>Jetty 9

Adicionar estas linhas a `start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.2.jar
```


## <a name="payara-5"></a>Payara 5

Adicionar `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` ao existente `jvm-options` no `glassfish/domains/domain1/config/domain.xml` :

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.2.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

Abra o console de gerenciamento vá para **servidores > Websphere Application servers > servidores de aplicativos**, escolha os servidores de aplicativos apropriados e clique em: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
Em "argumentos da JVM genérica", adicione o seguinte:
```
-javaagent:path/to/applicationinsights-agent-3.0.2.jar
```
Depois disso, salve e reinicie o servidor de aplicativos.


## <a name="openliberty-18"></a>OpenLiberty 18

Crie um novo arquivo `jvm.options` no diretório do servidor (por exemplo `<openliberty>/usr/servers/defaultServer` ,) e adicione esta linha:
```
-javaagent:path/to/applicationinsights-agent-3.0.2.jar
```
