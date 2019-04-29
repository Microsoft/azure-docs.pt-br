---
title: Guia do desenvolvedor Java para o Serviço de Aplicativo no Linux – Azure | Microsoft Docs
description: Saiba como configurar aplicativos Java em execução no Serviço de Aplicativo do Azure no Linux.
keywords: serviço de aplicativo do azure, aplicativo Web, linux, oss, java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 12/10/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 5c9f70650f518c72a75d9a7826e7cbc30a95a00c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852693"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Guia do desenvolvedor Java para o Serviço de Aplicativo no Linux

O Serviço de Aplicativo do Azure no Linux permite que desenvolvedores de Java criem, implantem e escalonem rapidamente os aplicativos Web empacotados do Tomcat ou do Java Standard Edition (SE) em um serviço baseado em Linux totalmente gerenciado. Implante aplicativos com o plug-ins do Maven na linha de comando ou em editores, como IntelliJ, Eclipse ou Visual Studio Code.

Este guia mostra os principais conceitos e instruções para desenvolvedores de Java que usam o Serviço de Aplicativo para Linux. Se você nunca tiver usado o Serviço de Aplicativo do Azure para Linux, leia o [Início rápido do Java](quickstart-java.md) primeiro. Dúvidas gerais sobre como usar o Serviço de Aplicativo para Linux que não são específicas do desenvolvimento em Java são respondidas nas [Perguntas frequentes do Serviço de Aplicativo no Linux](app-service-linux-faq.md).

## <a name="deploying-your-app"></a>Implantação do aplicativo

Você pode usar [plug-in do Maven para serviço de aplicativo do Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) para implantar os arquivos. jar e. war. Também há suporte para implantação com IDEs populares com [Kit de ferramentas do Azure para IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) ou [Kit de ferramentas do Azure para Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Caso contrário, o método de implantação será dependem de seu tipo de arquivo morto:

- Para implantar arquivos .war para Tomcat, use o ponto de extremidade `/api/wardeploy/` para executar POST de seu arquivo morto. Para obter mais informações sobre essa API, confira [essa documentação](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Para implantar arquivos .jar nas imagens do Java SE, use o ponto de extremidade `/api/zipdeploy/` do site do Kudu. Para obter mais informações sobre essa API, confira [essa documentação](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Não implante seu .war ou .jar usando FTP. A ferramenta FTP foi projetada para carregar os scripts de inicialização, dependências ou outros arquivos de tempo de execução. Não é a opção ideal para a implantação de aplicativos Web.

## <a name="logging-and-debugging-apps"></a>Aplicativos de registro em log e depuração

Relatórios de desempenho, visualizações de tráfego e verificações de integridade estão disponíveis para cada aplicativo por meio do portal do Azure. Confira a [Visão geral de diagnóstico do Serviço de Aplicativo do Azure](/azure/app-service/overview-diagnostics) para saber mais sobre como acessar e usar essas ferramentas de diagnóstico.

## <a name="application-performance-monitoring"></a>Monitoramento de desempenho de aplicativos

Consulte [Ferramentas de monitoramento de desempenho de aplicativos com aplicativos Java no Serviço de Aplicativo do Azure no Linux](how-to-java-apm-monitoring.md) para obter instruções sobre como configurar o New Relic e o AppDynamics com aplicativos Java executados no Serviço de Aplicativo no Linux.

### <a name="ssh-console-access"></a>Acesso ao console SSH

Conectividade SSH para o ambiente do Linux que executam seu aplicativo está disponível. Confira [Suporte do SSH para o Serviço de Aplicativo do Azure no Linux](/azure/app-service/containers/app-service-linux-ssh-support) para ver instruções completas sobre como se conectar com o sistema do Linux por meio do navegador da Web ou terminal local.

### <a name="streaming-logs"></a>Logs de streaming

Para depurar e solucionar problemas de maneira rápida, é possível transmitir logs para o console usando a CLI do Azure. Configure a CLI com o `az webapp log config` para habilitar o registro em log:

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

Em seguida, transmita os logs para o console usando `az webapp log tail`:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

Para saber mais, confira [Transmitir logs com a CLI do Azure](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Registro em log do aplicativo

Habilite o [registro em log de aplicativos](/azure/app-service/troubleshoot-diagnostic-logs#enablediag) por meio do portal do Azure ou da [CLI do Azure](/cli/azure/webapp/log#az-webapp-log-config) para configurar o Serviço de Aplicativo do Azure para gravar a saída do console padrão do aplicativo e os fluxos de erro do console padrão no sistema de arquivos local ou no Armazenamento de Blobs do Azure. O registro em log na instância do sistema de arquivos do Serviço de Aplicativo local será desabilitado 12 horas após ser configurado. Se você precisar de uma retenção mais longa, configure o aplicativo para gravar a saída em um contêiner do armazenamento de blobs. Seus logs de aplicativos Java e Tomcat podem ser encontradas no `/home/LogFiles/Application/` directory.

Se o aplicativo usar [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para rastreamento, será possível encaminhá-los para revisão no Azure Application Insights usando as instruções de configuração de estrutura de registros em [Explorar os logs de rastreamento de Java no Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Solucionando problemas de ferramentas

Imagens internas do Java se baseiam os [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) sistema operacional. Use o `apk` Gerenciador de pacotes para instalar qualquer solução de problemas de ferramentas ou comandos.

## <a name="customization-and-tuning"></a>Personalização e ajuste

O serviço de aplicativo do Azure para Linux dá suporte a fora a caixa de ajuste e a personalização por meio do portal do Azure e da CLI. Examine os seguintes artigos para saber mais sobre a configuração de aplicativos Web específica para não Java:

- [Definir configurações do Serviço de Aplicativo](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurar um domínio personalizado](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Habilitar SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Adicionar uma CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurar o site do Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Definir opções de tempo de execução do Java

Para definir a memória alocada ou outras opções de tempo de execução da JVM em ambientes de Java SE e o Tomcat, crie uma [configuração de aplicativo](/azure/app-service/web-sites-configure#app-settings) denominado `JAVA_OPTS` com as opções. O Serviço de Aplicativo no Linux passa essa configuração como uma variável de ambiente para o tempo de execução do Java quando ele é iniciado.

No portal do Azure, em **Configurações do aplicativo** para o aplicativo Web, crie uma configuração de aplicativo denominada `JAVA_OPTS` que inclui as configurações adicionais, como `-Xms512m -Xmx1204m`.

Para configurar a configuração de aplicativo do plug-in do Maven, adicione marcas/valor da configuração na seção de plug-in do Azure. O exemplo a seguir define um tamanho de heap de Java mínimo e máximo específico:

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

Se você estiver implantando um aplicativo de JAR, ele deve ser chamado `app.jar` para que a imagem interna possa identificar corretamente o seu aplicativo. (O plug-in do Maven faz essa renomeação automaticamente.) Se você não quiser renomear seu JAR do `app.jar`, você pode carregar um script de shell com o comando para executar o JAR. Em seguida, cole o caminho completo para esse script na [arquivo de inicialização](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-faq#startup-file) caixa de texto na seção de configuração do portal.

### <a name="turn-on-web-sockets"></a>Ativar os soquetes da Web

Ative o suporte para soquetes da Web no portal do Azure, nas **Configurações de aplicativo**. Será necessário reiniciar o aplicativo para a configuração entrar em vigor.

Ative o suporte para soquete da Web usando a CLI do Azure com o seguinte comando:

```azurecli-interactive
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true
```

Depois, reinicie o aplicativo:

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
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

### <a name="adjust-startup-timeout"></a>Ajustar o tempo limite de inicialização

Se seu aplicativo Java é especialmente grande, você deverá aumentar o limite de tempo de inicialização. Para fazer isso, crie uma configuração de aplicativo, `WEBSITES_CONTAINER_START_TIME_LIMIT` e defini-lo como o número de segundos que o serviço de aplicativo deve aguardar antes de atingir o tempo limite. O valor máximo é `1800` segundos.

## <a name="secure-applications"></a>Aplicativos seguros

Os aplicativos Java em execução no Serviço de Aplicativo para Linux têm o mesmo conjunto de [melhores práticas de segurança](/azure/security/security-paas-applications-using-app-services) que outros aplicativos.

### <a name="authenticate-users"></a>Autenticar usuários

Configurar a autenticação do aplicativo no portal do Azure com o **autenticação e autorização** opção. Em seguida, será possível habilitar a autenticação usando o Azure Active Directory ou os logons de redes sociais, como Facebook, Google ou GitHub. A configuração do portal do Azure só funciona ao configurar um único provedor de autenticação.  Para saber mais, confira [Configurar o aplicativo do Serviço de Aplicativo para usar o logon do Azure Active Directory](/azure/app-service/configure-authentication-provider-aad) e os artigos relacionados para outros provedores de identidade.

Se você precisar habilitar vários provedores de entrada, siga as instruções no artigo [Personalizar a autenticação do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to).

Os desenvolvedores do Spring Boot podem usar o [inicializador do Spring Boot do Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) para proteger aplicativos usando APIs e anotações conhecidas do Spring Security. Lembre-se de aumentar o tamanho máximo do cabeçalho no arquivo `application.properties`. Sugerimos um valor igual a `16384`.

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções em [Associar um certificado SSL personalizado existente](/azure/app-service/app-service-web-tutorial-custom-ssl) para carregar um certificado SSL existente e associá-lo ao nome de domínio do aplicativo. Por padrão, o aplicativo ainda permitirá conexões HTTP – siga as etapas específicas no tutorial para impor o SSL e o TLS.

### <a name="use-keyvault-references"></a>Usar referências de Cofre de chaves

[Azure KeyVault](../../key-vault/key-vault-overview.md) fornece gerenciamento centralizado de segredos com histórico de auditoria e políticas de acesso. Você pode armazenar segredos (como senhas ou cadeias de caracteres de conexão) no cofre de chaves e acessar esses segredos em seu aplicativo por meio de variáveis de ambiente.

Primeiro, siga as instruções para [concedendo acesso ao aplicativo para o Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e [fazer uma referência ao seu segredo do KeyVault em uma configuração de aplicativo](../app-service-key-vault-references.md#reference-syntax). Você pode validar que a referência será resolvida para o segredo, imprimindo a variável de ambiente ao acessar remotamente o terminal de serviço de aplicativo.

Para injetar esses segredos no arquivo de configuração Spring ou Tomcat, use a sintaxe de injeção de variável de ambiente (`${MY_ENV_VAR}`). Para arquivos de configuração de Spring, consulte esta documentação sobre [externalizados configurações](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="data-sources"></a>Fontes de dados

### <a name="tomcat"></a>Tomcat

Essas instruções se aplicam a todas as conexões de banco de dados. Você precisará preencher os espaços reservados com nome de classe do driver do banco de dados escolhido e o arquivo JAR. É fornecida uma tabela com nomes de classe e downloads de driver para bancos de dados comuns.

| Banco de dados   | Nome de Classe do Driver                             | Driver JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Baixar](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Baixar](https://dev.mysql.com/downloads/connector/j/) (Selecione "Independente de Plataforma") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Baixar](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Para configurar o Tomcat para usar a JDBC (Conectividade de Banco de Dados Java) ou a JPA (API de Persistência Java), primeiro personalize a variável de ambiente `CATALINA_OPTS` lida em Tomcat na inicialização do backup. Defina esses valores por meio de uma configuração de aplicativo no [plug-in Maven do Serviço de Aplicativo](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>  
    <property>  
        <name>CATALINA_OPTS</name>  
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>  
    </property>  
</appSettings>  
```

Ou defina as variáveis de ambiente na folha "Configurações de Aplicativo" no portal do Azure.

Em seguida, determine se a fonte de dados deve estar disponível para um aplicativo ou para todos os aplicativos em execução no servlet do Tomcat.

#### <a name="application-level-data-sources"></a>Fontes de dados de nível de aplicativo

1. Crie um arquivo `context.xml` no diretório `META-INF/` do seu projeto. Crie o diretório `META-INF/` se ele não existir.

2. Em `context.xml`, adicione um elemento `Context` para vincular a fonte de dados a um endereço JNDI. Substitua o espaço reservado `driverClassName` pelo nome de classe do seu driver da tabela acima.

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

3. Atualize o `web.xml` do aplicativo para usar a fonte de dados no aplicativo.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Recursos compartilhados, o nível de servidor

1. Copie o conteúdo de `/usr/local/tomcat/conf` para `/home/tomcat/conf` na instância do Serviço de Aplicativo no Linux usando SSH se já não houver uma configuração.

    ```bash
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Adicione um elemento de Contexto em seu `server.xml` dentro do elemento `<Server>`.

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

3. Atualize o `web.xml` do aplicativo para usar a fonte de dados no aplicativo.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finally-place-the-driver-jars-in-the-tomcat-classpath-and-restart-your-app-service"></a>Por fim, coloque os JARs de driver no classpath do Tomcat e reinicie o serviço de aplicativo

1. Verifique se os arquivos do driver JDBC estão disponíveis para o carregador de classes do Tomcat colocando-os no diretório `/home/tomcat/lib`. (Crie esse diretório se ele ainda não existir.) Para carregar esses arquivos na instância do Serviço de Aplicativo, siga estas etapas:  
   1. Instale a extensão de aplicativo Web do Serviço de Aplicativo do Azure:

      ```azurecli-interactive
      az extension add –name webapp
      ```

   1. Execute o seguinte comando de CLI para criar um túnel SSH do sistema local para o Serviço de Aplicativo:

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

   1. Faça a conexão da porta de túnel local com o cliente SFTP e carregue os arquivos na pasta `/home/tomcat/lib`.

      Como alternativa, você pode usar um cliente de FTP para carregar o driver JDBC. Siga estas [instruções para obter suas credenciais FTP](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

2. Se você tiver uma fonte de dados no nível do servidor, reinicie o aplicativo Linux Serviço de Aplicativo. O Tomcat redefinirá `CATALINA_HOME` como `/home/tomcat/conf` e usará a configuração atualizada.

### <a name="spring-boot"></a>Spring Boot

Para se conectar a fontes de dados em aplicativos Spring Boot, sugerimos criar cadeias de caracteres de conexão e injetando-as em seu `application.properties` arquivo.

1. Na seção "Configurações de aplicativo" da folha de serviço de aplicativo, defina um nome para a cadeia de caracteres, cole a cadeia de conexão JDBC no campo de valor e defina o tipo como "Custom". Opcionalmente, você pode definir essa cadeia de caracteres de conexão como configuração do slot.

    ![Criando uma cadeia de caracteres de conexão no portal.][1]

    Essa cadeia de caracteres de conexão é acessível ao nosso aplicativo como uma variável de ambiente denominada `CUSTOMCONNSTR_<your-string-name>`. Por exemplo, a cadeia de caracteres de conexão que criamos acima será nomeada `CUSTOMCONNSTR_exampledb`.

2. No seu `application.properties` de arquivos, fazer referência a essa cadeia de caracteres de conexão com o nome da variável de ambiente. Para nosso exemplo, usaríamos a seguir.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Consulte a [documentação do Spring Boot no acesso a dados](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html
) e [externalizados configurações](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) para obter mais informações sobre esse tópico.

## <a name="docker-containers"></a>Contêineres do Docker

Para usar o JDK do Zulu com suporte do Azure em seus contêineres, efetue pull e use as imagens predefinidas conforme documentado em [página de download do Azul Zulu Enterprise para Azure com suporte](https://www.azul.com/downloads/azure-only/zulu/) ou use os exemplos do `Dockerfile` dos [Repositório GitHub do Microsoft Java](https://github.com/Microsoft/java/tree/master/docker).

## <a name="runtime-availability-and-statement-of-support"></a>Disponibilidade de tempo de execução e declaração de suporte

O Serviço de Aplicativo para Linux dá suporte a dois tempos de execução para hospedagem gerenciada de aplicativos Web do Java:

- O [contêiner do servlet do Tomcat](https://tomcat.apache.org/) para execução de aplicativos empacotados como arquivos Web (WAR). As versões com suporte são 8.5 e 9.0.
- O ambiente de tempo de execução do Java SE para executar aplicativos empacotados como arquivos Java (JAR). A única versão principal com suporte é a Java 8.

## <a name="java-runtime-statement-of-support"></a>Declaração de suporte do tempo de execução do Java

### <a name="jdk-versions-and-maintenance"></a>Versões e manutenção do JDK

O JDK (Java Development Kit) com suporte do Azure é o [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornecido pela [Azul Systems](https://www.azul.com/).

Atualizações de versão principal serão fornecidas por meio de novas opções de tempo de execução no Serviço de Aplicativo do Azure para Linux. Os clientes podem atualizar para essas versões mais recentes do Java configurando a implantação do Serviço de Aplicativo. Além disso, são responsáveis por testar e garantir que a atualização da versão principal atenda às suas necessidades.

Os JDKs com suporte são corrigidos automaticamente a cada trimestre em janeiro, abril, julho e outubro de cada ano.

### <a name="security-updates"></a>Atualizações de segurança

Os patches e as correções para grandes vulnerabilidades de segurança serão liberados assim que forem disponibilizados pela Azul Systems. Uma "grande" vulnerabilidade é definida por uma pontuação básica de 9,0 ou mais na [versão 2 do NIST Common Vulnerability Scoring System](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Reprovação e desativação

Se um tempo de execução do Java com suporte for desativado, os desenvolvedores do Azure que usam o tempo de execução afetado receberão um aviso de reprovação pelo menos seis meses antes de o tempo de execução ser desativado.

### <a name="local-development"></a>Desenvolvimento local

Os desenvolvedores podem baixar a Production Edition do Azul Zulu Enterprise JDK para desenvolvimento local do [site de download da Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Suporte ao desenvolvimento

O suporte ao produto do [Azul Zulu JDK com suporte do Azure](https://www.azul.com/downloads/azure-only/zulu/) está disponível durante o desenvolvimento para Azure ou [Microsoft Azure Stack](https://azure.microsoft.com/overview/azure-stack/) com um [plano de suporte qualificado do Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Suporte de tempo de execução

Os desenvolvedores podem [abrir um problema](/azure/azure-supportability/how-to-create-azure-support-request) com o Azul Zulu JDK por meio do Suporte do Azure se tiverem um [plano de suporte qualificado](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Próximas etapas

Acesse a central para [Desenvolvedores do Azure para Java](/java/azure/) para conferir inícios rápidos, tutoriais e documentação de referência do Java.

<!--Image references-->
[1]: ./media/app-service-linux-java/connection-string.png
