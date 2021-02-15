---
title: Perguntas frequentes sobre tecnologias de código aberto
description: Obtenha respostas para perguntas frequentes sobre tecnologias de software livre no Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 36dfbf0fda060a8f273fee64098d6234b575088c
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831832"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Perguntas frequentes sobre tecnologias de código aberto para Aplicativos Web do Azure

Este artigo apresenta perguntas frequentes (FAQs) sobre problemas com tecnologias de código aberto para a [funcionalidade do Aplicativos Web do Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Como ativar registro em log PHP para solucionar problemas de PHP?

Para ativar o registro em log de PHP:

1. Entre no site do **kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. No menu superior, selecione **console de depuração**  >  **cmd**.
3. Selecione a pasta **Site**.
4. Selecione a pasta **wwwroot**.
5. Selecione o **+** ícone e, em seguida, selecione **novo arquivo**.
6. Defina o nome de arquivo para **.user.ini**.
7. Selecione o ícone de lápis ao lado de **.user.ini**.
8. No arquivo, adicione este código:`log_errors=on`
9. Clique em **Salvar**.
10. Selecione o ícone de lápis ao lado de **wp-config.php**.
11. Adicione o texto para o código a seguir:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. No portal do Azure, no menu do aplicativo web, reinicie o aplicativo web.

Para obter mais informações, consulte [Habilitar logs de erros do WordPress](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Como registrar erros do aplicativo Python em aplicativos que são hospedados no Serviço de Aplicativo?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Como alterar a versão do aplicativo Node.js que está hospedado no Serviço de Aplicativo?

Para alterar a versão do aplicativo Node.js, você pode usar uma das seguintes opções:

* No portal do Azure, use **Configurações do aplicativo**.
  1. No portal do Azure, vá para seu aplicativo web.
  2. Na folha **Configurações**, selecione **Configurações do aplicativo**.
  3. Em **Configurações do aplicativo**, você pode incluir WEBSITE_NODE_DEFAULT_VERSION como a chave e a versão do Node.js que desejar como o valor.
  4. Vá para o **console do kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
  5. Para verificar a versão do Node.js, digite o seguinte comando:  
     ```
     node -v
     ```
* Modificar o arquivo iisnode.yml. Alterar a versão do Node.js no arquivo iisnode.yml apenas define o ambiente de runtime que iisnode usa. O Kudu cmd e outros ainda usam a versão de Node.js que está definida em **Configurações do aplicativo** no portal do Azure.

  Para definir o iisnode.yml manualmente, crie um arquivo de iisnode.yml na pasta raiz do aplicativo. No arquivo, inclua a seguinte linha:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Defina o arquivo de iisnode.yml usando package.json durante a implantação do controle do código fonte.
  O processo de implantação de controle do código-fonte do Azure envolve as seguintes etapas:
  1. Mova o conteúdo do aplicativo Web do Azure.
  2. Crie um script de implantação padrão, se não houver um (arquivos deploy.cmd, .deployment) na pasta raiz de aplicativo Web.
  3. Execute um script de implantação em que ele cria um arquivo iisnode.yml se mencionar a versão do Node.js no arquivo package.json > mecanismo`"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. O arquivo iisnode.yml tem a seguinte linha de código:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>É exibida a mensagem "Erro ao estabelecer uma conexão de banco de dados" em meu aplicativo de WordPress que está hospedado no Serviço de Aplicativo. Como solucionar isso?

Se você vir esse erro em seu aplicativo WordPress do Azure, para permitir php_errors.log e debug.log, conclua as etapas detalhadas em [Habilitar logs de erros do WordPress](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2).

Quando os logs estão habilitados, reproduza o erro e, em seguida, verifique os logs para ver se você está executando sem conexões:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Se você vir esse erro nos arquivos de debug.log ou php_errors.log, seu aplicativo está excedendo o número de conexões. Se você estiver hospedando no ClearDB, verifique o número de conexões que estão disponíveis no seu [plano de serviço](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Como depurar um aplicativo Node.js que está hospedado no Serviço de Aplicativo?

1.  Vá para o **console do kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole` ).
2.  Vá para a pasta de logs de aplicativo (D:\home\LogFiles\Application).
3.  No arquivo logging_errors.txt, verifique se há conteúdo.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Como instalar módulos Python nativos em um aplicativo web do Serviço de Aplicativo ou aplicativo de API?

Alguns pacotes não podem ser instalados usando o pip no Azure. O pacote não pode estar disponível no índice de pacote do Python ou um compilador pode ser necessário (um compilador não está disponível no computador que está executando o aplicativo web no Serviço de Aplicativo). Para obter informações sobre como instalar os módulos nativos em aplicativos web do Serviço de Aplicativo e aplicativos de API, consulte [Instalar módulos Python no Serviço de Aplicativo](/archive/blogs/azureossds/install-native-python-modules-on-azure-web-apps-api-apps).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Como implantar um aplicativo de Django para o Serviço de Aplicativo usando o Git e a nova versão do Python?

Para obter informações sobre como instalar Django, consulte [Implantando um aplicativo Django para o Serviço de Aplicativo](/archive/blogs/azureossds/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python).

## <a name="where-are-the-tomcat-log-files-located"></a>Onde estão localizados os arquivos de log do Tomcat?

Para o Azure Marketplace e implantações personalizadas:

* Local da pasta: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Arquivos de interesse:
    * catalina.*aaaa-mm-dd*.log
    * host-manager.*aaaa-mm-dd*.log
    * localhost.*aaaa-mm-dd*.log
    * manager.*aaaa-mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log


Para implantações de **Configurações do aplicativo** do portal:

* Local da pasta: D:\home\LogFiles
* Arquivos de interesse:
    * catalina.*aaaa-mm-dd*.log
    * host-manager.*aaaa-mm-dd*.log
    * localhost.*aaaa-mm-dd*.log
    * manager.*aaaa-mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Como solucionar problemas de erros de conexão do driver JDBC?

Você verá a seguinte mensagem nos seus logs Tomcat:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Para resolver o erro:

1. Remova o arquivo sqljdbc*.jar da pasta do app/lib.
2. Se você estiver usando o servidor web Tomcat personalizado ou o Azure Marketplace Tomcat, copie esse arquivo. jar para a pasta lib Tomcat.
3. Se você estiver habilitando Java do portal do Azure (selecione   >  **servidor Tomcat** do Java 1,8), copie o arquivo JAR sqljdbc. * na pasta que é paralela ao seu aplicativo. Em seguida, adicione a seguinte configuração de classpath para o arquivo web.config:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Por que vejo erros ao tentar copiar os arquivos de log em tempo real?

Se você tentar copiar arquivos de log em tempo real para um aplicativo Java (por exemplo, Tomcat), você poderá ver esse erro FTP:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

A mensagem de erro pode variar, dependendo do cliente FTP.

Todos os aplicativos Java tem esse problema de bloqueio. Apenas o Kudu oferece suporte para baixar este arquivo enquanto o aplicativo está em execução.

Parar o aplicativo permite o acesso ao FTP para esses arquivos.

Outra solução alternativa é gravar um WebJob que é executado em um agendamento e copiá-los para um diretório diferente. Para um projeto de exemplo, consulte o projeto [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob).

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Onde encontrar os arquivos de log para Jetty?

Para o Marketplace e implantações personalizadas, o arquivo de log está na pasta D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs. Observe que o local da pasta depende da versão do Jetty que você está usando. Por exemplo, o caminho fornecido aqui é para Jetty 9.1.2. Procure jetty_ *YYYY_MM_DD*.stderrout.log.

Para implantações de Configuração do aplicativo do portal, o arquivo de log está em D:\home\LogFiles. Procure jetty_ *YYYY_MM_DD*. stderrout. log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Posso enviar email de meu aplicativo web do Azure?

Serviço de Aplicativo não tem uma funcionalidade de email interno. Para algumas boas alternativas para enviar email de seu aplicativo, consulte esta [Discussão de Stack Overflow](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Por que meu site WordPress redireciona para outra URL?

Se você ter migrado recentemente no Azure, WordPress pode redirecionar para a URL do domínio antigo. Isso é causado por uma configuração no banco de dados MySQL.

WordPress Buddy + é uma extensão de Site do Azure que você pode usar para atualizar a URL de redirecionamento diretamente no banco de dados. Para obter mais informações sobre como usar o WordPress Buddy +, consulte [Ferramentas WordPress e migração do MySQL com WordPress Buddy +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

Como alternativa, se você preferir atualizar manualmente o redirecionamento de URL usando consultas SQL ou PHPMyAdmin, consulte [WordPress: Redirecionamento de URL errado](/archive/blogs/azureossds/wordpress-redirecting-to-wrong-url).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Como alterar minha senha de logon do WordPress?

Se você esqueceu sua senha de logon do WordPress, você pode usar WordPress Buddy + para atualizá-la. Para redefinir sua senha, instale a extensão do Azure Site WordPress Buddy + e, em seguida, conclua as etapas descritas em [Ferramentas WordPress e migração do MySQL com WordPress Buddy +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Não consigo entrar WordPress. Como resolver isso?

Se você estiver o WordPress bloqueado depois de instalar recentemente um plug-in, você pode ter um plug-in com defeito. WordPress Buddy + é uma extensão de Site do Azure que podem ajudá-lo desabilitar plug-ins no WordPress. Para obter mais informações, consulte [Ferramentas WordPress e migração do MySQL com WordPress Buddy +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Como migrar meu banco de dados do WordPress?

Você tem várias opções para migrar o banco de dados MySQL que está conectado ao seu site de WordPress:

* Desenvolvedores: Usar o [prompt de comando ou PHPMyAdmin](/archive/blogs/azureossds/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service)
* Não-desenvolvedores: Usar [WordPress Buddy +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Como ajudar a proteger melhor o WordPress?

Para saber mais sobre práticas recomendadas de segurança para WordPress, consulte [Práticas recomendadas para segurança do WordPress no Azure](/archive/blogs/azureossds/best-practices-for-wordpress-security-on-azure).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Estou tentando usar PHPMyAdmin e é exibida a mensagem "Acesso negado." Como resolver isso?

Se a funcionalidade do MySQL no aplicativo não estiver em execução ainda nesta instância do Serviço de Aplicativo, você poderá enfrentar esse problema. Para resolver o problema, tente acessar o site. Isso inicia os processos necessários, incluindo o processo do MySQL no aplicativo. Para verificar se MySQL no aplicativo está em execução, no Process Explorer, certifique-se de que mysqld.exe está listado nos processos.

Depois de garantir que MySQL no aplicativo está em execução, tente usar PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Recebo um erro de HTTP 403 ao tentar importar ou exportar meu banco de dados MySQL no aplicativo usando PHPMyadmin. Como resolver isso?

Se você estiver usando uma versão mais antiga do Chrome, talvez você esteja tendo um bug conhecido. Para resolver o problema, atualize para uma versão mais recente do Chrome. Além disso, tente usar um navegador diferente, como o Internet Explorer ou o Microsoft Edge, em que o problema não ocorre.