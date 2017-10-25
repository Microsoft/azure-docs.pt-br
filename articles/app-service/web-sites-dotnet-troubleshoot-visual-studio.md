---
title: "Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio"
description: "Saiba como solucionar problemas de um aplicativo Web do Azure usando ferramentas de log, rastreamento e depuração remota criadas no Visual Studio 2013."
services: app-service
documentationcenter: .net
author: tdykstra
manager: erikre
editor: 
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.author: rachelap
ms.openlocfilehash: e42ff64fdd2be87fc19be267d4e2a29e38f67ef5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-a-web-app-in-azure-app-service-using-visual-studio"></a>Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio
## <a name="overview"></a>Visão geral
Este tutorial mostra como usar ferramentas do Visual Studio que ajudam a depurar um aplicativo Web no [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714), seja executando-o no [modo de depuração](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) remotamente ou exibindo logs do aplicativo e do servidor Web.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

O que você aprenderá:

* Quais funções de gerenciamento de aplicativo Web do Azure estão disponíveis no Visual Studio.
* Como usar a exibição remota do Visual Studio para fazer alterações rápidas em um aplicativo Web remoto.
* Como executar o modo de depuração remotamente enquanto um projeto está em execução no Azure, tanto para um aplicativo Web quanto para um Trabalho Web.
* Como criar logs de rastreamento de aplicativos e exibi-los enquanto o aplicativo os está criando.
* Como exibir logs do servidor Web, inclusive mensagens de erro detalhadas e rastreamento de solicitação com falha.
* Como enviar logs de diagnóstico para uma conta de armazenamento do Azure e exibi-los nela.

Se você tiver o Visual Studio Ultimate, também é possível utilizar o [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) para depuração. O IntelliTrace não é abordado neste tutorial.

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial funciona com o ambiente de desenvolvimento, o projeto Web e o aplicativo Web do Azure que você configurou na [Introdução ao Azure e ao ASP.NET][GetStarted]. Para as seções do WebJobs, você precisará do aplicativo que você criou na [Introdução ao SDK do Azure WebJobs][GetStartedWJ].

Os exemplos de código mostrados neste tutorial são para um aplicativo Web MVC em C#, mas os procedimentos para solução de problemas são os mesmos para aplicativos em Visual Basic e Web Forms.

O tutorial presume que você esteja usando o Visual Studio 2015 ou 2013. Se você estiver usando o Visual Studio 2013, os recursos do WebJobs requererão a [Atualização 4](http://go.microsoft.com/fwlink/?LinkID=510314) ou posterior.

O recurso de logs de streaming funciona apenas para aplicativos que tenham como alvo o .NET Framework 4 ou posterior.

## <a name="sitemanagement"></a>Gerenciamento e configuração de aplicativo Web
O Visual Studio fornece acesso a um subconjunto das funções de gerenciamento e das definições de configuração disponíveis no [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Nesta seção, você verá o que está disponível usando o **Gerenciador de Servidores**. Para ver os últimos recursos de integração do Azure, experimente também o **Cloud Explorer** . Você pode abrir as duas janelas do menu de **Exibição** .

1. Se você ainda não entrou no Azure usando o Visual Studio, clique no botão **Conectar ao Azure** do **Gerenciador de Servidores**.

    Uma alternativa é instalar um certificado de gerenciamento que dê acesso à conta. Se você optar por instalar um certificado, clique com o botão direito do mouse no nó **Azure** do **Gerenciador de Servidores** e clique em **Gerenciar e Filtrar Assinaturas** no menu de contexto. Na caixa de diálogo **Gerenciar Assinaturas do Azure**, clique na guia **Certificados** e em **Importar**. Siga as instruções para baixar e importe um arquivo de assinatura (também chamado de arquivo *.publishsettings* ) para a conta do Azure.

   > [!NOTE]
   > Se você baixar um arquivo de assinatura, salve-o em uma pasta fora dos diretórios de código-fonte (por exemplo, na pasta Downloads) e exclua-o após a conclusão da importação. Um usuário mal-intencionado que consiga acesso ao arquivo de assinatura pode editar, criar e excluir os serviços do Azure.
   >
   >

    Para obter mais informações sobre como se conectar aos recursos do Azure no Visual Studio, consulte [Gerenciar contas, assinaturas e funções administrativas](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. No **Gerenciador de Servidores**, expanda **Azure** e **Serviço de Aplicativo**.
3. Expanda o grupo de recursos que inclui o aplicativo Web que você criou em [Introdução ao Azure e ao ASP.NET][GetStarted] e clique com o botão direito do mouse no nó do aplicativo Web e clique em **Exibir Configurações**.

    ![Exibir Configurações no Gerenciador de Servidores](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    A guia **Aplicativo Web do Azure** aparece e nela você pode ver as tarefas de configuração e gerenciamento de aplicativos Web que estão disponíveis no Visual Studio.

    ![Janela Aplicativo Web do Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    Neste tutorial, você usará os menus suspensos de log e rastreamento. Você também usará a depuração remota, mas utilizará um método diferente para habilitá-la.

    Para saber mais sobre as caixas Configurações do Aplicativo e Cadeias de Conexão nessa janela, confira [Aplicativos Web do Azure: como as cadeias de caracteres do aplicativo e as cadeias de conexão funcionam](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx).

    Se desejar executar uma tarefa de gerenciamento do aplicativo Web que não possa ser concluída nessa janela, clique em **Abrir no Portal de Gerenciamento** para abrir uma janela do navegador para o portal do Azure.

## <a name="remoteview"></a>Acessar arquivos de aplicativo Web no Gerenciador de Servidores
Você tipicamente implanta um projeto Web com o `customErrors`sinalizador no arquivo Web.config definido como `On` ou `RemoteOnly`, o que significa que você não receberá uma mensagem de erro útil quando algo dá errado. Para muitos erros, tudo o que você recebe é uma página como uma das páginas a seguir.

**Erro de servidor no aplicativo '/':**

![Página de erro inútil](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Ocorreu um erro:**

![Página de erro inútil](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**O site não pode exibir a página**

![Página de erro inútil](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Normalmente, a maneira mais fácil de encontrar a causa do erro é habilitar mensagens de erro detalhadas, que a primeira das capturas de tela anteriores explica como fazer. Isso exige uma alteração no arquivo Web.config implantado. Você pode editar o arquivo *Web.config* no projeto e reimplantar o projeto ou criar uma [transformação de Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) e implantar uma build de depuração, mas há uma maneira mais rápida: no **Gerenciador de Soluções**, você pode exibir e editar diretamente arquivos no aplicativo Web remoto usando o recurso *exibição remota*.

1. No **Gerenciador de Servidores**, expanda **Azure**, expanda **Serviço de Aplicativo**, expanda o grupo de recursos em que seu aplicativo Web está localizado em e, em seguida, expanda o nó de seu aplicativo Web.

    Você vê nós que dão acesso aos arquivos de conteúdo e de log do aplicativo Web.
2. Expanda o nó **Arquivos** e clique duas vezes no arquivo *Web.config* .

    ![Abrir Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    O Visual Studio abre o arquivo Web.config no site remoto e mostra Remoto ao lado do nome do arquivo na barra de título.
3. Adicione a seguinte linha ao elemento `system.web` :

    `<customErrors mode="Off"></customErrors>`

    ![Editar Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Atualize o navegador que está mostrando a mensagem de erro inútil, e agora você recebe uma mensagem de erro detalhada, como o seguinte exemplo:

    ![Mensagem de erro detalhada](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (O erro mostrado foi criado adicionando-se a linha mostrada em vermelho a *Views\Home\Index.cshtml*.)

A edição do arquivo Web.config é apenas um exemplo dos cenários em que a capacidade de ler e editar arquivos no aplicativo Web do Azure facilita a solução de problemas.

## <a name="remotedebug"></a>Depuração remota de aplicativos Web
Se a mensagem de erro detalhada não oferecer informações suficientes e não for possível recriar o erro localmente, outra maneira de solucionar o problema é executar no modo de depuração remotamente. Você pode definir os pontos de interrupção, manipular diretamente a memória, percorrer o código e até mesmo alterar o caminho do código.

A depuração remota não funciona em edições Express do Visual Studio.

Esta seção mostra como depurar remotamente usando o projeto que você criou em [Introdução ao Azure e ao ASP.NET][GetStarted].

1. Abra o projeto Web que você criou em [Introdução ao Azure e ao ASP.NET][GetStarted].
2. Abra o *Controllers\HomeController.cs*.
3. Exclua o método `About()` e insira o código a seguir em seu lugar.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }
4. [Defina um ponto de interrupção](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) na linha `ViewBag.Message`.
5. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e clique em **Publicar**.
6. Na lista suspensa **Perfil**, selecione o mesmo perfil que você usou em [Introdução ao Azure e ao ASP.NET][GetStarted].
7. Clique na guia **Configurações** e altere **Configuração** para **Depuração** e, em seguida, clique em **Publicar**.

    ![Publicar em modo de depuração](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)
8. Quando a implantação for concluída e o navegador abrir a URL do Azure do seu aplicativo Web, feche o navegador.
9. No **Gerenciador de Servidores**, clique com o botão direito do mouse no seu aplicativo Web e clique em **Anexar Depurador**.

    ![Anexar Depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    O navegador é aberto automaticamente na home page em execução no Azure. Convém aguardar cerca de 20 segundos enquanto o Azure configura o servidor para depuração. Esse atraso ocorre apenas na primeira execução que você realiza em modo de depuração em um aplicativo Web. Nas próximas vezes, durante as próximas 48 horas, quando você iniciar a depuração novamente não haverá atraso.

    **Observação:** se você tiver problemas para iniciar o depurador, tente fazer isso usando o **Cloud Explorer** em vez do **Gerenciador de Servidores**.
10. Clique em **Sobre** no menu.

     O Visual Studio para no ponto de interrupção, e o código está em execução no Azure, e não no computador local.
11. Focalize a variável `currentTime` para ver o valor da hora.

     ![Exibir variável no modo de depuração em execução no Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

     A hora que você vê é a hora do servidor do Azure, que pode estar em um fuso horário diferente do computador local.
12. Insira um novo valor para a variável `currentTime` , como "Agora em execução no Azure".
13. Pressione F5 para continuar a execução.

     A página Sobre em execução no Azure exibe o novo valor que você inseriu na variável currentTime.

     ![A página Sobre com o novo valor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a> Trabalhos Web de depuração remota
Esta seção mostra como depurar remotamente usando o projeto e o site que você criou na [Introdução ao SDK de Trabalhos Web do Azure](https://github.com/Azure/azure-webjobs-sdk/wiki).

Os recursos mostrados nesta seção só estão disponíveis no Visual Studio 2013 com a Atualização 4 ou posterior.

A depuração remota só funciona com Trabalhos Web contínuos. Trabalhos Web agendados e sob demanda não oferecem suporte a depuração.

1. Abra o projeto Web que você criou na [Introdução ao SDK do Azure WebJobs][GetStartedWJ].
2. No projeto ContosoAdsWebJob, abra *Functions.cs*.
3. [Defina um ponto de interrupção](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) na primeira instrução do método `GnerateThumbnail`.

    ![Definir ponto de interrupção](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)
4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto Web (não no projeto de Trabalho Web) e clique em **Publicar**.
5. Na lista suspensa **Perfil** , selecione o mesmo perfil que você utilizou em [Introdução ao SDK de Trabalhos Web do Azure](https://github.com/Azure/azure-webjobs-sdk/wiki).
6. Clique na guia **Configurações** e altere **Configuração** para **Depuração** e, em seguida, clique em **Publicar**.

    O Visual Studio implanta os projetos Web e de Trabalho Web, e o seu navegador abre a URL do Azure do seu aplicativo Web.
7. Em **Gerenciador de Servidores**, expanda **Azure > Serviço de Aplicativo > seu grupo de recursos > seu aplicativo Web > WebJobs > Contínuo** e clique com o botão direito do mouse em **ContosoAdsWebJob**.
8. Clique em **Anexar o depurador**.

    ![Anexar Depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    O navegador é aberto automaticamente na home page em execução no Azure. Convém aguardar cerca de 20 segundos enquanto o Azure configura o servidor para depuração. Esse atraso ocorre apenas na primeira execução que você realiza em modo de depuração em um aplicativo Web. Na próxima vez que você anexar o depurador não haverá atraso, se você o fizer no prazo de 48 horas.
9. No navegador da Web que estiver aberto na home page do Contoso Ads, crie um novo anúncio.

    A criação de um anúncio faz com que uma mensagem de fila seja criada, a qual será capturada pelo trabalho Web e processada. Quando o SDK de trabalhos Web chamar a função para processar a mensagem de fila, o código atingirá seu ponto de interrupção.
10. Quando o depurador for interrompido no seu ponto de interrupção, será possível examinar e alterar os valores das variáveis enquanto o programa estiver sendo executado na nuvem. Na ilustração a seguir, o depurador mostra o conteúdo do objeto blobInfo que foi passado para o método GenerateThumbnail.

     ![Objeto blobInfo no depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)
11. Pressione F5 para continuar a execução.

     O método GenerateThumbnail termina de criar a miniatura.
12. No navegador, atualize a página de índice e você verá a miniatura.
13. No Visual Studio, pressione SHIFT+F5 para parar a depuração.
14. No **Gerenciador de Servidores**, clique com botão direito do mouse no nó ContosoAdsWebJob e clique em **Painel de exibição**.
15. Entre com suas credenciais do Azure e, em seguida, clique no nome do trabalho Web para ir à página do seu trabalho Web.

     ![Clicar em ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     O painel que mostra a função GenerateThumbnail foi executada recentemente.

     (Na próxima vez que você clicar em **Painel de exibição**, não será necessário entrar e o navegador vai diretamente para a página do Trabalho Web.)
16. Clique no nome da função para ver detalhes sobre a execução da função.

     ![Detalhes da função](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Se a sua função [criou logs](https://github.com/Azure/azure-webjobs-sdk/wiki), você pode clicar em **ToggleOutput** para vê-los.

## <a name="notes-about-remote-debugging"></a>Observações sobre a depuração remota
* A execução em modo de depuração em produção não é recomendada. Se o seu aplicativo Web de produção não estiver dimensionado para várias instâncias de servidor, a depuração impedirá o servidor Web de responder a outras solicitações. Se houver várias instâncias de servidor Web, ao anexar o depurador você obterá uma instância aleatória e não há como garantir que suas solicitações subsequentes do navegador irão para essa instância. Além disso, normalmente você não implanta uma compilação de depuração em produção e as otimizações do compilador para compilações de versão podem impossibilitar a exibição do que está acontecendo linha por linha em seu código-fonte. Para solucionar problemas de produção, os melhores recursos são o rastreamento do aplicativo e os logs do servidor Web.
* Evite paradas longas em pontos de interrupção durante a depuração remota. O Azure trata um processo parado por mais de alguns minutos como um processo sem resposta e o desliga.
* Enquanto você está depurando, o servidor está enviando dados para o Visual Studio, o que pode afetar os encargos de largura de banda. Para obter informações sobre as taxas de largura de banda, consulte [Preço do Azure](https://azure.microsoft.com/pricing/calculator/).
* Verifique se o atributo `debug` do elemento `compilation` no arquivo *Web.config* está definido como verdadeiro. Por padrão, ele é definido como true quando você publica uma configuração de compilação de depuração.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>
* Se você descobrir que o depurador não entra no código que você deseja depurar, será preciso alterar a configuração de Just My Code.  Para obter mais informações, consulte [Restringir a entrada em Just My Code](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code)
* Um timer é iniciado no servidor quando você habilita o recurso de depuração remota e, após 48 horas, o recurso é desativado automaticamente. Esse limite de 48 horas é definido por razões de segurança e desempenho. Você pode reativá-lo facilmente quantas vezes quiser. É recomendável deixá-lo desabilitado quando você não está depurando ativamente.
* Você pode anexar manualmente o depurador a qualquer processo, não apenas ao processo do aplicativo Web (w3wp.exe). Para obter mais informações sobre como usar o modo de depuração no Visual Studio, consulte [Depurando no Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

## <a name="logsoverview"></a>Visão geral dos logs de diagnóstico
Um aplicativo em ASP.NET executado em um aplicativo Web do Azure pode criar os seguintes tipos de logs:

* **Logs de rastreamento de aplicativos**<br/>
  O aplicativo cria esses logs chamando métodos da classe [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx) .
* **Logs do servidor Web**<br/>
  O servidor Web cria uma entrada no log para cada solicitação HTTP ao aplicativo Web.
* **Logs de mensagens de erro detalhadas**<br/>
  O servidor Web cria uma página HTML com algumas informações adicionais para solicitações HTTP com falha (aquelas que resultam em um código de status 400 ou superior).
* **Logs de rastreamento de solicitações com falha**<br/>
  O servidor Web cria um arquivo XML com informações de rastreamento detalhadas para solicitações HTTP com falha. O servidor Web também fornece um arquivo XSL para formatar o XML em um navegador.

Como o registro em log afeta o desempenho do aplicativo Web, o Azure oferece a possibilidade de habilitar ou desabilitar cada tipo de log conforme necessário. Para logs de aplicativo, você pode especificar que apenas os logs acima de um determinado nível de gravidade devem ser gravados. Por padrão, quando você cria um novo aplicativo Web, todo o registro em log está desabilitado.

Os logs são gravados em arquivos em uma pasta *LogFiles* no sistema de arquivos de seu aplicativo Web e são acessíveis via FTP. Os logs de servidor Web e de aplicativo também podem ser gravados em uma conta de armazenamento do Azure. Você pode manter um volume maior de logs em uma conta de armazenamento do que o que é possível no sistema de arquivos. Você está limitado a um máximo de 100 megabytes de logs ao usar o sistema de arquivos. (Os logs do sistema de arquivos são apenas para retenção de curto prazo. O Azure exclui arquivos de log antigos para abrir espaço para novos quando o limite é atingido.)  

## <a name="apptracelogs"></a>Criar e exibir logs de rastreamento de aplicativos
Nesta seção, você executará as seguintes tarefas:

* Adicionar instruções de rastreamento ao projeto Web que você criou em [Introdução ao Azure e ASP.NET][GetStarted].
* Exiba os logs ao executar o projeto localmente.
* Exiba os logs conforme eles são gerados pelo aplicativo em execução no Azure.

Para saber mais sobre como criar logs de aplicativo em Trabalhos Web, confira [Como trabalhar com o armazenamento de fila do Azure utilizando o SDK de Trabalhos Web: como gravar logs](https://github.com/Azure/azure-webjobs-sdk/wiki). As instruções a seguir para exibir logs e controlar como eles são armazenados no Azure também se aplicam a logs de aplicativos criados por Trabalhos Web.

### <a name="add-tracing-statements-to-the-application"></a>Adicionar instruções de rastreamento ao aplicativo
1. Abra *Controllers\HomeController.cs* e substitua os métodos `Index`, `About` e `Contact` pelo seguinte código para adicionar instruções `Trace` e uma instrução `using` para `System.Diagnostics`:

        public ActionResult Index()
        {
            Trace.WriteLine("Entering Index method");
            ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
            Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
            Trace.WriteLine("Leaving Index method");
            return View();
        }

        public ActionResult About()
        {
            Trace.WriteLine("Entering About method");
            ViewBag.Message = "Your app description page.";
            Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
            Trace.WriteLine("Leaving About method");
            return View();
        }

        public ActionResult Contact()
        {
            Trace.WriteLine("Entering Contact method");
            ViewBag.Message = "Your contact page.";
            Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
            Trace.WriteLine("Leaving Contact method");
            return View();
        }        
2. Adicione uma instrução `using System.Diagnostics;` ao todo do arquivo.

### <a name="view-the-tracing-output-locally"></a>Exibir a saída do rastreamento localmente
1. Pressione F5 para executar o aplicativo em modo de depuração.

    O ouvinte de rastreamento padrão grava toda a saída de rastreamento na janela **Saída** , junto com as outras saídas de Depuração. A ilustração a seguir mostra a saída das instruções de rastreamento que você adicionou ao método `Index` .

    ![Rastreamento na janela Depuração](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    As etapas a seguir mostram como exibir a saída do rastreamento em uma página da web, sem compilação em modo de depuração.
2. Abra o arquivo Web.config do aplicativo (localizado na pasta do projeto) e adicione um elemento `<system.diagnostics>` ao final do arquivo pouco antes do elemento de fechamento `</configuration>`:

          <system.diagnostics>
            <trace>
              <listeners>
                <add name="WebPageTraceListener"
                    type="System.Web.WebPageTraceListener,
                    System.Web,
                    Version=4.0.0.0,
                    Culture=neutral,
                    PublicKeyToken=b03f5f7f11d50a3a" />
              </listeners>
            </trace>
          </system.diagnostics>

    O `WebPageTraceListener` permite exibir a saída do rastreamento navegando até `/trace.axd`.
3. Adicione um <a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">elemento trace</a> em `<system.web>` no arquivo Web.config, como no seguinte exemplo:

        <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
4. Pressione CTRL+F5 para executar o aplicativo.
5. Na barra de endereços da janela do navegador, adicione *trace.axd* à URL e pressione Enter (a URL será semelhante a http://localhost:53370/trace.axd).
6. Na página de **Rastreamento do aplicativo**, clique em **Exibir detalhes** na primeira linha (não a linha BrowserLink).

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    A página **Solicitar Detalhes** é exibida e, na seção **Informações de Rastreamento**, você vê a saída das instruções de rastreamento que adicionou ao método `Index`.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Por padrão, `trace.axd` só está disponível localmente. Se quisesse disponibilizá-lo em um aplicativo Web remoto, você poderia adicionar `localOnly="false"` ao elemento `trace` no arquivo *Web.config*, conforme mostrado no seguinte exemplo:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    No entanto, a habilitação de `trace.axd` em um aplicativo Web de produção não costuma ser recomendada por motivos de segurança e, nas seções a seguir, você verá uma maneira mais fácil de ler logs de rastreamento em um aplicativo Web do Azure.

### <a name="view-the-tracing-output-in-azure"></a>Exibir a saída de rastreamento no Azure
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto Web e clique em **Publicar**.
2. Na caixa de diálogo **Publicar na Web**, clique em **Publicar**.

    Depois de publicar sua atualização, o Visual Studio abre uma janela do navegador em sua home page (supondo que você não limpou a **URL de destino** na guia **Conexão**).
3. No **Gerenciador de Servidores**, clique com o botão direito do mouse no aplicativo Web e selecione **Exibir Logs de Streaming**.

    ![Exibir Logs de Streaming no menu de contexto](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    A janela **Saída** mostra que você está conectado ao serviço de streaming de log e adiciona uma linha de notificação a cada minuto que passa sem um log para exibir.

    ![Exibir Logs de Streaming no menu de contexto](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. Na janela do navegador que mostra a home page do aplicativo, clique em **Contato**.

    Em alguns segundos, a saída do rastreamento em nível de erro adicionado ao método `Contact` é exibida na janela **Saída** .

    ![Rastreamento de erro na janela Saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    O Visual Studio está mostrando apenas rastreamentos em nível de erro porque essa é a configuração padrão quando você habilita o serviço de monitoramento de log. Quando você cria um novo aplicativo Web do Azure, todo registro em log é desabilitado por padrão, como você viu quando abriu a página de configurações do aplicativo Web anteriormente:

    ![Log do aplicativo desativado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    No entanto, ao selecionar **Exibir logs de streaming**, o Visual Studio alterou automaticamente **Registro em Log de aplicativo (sistema de arquivos)** para **Erro**, o que significa que os logs em nível de erro são relatados. Para ver todos os logs de rastreamento, você pode alterar essa configuração para **Detalhado**. Quando você seleciona um nível de severidade inferior a erro, todos os logs para níveis de severidade mais altos são relatados. Portanto, quando selecionar detalhado, você também verá logs de informações, avisos e erros.  

1. No **Gerenciador de Servidores**, clique com o botão direito do mouse no aplicativo Web e clique em **Exibir Configurações** como fez anteriormente.
2. Altere **Registro em Log de Aplicativo (Sistema de Arquivos)** para **Detalhado** e, em seguida, clique em **Salvar**.

    ![Configurando o rastreamento como Detalhado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
3. Na janela do navegador que está mostrando sua página **Contato**, clique em **Página Inicial**, em seguida, clique em **Sobre** e, em seguida, em **Contato**.

    Em poucos segundos, a janela **Saída** janela mostra toda a saída de rastreamento.

    ![Saída de rastreamento detalhado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    Nesta seção você habilitou e desabilitou o registro em log usando as configurações de aplicativo Web do Azure. Você também pode habilitar e desabilitar os ouvintes de rastreamento modificando o arquivo Web.config. No entanto, a modificação do arquivo Web.config faz com que o domínio do aplicativo seja reciclado, enquanto a habilitação do registro em log por meio da configuração do aplicativo Web não faz isso. Se o problema levar muito tempo para ser reproduzido, ou for intermitente, a reciclagem do domínio de aplicativo pode "corrigir" isso e forçar você a esperar até que o problema ocorra novamente. Como a habilitação do diagnóstico no Azure não faz isso, você não pode começar a capturar informações de erro imediatamente.

### <a name="output-window-features"></a>Recursos da janela Saída
A guia **Logs do Azure** da janela **Saída** tem vários botões e uma caixa de texto:

![Botões da guia Logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Desempenham as seguintes funções:

* Limpe a janela **Saída** .
* Habilite ou desabilite a quebra automática de linha.
* Inicie ou pare logs de monitoramento.
* Especifique quais logs monitorar.
* Baixe logs.
* Filtre logs com base em uma cadeia de caracteres de pesquisa ou uma expressão regular.
* Feche a janela **Saída** .

Se você inserir uma cadeia de caracteres de pesquisa ou uma expressão regular, o Visual Studio filtrará as informações de log no cliente. Isso significa que você pode inserir os critérios depois que os logs são exibidos na janela **Saída** e pode alterar os critérios de filtragem sem precisar gerar os logs novamente.

## <a name="webserverlogs"></a>Exibir logs de servidor Web
Logs do servidor Web registram toda a atividade HTTP para o aplicativo Web. Para vê-los na janela **Saída** , você precisa habilitá-los no aplicativo Web e informar ao Visual Studio que deseja monitorá-los.

1. Na guia **Configuração de Aplicativo Web do Azure** que você abriu no **Gerenciador de Servidores**, altere o Registro em Log de Servidor Web para **Ativado** e, em seguida, clique em **Salvar**.

    ![Habilitar o log de servidor web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. Na janela **Saída**, clique no botão **Especificar quais logs do Azure monitorar**.

    ![Especificar quais logs do Azure monitorar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. Na caixa de diálogo **Opções de Registro em Log do Azure**, selecione **Logs do servidor Web** e clique em **OK**.

    ![Monitorar logs de servidor Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. Na janela do navegador que mostra o aplicativo Web, clique em **Página Inicial**, clique em **Sobre** e, em seguida, clique em **Contato**.

    Os logs de aplicativo geralmente aparecem primeiro, seguidos dos logs de servidor web. Talvez seja necessário esperar algum tempo para que os logs sejam exibidos.

    ![Logs de servidor Web na janela Saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Por padrão, quando você habilita os logs do servidor Web pela primeira vez usando o Visual Studio, o Azure grava os logs no sistema de arquivos. Como alternativa, você pode usar o portal do Azure para especificar que os logs de servidor Web devem ser gravados em um contêiner de blob em uma conta de armazenamento.

Se você usar o portal para habilitar o registro em log do servidor Web para uma conta de armazenamento do Azure e desabilitar o registro em log no Visual Studio, quando reabilitar o registro em log no Visual Studio, as configurações da conta de armazenamento serão restauradas.

## <a name="detailederrorlogs"></a>Exibir logs de mensagens de erro detalhadas
Os logs detalhados de erro fornecem algumas informações adicionais sobre solicitações HTTP que resultam em códigos de resposta de erro (400 ou acima). Para vê-los na janela **Saída** , você precisa habilitá-los no aplicativo Web e informar ao Visual Studio que deseja monitorá-los.

1. Na guia **Configuração de Aplicativo Web do Azure** que você abriu no **Gerenciador de Servidores**, altere **Mensagens de Erro Detalhadas** para **Ativado** e clique em **Salvar**.

    ![Habilitar mensagens de erro detalhadas](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)
2. Na janela **Saída**, clique no botão **Especificar quais logs do Azure monitorar**.
3. Na caixa de diálogo **Opções de Registro em Log do Azure**, clique em **Todos os logs** e em **OK**.

    ![Monitorar todos os logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)
4. Na barra de endereços da janela do navegador, adicione um caractere extra à URL para provocar um erro 404 (por exemplo, `http://localhost:53370/Home/Contactx`) e pressione Enter.

    Após vários segundos o log detalhado de erros é exibido na janela **Saída** do Visual Studio.

    ![Log detalhado de erros na janela Saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Clique no link mantendo a tecla Control pressionada para ver a saída do log formatada em um navegador:

    ![Log detalhado de erros na janela do navegador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Baixar logs do sistema de arquivos
Todos os logs que podem ser monitorados na janela **Saída** também podem ser baixados como um arquivo *. zip* .

1. Na janela **Saída**, clique em **Baixar Logs de Streaming**.

    ![Botões da guia Logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    O Explorador de Arquivos abre em sua pasta *Downloads* com o arquivo baixado selecionado.

    ![Arquivo baixado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Extraia o arquivo *. zip* e você verá a seguinte estrutura de pastas:

    ![Arquivo baixado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Os logs de rastreamento de aplicativos estão em arquivos *.txt* na pasta *LogFiles\Application*.
   * Os logs de servidor Web estão em arquivos *.log* na pasta *LogFiles\http\RawLogs*. Você pode usar uma ferramenta como o [Analisador de log (a página pode estar em inglês)](http://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) para exibir e manipular esses arquivos.
   * Os logs de mensagens de erro detalhadas estão em arquivos *.html* na pasta *LogFiles\DetailedErrors*.

     (A pasta *deployments* é para os arquivos criados pela publicação de controle do código-fonte; ela não tem nada relacionado à publicação do Visual Studio. A pasta *Git* é para rastreamentos relacionados à publicação de código-fonte e o serviço de streaming de arquivos de log.)  

## <a name="storagelogs"></a>Exibir logs de armazenamento
Os logs de rastreamento de aplicativos também podem ser enviados para uma conta de armazenamento do Azure, e você pode exibi-los no Visual Studio. Para fazer isso, você criará uma conta de armazenamento, habilitará os logs de armazenamento no portal clássico e os exibirá na guia **Logs** da janela **Aplicativo Web do Azure**.

Você pode enviar logs para qualquer um ou todos os três destinos:

* O sistema de arquivos.
* Tabelas de conta de armazenamento.
* Blobs de conta de armazenamento.

Você pode especificar um nível de gravidade diferente para cada destino.

Tabelas facilitam a exibição de detalhes de logs online e dão suporte ao streaming; você pode consultar logs em tabelas e ver novos logs à medida que são criados. Blobs facilitam o download de logs em arquivos e a análise deles usando HDInsight, porque HDInsight sabe como trabalhar com o armazenamento de blob. Para obter mais informações, consulte **Hadoop e MapReduce** em [Opções de armazenamento de dados (Compilando aplicativos de nuvem do mundo real com o Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

Você tem logs de sistema de arquivos definidos como nível detalhado no momento; as etapas a seguir orientam você em meio à configuração dos logs de nível de informações para ir até tabelas de conta de armazenamento. O nível de informações significa que todos os logs criados chamando `Trace.TraceInformation`, `Trace.TraceWarning` e `Trace.TraceError` serão exibidos, mas não os logs criados chamando `Trace.WriteLine`.

As contas de armazenamento oferecem mais armazenamento e retenção por mais tempo para logs em comparação com o sistema de arquivos. Outra vantagem do envio dos logs de rastreamento de aplicativos para o armazenamento é que você obtém informações adicionais com cada log que não obtém dos logs do sistema de arquivos.

1. Clique com botão direito do mouse em **Armazenamento** sob o nó do Azure e, em seguida, clique em **Criar conta de armazenamento**.

![Criar conta de armazenamento](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. Na caixa de diálogo **Criar conta de armazenamento** , digite um nome para a conta de armazenamento.

    O nome deve ser exclusivo (nenhuma outra conta de armazenamento do Azure pode ter o mesmo nome). Se o nome digitado já estiver sendo utilizado, você terá a oportunidade de alterá-lo.

    A URL para acessar sua conta de armazenamento será *{nome}*.core.windows.net.
2. Defina a lista suspensa de **região ou grupo de afinidade** para a região mais próxima de você.

    Essa configuração especifica qual datacenter do Azure hospedará a conta de armazenamento. Para este tutorial, a escolha não fará grande diferença, mas para um aplicativo Web em produção convém que o servidor Web e a conta de armazenamento estejam na mesma região para minimizar os encargos de dados egressos e com latência. O aplicativo Web (que você criará posteriormente) deve ser executado em uma região tão próxima quanto possível dos navegadores acessando seu aplicativo Web para minimizar a latência.
3. Defina a lista suspensa **Replicação** para **Localmente redundante**.
   
    Quando a replicação geográfica está habilitada para uma conta de armazenamento, o conteúdo armazenado é replicado para um datacenter secundário para habilitar o failover para essa localidade no caso de ocorrer um grande desastre no local principal. A replicação geográfica pode incorrer em custos adicionais. Para contas de teste e desenvolvimento, geralmente, você não deseja pagar pela replicação geográfica. Para saber mais, confira [Criar, gerenciar ou excluir uma conta de armazenamento](../storage/common/storage-create-storage-account.md).
4. Clique em **Criar**.

    ![Nova conta de armazenamento](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. Na janela **Aplicativo Web do Azure** do Visual Studio, clique na guia **Logs** e depois em **Configurar registro em log no Portal de Gerenciamento**.

    <!-- todo:screenshot of new portal if the VS page link goes to new portal -->
    ![Configurar o registro em log](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    Isso abre a guia **Configurar** no portal clássico para seu aplicativo Web.
6. Na guia **Configurar** do portal clássico, role até a seção de diagnóstico de aplicativos e altere o **Registro em Log do Aplicativo (Armazenamento de Tabelas)** para **Ativado**.
7. Altere o **Nível de Log** para **Informações**.
8. Clique em **Gerenciar Armazenamento da Tabela**.

    ![Clicar em Gerenciar Armazenamento da Tabela](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    Na caixa de diálogo **Gerenciar armazenamento da tabela para diagnóstico do aplicativo** , você poderá escolher a conta de armazenamento, se tiver mais de uma. Você pode criar uma nova tabela ou usar uma existente.

    ![Gerenciar Armazenamento da Tabela](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. Na caixa **Gerenciar armazenamento da tabela para diagnóstico de aplicativo** , clique na marca de seleção para fechar a caixa.
10. No portal clássico, na guia **Configurar**, clique em **Salvar**.
11. Na janela do navegador que exibe o aplicativo Web do aplicativo, clique em **Página Inicial**, clique em **Sobre** e, em seguida, clique em **Contato**.

     As informações de log produzidas pela navegação nessas páginas da Web serão gravadas na conta de armazenamento.
12. Na guia **Logs** da janela **Aplicativo Web do Azure** no Visual Studio, clique em **Atualizar** em **Resumo do Diagnóstico**.

     ![Clicar em Atualizar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     Por padrão, a seção **Resumo do Diagnóstico** mostra logs dos últimos 15 minutos. Você pode alterar o período para ver mais logs.

     (Se você receber um erro "tabela não encontrada", verifique se navegou até as páginas que fazem o rastreamento depois de habilitar **Registro em Log do Aplicativo (Armazenamento)** e clicar em **Salvar**.)

     ![Logs de armazenamento](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Observe que nessa exibição você vê a **ID do Processo** e a **ID do thread** de cada log, o que você não obtém nos logs do sistema de arquivos. Você pode ver campos adicionais exibindo a tabela de armazenamento do Azure diretamente.
13. Clique em **exibir todos os logs de aplicativo**.

     A tabela do log de rastreamento é exibida no visualizador da tabela de armazenamento do Azure.

     (Se você receber um erro "a sequência não contém elementos", abra o **Gerenciador de Servidores**, expanda o nó da conta de armazenamento sob o nó **Azure**, clique com o botão direito do mouse em **Tabelas** e clique em **Atualizar**.)

     ![Logs de armazenamento na exibição de tabela](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     Esta exibição mostra campos adicionais que você não vê em outros modos de exibição. Esse modo de exibição permite filtrar logs usando a interface do usuário especial do Construtor de Consultas para construir uma consulta. Para obter mais informações, consulte Trabalhando com recursos de tabela - Filtrando entidades em [Procurando recursos de armazenamento com o Gerenciador de Servidores (a página pode estar em inglês)](http://msdn.microsoft.com/library/ff683677.aspx)
14. Para visualizar os detalhes de uma única linha, clique duas vezes em uma das linhas.

     ![Tabela de rastreamento no Gerenciador de Servidores](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

## <a name="failedrequestlogs"></a>Exibir logs de rastreamento de solicitações com falha
Os logs de rastreamento de solicitação com falha são úteis quando você precisa entender os detalhes de como o IIS trata uma solicitação HTTP, em cenários como problemas de autenticação ou regravação de URL.

Os aplicativos Web do Azure usam a mesma funcionalidade de rastreamento de solicitação com falha disponível no IIS 7.0 e em versões posteriores. No entanto, você não tem acesso às configurações do IIS que configuram quais erros são registrados em log. Quando você habilita o rastreamento de solicitação com falha, todos os erros são capturados.

Você pode habilitar o rastreamento de solicitação com falha usando o Visual Studio, mas não pode exibi-lo no Visual Studio. Esses logs são arquivos XML. O serviço de log de streaming monitora apenas arquivos que são considerados legíveis em modo de texto sem formatação: *.txt*, *.html* e *.log*.

Você pode exibir os logs de rastreamento de solicitação com falha em um navegador diretamente por meio de FTP ou localmente depois de usar uma ferramenta FTP para baixá-los em seu computador local. Nesta seção você irá exibi-los diretamente em um navegador.

1. Na guia **Configuração** da janela **Aplicativo Web do Azure** que você abriu no **Gerenciador de Servidores**, altere **Rastreamento de Solicitação com Falha** para **Ativado** e clique em **Salvar**.

    ![Habilitar o rastreamento de solicitações com falha](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Na barra de endereços da janela do navegador que mostra o aplicativo Web, adicione um caractere extra para a URL e pressione Enter para provocar um erro 404.

    Isso faz com que um log de rastreamento de solicitação com falha seja criado e as etapas seguintes mostram como exibir ou baixar o log.
3. No Visual Studio, na guia **Configuração** da janela **Aplicativo Web do Azure**, clique em **Abrir no Portal de Gerenciamento**.
4. Na folha **Configurações** do [Portal do Azure](https://portal.azure.com) do seu aplicativo Web, clique em **Credenciais de implantação** e insira um novo nome de usuário e uma senha.

    ![Novo nome de usuário e senha FTP](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    **Quando você fizer logon, será preciso usar o nome de usuário completo com o nome do aplicativo Web como prefixo dele. Por exemplo, se você inserir "myid" como um nome de usuário e o site é "meuexemplo", fará logon como "meuexemplo\myid".
5. Em uma nova janela do navegador, vá para a URL mostrada em **Nome do host FTP** ou **Nome do host FTPS** na folha **Aplicativo Web** do seu aplicativo Web.
6. Faça logon usando as credenciais FTP que você criou anteriormente (incluindo o prefixo do nome do aplicativo Web para o nome de usuário).

    O navegador mostra a pasta raiz do aplicativo Web.
7. Abra a pasta *LogFiles* .

    ![Abrir a pasta LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)
8. Abra a pasta chamada W3SVC mais um valor numérico.

    ![Abrir a pasta W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    A pasta contém arquivos XML para todos os erros que foram registrados em log depois que você habilitou o rastreamento de solicitação com falha e um arquivo XSL que um navegador pode usar para formatar o XML.

    ![Pasta W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)
9. Clique no arquivo XML da solicitação com falha da qual você deseja ver as informações de rastreamento.

    A ilustração a seguir mostra parte das informações de rastreamento de um erro de amostra.

    ![Rastreamento de solicitação com falha no navegador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="nextsteps"></a>Próximas etapas
Você viu como o Visual Studio facilita a exibição de logs criados por um aplicativo Web do Azure. As seguintes seções fornecem links para mais recursos sobre tópicos relacionados:

* Solução de problemas de aplicativo Web do Azure
* Depurando no Visual Studio
* Depuração remota no Azure
* Rastreando em aplicativos ASP.NET
* Analisando logs de servidor Web
* Analisando logs de rastreamento de solicitação com falha
* Depurando serviços de nuvem

### <a name="azure-web-app-troubleshooting"></a>Solução de problemas de aplicativo Web do Azure
Para saber mais sobre como solucionar problemas de aplicativos Web no Serviço de Aplicativo do Azure, confira os seguintes recursos:

* [Como monitorar aplicativos Web](/manage/services/web-sites/how-to-monitor-websites/)
* [Investigando perdas de memória em aplicativos Web do Azure com o Visual Studio 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Postagem no blog do Microsoft ALM sobre os recursos do Visual Studio para análise de problemas de memória gerenciados.
* [Ferramentas online de aplicativos Web do Azure sobre as quais você deve saber](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Postagem de blog de Amit Apple.

Para obter ajuda com uma pergunta específica de solução de problemas, inicie um thread em um dos seguintes fóruns:

* [O fórum do Azure no site do ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [O fórum do Azure no MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Depurando no Visual Studio
Para obter mais informações sobre como usar o modo de depuração no Visual Studio, consulte o tópico do MSDN [Depurando no Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) e [Dicas de depuração no Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Depuração remota no Azure
Para saber mais sobre a depuração remota para aplicativos Web do Azure e Trabalhos Web, confira os seguintes recursos:

* [Introdução à depuração remota em aplicativos Web de Serviço de Aplicativo do Azure](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Introdução à depuração remota em aplicativos Web de Serviço de Aplicativo do Azure parte 2 - Por dentro da depuração remota](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introdução à depuração remota em aplicativos Web de Serviço de Aplicativo do Azure parte 3 - Ambiente de várias instâncias e GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Depuração de Trabalhos Web (vídeo)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Se o aplicativo Web usar uma API Web do Azure ou um back-end de Serviços Móveis e você precisar depurá-lo, confira [Depurando back-end do .NET no Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Rastreando em aplicativos ASP.NET
Não existem introduções completas e atualizadas para rastreamento do ASP.NET disponíveis na Internet. O melhor a fazer é começar com materiais introdutórios antigos escritos para formulários da Web porque o MVC ainda não existia e complementar isso com postagens em blog mais recentes focalizadas em problemas específicas. Alguns bons locais para começar são os seguintes recursos:

* [Monitoramento e telemetria (Compilando aplicativos de nuvem do mundo real com o Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  Capítulo do livro eletrônico com recomendações para rastreamento em aplicativos de nuvem do Azure.
* [Rastreamento do ASP.NET](http://msdn.microsoft.com/library/ms972204.aspx)<br/>
  Antigo, mas ainda é um bom recurso para obter uma introdução básica sobre o assunto.
* [Ouvintes de rastreamento](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  Informações sobre ouvintes de rastreamento, mas não menciona o [WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Passo a passo: Integrando o rastreamento do ASP.NET com rastreamento do System.Diagnostics](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  Isso também é antigo, mas inclui algumas informações adicionais que o artigo introdutório não cobre.
* [Rastreamento em exibições do Razor do ASP.NET MVC](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Além do rastreamento em exibições do Razor, a postagem também explica como criar um filtro de erros para registrar em log todas as exceções não tratadas em um aplicativo MVC. Para obter informações sobre como registrar em log exceções não tratadas em um aplicativo de formulários da Web, consulte o exemplo do Global.asax em [Concluir exemplo para manipuladores de erro](http://msdn.microsoft.com/library/bb397417.aspx) no MSDN. No MVC ou em Formulários da Web, para registrar certas exceções em log, mas deixar o tratamento da estrutura padrão cuidar deles, você poderá capturar e relançar como no exemplo a seguir:

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        }
* [Registro em log de rastreamento do diagnóstico de streaming na linha de comando do Azure (mais Glimpse!)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Como usar a linha de comando para fazer o que este tutorial mostra no Visual Studio. [Glimpse (a página pode estar em inglês)](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) é uma ferramenta para depuração de aplicativos ASP.NET.
* [Usando diagnóstico e registro em log de Aplicativos Web – com David Ebbo](/documentation/videos/azure-web-site-logging-and-diagnostics/) e [Logs de streaming dos Aplicativos Web – com David Ebbo](/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Vídeos de Scott Hanselman e David Ebbo.

Para o registro em log de erros, uma alternativa para escrever seu próprio código de rastreamento é usar uma estrutura de registros em log de software livre, como o [ELMAH](http://nuget.org/packages/elmah/). Para obter mais informações, consulte as [postagens no blog de Scott Hanselman sobre o ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Além disso, observe que você não precisa usar o rastreamento do ASP.NET ou do System.Diagnostics se quiser obter logs de streaming do Azure. O serviço de log de streaming de aplicativo Web do Azure transmitirá qualquer arquivo *.txt*, *.html* ou *.log* que encontrar na pasta *LogFiles*. Portanto, você pode criar seu próprio sistema de log que grava no sistema de arquivos do aplicativo Web, e o arquivo será automaticamente transmitido e baixado. Tudo o que você precisa fazer é escrever o código do aplicativo que cria arquivos na pasta *d:\home\logfiles*.

### <a name="analyzing-web-server-logs"></a>Analisando logs de servidor Web
Para obter mais informações sobre como analisar logs de servidor web, consulte os seguintes recursos:

* [LogParser](http://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Uma ferramenta para exibir dados em logs de servidor Web (arquivos*.log* ).
* [Solução de problemas de desempenho do IIS ou erros de aplicativo usando o LogParser](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Uma introdução à ferramenta LogParser que pode ser usada para analisar logs de servidor web.
* [Postagens no blog por Robert McMurray sobre como usar o LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [O código de status HTTP no IIS 7.0, IIS 7.5 e IIS 8.0](http://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analisando logs de rastreamento de solicitação com falha
O site do Microsoft TechNet inclui uma seção [Usando rastreamento de solicitação com falha](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) que pode ser útil para entender como usar esses logs. No entanto, essa documentação se concentra principalmente na configuração do rastreamento de solicitação com falha no IIS, o que você não pode fazer em Aplicativos Web do Azure.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
