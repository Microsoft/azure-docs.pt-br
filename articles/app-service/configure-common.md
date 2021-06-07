---
title: Configurar aplicativos no portal
description: Saiba como definir configurações comuns para um aplicativo do serviço de aplicativo no portal do Azure. Configurações do aplicativo, configuração do aplicativo, cadeias de conexão, plataforma, pilha de idiomas, contêiner, etc.
keywords: serviço de aplicativo do Azure, aplicativo Web, configurações de aplicativo, variáveis de ambiente
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 12/07/2020
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: a865c1070150b31399b5b738a0a469a07e0b13de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122350"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Configurar um aplicativo do serviço de aplicativo no portal do Azure

Este artigo explica como definir configurações comuns para aplicativos Web, back-end móvel ou aplicativo de API usando o [portal do Azure].

## <a name="configure-app-settings"></a>Definir configurações de aplicativo

No serviço de aplicativo, as configurações de aplicativo são variáveis passadas como variáveis de ambiente para o código do aplicativo. Para aplicativos Linux e contêineres personalizados, o serviço de aplicativo passa configurações de aplicativo para o contêiner usando o `--env` sinalizador para definir a variável de ambiente no contêiner.

No [Azure portal], pesquise e selecione **Serviços de Aplicativos** e, em seguida, selecione o nome do seu aplicativo. 

![Pesquisar serviços de aplicativos](./media/configure-common/search-for-app-services.png)

No menu à esquerda do aplicativo, selecione   >  **configurações do aplicativo** de configuração.

![Configurações do aplicativo](./media/configure-common/open-ui.png)

Para os desenvolvedores de ASP.NET e ASP.NET Core, definir configurações de aplicativo no serviço de aplicativo é como configurá-los em `<appSettings>` *Web.config* ou *appsettings.jsem*, mas os valores no serviço de aplicativo substituem aqueles em *Web.config* ou *appsettings.jsem*. Você pode manter as configurações de desenvolvimento (por exemplo, a senha do MySQL local) em *Web.config* ouappsettings.jsos segredos *de* produção e do Azure (por exemplo, a senha do banco de dados MySQL) com segurança no serviço de aplicativo. O mesmo código usa suas configurações de desenvolvimento quando você depura localmente e usa seus segredos de produção quando implantado no Azure.

Outras pilhas de idiomas, da mesma forma, obtêm as configurações do aplicativo como variáveis de ambiente no tempo de execução. Para obter as etapas específicas da pilha de idiomas, consulte:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Contêineres personalizados](configure-custom-container.md#configure-environment-variables)

As configurações do aplicativo são sempre criptografadas quando armazenadas (criptografadas em repouso).

> [!NOTE]
> As configurações do aplicativo também podem ser resolvidas de [Key Vault](../key-vault/index.yml) usando [referências Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostrar valores ocultos

Por padrão, os valores para as configurações do aplicativo ficam ocultos no portal para segurança. Para ver um valor oculto de uma configuração de aplicativo, clique no campo **valor** dessa configuração. Para ver os valores de todas as configurações de aplicativo, clique no botão **Mostrar valor** .

### <a name="add-or-edit"></a>Adicionar ou editar

Para adicionar uma nova configuração de aplicativo, clique em **nova configuração de aplicativo**. Na caixa de diálogo, você pode [fixar a configuração no slot atual](deploy-staging-slots.md#which-settings-are-swapped).

Para editar uma configuração, clique no botão **Editar** no lado direito.

Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **salvar** de volta na página de **configuração** .

> [!NOTE]
> Em um contêiner do Linux padrão ou um contêiner personalizado do Linux, qualquer estrutura de chave JSON aninhada no nome da configuração do aplicativo como `ApplicationInsights:InstrumentationKey` precisa ser configurada no serviço de aplicativo como `ApplicationInsights__InstrumentationKey` para o nome da chave. Em outras palavras, qualquer um `:` deve ser substituído por `__` (sublinhado duplo).
>

### <a name="edit-in-bulk"></a>Editar em massa

Para adicionar ou editar as configurações do aplicativo em massa, clique no botão de **edição avançado** . Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **salvar** de volta na página de **configuração** .

As configurações do aplicativo têm a seguinte formatação JSON:

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

### <a name="automate-app-settings-with-the-azure-cli"></a>Automatize as configurações do aplicativo com o CLI do Azure

Você pode usar o CLI do Azure para criar e gerenciar configurações na linha de comando.

- Atribua um valor a uma configuração com [AZ webapp config app Settings Set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set):

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings <setting-name>="<value>"
    ```
        
    Substitua `<setting-name>` pelo nome da configuração e `<value>` pelo valor a ser atribuído a ela. Esse comando criará a configuração se ela ainda não existir.
    
- Mostre todas as configurações e seus valores com [AZ webapp config appSettings List](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_list):
    
    ```azurecli-interactive
    az webapp config appsettings list --name <app-name> --resource-group <resource-group-name>
    ```
    
- Remova uma ou mais configurações com [AZ webapp config app Settings Delete](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_delete):

    ```azurecli-interactive
    az webapp config appsettings delete --name <app-name> --resource-group <resource-group-name> --setting-names {<names>}
    ```
    
    Substitua `<names>` por uma lista separada por espaços de nomes de configuração.

## <a name="configure-connection-strings"></a>Configurar cadeias de conexão

No [Azure portal], pesquise e selecione **Serviços de Aplicativos** e, em seguida, selecione o nome do seu aplicativo. No menu à esquerda do aplicativo, selecione   >  **configurações do aplicativo** de configuração.

![Configurações do aplicativo](./media/configure-common/open-ui.png)

Para desenvolvedores de ASP.NET e ASP.NET Core, definir cadeias de conexão no serviço de aplicativo é como configurá-los em `<connectionStrings>` no *Web.config*, mas os valores definidos no serviço de aplicativo substituem aqueles em *Web.config*. Você pode manter as configurações de desenvolvimento (por exemplo, um arquivo de banco de dados) em *Web.config* e os segredos de produção (por exemplo, credenciais do banco de dados SQL) com segurança no serviço de aplicativo. O mesmo código usa suas configurações de desenvolvimento quando você depura localmente e usa seus segredos de produção quando implantado no Azure.

Para outras pilhas de idiomas, é melhor usar [as configurações do aplicativo](#configure-app-settings) , pois as cadeias de conexão exigem formatação especial nas chaves variáveis para acessar os valores. 

> [!NOTE]
> Há um caso em que você talvez queira usar cadeias de conexão em vez de configurações do aplicativo para idiomas non-.NET: certos tipos de banco de dados do Azure são submetidos a backup junto com o aplicativo _somente_ se você configurar uma cadeia de conexão para o banco de dados em seu aplicativo do serviço de aplicativo. Para obter mais informações, consulte [o que é feito](manage-backup.md#what-gets-backed-up)backup. Se você não precisar desse backup automatizado, use as configurações do aplicativo.

Em tempo de execução, as cadeias de conexão estão disponíveis como variáveis de ambiente, prefixadas com os seguintes tipos de conexão:

* SqlServer `SQLCONNSTR_`  
* MySQL: `MYSQLCONNSTR_` 
* SQLAzure `SQLAZURECONNSTR_` 
* Personalizado: `CUSTOMCONNSTR_`
* PostgreSQL `POSTGRESQLCONNSTR_`  

Por exemplo, uma cadeia de conexão do MySql chamada *connectionstring1* pode ser acessada como a variável de ambiente `MYSQLCONNSTR_connectionString1` . Para obter as etapas específicas da pilha de idiomas, consulte:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Contêineres personalizados](configure-custom-container.md#configure-environment-variables)

As cadeias de conexão são sempre criptografadas quando armazenadas (criptografadas em repouso).

> [!NOTE]
> As cadeias de conexão também podem ser resolvidas de [Key Vault](../key-vault/index.yml) usando [referências Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostrar valores ocultos

Por padrão, os valores para cadeias de conexão ficam ocultos no portal para segurança. Para ver um valor oculto de uma cadeia de conexão, basta clicar no campo **valor** dessa cadeia de caracteres. Para ver os valores de todas as cadeias de conexão, clique no botão **Mostrar valor** .

### <a name="add-or-edit"></a>Adicionar ou editar

Para adicionar uma nova cadeia de conexão, clique em **nova cadeia de conexão**. Na caixa de diálogo, você pode colocar [a cadeia de conexão no slot atual](deploy-staging-slots.md#which-settings-are-swapped).

Para editar uma configuração, clique no botão **Editar** no lado direito.

Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **salvar** de volta na página de **configuração** .

### <a name="edit-in-bulk"></a>Editar em massa

Para adicionar ou editar cadeias de conexão em massa, clique no botão de **edição avançado** . Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **salvar** de volta na página de **configuração** .

As cadeias de conexão têm a seguinte formatação JSON:

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Definir as configurações gerais

No [Azure portal], pesquise e selecione **Serviços de Aplicativos** e, em seguida, selecione o nome do seu aplicativo. No menu à esquerda do aplicativo, selecione **configuração**  >  **geral configurações**.

![Configurações gerais](./media/configure-common/open-general.png)

Aqui, você pode definir algumas configurações comuns para o aplicativo. Algumas configurações exigem que você [Escale verticalmente para os tipos de preço mais altos](manage-scale-up.md).

- **Configurações de pilha**: a pilha de software para executar o aplicativo, incluindo as versões de idioma e SDK.

    Para aplicativos do Linux e aplicativos de contêiner personalizados, você pode selecionar a versão do Language Runtime e definir um **comando de inicialização** opcional ou um arquivo de comando de inicialização.

    ![Configurações gerais para contêineres do Linux](./media/configure-common/open-general-linux.png)

- **Configurações de plataforma**: permite que você defina as configurações para a plataforma de hospedagem, incluindo:
    - Bit de **bits: 32** bits ou 64 bits.
    - **Protocolo WebSocket**: para [ASP.net signalr] ou [Socket.Io](https://socket.io/), por exemplo.
    - **Always on**: mantém o aplicativo carregado mesmo quando não há tráfego. Ele é necessário para trabalhos Web contínuos ou para trabalhos Web que são disparados usando uma expressão CRON.
      > [!NOTE]
      > Com o recurso Always On, o balanceador de carga de front-end envia uma solicitação para a raiz do aplicativo. Este ponto de extremidade do aplicativo do serviço de aplicativo não pode ser configurado.
    - **Versão do pipeline gerenciado**: o [modo de pipeline]do IIS. Defina-o como **clássico** se você tiver um aplicativo herdado que exija uma versão mais antiga do IIS.
    - **Versão de http**: defina como **2,0** para habilitar o suporte para o protocolo [https/2](https://wikipedia.org/wiki/HTTP/2) .
    > [!NOTE]
    > A maioria dos navegadores modernos dá suporte para protocolo HTTP/2 somente em TLS, enquanto o tráfego não criptografado continua usando HTTP / 1.1. Para garantir que os navegadores cliente se conectem ao seu aplicativo com HTTP/2, proteja seu nome DNS personalizado. Para obter mais informações, consulte [proteger um nome DNS personalizado com uma associação TLS/SSL no serviço Azure app](configure-ssl-bindings.md).
    - **Afinidade de arr**: em uma implantação de várias instâncias, verifique se o cliente é roteado para a mesma instância durante a vida útil da sessão. Você pode definir essa opção como **off** para aplicativos sem estado.
- **Depuração**: habilite a depuração remota para aplicativos [ASP.net](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)ou [Node.js](configure-language-nodejs.md#debug-remotely) . Essa opção é desativada automaticamente após 48 horas.
- **Certificados de cliente de entrada**: requer certificados de cliente na [autenticação mútua](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Configurar documentos padrão

Essa configuração é apenas para aplicativos do Windows.

No [Azure portal], pesquise e selecione **Serviços de Aplicativos** e, em seguida, selecione o nome do seu aplicativo. No menu à esquerda do aplicativo, selecione **configuração**  >  **documentos padrão**.

![Documentos padrão](./media/configure-common/open-documents.png)

O documento padrão é a página da Web que é exibida na URL raiz de um site. O primeiro arquivo correspondente na lista é usado. Para adicionar um novo documento padrão, clique em **novo documento**. Não se esqueça de clicar em **salvar**.

Se o aplicativo usa módulos que roteiam com base na URL em vez de fornecer conteúdo estático, não há necessidade de documentos padrão.

## <a name="configure-path-mappings"></a>Configurar mapeamentos de caminho

No [Azure portal], pesquise e selecione **Serviços de Aplicativos** e, em seguida, selecione o nome do seu aplicativo. No menu à esquerda do aplicativo, selecione   >  **mapeamentos de caminho** de configuração.

![Mapeamentos de caminho](./media/configure-common/open-path.png)

> [!NOTE] 
> A guia **mapeamentos de caminho** pode exibir configurações específicas do sistema operacional que diferem do exemplo mostrado aqui.

### <a name="windows-apps-uncontainerized"></a>Aplicativos do Windows (não-contêinered)

Para aplicativos do Windows, você pode personalizar os mapeamentos de manipulador do IIS e os diretórios e aplicativos virtuais.

Mapeamentos de manipulador permitem que você adicione processadores de script personalizados para manipular solicitações para extensões de arquivo específicas. Para adicionar um manipulador personalizado, clique em **novo mapeamento de manipulador**. Configure o manipulador da seguinte maneira:

- **Extensão**. A extensão de arquivo que você deseja manipular, como *\* . php* ou *Handler. fcgi*.
- **Processador de script**. O caminho absoluto do processador de script para você. As solicitações para arquivos que correspondem à extensão de arquivo são processadas pelo processador de script. Use o caminho `D:\home\site\wwwroot` para se referir ao diretório raiz do seu aplicativo.
- **Argumentos**. Argumentos de linha de comando opcionais para o processador de script.

Cada aplicativo tem o caminho raiz padrão ( `/` ) mapeado para `D:\home\site\wwwroot` , onde seu código é implantado por padrão. Se a raiz do aplicativo estiver em uma pasta diferente ou se o repositório tiver mais de um aplicativo, você poderá editar ou adicionar diretórios e aplicativos virtuais aqui. 

Na guia **mapeamentos de caminho** , clique em **novo aplicativo virtual ou diretório**. 

- Para mapear um diretório virtual para um caminho físico, deixe a caixa de seleção **diretório** marcada. Especifique o diretório virtual e o caminho relativo (físico) correspondente para a raiz do site ( `D:\home` ).
- Para marcar um diretório virtual como um aplicativo Web, desmarque a caixa de seleção **diretório** .
  
  ![Caixa de seleção de diretório](./media/configure-common/directory-check-box.png)

### <a name="containerized-apps"></a>Aplicativos em contêineres

Você pode [Adicionar armazenamento personalizado para seu aplicativo em contêiner](configure-connect-to-azure-storage.md). Os aplicativos em contêineres incluem todos os aplicativos do Linux e também os contêineres personalizados do Windows e Linux em execução no serviço de aplicativo. Clique em **nova montagem de armazenamento do Azure** e configure seu armazenamento personalizado da seguinte maneira:

- **Nome**: o nome de exibição.
- **Opções de configuração**: **básico** ou **avançado**.
- **Contas de armazenamento**: a conta de armazenamento com o contêiner desejado.
- **Tipo de armazenamento**: **BLOBs do Azure** ou **arquivos do Azure**.
  > [!NOTE]
  > Os aplicativos de contêiner do Windows só dão suporte a arquivos do Azure.
- **Contêiner de armazenamento**: para configuração básica, o contêiner desejado.
- **Nome do compartilhamento**: para configuração avançada, o nome do compartilhamento de arquivos.
- **Chave de acesso**: para configuração avançada, a chave de acesso.
- **Caminho de montagem**: o caminho absoluto em seu contêiner para montar o armazenamento personalizado.

Para obter mais informações, consulte [acessar o armazenamento do Azure como um compartilhamento de rede de um contêiner no serviço de aplicativo](configure-connect-to-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Definir configurações de pilha de idiomas

- [ASP.NET Core](configure-language-dotnetcore.md)
- [Node.js](configure-language-nodejs.md)
- [PHP](configure-language-php.md)
- [Python](configure-language-python.md)
- [Java](configure-language-java.md)
- [Ruby](configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Configurar contêineres personalizados

Consulte [configurar um contêiner personalizado do Linux para o serviço Azure app](configure-custom-container.md)

## <a name="next-steps"></a>Próximas etapas

- [Configurar um nome de domínio personalizado no serviço Azure App]
- [Configurar ambientes de preparo no Serviço de Aplicativo do Azure]
- [Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure](configure-ssl-bindings.md)
- [Habilitar logs de diagnóstico](troubleshoot-diagnostic-logs.md)
- [Dimensionar um aplicativo no Serviço de Aplicativo do Azure]
- [Conceitos básicos de monitoramento no Serviço de Aplicativo do Azure]
- [Alterar configurações de applicationHost.config com applicationHost. xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Portal do Azure]: https://portal.azure.com/
[Configurar um nome de domínio personalizado no serviço Azure App]: ./app-service-web-tutorial-custom-domain.md
[Configurar ambientes de preparo no Serviço de Aplicativo do Azure]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: ./web-sites-monitor.md
[Conceitos básicos de monitoramento no Serviço de Aplicativo do Azure]: ./web-sites-monitor.md
[modo de pipeline]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Dimensionar um aplicativo no Serviço de Aplicativo do Azure]: ./manage-scale-up.md
