---
title: "Como migrar e publicar um aplicativo Web em um serviço de nuvem do Azure por meio do Visual Studio | Microsoft Docs"
description: "Saiba como migrar e publicar seu aplicativo Web em um serviço de nuvem do Azure por meio do Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 9394adfd-a645-4664-9354-dd5df08e8c91
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: d5de4f5a7357cf5adde7773867356d47ad447bab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Como migrar e publicar um aplicativo Web em um serviço de nuvem do Azure por meio do Visual Studio
Para tirar proveito dos serviços de hospedagem e a escalabilidade do Azure, você pode migrar e publicar seu aplicativo Web em um serviço de nuvem do Azure. Você pode executar um aplicativo Web no Azure fazendo alterações mínimas nele.

> [!NOTE]
> Este tópico é sobre a implantação de serviços de nuvem, não em sites. Para obter informações sobre a implantação de sites da Web, consulte [Implantar um aplicativo Web no Serviço de Aplicativo do Azure](app-service/app-service-deploy-local-git.md).
>
>

Para obter uma lista de modelos específicos com suporte para Visual C# e Visual Basic, consulte a seção **Modelos de projeto com suporte** mais adiante neste tópico.

Primeiro, você precisa habilitar o aplicativo Web para o Azure por meio do Visual Studio. A ilustração a seguir mostra as etapas principais para a publicação do aplicativo Web existente adicionando um projeto do Azure para ser usado para a implantação. Esse processo adiciona um projeto do Azure com a função web necessária à sua solução. Com base no tipo de projeto Web que você tem, as propriedades do projeto para assemblies também são atualizadas se o pacote de serviço exigir assemblies adicionais para a implantação.

![Publicar um aplicativo Web no Microsoft Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

> [!NOTE]
> O comando **Converter**, **Converter para o Projeto de Serviço de Nuvem do Azure** é exibido somente para o projeto Web em sua solução. Por exemplo, o comando não ficaria disponível para um projeto do Silverlight na solução.
> Quando você cria um pacote de serviço ou publica seu aplicativo no Azure, avisos ou erros podem ocorrer. Esses avisos e erros podem ajudá-lo a corrigir problemas antes da implantação no Azure. Por exemplo, você pode receber um aviso sobre um assembly ausente. Para obter mais informações sobre como tratar avisos como erros, consulte [Configurar um projeto de serviço de nuvem do Azure com o Visual Studio](vs-azure-tools-configuring-an-azure-project.md). Se você cria seu aplicativo, executa seu aplicativo localmente usando o emulador de computação ou publica seu aplicativo no Azure, você pode ver o seguinte erro na janela **Lista de Erros**: **O caminho especificado, o nome do arquivo ou ambos são muito longos**. Esse erro ocorre quando o nome totalmente qualificado do projeto do Azure é muito longo. O nome do projeto, incluindo o caminho completo, não pode ter mais de 146 caracteres. Por exemplo, este é o nome completo do projeto inclui o caminho de um projeto do Azure que é criado para um aplicativo do Silverlight: `c:\users\<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Talvez você precise mover sua solução para um diretório diferente com um caminho mais curto para reduzir o tamanho do nome totalmente qualificado do projeto.
>
>

Para migrar e publicar um aplicativo Web no Azure do Visual Studio, siga estas etapas.

## <a name="enable-a-web-application-for-deployment-to-azure"></a>Habilitar um aplicativo Web para implantação no Azure
### <a name="to-enable-a-web-application-for-deployment-to-azure"></a>Para habilitar um aplicativo Web para implantação no Azure
1. Para habilitar o aplicativo Web para implantação no Azure, abra o menu de atalho para um projeto Web em sua solução e escolha Adicionar Projeto de Implantação do Azure.

    As seguintes ações ocorrem:

   * Um projeto do Azure chamado `<name of the web project>.Azure` é adicionado à solução para seu aplicativo.
   * Uma função web para o projeto Web é adicionada ao projeto do Azure.
   * A propriedade **Copiar Local** é definida como verdadeira para todos os assemblies que são necessários para MVC 2, MVC 3, MVC 4 e aplicativos de negócios do Silverlight. Isso adicionará esses assemblies ao pacote de serviço usado para a implantação.

   > [!IMPORTANT]
   > Se tiver outros assemblies ou arquivos necessários para o aplicativo Web, você precisa definir manualmente as propriedades para esses arquivos. Para obter informações sobre como definir essas propriedades, consulte a seção **Incluir Arquivos no Pacote de Serviço** posteriormente neste artigo.
   >
   > [!NOTE]
   > Se já existir uma função web para um projeto Web específico em um projeto do Azure na solução, **Converter**, **Converter em projeto de serviço de nuvem do Azure** não é exibido no menu de atalho para este projeto Web.
   >
   >

   Se tiver vários projetos Web em seu aplicativo Web e desejar criar funções web para cada projeto Web, você deve seguir as etapas neste procedimento para cada um dos projetos Web. Isso cria projetos do Azure separados para cada função web. Cada projeto Web pode ser publicado separadamente. Como alternativa, você pode adicionar manualmente outra função web a um projeto do Azure existente em seu aplicativo Web. Para fazer isso, abra o menu de atalho para a pasta **funções** em seu projeto do Azure, escolha **Adicionar**, em seguida, **Projeto de Função Web na solução**, escolha o projeto para adicionar como uma função web e, em seguida, escolha o botão **OK**.

## <a name="use-an-azure-sql-database-for-your-application"></a>Usar um Banco de Dados SQL do Azure para seu aplicativo
Se você tiver uma cadeia de conexão para o aplicativo Web que usa um banco de dados do SQL Server no local, é necessário alterar essa cadeia de conexão para usar uma instância do Banco de Dados SQL que o Azure hospeda.

> [!IMPORTANT]
> Sua assinatura deve habilitá-lo usar o Banco de Dados SQL. Se acessar sua assinatura do [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885), você pode determinar quais serviços são fornecidos pela assinatura. As instruções a seguir se aplicam ao [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885)lançado. Se estiver usando o [Portal do Azure](http://portal.microsoft.com), vá para o próximo procedimento.
>
>

### <a name="to-use-a-sql-database-instance-in-your-web-role-for-your-connection-string"></a>Para usar uma instância do Banco de Dados SQL em sua função web para a cadeia de conexão
1. Para criar uma instância do Banco de Dados SQL no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885), siga as etapas no seguinte artigo: [Criar um servidor de Banco de Dados SQL](http://go.microsoft.com/fwlink/?LinkId=225109).

   > [!NOTE]
   > Quando configura as regras de firewall para sua instância do banco de dados SQL, você deve selecionar a caixa de seleção **Permitir que outros serviços do Azure acessem este servidor** .
   >
   >
2. Para criar uma instância do banco de dados SQL a ser usado para a cadeia de conexão, siga as etapas na próxima seção no seguinte artigo: [Criar um banco de dados SQL](http://go.microsoft.com/fwlink/?LinkId=225110).
3. Para copiar a cadeia de conexão ADO.NET a ser usada para a cadeia de conexão, execute as seguintes etapas no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).  

   1. Escolha o botão **Banco de Dados** e, em seguida, abra o nó para a assinatura que você usou para criar a instância do banco de dados SQL.
   2. Para exibir as instâncias disponíveis do banco de dados SQL, escolha o nó **Bancos de Dados SQL** .
   3. Escolha o banco de dados para exibir suas propriedades. A exibição **Propriedades** aparece.

      > [!NOTE]
      > Se a exibição **Propriedades** não aparecer, talvez seja necessário abri-la usando o divisor.
      >
      >
   4. Para exibir as cadeias de conexão, escolha o botão de reticências (...) ao lado de Exibir.

      A caixa de diálogo **Cadeias de Conexão** é exibida.
   5. Para copiar a cadeia de conexão ADO.NET, realce o texto e escolha as teclas Ctrl + C.
   6. Para fechar a caixa de diálogo, escolha o botão **Fechar** .
4. Para substituir a cadeia de conexão no arquivo web.config para usar essa instância do Banco de Dados SQL, abra o arquivo web.config, realce a entrada da cadeia de conexão existente e escolha as teclas Ctrl + V. A cadeia de conexão ADO.NET para a instância do Banco de Dados SQL substitui a cadeia de conexão existente.
5. Você também deve adicionar o parâmetro `MultipleActiveResultSets=True` para a cadeia de conexão. A cadeia de conexão deve ter o seguinte formato:

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```
6. (Opcional) Um método alternativo para alterar a cadeia de conexão diretamente no arquivo web.config é adicionar uma seção a um dos arquivos de transformação web.config, dependendo da configuração da compilação que você usar para criar o pacote de serviço. Abra o arquivo Web.Debug.Config ou o arquivo Web.Release.Config. Adicione a seção a seguir ao arquivo:

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```
7. Salve o arquivo modificado e republique o aplicativo.

### <a name="to-use-an-instance-of-sql-database-by-using-the-azure-classic-portal"></a>Para usar uma instância do Banco de Dados SQL usando o Portal Clássico do Azure
1. No [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885), escolha o nó de Bancos de Dados SQL.

   * Se a instância do Banco de Dados SQL que você deseja usar aparecer, abra-a.
   * Se não tiver criado nenhuma instância, escolha o link apropriado e crie uma instância.
2. Depois que você abrir ou criar uma instância de banco de dados, escolha o link **cadeias de conexão** .
3. Na parte inferior da página, escolha o link para definir as configurações de firewall e aceite os valores padrão ou configure os valores de que você precisa.
4. Copie a cadeia de conexão ADO.NET, cole-a em seu arquivo web.config sobre a cadeia de conexão antiga do banco de dados local e certifique-se de adicionar `MultipleActiveResultSets=True`.

## <a name="publish-a-web-application-to-azure"></a>Publicar um aplicativo Web no Azure
### <a name="to-publish-a-web-application-to-azure"></a>Para publicar um aplicativo Web no Azure
1. Para testar o aplicativo no ambiente de desenvolvimento local usando o emulador de computação do Azure, abra o menu de atalho do projeto do Azure para a função web e escolha **Definir como um Projeto Inicial**. Em seguida, escolha **Depurar**, **Iniciar depuração** (teclado: **F5**).

    A caixa de diálogo **Iniciar o ambiente de depuração Azure** é aberta e o aplicativo é iniciado no navegador. Para obter detalhes específicos sobre como iniciar cada tipo de aplicativo Web no emulador de computação, consulte a tabela nesta seção.
2. Para configurar os serviços para que seu aplicativo seja publicado no Azure, você precisa ter uma conta da Microsoft e uma assinatura do Azure. Use as etapas no tópico a seguir para configurar os serviços: [preparar, publicar ou implantar um aplicativo do Azure do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).
3. Para publicar o aplicativo Web no Azure, abra o menu de atalho do projeto Web e escolha **Publicar no Azure**.

    A caixa de diálogo **Publicar aplicativo do Azure** é aberta e o Visual Studio inicia o processo de implantação. Para obter mais informações sobre como publicar o aplicativo, consulte a seção **Publicar um aplicativo do Azure do Visual Studio** em [Publicando um serviço de nuvem usando as ferramentas do Azure](vs-azure-tools-publishing-a-cloud-service.md).

   > [!NOTE]
   > Você também pode publicar o aplicativo Web por meio do projeto do Azure. Para fazê-lo, abra o menu de atalho do projeto do Azure e escolha **Publicar**.
   >
   >
4. Para ver o progresso da implantação, você pode exibir a janela **Log de atividades do Azure** . Esse log é exibido automaticamente quando o processo de implantação é iniciado. Você pode expandir o item de linha no log de atividades para exibir informações detalhadas, como mostrado na ilustração a seguir:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)
5. (Opcional) Para cancelar o processo de implantação, abra o menu de atalho do item de linha no log de atividades e escolha **Cancelar e remover**. Isso interrompe o processo de implantação e exclui o ambiente de implantação do Azure.

   > [!NOTE]
   > Para remover este ambiente de implantação após ele ter sido implantado, você deve usar o [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
   >
   >
6. (Opcional) Após as instâncias de função terem sido iniciadas, o Visual Studio mostra automaticamente o ambiente de implantação no nó **Computação do Azure** no **Gerenciador de Nuvem** ou **Gerenciador de Servidores**. Aqui você pode exibir o status das instâncias de função individuais.

    A ilustração a seguir mostra as instâncias de função no **Gerenciador de Servidores** enquanto eles ainda estão no estado Inicializando:

    ![VST_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)
7. Para acessar seu aplicativo após a implantação, escolha a seta ao lado de sua implantação quando um status de **Concluído** aparecer no **Log de atividades do Azure**. Isso exibe a URL para o aplicativo Web no Azure. Consulte a tabela a seguir para obter detalhes sobre como iniciar um tipo específico de aplicativo Web do Azure.

    A tabela a seguir lista os detalhes de como iniciar aplicativos Web específicos por meio do Azure ou como executar ou depurar um aplicativo Web localmente usando o Emulador de Computação do Azure:

   | Tipo de aplicativo Web | Executar/depurar localmente usando Emulador de Computação | em execução no Azure |
   | --- | --- | --- |
   | Aplicativo Web do ASP.NET |Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5**.). |Escolha o hiperlink da URL exibido na guia **Implantação** para que o **Log de Atividades do Azure** carregue a página inicial no navegador. |
   | Aplicativo Web ASP.NET MVC 2 |Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5**.). |Escolha o hiperlink da URL exibido na guia **Implantação** para que o **Log de Atividades do Azure** carregue a página inicial no navegador. |
   | Aplicativo Web ASP.NET MVC 3 |Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5**.). |Escolha o hiperlink da URL exibido na guia **Implantação** para que o **Log de Atividades do Azure** carregue a página inicial no navegador. |
   | Aplicativo Web ASP.NET MVC 4 |Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5**.). |Escolha o hiperlink da URL exibido na guia **Implantação** para que o **Log de Atividades do Azure** carregue a página inicial no navegador. |
   | Aplicativo Web ASP.NET vazio |Você deve adicionar uma página .aspx ao seu aplicativo que você define como a página inicial do projeto Web. Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5**.). |Se você tiver uma página padrão .aspx em seu aplicativo, escolha o hiperlink da URL exibido na guia **Implantação** para o **Log de Atividades do Azure** e essa página é carregada no navegador. Se você tiver uma página. aspx diferente, você precisa navegar até essa página específica usando o seguinte formato para sua url: `<url for deployment>/<name of page>.aspx` |
   | Aplicativo Silverlight |Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5**.). |Você precisará navegar até a página específica para seu aplicativo usando o seguinte formato de URL: `<url for deployment>/<name of page>.aspx` |
   | Aplicativo de negócios Silverlight |Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5**.). |Você precisará navegar até a página específica para seu aplicativo usando o seguinte formato de URL: `<url for deployment>/<name of page>.aspx` |
   | Aplicativo de navegação Silverlight |Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5**.). |Você precisará navegar até a página específica para seu aplicativo usando o seguinte formato de URL:`<url for deployment>/<name of page>.aspx` |
   | Aplicativo de serviço WCF |Você deve definir o arquivo .svc como a página inicial do seu projeto de serviço WCF. Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5**.). |Você precisará navegar até o arquivo svc para seu aplicativo usando o seguinte formato de URL: `<url for deployment>/<name of service file>.svc` |
   | Aplicativo de serviço de fluxo de trabalho WCF |Você deve definir o arquivo .svc como a página inicial do seu projeto de serviço WCF. Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5**.). |Você precisará navegar até o arquivo svc para seu aplicativo usando o seguinte formato de URL: `<url for deployment>/<name of service file>.svc` |
   | Entidades dinâmicas do ASP.NET |Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5**.). |Você deve atualizar a cadeia de conexão (consulte a próxima seção). Você também precisará navegar até a página específica para seu aplicativo usando o seguinte formato de URL: `<url for deployment>/<name of page>.aspx` |
   | Dados dinâmicos ASP.NET Linq to SQL |Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5**.). |Você deve seguir as etapas neste procedimento: Usar um banco de dados SQL Azure para seu aplicativo (consulte a seção anterior neste tópico). Você também precisará navegar até a página específica para seu aplicativo usando o seguinte formato de URL: `<url for deployment>/<name of page>.aspx` |

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Atualizar uma cadeia de conexão para Entidades dinâmicas do ASP.NET
### <a name="to-update-a-connection-string-for-aspnet-dynamic-entities"></a>Para atualizar uma cadeia de conexão para Entidades dinâmicas do ASP.NET
1. Para criar um banco de dados do SQL Azure que pode ser usado para um aplicativo Web de entidades dinâmicas do ASP.NET, siga as etapas no procedimento **usar um banco de dados do SQL Azure para seu aplicativo** anteriormente neste tópico.
2. Adicione as tabelas e campos de que você precisa para esse banco de dados por meio do [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
3. A cadeia de conexão para esse tipo de aplicativo tem o formato a seguir no arquivo web.config:  

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Atualize o valor de *connectionString* com a cadeia de conexão ADO.NET para seu banco de dados do SQL Azure da seguinte maneira:

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```
4. Para salvar o arquivo web.config com as alterações feitas à cadeia de conexão, na barra de menus, escolha **Arquivo**, **Salvar web.config**.

## <a name="supported-project-templates"></a>Modelos de projeto com suporte
Para publicar um aplicativo Web no Azure, o aplicativo deve usar um dos modelos de projeto para C# ou Visual Basic listados na tabela a seguir.

| Grupo de modelos de projeto | Modelo do projeto |
| --- | --- |
| Web |Aplicativo Web do ASP.NET |
| Web |Aplicativo Web ASP.NET MVC 2 |
| Web |Aplicativo Web ASP.NET MVC 3 |
| Web |Aplicativo Web ASP.NET MVC 4 |
| Web |Aplicativo Web ASP.NET vazio |
| Web |Aplicativo Web ASP.NET MVC 2 vazio |
| Web |Aplicativo Web de entidades de dados dinâmicos ASP.NET |
| Web |Aplicativo Web de dados dinâmicos ASP.NET Linq do SQL |
| Silverlight |Aplicativo Silverlight |
| Silverlight |Aplicativo de negócios Silverlight |
| Silverlight |Aplicativo de navegação Silverlight |
| WCF |Aplicativo de serviço WCF |
| WCF |Aplicativo de serviço de fluxo de trabalho WCF |
| Fluxo de trabalho |Aplicativo de serviço de fluxo de trabalho WCF |

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a publicação, consulte [preparar, publicar ou implantar um aplicativo do Azure do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md). Confira também [Configuração de credenciais de autenticação nomeadas](vs-azure-tools-setting-up-named-authentication-credentials.md).
