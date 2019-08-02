---
title: Compilar aplicativo Web Enterprise Java no Linux – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como fazer um aplicativo Java Enterprise funcionar em Wildfly no Serviço de Aplicativo do Azure no Linux.
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: 2d26d9e145030e5972289c224dc2f76078d67527
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498494"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Tutorial: Compilar um aplicativo Web Java EE e um aplicativo Web Postgres no Azure

Este tutorial mostrará como criar um aplicativo Web Java EE (Enterprise Edition) no Serviço de Aplicativo do Azure e conectá-lo a um banco de dados do Postgres. Quando tiver terminado, você terá um aplicativo [WildFly](https://www.wildfly.org/about/) armazenando dados no [Banco de Dados do Azure para Postgres](https://azure.microsoft.com/services/postgresql/) em execução no [Serviço de Aplicativo do Azure no Linux](app-service-linux-intro.md).

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Implantar um aplicativo Java EE no Azure usando o Maven
> * Criar um banco de dados Postgres no Azure
> * Configurar o servidor WildFly para usar Postgres
> * Atualizar o aplicativo e reimplantar
> * Executar testes de unidade no WildFly

## <a name="prerequisites"></a>Pré-requisitos

1. [Baixe e instale o Git](https://git-scm.com/)
2. [Baixe e instale o Maven 3](https://maven.apache.org/install.html)
3. [Baixe e instale a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Clonar e editar o aplicativo de exemplo

Nesta etapa, você clona o aplicativo de exemplo e configura o Modelo de Objeto do Projeto Maven (POM ou *pom.xml*) para implantação.

### <a name="clone-the-sample"></a>Clonar o exemplo

Na janela do terminal, navegue até um diretório de trabalho e clone [o repositório de exemplo](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Atualizar o POM do Maven

Atualize o Plug-in do Azure do Maven com o grupo de recursos e o nome desejados de seu Serviço de Aplicativo. Você não precisa criar o Plano do Serviço de Aplicativo ou a instância com antecedência. O plug-in do Maven criará o grupo de recursos e o Serviço de Aplicativo, se ele ainda não existir.

Role a tela para baixo até a seção `<plugins>` do *pom.xml*, linha 200, para fazer as alterações.

```xml
<!-- Azure App Service Maven plugin for deployment -->
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>${version.maven.azure.plugin}</version>
  <configuration>
    <appName>YOUR_APP_NAME</appName>
    <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup>
    <linuxRuntime>wildfly 14-jre8</linuxRuntime>
  ...
</plugin>  
```

Substitua `YOUR_APP_NAME` e `YOUR_RESOURCE_GROUP` pelos nomes do grupo de recursos e do Serviço de Aplicativo.

## <a name="build-and-deploy-the-application"></a>Compilar e implantar o aplicativo

Agora, usaremos o Maven para compilar nosso aplicativo e implantá-lo no Serviço de Aplicativo.

### <a name="build-the-war-file"></a>Compilar o arquivo .war

O POM neste projeto está configurado para empacotar o aplicativo em um arquivo WAR (Web Archive). Compile o aplicativo usando o Maven:

```bash
mvn clean install -DskipTests
```

Os casos de teste neste aplicativo são projetados execução quando o aplicativo for implantado no WildFly. Vamos ignorar os testes para compilar no local, e você executará os testes após a implantação do aplicativo no Serviço de Aplicativo.

### <a name="deploy-to-app-service"></a>Implantar no Serviço de Aplicativo

Agora que o WAR está pronto, podemos usar o plug-in do Azure para implantar no Serviço de Aplicativo:

```bash
mvn azure-webapp:deploy
```

Após a conclusão da implantação, prossiga para a próxima etapa.

### <a name="create-a-record"></a>Criar um registro

Abra um navegador e navegue até `https://<your_app_name>.azurewebsites.net/`. Parabéns, você implantou um aplicativo Java EE no Serviço de Aplicativo do Azure!

Agora, o aplicativo está usando um banco de dados H2 em memória. Clique em "administrador" na barra de navegação e crie uma nova categoria. Você perderá o registro no banco de dados em memória se reiniciar a instância do Serviço de Aplicativo. Nas etapas a seguir, você corrigirá esse problema com o provisionamento de um banco de dados Postgres no Azure e com a configuração do WildFly para usá-lo.

![Local do botão de administrador](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Provisionar um banco de dados Postgres

Para provisionar um servidor de banco de dados do Postgres, abra um terminal e use o comando [az postgres server create](https://docs.microsoft.com/cli/azure/postgres/server), como mostrado no exemplo a seguir. Substitua os espaços reservados (incluindo os colchetes angulares) pelos valores de sua escolha, usando o mesmo recurso de grupo fornecido anteriormente para sua instância do Serviço de Aplicativo. As credenciais de administrador fornecidas permitirão o acesso futuro, portanto, certifique-se de anotá-las para uso posterior.

```bash
az postgres server create \
    --name <server name> \
    --resource-group <resource group> \
    --location <location>
    --sku-name GP_Gen5_2 \
    --admin-user <administrator username> \
    --admin-password <administrator password> \
```

Depois de executar esse comando, vá até o portal do Azure e acesse seu banco de dados do Postgres. Quando a folha estiver ativa, copie os valores de "Nome do servidor" e "Nome de logon do administrador do servidor"; você precisará deles mais tarde.

### <a name="allow-access-to-azure-services"></a>Permitir acesso aos Serviços do Azure

No painel **Segurança de conexão** da folha Banco de Dados do Azure, alterne o botão "Permitir acesso aos serviços do Azure" para a posição **ATIVADO**.

![Permitir acesso aos Serviços do Azure](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Atualizar o aplicativo Java para Postgres

Agora, faremos algumas alterações no aplicativo Java a fim de habilitá-lo para usar nosso banco de dados Postgres.

### <a name="add-postgres-credentials-to-the-pom"></a>Adicionar credenciais do Postgres ao POM

Em *pom.xml*, substitua os valores de espaço reservado em maiúsculas pelo nome do servidor Postgres, pelo nome de logon de administrador e pela senha. Esses campos estão dentro do Plug-in do Maven do Azure. (Não se esqueça de substituir `YOUR_SERVER_NAME`, `YOUR_PG_USERNAME` e `YOUR_PG_PASSWORD` nas tags `<value>`… não dentro das marcas `<name>`!)

```xml
<plugin>
      ...
      <appSettings>
      <property>
        <name>POSTGRES_CONNECTIONURL</name>
        <value>jdbc:postgresql://YOUR_SERVER_NAME:5432/postgres?ssl=true</value>
      </property>
      <property>
        <name>POSTGRES_USERNAME</name>
        <value>YOUR_PG_USERNAME</value>
      </property>
      <property>
        <name>POSTGRES_PASSWORD</name>
        <value>YOUR_PG_PASSWORD</value>
      </property>
    </appSettings>
  </configuration>
</plugin>
```

### <a name="update-the-java-transaction-api"></a>Atualizar a API de Transação Java

Em seguida, precisamos editar nossa configuração da JPA (API de Transação Java) para que nosso aplicativo Java se comunique com o Postgres em vez do banco de dados H2 em memória que estávamos usando anteriormente. Abra um editor para *src/main/resources/META-INF/persistence.xml*. Substitua o valor de `<jta-data-source>` por `java:jboss/datasources/postgresDS`. Agora, o JTA XML deve ter esta configuração:

```xml
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
```

## <a name="configure-the-wildfly-application-server"></a>Configurar o servidor de aplicativo do WildFly

Antes de implantar nosso aplicativo reconfigurado, precisamos atualizar o servidor de aplicativo do WildFly com o módulo do Postgres e suas dependências. Encontre mais informações sobre configuração em [Configurar o servidor WildFly](configure-language-java.md#configure-java-ee-wildfly).

Para configurar o servidor, precisaremos dos quatro arquivos no diretório *wildfly_config/* :

- **postgresql-42.2.5.jar**: Esse arquivo JAR é o driver JDBC para Postgres. Para saber mais, confira o [site oficial](https://jdbc.postgresql.org/index.html).
- **postgres-module.xml**: Esse arquivo XML declara um nome para o módulo do Postgres (org.postgres). Ele também especifica os recursos e as dependências necessárias uso do módulo.
- **jboss_cli_commands.cli**: Esse arquivo contém os comandos de configuração que serão executados pela CLI do JBoss. Os comandos adicionam o módulo do Postgres ao servidor de aplicativo do WildFly, fornecem as credenciais, declaram um nome JNDI, definem o tempo limite e muito mais. Se você não estiver familiarizado com a CLI do JBoss, confira a [documentação oficial](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script.sh**: Por fim, esse script de shell será executado sempre que a instância do Serviço de Aplicativo for iniciada. O script executa apenas uma função: canaliza os comandos em *jboss_cli_commands.cli* para a CLI do JBoss.

Recomendamos muito a leitura do conteúdo desses arquivos, especialmente do *jboss_cli_commands.cli*.

### <a name="ftp-the-configuration-files"></a>Enviar por FTP os arquivos de configuração

Precisaremos enviar o conteúdo de *wildfly_config/* por FTP para nossa instância do Serviço de Aplicativo. Para obter as credenciais do FTP, clique no botão **Obter Perfil de Publicação** na folha Serviço de Aplicativo no portal do Azure. O nome de usuário e a senha do FTP estarão no documento XML baixado. Para saber mais sobre o perfil de publicação, confira [este documento](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

Com uma ferramenta FTP de sua escolha, transfira os quatro arquivos em *wildfly_config /* para */home/site/implantações/tools/* . (Observe que você não deve transferir o diretório, apenas os arquivos.)

### <a name="finalize-app-service"></a>Finalizar o Serviço de Aplicativo

Na folha Serviço de Aplicativo, navegue até o painel "Configurações de aplicativo". Em "Runtime", configure o campo "Arquivo de inicialização" como */home/site/deployments/tools/startup_script.sh*. Isso fará com que o script de shell seja executado após a criação da instância do Serviço de Aplicativo, mas antes do servidor do WildFly ser iniciado.

Por fim, reinicie o Serviço de Aplicativo. O botão está no painel "Visão geral".

## <a name="redeploy-the-application"></a>Reimplantar o aplicativo

Em uma janela de terminal, recompile e reimplante o aplicativo.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Parabéns! Agora, seu aplicativo está usando um banco de dados Postgres, e todos os registros criados no aplicativo serão armazenados no Postgres em vez de no banco de dados H2 em memória anterior. Para confirmar isso, crie um registro e reinicie o Serviço de Aplicativo. Os registros ainda estarão lá quando seu aplicativo for reiniciado.

## <a name="clean-up"></a>Limpar

Se não precisar desses recursos para outro tutorial (confira as Próximas etapas), você poderá excluí-los executando o seguinte comando:

```bash
az group delete --name <your-resource-group>
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Implantar um aplicativo Java EE no Azure usando o Maven
> * Criar um banco de dados Postgres no Azure
> * Configurar o servidor WildFly para usar Postgres
> * Atualizar o aplicativo e reimplantar
> * Executar testes de unidade no WildFly

Prossiga para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo.

> [!div class="nextstepaction"]
> [Tutorial: Mapear o nome DNS personalizado para seu aplicativo](../app-service-web-tutorial-custom-domain.md)

Se preferir, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar o aplicativo Java](configure-language-java.md)
