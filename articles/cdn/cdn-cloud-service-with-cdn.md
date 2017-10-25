---
title: "Integrar um serviço de nuvem do Azure com a CDN do Azure | Microsoft Docs"
description: "Aprenda como implantar um serviço de nuvem que serve o conteúdo de um ponto de extremidade CDN do Azure integrado"
services: cdn, cloud-services
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: tysonn
ms.assetid: b3c0108f-9ec5-43a8-8fd0-40eafbd32637
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f2849fe25fd0d5b3dc26598ffba7591cb7433161
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="intro"></a> Integrar um serviço de nuvem à CDN do Azure
Um serviço de nuvem pode ser integrado com o CDN do Azure, fornecendo qualquer conteúdo do local do serviço de nuvem. Esta abordagem lhe dá as seguintes vantagens:

* Implantar e atualizar, de maneira fácil, imagens, scripts e folhas de estilo nos diretórios de projetos de seu serviço de nuvem
* Atualizar de maneira fácil os pacotes NuGet em seu serviço de nuvem, como as versões jQuery ou Bootstrap
* Gerenciar seu aplicativo Web e o conteúdo fornecido por CDN por meio da mesma interface do Visual Studio
* Fluxo de trabalho de implantação unificado para seu aplicativo Web e o conteúdo fornecido por CDN
* Integrar agrupamento e minificação ASP.NET à CDN do Azure

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste tutorial, você aprenderá a:

* [Integrar um ponto de extremidade da CDN do Azure a seu serviço de nuvem e fornecer conteúdo estático em suas páginas Web por meio da CDN do Azure](#deploy)
* [Definir configurações de cache para conteúdo estático em seu serviço de nuvem](#caching)
* [Fornecer conteúdo por ações do controlador na CDN do Azure](#controller)
* [Fornecer conteúdo agrupado e minificado por meio da CDN do Azure enquanto preserva a experiência de depuração de script no Visual Studio](#bundling)
* [Configurar o fallback de seus scripts e CSS quando a CDN do Azure estiver offline](#fallback)

## <a name="what-you-will-build"></a>O que você compilará
Você implantará uma função Web de serviço de nuvem usando o modelo ASP.NET MVC padrão, adicionará código para fornecer conteúdo de uma CDN do Azure integrada, como uma imagem, resultados de ação do controlador e os arquivos JavaScript e CSS padrão, e também escreverá código para configurar o mecanismo de fallback para grupos fornecidos caso o CDN esteja offline.

## <a name="what-you-will-need"></a>O que será necessário
Este tutorial tem os seguintes pré-requisitos:

* Uma [conta do Microsoft Azure](/account/)
* Visual Studio 2015 com [SDK do Azure](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)

> [!NOTE]
> Você de uma conta do Azure para concluir este tutorial:
> 
> * Você pode [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/) - Você obtém créditos que podem ser usados para experimentar serviços pagos do Azure e, mesmo após eles serem utilizados, você pode manter a conta e utilizar os serviços gratuitos do Azure, como os Sites.
> * Você pode [ativar benefícios para assinantes do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) - Todos os meses, sua assinatura do MSDN oferece créditos que podem ser usados para serviços pagos do Azure.
> 
> 

<a name="deploy"></a>

## <a name="deploy-a-cloud-service"></a>Implantar um serviço de nuvem
Nesta seção, você vai implantar o modelo de aplicativo MVC ASP.NET padrão no Visual Studio 2015 para uma função de Web do serviço de nuvem e, em seguida, integrá-la a um novo ponto de extremidade CDN. Siga as instruções abaixo:

1. No Visual Studio 2015, crie um novo serviço de nuvem do Azure na barra de menu acessando **Arquivo > Novo > Projeto > Nuvem > Serviço de Nuvem do Azure**. Dê um nome a ele e clique em **OK**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)
2. Selecione **Função Web ASP.NET** e clique no botão **>**. Clique em OK.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)
3. Selecione **MVC** e clique em **OK**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)
4. Agora, publique esta função Web em um serviço de nuvem do Azure. Clique com o botão direito do mouse no projeto do serviço de nuvem e selecione **Publicar**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)
5. Se você ainda não entrou no Microsoft Azure, clique em **Adicionar uma conta...** na lista suspensa e clique no item de menu **Adicionar uma conta**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)
6. Na página de entrada, entre com a conta da Microsoft que você utilizou para ativar a conta do Azure.
7. Após ter entrado, clique em **Avançar**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)
8. Se você ainda não tiver criado uma conta de armazenamento ou um serviço de nuvem, o Visual Studio o ajudará a criar ambos. Na caixa de diálogo **Criar Serviço de Nuvem e Conta** , digite o nome do serviço desejado e selecione a região desejada. Em seguida, clique em **Criar**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)
9. Na página de configurações de publicação, verifique a configuração e clique em **Publicar**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)
   
   > [!NOTE]
   > O processo de publicação para serviços de nuvem leva muito tempo. A opção Habilitar Implantação da Web para todas as funções pode acelerar muito a depuração do serviço de nuvem fornecendo atualizações rápidas (mas temporárias) para suas funções Web. Para saber mais sobre esta opção, consulte [Publicando um Serviço de Nuvem usando as Ferramentas do Azure](http://msdn.microsoft.com/library/ff683672.aspx).
   > 
   > 
   
    Quando o **Log de atividades do Microsoft Azure** mostrar que o status da publicação é **Concluído**, crie um ponto de extremidade CDN que esteja integrado a esse serviço de nuvem.
   
   > [!WARNING]
   > Se após a publicação o serviço de nuvem implantado exibir uma tela de erro, provavelmente é porque o serviço de nuvem que você implantou está usando um [SO convidado que não inclui o .NET 4.5.2](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates).  Você pode contornar esse problema [Implantando o .NET 4.5.2 como uma tarefa de inicialização](../cloud-services/cloud-services-dotnet-install-dotnet.md).
   > 
   > 

## <a name="create-a-new-cdn-profile"></a>Criar um novo perfil CDN
Um perfil CDN é um conjunto de pontos de extremidade CDN.  Cada perfil contém um ou mais pontos de extremidade CDN.  Você pode usar vários perfis para organizar seus pontos de extremidade CDN por domínio de Internet, aplicativo Web ou algum outro critério.

> [!TIP]
> Se já tiver um perfil CDN que deseja usar para este tutorial, vá para [Criar um novo ponto de extremidade CDN](#create-a-new-cdn-endpoint).
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Criar um novo ponto de extremidade CDN
**Para criar um novo ponto de extremidade CDN para sua conta de armazenamento**

1. No [Portal de Gerenciamento do Azure](https://portal.azure.com), navegue até o seu perfil CDN.  Você pode ter fixado ao painel na etapa anterior.  Se não, você poderá encontrá-lo clicando em **Procurar**, em **Perfis CDN** e clicando no perfil ao qual você pretende adicionar o ponto de extremidade.
   
    A folha do perfil CDN é exibida.
   
    ![Perfil CDN][cdn-profile-settings]
2. Clique no botão **Adicionar Ponto de Extremidade** .
   
    ![Adicionar botão de ponto de extremidade][cdn-new-endpoint-button]
   
    A folha **Adicionar um ponto de extremidade** é exibida.
   
    ![Adicionar folha de ponto de extremidade][cdn-add-endpoint]
3. Insira um **Nome** para esse ponto de extremidade CDN.  Esse nome será usado para acessar os recursos armazenados em cache no domínio `<EndpointName>.azureedge.net`.
4. No menu suspenso **Tipo de origem** , selecione *Serviço de nuvem*.  
5. No menu suspenso **Nome do host de origem** , selecione Serviço de nuvem.
6. Mantenha os padrões para **Caminho de origem**, **Cabeçalho de host de origem** e **Porta de protocolo/origem**.  Você deve especificar pelo menos um protocolo (HTTP ou HTTPS).
7. Clique no botão **Adicionar** para criar um novo ponto de extremidade.
8. Depois que o ponto de extremidade é criado, ele aparece em uma lista de pontos de extremidade do perfil. O modo de exibição de lista mostra a URL a ser usada para acessar o conteúdo armazenado em cache, bem como o domínio de origem.
   
    ![Ponto de extremidade CDN][cdn-endpoint-success]
   
   > [!NOTE]
   > O ponto de extremidade não estará imediatamente disponível para uso.  Pode levar até 90 minutos para que o registro seja propagado pela rede CDN. Os usuários que tentarem usar imediatamente o nome de domínio CDN poderão receber o código de status 404 até que o conteúdo esteja disponível pela CDN.
   > 
   > 

## <a name="test-the-cdn-endpoint"></a>Testar o ponto de extremidade CDN
Quando o status da publicação for **Concluído**, abra uma janela do navegador e vá para **http://<cdnName>*.azureedge.net/Content/bootstrap.css**. Em minha configuração, essa URL é:

    http://camservice.azureedge.net/Content/bootstrap.css

Que corresponde à seguinte URL de origem no ponto de extremidade da CDN:

    http://camcdnservice.cloudapp.net/Content/bootstrap.css

Quando você navega para **http://*&lt;cdnName>*.azureedge.net/Content/bootstrap.css**, dependendo do navegador, receberá uma solicitação para baixar ou abrir o bootstrap.css proveniente de seu aplicativo Web publicado.

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

De maneira semelhante, você pode acessar qualquer URL acessível publicamente em **http://*&lt;nomedoServiço>*.cloudapp.net/**, diretamente de seu ponto de extremidade CDN. Por exemplo:

* Um arquivo .js do caminho /Script
* Qualquer arquivo de conteúdo do caminho /Content
* Qualquer controller/action
* Se cadeias de consulta estiverem habilitadas em seu ponto de extremidade da CDN, qualquer URL com cadeias de consulta

Na verdade, com a configuração acima, você pode hospedar todo o serviço de nuvem por meio de **http://*&lt;nome_da_CDN>*.azureedge.net/**. Se navegar até **http://camservice.azureedge.net/**, eu obtenho o resultado da ação de Home/Index.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

No entanto, isso não significa que sempre é uma boa ideia fornecer um serviço de nuvem inteiro por meio da CDN do Azure. 

Uma CDN com otimização de entrega estática não acelera, necessariamente, a entrega de ativos dinâmicos que não devem ser armazenados em cache ou são atualizados com muita frequência, pois a CDN deve efetuar pull de uma nova versão do ativo do servidor de origem com bastante frequência. Para este cenário, você pode habilitar a otimização [DSA](cdn-dynamic-site-acceleration.md) (aceleração de site dinâmico), em seu ponto de extremidade da CDN, que usa várias técnicas para acelerar a entrega de ativos dinâmicos não armazenáveis em cache. 

Se você tem um site com uma mistura de conteúdo estático e dinâmico, você pode escolher distribuir o conteúdo estático da CDN com um tipo de otimização estática (por exemplo, a entrega da Web geral) e distribuir o conteúdo dinâmico diretamente do servidor de origem ou por meio de um ponto de extremidade de CDN com otimização DSA ativada de acordo com cada caso. Para isso, você já viu como acessar arquivos de conteúdo individuais do ponto de extremidade CDN. Você verá como fornecer uma ação de controlador específica por meio de um ponto de extremidade de CDN específico em Fornecer conteúdo por meio de ações do controlador na CDN do Azure.

A alternativa é determinar que conteúdo fornecer por meio da CDN do Azure caso a caso, em seu serviço de nuvem. Para isso, você já viu como acessar arquivos de conteúdo individuais do ponto de extremidade CDN. Você verá como fornecer uma ação de controlador específica por meio do ponto de extremidade CDN em [Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure](#controller).

<a name="caching"></a>

## <a name="configure-caching-options-for-static-files-in-your-cloud-service"></a>Configurar opções de cache para arquivos estáticos em seu serviço de nuvem
Com a integração da CDN do Azure a seu serviço de nuvem, você pode especificar como quer que o conteúdo estático seja armazenado em cache no ponto de extremidade da CDN. Para fazer isso, abra *Web.config* em seu projeto de função Web (por exemplo, FunçãodaWeb1) e adicione um elemento `<staticContent>` a `<system.webServer>`. O XML configura o cache para expirar em 3 dias.  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

Após você ter feito isso, todos os arquivos estáticos do serviço de nuvem obedecerão à mesma regra do seu cache de CDN. Para ter um controle mais granular das configurações de cache, adicione um arquivo *Web.config* a uma pasta e adicione suas configurações. Por exemplo, adicione um arquivo *Web.config* à pasta *\Content* e substitua o conteúdo pelo seguinte XML:

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <staticContent>
          <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
        </staticContent>
      </system.webServer>
    </configuration>

Esta configuração faz com que todos os arquivos estáticos da pasta *\Content* sejam armazenados em cache por 15 dias.

Para saber mais sobre como configurar o elemento `<clientCache>`, consulte [Cache do cliente&lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

Em [Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure](#controller), eu também mostrarei como você pode definir as configurações de cache para os resultados de ações do controlados no cache de CDN.

<a name="controller"></a>

## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure
Quando você integra uma função Web de um serviço de nuvem à CDN do Azure, é relativamente fácil fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure. Além de fornecer o serviço de nuvem diretamente da CDN do Azure (demonstrado acima), [Maarten Balliauw](https://twitter.com/maartenballiauw) mostra como fazer isso com um divertido controlador MemeGenerator em [Reduzindo a latência na Web com a CDN do Azure](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). Eu vou somente reproduzir isso aqui.

Suponha que, em seu serviço de nuvem, você queira gerar memes com base em uma imagem de Chuck Norris jovem (foto de [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) como esta:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Você tem uma ação `Index` simples que permite que os clientes especifiquem os superlativos na imagem e gera o meme quando eles postam a ação. Como se trata de Chuck Norris, você esperaria que esta página se tornasse extremamente popular globalmente. Este é um bom exemplo do fornecimento de conteúdo semidinâmico com a CDN do Azure.

Siga as etapas acima para configurar esta ação do controlador:

1. Na pasta *\Controllers*, crie um novo arquivo .cs chamado *MemeGeneratorController.cs* e substitua o conteúdo pelo código a seguir. Certifique-se de substituir a porção destacada pelo nome de sua CDN.  
   
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;
   
        namespace WebRole1.Controllers
        {
            public class MemeGeneratorController : Controller
            {
                static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();
   
                public ActionResult Index()
                {
                    return View();
                }
   
                [HttpPost, ActionName("Index")]
                public ActionResult Index_Post(string top, string bottom)
                {
                    var identifier = Guid.NewGuid().ToString();
                    if (!Memes.ContainsKey(identifier))
                    {
                        Memes.Add(identifier, new Tuple<string, string>(top, bottom));
                    }
   
                    return Content("<a href=\"" + Url.Action("Show", new {id = identifier}) + "\">here's your meme</a>");
                }
   
                [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
                public ActionResult Show(string id)
                {
                    Tuple<string, string> data = null;
                    if (!Memes.TryGetValue(id, out data))
                    {
                        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
                    }
   
                    if (Debugger.IsAttached) // Preserve the debug experience
                    {
                        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                    else // Get content from Azure CDN
                    {
                        return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                }
   
                [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
                public ActionResult Generate(string top, string bottom)
                {
                    string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
                    Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);
   
                    using (Graphics graphics = Graphics.FromImage(bitmap))
                    {
                        SizeF size = new SizeF();
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                        }
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                        }
                    }
   
                    MemoryStream ms = new MemoryStream();
                    bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
                    return File(ms.ToArray(), "image/png");
                }
   
                private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
                {
                    // Compute actual size, shrink if needed
                    while (true)
                    {
                        size = g.MeasureString(text, font);
   
                        // It fits, back out
                        if (size.Height < i.Height &&
                             size.Width < i.Width) { return font; }
   
                        // Try a smaller font (90% of old size)
                        Font oldFont = font;
                        font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                        oldFont.Dispose();
                    }
                }
            }
        }
2. Clique com o botão direito na ação `Index()` padrão e selecione **Adicionar Exibição**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)
3. Aceite as configurações acima e clique em **Adicionar**.
   
   ![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)
4. Abra o novo *Views\MemeGenerator\Index.cshtml* e substitua o conteúdo pelo HTML simples a seguir para enviar os superlativos:
   
        <h2>Meme Generator</h2>
   
        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>
5. Publique o serviço de nuvem novamente e navegue até **http://*&lt;NomedoServiço>*.cloudapp.net/MemeGenerator/Index** em seu navegador.

Quando você envia os valores de formulário a `/MemeGenerator/Index`, o método de ação `Index_Post` retorna um link para o método de ação `Show` com o respectivo identificador de entrada. Quando clicar em senha, você chegará ao seguinte código:  

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
        Tuple<string, string> data = null;
        if (!Memes.TryGetValue(id, out data))
        {
            return new HttpStatusCodeResult(HttpStatusCode.NotFound);
        }

        if (Debugger.IsAttached) // Preserve the debug experience
        {
            return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
        else // Get content from Azure CDN
        {
            return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
    }

Se o seu depurador local estiver anexado, você terá a experiência de depuração normal com um redirecionamento local. Se estiver sendo executado no serviço de nuvem, será redirecionado para:

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Que corresponde ao seguinte URL de origem no ponto de extremidade da CDN:

    http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


Você então pode usar o atributo `OutputCacheAttribute` no método `Generate` para especificar como o resultado da ação deve ser armazenado em cache, e a CDN do Azure seguirá a especificação. O código abaixo especifica uma validade de cache de 1 hora (3.600 segundos).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Da mesma forma, você pode fornecer conteúdo de qualquer ação de controlador no serviço de nuvem por meio da CDN do Azure, com a opção de cache desejada.

Na próxima seção, eu mostrarei como fornecer o CSS e scripts agrupados e minificados por meio da CDN do Azure.

<a name="bundling"></a>

## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>Integrar agrupamento e minificação ASP.NET à CDN do Azure
Scripts e folhas de estilo CSS são alterados com pouca frequência e são candidatos ideais para o cache da CDN do Azure. Fornecer toda a função Web por meio da CDN do Azure é a maneira mais fácil de integrar agrupamento e minificação à CDN do Azure. No entanto, como talvez você não queira fazer isso, eu vou mostrar como fazê-lo enquanto preserva a experiência do desenvolvedor desejada de agrupamento e minificação ASP.NET, como:

* Ótima experiência no modo de depuração
* Implantação simplificada
* Atualizações imediatas para clientes para atualizações de versão de script/CSS
* Mecanismo de fallback quando ocorre falha em seu ponto de extremidade CDN
* Minimizar modificações de código

No projeto **FunçãoDaWeb1** criado em [Integrar um ponto de extremidade da CDN do Azure ao seu site do Azure e fornecer conteúdo estático em suas páginas Web na CDN do Azure](#deploy), abra *App_Start\BundleConfig.cs* e examine as chamadas de método `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

A primeira instrução `bundles.Add()` adiciona um grupo de script ao diretório virtual `~/bundles/jquery`. Depois, abra *Views\Shared\_Layout.cshtml* para ver como a marcação do grupo de script é renderizada. Você deve encontrar a seguinte linha de código Razor:

    @Scripts.Render("~/bundles/jquery")

Quando for executado na função Web do Azure, este código Razor renderizará uma marca `<script>` para o grupo de script semelhante à seguinte:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

No entanto, quando for executado no Visual Studio digitando `F5`, ele renderizará cada arquivo de script do grupo individualmente (no caso acima, somente o arquivo de script está no grupo):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Isso permite que você depure o código JavaScript em seu ambiente de desenvolvimento enquanto reduz conexões clientes simultâneas (agrupamento) e melhora o desempenho do download de arquivos (minificação) em produção. É um ótimo recurso a ser preservado com a integração à CDN do Azure. Além disso, como o grupo renderizado já contém uma cadeia da versão gerada automaticamente, é aconselhável replicar essa funcionalidade para que sempre que você atualizar sua versão do jQuery por meio do NuGet, ela possa ser atualizada no lado do cliente assim que possível.

Siga as etapas abaixo para integrar agrupamento e minificação ASP.NET ao ponto de extremidade CDN.

1. Em *App_Start\BundleConfig.cs*, modifique os métodos `bundles.Add()` para usar um [Construtor de pacotes](http://msdn.microsoft.com/library/jj646464.aspx) diferente, um que especifique um endereço CDN. Para fazer isso, substitua a `RegisterBundles` definição do método pelo seguinte código:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            bundles.UseCdn = true;
            var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
                .GetName().Version.ToString();
            var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                        "~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                        "~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you're
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
                        "~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    Substitua `<yourCDNName>` pelo nome da sua CDN do Azure.
   
    De maneira simples, você está definindo `bundles.UseCdn = true` e adicionou uma URL da CDN criada cuidadosamente a cada grupo. Por exemplo, o primeiro construtor do código:
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
   
    é igual a:
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))
   
    Este construtor instrui o agrupamento e minificação ASP.NET a renderizar arquivos de script individuais quando depurados localmente, mas usar o endereço CDN especificado para acessar o script em questão. No entanto, observe duas importantes características dessa URL da CDN criada cuidadosamente:
   
   * A origem do URL da CDN é `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, que é na realidade o diretório virtual do grupo de scripts em seu serviço de nuvem.
   * Como você está usando um construtor CDN, a marcação do script CDN para o grupo não contém mais a cadeia da versão gerada automaticamente na URL renderizada. Você deve gerar manualmente uma cadeia de versão única sempre que o grupo de scripts for modificado para gerar uma perda de cache em sua CDN do Azure. Ao mesmo tempo, essa cadeia de versão única deve permanecer constante ao longo da vida útil da implantação para maximizar as ocorrências no cache na CDN do Azure após a implantação do grupo.
   * A cadeia de consulta v=<W.X.Y.Z> efetua pull em *Properties\AssemblyInfo.cs* no projeto da função Web. Você pode ter um fluxo de trabalho de implantação que inclua o incremento da versão de assembly sempre que você publicar no Azure. Ou pode apenar modificar *Properties\AssemblyInfo.cs* em seu projeto para incrementar automaticamente a cadeia da versão sempre que compilar, usando o caractere curinga “*”. Por exemplo:
     
        [assembly: AssemblyVersion("1.0.0.*")]
     
     Qualquer outra estratégia para simplificar a geração de uma cadeia única para a vida útil de uma implantação funcionará aqui.
2. Publique o serviço de nuvem novamente e acesse a página inicial.
3. Exiba o código HTML da página. Você deverá ver a URL da CDN renderizada, com uma cadeia de versão única sempre que republicar mudanças em seu serviço de nuvem. Por exemplo:  
   
        ...
   
        <link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>
   
        <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>
   
        ...
   
        <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>
   
        <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>
   
        ...
4. No Visual Studio, depure o serviço de nuvem no Visual Studio digitando `F5`.,
5. Exiba o código HTML da página. Você ainda verá cada arquivo de script renderizado individualmente, para que possa ter uma experiência de depuração consistente no Visual Studio.  
   
        ...
   
            <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>
   
            <script src="/Scripts/modernizr-2.6.2.js"></script>
   
        ...
   
            <script src="/Scripts/jquery-1.10.2.js"></script>
   
            <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>
   
        ...   

<a name="fallback"></a>

## <a name="fallback-mechanism-for-cdn-urls"></a>Mecanismo de fallback para URLs da CDN
Quando seu ponto de extremidade da CDN do Azure falhar por qualquer motivo, é melhor que sua página da Web seja inteligente o suficiente para acessar o servidor Web de origem como a opção de fallback para carregar JavaScript ou Bootstrap. Perder imagens do site devido à indisponibilidade da CDN já é grave, mas perder funcionalidades fundamentais da página fornecidas por seus scripts e folhas de estilo é ainda muito mais.

A classe [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) contém uma propriedade chamada [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) que permite que você configure o mecanismo de fallback para falha da CDN. Para usar essa propriedade, siga as etapas abaixo:

1. No projeto de função Web, abra *App_Start\BundleConfig.cs*, onde você adicionou uma URL da CDN a cada [Construtor de grupo](http://msdn.microsoft.com/library/jj646464.aspx) e faça as seguintes alterações em destaque para adicionar um mecanismo de fallback aos grupos padrão:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
                .GetName().Version.ToString();
            var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
            bundles.UseCdn = true;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
                        { CdnFallbackExpression = "window.jquery" }
                        .Include("~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval"))
                        { CdnFallbackExpression = "$.validator" }
                        .Include("~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer"))
                        { CdnFallbackExpression = "window.Modernizr" }
                        .Include("~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                        { CdnFallbackExpression = "$.fn.modal" }
                        .Include(
                                  "~/Scripts/bootstrap.js",
                                  "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    Quando `CdnFallbackExpression` não é nulo, o script é injetado no HTML para testar se o grupo é carregado com sucesso e, se não, acessar o grupo diretamente por meio do servidor Web de origem. Esta propriedade precisa ser definida como uma expressão JavaScript que testa se o grupo CDN respectivo é carregado corretamente. A expressão necessária para testar cada grupo difere de acordo com o conteúdo. Para os grupos padrão acima:
   
   * `window.jquery` é definido em jquery-{version}.js
   * `$.validator` é definido em jquery.validate.js
   * `window.Modernizr` é definido em modernizer-{version}.js
   * `$.fn.modal` é definido em bootstrap.js
     
     Você pode ter notado que não defini CdnFallbackExpression para o grupo `~/Cointent/css` . Isso ocorre porque no momento há um [bug no System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) que injeta uma marcação `<script>` para o CSS de fallback em vez da marcação `<link>` esperada.
     
     Há, no entanto, um bom [Fallback de Grupo de Estilo](https://github.com/EmberConsultingGroup/StyleBundleFallback) oferecido pelo [Ember Consulting Group](https://github.com/EmberConsultingGroup).
2. Para usar a solução alternativa para CSS, crie um novo arquivo .cs na pasta *App_Start* de seu projeto de função Web, nomeie esse arquivo como *StyleBundleExtensions.cs* e substitua seu conteúdo pelo [código do GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).
3. Em *App_Start\StyleFundleExtensions.cs*, renomeie o namespace como nome da sua função Web (por exemplo, **FunçãoDaWeb1**).
4. Volte para e `App_Start\BundleConfig.cs` modifique a última `bundles.Add` instrução com o seguinte código em destaque:  
   
        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
            <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
            .Include(
                  "~/Content/bootstrap.css",
                  "~/Content/site.css"));
   
    Este novo método de extensão usa a mesma ideia para injetar script no HTML para verificar o DOM para um nome de classe, nome de regra e valor de regra correspondente no grupo CSS, e retorna ao servidor Web original se não encontrar um resultado correspondente.
5. Publique o serviço de nuvem novamente e acesse a página inicial.
6. Exiba o código HTML da página. Você deve encontrar scripts injetados semelhantes ao seguinte:    
   
        ...
   
        <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
        <script>(function() {
                        var loadFallback,
                            len = document.styleSheets.length;
                        for (var i = 0; i < len; i++) {
                            var sheet = document.styleSheets[i];
                            if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
                                var meta = document.createElement('meta');
                                meta.className = 'sr-only';
                                document.head.appendChild(meta);
                                var value = window.getComputedStyle(meta).getPropertyValue('width');
                                document.head.removeChild(meta);
                                if (value !== '1px') {
                                    document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
                                }
                            }
                        }
                        return true;
                    }())||document.write('<script src="/Content/css"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
        <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
   
        ...
   
            <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
        <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
        <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
   
        ...

    Observe que o script injetado para o grupo CSS ainda contém o excedente errante da propriedade `CdnFallbackExpression` na linha:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

    Mas como a primeira parte da expressão || sempre retornará o valor verdadeiro (na linha diretamente acima), a função document.write() nunca será executada.

## <a name="more-information"></a>Mais informações
* [Visão geral da Rede de Distribuição de Conteúdo (CDN) do Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
* [Usando o Azure CDN](cdn-create-new-endpoint.md)
* [Agrupamento e minificação ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)

[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png
