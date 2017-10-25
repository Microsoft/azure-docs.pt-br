---
title: "Migrar dos Serviços Móveis para o Aplicativo Móvel do Serviço de Aplicativo"
description: "Saiba como migrar facilmente seu aplicativo de Serviços Móveis para um Aplicativo Móvel do Serviço de Aplicativo"
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 07507ea2-690f-4f79-8776-3375e2adeb9e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: glenga
ms.openlocfilehash: ee64913629124f886e91478c21304956fbec9f90
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="article-top"></a>Migrar seu Serviço Móvel do Azure existente para o Serviço de Aplicativo do Azure
Com a [disponibilidade geral do Serviço de Aplicativo do Azure], sites de Serviços Móveis do Azure podem ser migrados facilmente no local para aproveitar todos os recursos do Serviço de Aplicativo do Azure.  Este documento explica o que esperar ao migrar seu site dos Serviços Móveis do Azure para o Serviço de Aplicativo do Azure.

## <a name="what-does-migration-do"></a>O que a migração faz ao seu site
A migração do Serviço Móvel do Azure torna o serviço móvel um aplicativo do [Serviço de Aplicativo do Azure] sem afetar o código.  Os hubs de notificação, a conexão de dados do SQL, as configurações de autenticação, os trabalhos agendados e nome de domínio permanecem inalterados.  Os clientes móveis que usarem o serviço móvel do Azure continuarão funcionando normalmente.  A migração reinicia o serviço depois que ele é transferido para o Serviço de Aplicativo do Azure.

[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Por que você deve migrar seu site
A Microsoft está recomendando que você migre o serviço móvel do Azure para aproveitar os recursos do Serviço de Aplicativo do Azure, incluindo:

* Novos recursos de host, incluindo [WebJobs] e [nomes de domínio personalizados].
* Monitorando e solucionando problemas com o New Relic ou o [Application Insights].
* Ferramentas do DevOps internas, incluindo [Slots de preparo], reversão e testes em produção.
* [Dimensionamento automático], balanceamento de carga e [monitoramento de desempenho].

Para obter mais informações sobre os benefícios do Serviço de Aplicativo do Azure, consulte o [Serviços Móveis vs. Serviço de Aplicativo].

## <a name="before-you-begin"></a>Antes de começar
Antes de começar qualquer trabalho importante no o site, você deve fazer backup dos scripts do serviço móvel e do Banco de Dados SQL.

## <a name="migrating-site"></a>Migrando seus sites
O processo de migração migra todos os sites em uma única região do Azure.

Para migrar seu site:

1. Faça logon no [Portal Clássico do Azure].
2. Selecione um serviço móvel na região que você deseja migrar.
3. Clique no botão **Migrar para o Serviço de Aplicativo** .

   ![O botão Migração][0]
4. Leia o diálogo Migrar para o Serviço de Aplicativo.
5. Insira o nome do serviço móvel na caixa fornecida.  Por exemplo, se o nome de domínio for contoso.azure-mobile.net, digite *contoso* na caixa fornecida.
6. Clique no botão de marca.

Monitore o status da migração no monitor de atividade. O site é listado como *migrando* no Portal Clássico do Azure.

  ![Monitor de atividade de migração][1]

Cada migração pode levar de 3 a 15 minutos por serviço móvel que está sendo migrado.  O site permanece disponível durante a migração.
O site é reiniciado ao fim do processo de migração.  O site está disponível durante o processo de reinicialização, que pode durar alguns segundos.

## <a name="finalizing-migration"></a>Finalizando a migração
Planeje testar o site de um cliente móvel ao fim do processo de migração.  Certifique-se de poder executar todas as ações comuns do cliente sem alterações ao cliente móvel.  

### <a name="update-app-service-tier"></a>Selecione um tipo de preço de Serviço de Aplicativo apropriado
Você tem mais flexibilidade nos preços depois de migrar para o Serviço de Aplicativo do Azure.

1. Faça logon no [portal do Azure].
2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
3. A folha Configurações abre por padrão.
4. Clique em **Plano do Serviço de Aplicativo** no menu Configurações.
5. Clique no bloco **Tipo de Preços**.
6. Clique no bloco apropriado para seus requisitos e clique em **Selecionar**.  Talvez seja necessário clicar em **Exibir tudo** para ver as camadas de preços disponíveis.

Como ponto de partida, recomendamos as seguintes camadas:

| Tipo de Preço do Serviço Móvel | Tipo de Preço do Serviço de Aplicativo |
|:--- |:--- |
| Grátis |F1 Gratuito |
| Basic |B1 Básico |
| Standard |S1 Standard |

Há flexibilidade considerável na escolha do tipo de preços certo para o aplicativo.  Consulte [Preços do Serviço de Aplicativo] para obter detalhes completos sobre os preços de seu novo Serviço de Aplicativo.

> [!TIP]
> A camada Standard do Serviço de Aplicativo contém acesso a vários recursos que você talvez queira usar, incluindo [Slots de preparo], backups automáticos e dimensionamento automático.  Confira os novos recursos lá!
>
>

### <a name="review-migration-scheduler-jobs"></a>Examine os trabalhos do agendador migrados
Os trabalhos do agendador não estarão visíveis até cerca de 30 minutos após a migração.  Trabalhos agendados continuam a ser executados em segundo plano.
Para exibir os trabalhos agendados depois que eles ficarem visíveis novamente:

1. Faça logon no [portal do Azure].
2. Selecione **Procurar>**, digite **Agenda** na caixa *Filtro* e selecione **Coleções do Agendador**.

Há um número limitado de trabalhos do agendador gratuito disponíveis após a migração.  Examine seu uso e os [Planos do Agendador do Azure].

### <a name="configure-cors"></a>Configure o CORS, se necessário
Compartilhamento de recursos entre origens é uma técnica para permitir que um site acesse uma API da Web em um domínio diferente.  Se estiver usando os Serviços Móveis do Azure com um site associado, você precisará configurar CORS como parte da migração.  Se você estiver acessando os Serviços Móveis do Azure exclusivamente por meio de dispositivos móveis, não será necessário configurar o CORS, exceto em casos raros.

As configurações de CORS migradas estão disponíveis como a Configuração do Aplicativo **MS_CrossDomainWhitelist**.  Para migrar o site para o recurso CORS do Serviço de Aplicativo:

1. Faça logon no [portal do Azure].
2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
3. A folha Configurações abre por padrão.
4. Clique em **CORS** no menu API.
5. Insira qualquer origem permitida na caixa fornecida, pressionando Enter depois de cada uma.
6. Quando a lista de origens permitidas estiver correta, clique no botão Salvar.

> [!TIP]
> Uma das vantagens de usar um Serviço de Aplicativo do Azure é que você pode executar seu site da web e serviço móvel no mesmo site.  Para obter mais informações, confira a seção [Próximas etapas](#next-steps).
>
>

### <a name="download-publish-profile"></a>Baixar um novo perfil de publicação
O perfil de publicação do seu site é alterado durante a migração para o Serviço de Aplicativo do Azure.  Se pretende publicar o site de dentro do Visual Studio, você precisa de um novo perfil de publicação.  Para baixar um novo perfil de publicação:

1. Faça logon no [portal do Azure].
2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
3. Clique em **Obter perfil de publicação**.

O arquivo PublishSettings é baixado para o computador.  Ele é normalmente chamado de *sitename*.PublishSettings.  Importe as configurações de publicação para o projeto existente:

1. Abra o Visual Studio e o seu projeto de Serviço móvel do Azure.
2. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Publicar...**
3. Clique em **Importar**
4. Clique em **Procurar** e selecione o arquivo de configurações de publicação baixado.  Clique em **OK**
5. Clique em **Validar Conexão** para garantir que as configurações de publicação funcionem.
6. Clique em **Publicar** para publicar o site.

## <a name="working-with-your-site"></a>Trabalhando com seu site após a migração
Comece a trabalhar com o novo Serviço de Aplicativo no [portal do Azure] após a migração.  A seguir estão algumas observações sobre operações específicas que você usou para executar o [Portal Clássico do Azure], juntamente com seus equivalentes do Serviço de Aplicativo.

### <a name="publishing-your-site"></a>Baixar e publicar seu site migrado
O site está disponível via git ou ftp e pode ser publicado novamente com vários mecanismos diferentes, incluindo WebDeploy, TFS, Mercurial, GitHub e FTP.  As credenciais de implantação são migradas com o restante do site.  Se você não definir suas credenciais de implantação ou não se lembrar delas, poderá redefini-las:

1. Faça logon no [portal do Azure].
2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
3. A folha Configurações abre por padrão.
4. Clique em **Credenciais de implantação** no menu PUBLICAÇÃO.
5. Insira as novas credenciais de implantação nas caixas fornecidas e clique no botão Salvar.

Você pode usar essas credenciais para clonar o site com o git ou configurar implantações automatizadas do GitHub, do TFS ou do Mercurial.  Para obter mais informações, consulte a [Documentação de implantação do Serviço de Aplicativo do Azure].

### <a name="appsettings"></a>Configurações do aplicativo
A maioria das configurações para um serviço móvel migrado está disponível por meio de Configurações do Aplicativo.  Você pode obter uma lista de configurações do aplicativo no [portal do Azure].
Para exibir ou alterar as configurações do aplicativo:

1. Faça logon no [portal do Azure].
2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
3. A folha Configurações abre por padrão.
4. Clique em **Configurações do aplicativo** no menu GERAL.
5. Role até a seção Configurações do Aplicativo e localize sua configuração de aplicativo.
6. Clique no valor da configuração do aplicativo para editar o valor.  Clique em **Salvar** para salvar o valor.

Você pode atualizar várias configurações de aplicativo ao mesmo tempo.

> [!TIP]
> Há duas Configurações de Aplicativo com o mesmo valor.  Por exemplo, você pode ver *ApplicationKey* e *MS\_ApplicationKey*.  Atualize ambas as configurações de aplicativo ao mesmo tempo.
>
>

### <a name="authentication"></a>Autenticação
Todas as configurações de autenticação estão disponíveis como configurações de aplicativo no site migrado.  Para atualizar as configurações de autenticação, é preciso alterar as configurações do aplicativo apropriadas.  A tabela a seguir mostra as configurações do aplicativo apropriadas para o seu provedor de autenticação:

| Provedor | ID do cliente | Segredo do cliente | Outras configurações |
|:--- |:--- |:--- |:--- |
| Conta da Microsoft |**MS\_MicrosoftClientID** |**MS\_MicrosoftClientSecret** |**MS\_MicrosoftPackageSID** |
| Facebook |**MS\_FacebookAppID** |**MS\_FacebookAppSecret** | |
| Twitter |**MS\_TwitterConsumerKey** |**MS\_TwitterConsumerSecret** | |
| Google |**MS\_GoogleClientID** |**MS\_GoogleClientSecret** | |
| AD do Azure |**MS\_AadClientID** | |**MS\_AadTenants** |

Observação: **MS\_AadTenants** é armazenado como uma lista separada por vírgulas de domínios de locatário (os campos "Locatários Permitidos" no portal de Serviços Móveis).

> [!WARNING]
> **Não use os mecanismos de autenticação no menu Configurações**
>
> O Serviço de aplicativo do Azure fornece um sistema de autenticação e autorização "sem código" separado no menu de Configurações *Autenticação/Autorização* e a opção (preterida) *Autenticação Móvel* no menu Configurações.  Essas opções são incompatíveis com um Serviço Móvel do Azure migrado.  Você pode [atualizar seu site](app-service-mobile-net-upgrading-from-mobile-services.md) para aproveitar a autenticação do Serviço de Aplicativo do Azure.
>
>

### <a name="easytables"></a>Dados
A guia *Dados* nos Serviços Móveis foi substituída pelas *Tabelas Fáceis* no Portal do Azure.  Para acessar tabelas fáceis:

1. Faça logon no [portal do Azure].
2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
3. A folha Configurações abre por padrão.
4. Clique em **Tabelas fáceis** no menu MÓVEL.

Você pode adicionar uma tabela clicando no botão **Adicionar** ou acessar suas tabelas existentes clicando em um nome de tabela.  Há várias operações que você pode executar nessa folha, incluindo:

* Alterando as permissões de tabela
* Editando os scripts operacionais
* Gerenciando o esquema da tabela
* Excluindo a tabela
* Limpando o conteúdo da tabela
* Excluindo linhas específicas da tabela

### <a name="easyapis"></a>API
A guia *API* nos Serviços Móveis foi substituída pelas *APIs Fáceis* no portal do Azure.  Para acessar APIs fáceis:

1. Faça logon no [portal do Azure].
2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
3. A folha Configurações abre por padrão.
4. Clique em **APIs fáceis** no menu MÓVEL.

Suas APIs migradas já estão listadas na folha.  Você também pode adicionar uma API dessa folha.  Para gerenciar uma API específica, clique na API.
Na nova folha, você pode ajustar as permissões e editar os scripts para a API.

### <a name="on-demand-jobs"></a>Trabalhos do Agendador
Todos os trabalhos do Agendador estão disponíveis por meio da seção de Coleções de trabalhos do Agendador.  Para acessar seus trabalhos do Agendador:

1. Faça logon no [portal do Azure].
2. Selecione **Procurar>**, digite **Agenda** na caixa *Filtro* e selecione **Coleções do Agendador**.
3. Selecione a Coleção de trabalhos para seu site.  Ele é chamado de *sitename*-jobs.
4. Clique em **Configurações**.
5. Clique em **Trabalhos do Agendador** em GERENCIAR.

Os trabalhos agendados são listados com a frequência especificada antes da migração.  Os trabalhos sob demanda são desabilitados.  Para executar um trabalho sob demanda:

1. Selecione o trabalho que você deseja executar.
2. Se necessário, clique em **Habilitar** para habilitar o trabalho.
3. Clique em **Configurações** e em **Agenda**.
4. Selecione uma Recorrência de **Uma vez** e clique em **Salvar**

Os trabalhos sob demanda estão localizados em `App_Data/config/scripts/scheduler post-migration`.  É recomendável converter todos os trabalhos sob demanda para [WebJobs] ou [Funções].  Grave novos trabalhos de agendador como [WebJobs] ou [Funções].

### <a name="notification-hubs"></a>Hubs de Notificação
Os Serviços Móveis usam os Hubs de Notificação para notificações por push.  As seguintes configurações de aplicativo são usadas para vincular o Hub de Notificação ao Serviço Móvel após a migração:

| Configuração de aplicativo | Descrição |
|:--- |:--- |
| **MS\_PushEntityNamespace** |O namespace do Hub de notificação |
| **MS\_NotificationHubName** |O nome do hub de notificação |
| **MS\_NotificationHubConnectionString** |A cadeia de conexão do hub de notificação |
| **MS\_NamespaceName** |Um alias para MS_PushEntityNamespace |

O Hub de notificação é gerenciado por meio do [portal do Azure].  Observe o nome do hub de notificação (você pode descobri-lo usando as Configurações do Aplicativo):

1. Faça logon no [portal do Azure].
2. Selecione **Procurar**> e depois selecione **Hubs de Notificação**
3. Clique no nome do hub de notificação associado ao serviço móvel.

> [!NOTE]
> Se o Hub de Notificação for um tipo "Misto", ele não será visível.  Hubs de notificação do tipo "misto” utilizam Hubs de notificação e recursos do barramento de serviço herdados.  [Converta seus namespaces mistos] antes de continuar.  Quando a conversão for concluída, o hub de notificação será exibido no [portal do Azure].
>
>

Para obter mais informações, consulte a documentação [Hubs de Notificação] .

> [!TIP]
> Recursos de gerenciamento de Hubs de notificação no [portal do Azure] ainda estão em visualização.  O [Portal Clássico do Azure] permanece disponível para gerenciar os hubs de notificação.
>
>

### <a name="legacy-push"></a>Configurações de envio por push herdado
Se configurou o envio por push no seu serviço móvel antes da introdução em Hubs de Notificação, você está usando o *envio por push herdado*.  Se estiver usando o envio por push e não vir um Hub de Notificação listado na configuração, é provável que você esteja usando o *envio por push herdado*.  Esse recurso é migrado com todos os outros recursos.  No entanto, recomendamos que você atualize para Hubs de Notificação logo após a migração ser concluída.

Enquanto isso, todas as configurações de envio por push herdado (com a exceção notável do certificado APNS) estão disponíveis nas Configurações de Aplicativo.  Atualize o certificado APNS substituindo o arquivo apropriado no sistema de arquivos.

### <a name="app-settings"></a>Outras configurações de aplicativo
As seguintes configurações de aplicativo adicionais são migradas do seu serviço móvel e estão disponíveis em *Configurações* > *App Configurações*:

| Configuração de aplicativo | Descrição |
|:--- |:--- |
| **MS\_MobileServiceName** |O nome do seu aplicativo |
| **MS\_MobileServiceDomainSuffix** |O prefixo do domínio. ou seja azure-mobile.net |
| **MS\_ApplicationKey** |A chave do seu aplicativo |
| **MS\_MasterKey** |A chave mestre do aplicativo |

A chave do aplicativo e a chave mestra são idênticas às chaves de aplicativo do serviço móvel original.  Em particular, a chave de aplicativo é enviada por clientes móveis para validar o uso da API móvel.

### <a name="cliequivalents"></a>Equivalentes de Linha de Comando
Você não pode mais usar o comando *azure mobile* para gerenciar o site de Serviços Móveis do Azure.  Em vez disso, muitas funções foram substituídas pelo comando *azure site* .  Use a tabela a seguir para localizar os equivalentes de comandos comuns:

| Comando *Azure Mobile* | Comando *Azure Site* equivalente |
|:--- |:--- |
| locais móveis |lista do local do site |
| lista móvel |lista de sites |
| mobile show *name* |site show *name* |
| mobile restart *name* |site restart *name* |
| mobile redeploy *name* |site deployment redeploy *commitId* *name* |
| mobile key set *name* *type* *value* |site appsetting delete *key* *name* <br/> site appsetting add *key*=*value* *name* |
| mobile config list *name* |site appsetting list *name* |
| mobile config get *name* *key* |site appsetting show *key* *name* |
| mobile config set *name* *key* |site appsetting delete *key* *name* <br/> site appsetting add *key*=*value* *name* |
| mobile domain list *name* |site domain list *name* |
| mobile domain add *name* *domain* |site domain add *domain* *name* |
| mobile domain delete *name* |site domain delete *domain* *name* |
| mobile scale show *name* |site show *name* |
| mobile scale change *name* |site scale mode *mode* *name* <br /> site scale instances *instances* *name* |
| mobile appsetting list *name* |site appsetting list *name* |
| mobile appsetting add *name* *key* *value* |site appsetting add *key*=*value* *name* |
| mobile appsetting delete *name* *key* |site appsetting delete *key* *name* |
| mobile appsetting show *name* *key* |site appsetting delete *key* *name* |

Atualize configurações de notificação por push ou de autenticação atualizando a configuração apropriada do aplicativo.
Edite arquivos e publique seu site via ftp ou git.

### <a name="diagnostics"></a>Diagnóstico e registro em log
Normalmente, o registro em log de diagnóstico está desabilitado em um Serviço de Aplicativo do Azure.  Para habilitar registro em log de diagnóstico:

1. Faça logon no [portal do Azure].
2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
3. A folha Configurações abre por padrão.
4. Selecione **Logs de diagnóstico** no menu RECURSOS.
5. Clique em **ATIVADO** para os seguintes logs: **Registro em log de aplicativo (sistema de arquivos)**, **Mensagens de erro detalhadas** e **Rastreamento de solicitação com falha**
6. Clique em **sistema de arquivos** para registro em log do servidor Web
7. Clique em **Salvar**

Para exibir os logs:

1. Faça logon no [portal do Azure].
2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
3. Clique no botão **Ferramentas**
4. Selecione **Transmissão de Log** no menu OBSERVE.

Os logs são exibidos na janela à medida que são gerados.  Você também pode baixar os logs para análise posterior usando suas credenciais de implantação. Para obter mais informações, confira a documentação sobre [Logs].

## <a name="known-issues"></a>Problemas conhecidos
### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Excluir um clone de aplicativo móvel migrado causa uma pane do site
Se você clonar o serviço móvel migrado usando o Azure PowerShell e excluir o clone, a entrada DNS do serviço de produção será removida.  O site não está mais acessível pela Internet.  

Resolução: se deseja clonar o site, faça isso por meio do portal.

### <a name="changing-webconfig-does-not-work"></a>A alteração de Web.config não funciona
Se você tiver um site ASP.NET, as alterações no arquivo `Web.config` não serão aplicadas.  O Serviço de Aplicativo do Azure cria um arquivo `Web.config` adequado durante a inicialização compatível com o tempo de execução dos Serviços Móveis.  Você pode substituir determinadas configurações (como cabeçalhos personalizados) usando um arquivo de transformação XML.  Crie um arquivo chamado `applicationHost.xdt` – esse arquivo deve terminar no diretório `D:\home\site` no Serviço do Azure.  Carregue o arquivo `applicationHost.xdt` por meio de um script de implantação personalizado ou diretamente, usando o Kudu.  A seguir é mostrado um documento de exemplo:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Para obter mais informações, confira a documentação [Exemplos de Transformação XDT] no GitHub.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Os Serviços Móveis migrados não podem ser adicionados ao Gerenciador de Tráfego
Ao criar um perfil do Gerenciador de Tráfego, você não pode escolher diretamente um serviço móvel migrado para o perfil.  Use um "ponto de extremidade externo".  O ponto de extremidade externo só pode ser adicionado por meio do PowerShell.  Para obter mais informações, confira o [Tutorial do Gerenciador de Tráfego](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Próximas etapas
Agora que o aplicativo foi migrado para o serviço de aplicativo, há ainda mais recursos que você pode usar:

* [Slots de preparo] de implantação permitem testar alterações ao seu site e executar um teste A/B.
* [WebJobs] fornecem uma substituição para trabalhos agendados sob demanda.
* Você pode [implantar continuamente] o site vinculando-o a GitHub, TFS ou Mercurial.
* Você pode usar o [Application Insights] para monitorar seu site.
* Atenda a um site e a uma API móvel com o mesmo código.

### <a name="upgrading-your-site"></a>Atualizar o site de serviços móveis para o SDK de aplicativos móveis do Azure
* Para projetos de servidor baseados em Node.js, o novo [SDK do Node.js de Aplicativos Móveis] oferece vários recursos novos. Por exemplo, você pode agora fazer desenvolvimento local e a depuração, usar qualquer versão do Node. js acima de 0,10 e personalizar com qualquer middleware Express. js.
* Para projetos de servidor baseado em .NET, os novos [pacotes NuGet do SDK de Aplicativos Móveis](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) têm mais flexibilidade em dependências do NuGet.  Esses pacotes dão suporte à nova autenticação de Serviço de Aplicativo e podem ser usados com qualquer projeto ASP.NET. Para saber mais sobre a atualização, consulte [Atualizar o serviço de celular do .NET existente para o Serviço de Aplicativo](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Preços do Serviço de Aplicativo]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Application Insights]: ../application-insights/app-insights-overview.md
[Dimensionamento automático]: ../app-service/web-sites-scale.md
[Serviço de Aplicativo do Azure]: ../app-service/app-service-web-overview.md
[Portal Clássico do Azure]: https://manage.windowsazure.com
[portal do Azure]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/en-us/regions/
[Planos do Agendador do Azure]: ../scheduler/scheduler-plans-billing.md
[implantar continuamente]: ../app-service/app-service-continuous-deployment.md
[Converta seus namespaces mistos]: https://azure.microsoft.com/en-us/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[nomes de domínio personalizados]: ../app-service/app-service-web-tutorial-custom-domain.md
[Fiddler]: http://www.telerik.com/fiddler
[disponibilidade geral do Serviço de Aplicativo do Azure]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Hybrid Connections]: ../app-service/app-service-hybrid-connections.md
[Logs]: ../app-service/web-sites-enable-diagnostic-log.md
[SDK do Node.js de Aplicativos Móveis]: https://github.com/azure/azure-mobile-apps-node
[Serviços Móveis vs. Serviço de Aplicativo]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Hubs de Notificação]: ../notification-hubs/notification-hubs-push-notification-overview.md
[monitoramento de desempenho]: ../app-service/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[Slots de preparo]: ../app-service/web-sites-staged-publishing.md
[VNet]: ../app-service/web-sites-integrate-with-vnet.md
[Exemplos de Transformação XDT]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[Funções]: ../azure-functions/functions-overview.md
