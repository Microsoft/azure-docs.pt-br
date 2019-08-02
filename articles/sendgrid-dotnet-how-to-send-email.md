---
title: Como usar o serviço de email SendGrid (.NET) | Microsoft Docs
description: Saiba como enviar email com o serviço de email SendGrid no Azure. Exemplos de código escritos em c# e usam a API .NET.
services: ''
documentationcenter: .net
author: thinkingserious
manager: erikre
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.author: erikre
ms.reviewer: dx@sendgrid.com
ms.openlocfilehash: c3211ba9f8a8b16ad4372c82d8e50c46f3ad6897
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876370"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Como enviar emails usando o SendGrid com o Azure
## <a name="overview"></a>Visão geral
Este guia demonstra como executar tarefas comuns de programação com o serviço de email SendGrid no Windows Azure. Os exemplos são escritos em C\# e dão suporte a .NET Standard 1.3. Os cenários abordados incluem a criação e o envio de emails, a adição de anexos e a habilitação de várias configurações de email e acompanhamento. Para obter mais informações sobre o SendGrid e o envio de emails, consulte a seção [Próximas etapas][Next steps] .

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de email SendGrid?
SendGrid é um [serviço de email baseado em nuvem] que fornece uma [entrega de email transacional], escalabilidade e análise em tempo real confiáveis com APIs flexíveis que facilitam a integração personalizada. Os casos de uso comuns do SendGrid incluem:

* Envio automático de recibos ou confirmações de compra para os clientes.
* Administração de listas de distribuição para envio mensal de panfletos eletrônicos e ofertas.
* Coleta de métricas em tempo real para, por exemplo, email bloqueado e engajamento do cliente.
* Encaminhamento de consultas dos clientes.
* Processamento de emails de entrada.

Para obter mais informações, [https://sendgrid.com](https://sendgrid.com) visite o repositório GitHub da [ C# biblioteca][sendgrid-csharp] do SendGrid ou do.

## <a name="create-a-sendgrid-account"></a>Criar uma conta do SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Referência à biblioteca de classes do .NET do SendGrid
O [pacote NuGet do SendGrid](https://www.nuget.org/packages/Sendgrid) é a maneira mais fácil de obter a API do SendGrid e para configurar seu aplicativo com todas as dependências. O NuGet é uma extensão do Visual Studio incluída no Microsoft Visual Studio 2015 e nas versões posteriores que facilita a instalação e a atualização de bibliotecas e ferramentas.

> [!NOTE]
> Para instalar o NuGet se você estiver executando uma versão do Visual Studio anterior ao Visual Studio 2015, visite [https://www.nuget.org](https://www.nuget.org) e clique no botão **Instalar NuGet**.
>
>

Para instalar o pacote NuGet do SendGrid no seu aplicativo, faça o seguinte:

1. Clique em **Novo projeto** e selecione um **Modelo**.

   ![Criar um novo projeto][create-new-project]
2. No **Gerenciador de Soluções**, clique com botão direito em **Referências**, em seguida, clique em **Gerenciar Pacotes NuGet**.

   ![pacote NuGet do SendGrid][SendGrid-NuGet-package]
3. Procure **SendGrid** e selecione o item **SendGrid** na lista de resultados.
4. Selecione a versão estável mais recente do pacote NuGet no menu suspenso da versão para poder trabalhar com o modelo de objeto e as APIs demonstradas neste artigo.

   ![Pacote SendGrid][sendgrid-package]
5. Clique em **Instalar** para concluir a instalação e, em seguida, feche essa caixa de diálogo.

A biblioteca de classes do .NET do SendGrid se chama **SendGrid**. Ela contém os seguintes namespaces:

* **SendGrid** para comunicação com a API do SendGrid.
* **SendGrid.Helpers.Mail** para que os métodos auxiliares criem facilmente objetos SendGridMessage, que especificam como enviar emails.

Adicione as declarações de namespace de código a seguir à parte superior de qualquer arquivo C# em que queira acessar o serviço de email SendGrid de forma programática.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Como: Criar um email
Use o objeto **SendGridMessage** para criar uma mensagem de email. Quando o objeto de mensagem for criado, você poderá definir as propriedades e os métodos, incluindo o remetente do email, o destinatário do email e o assunto e o corpo do email.

O exemplo a seguir demonstra como criar um objeto de email totalmente preenchido:

    var msg = new SendGridMessage();

    msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

    var recipients = new List<EmailAddress>
    {
        new EmailAddress("jeff@example.com", "Jeff Smith"),
        new EmailAddress("anna@example.com", "Anna Lidman"),
        new EmailAddress("peter@example.com", "Peter Saddow")
    };
    msg.AddTos(recipients);

    msg.SetSubject("Testing the SendGrid C# Library");

    msg.AddContent(MimeType.Text, "Hello World plain text!");
    msg.AddContent(MimeType.Html, "<p>Hello World!</p>");

Para obter mais informações sobre todas as propriedades e métodos com suporte do tipo **SendGrid** , consulte [sendgrid-csharp][sendgrid-csharp] no GitHub.

## <a name="how-to-send-an-email"></a>Como: Envie um email
Depois de criar uma mensagem de email, você poderá enviá-la usando a API do SendGrid. Como alternativa, você pode usar [. Biblioteca interna da rede][NET-library].

O envio de email requer que você forneça sua chave de API do SendGrid. Se você precisar de detalhes sobre como configurar chaves de API, visite a [documentação][documentation]de chaves de API do SendGrid.

Você pode armazenar essas credenciais por meio do Portal do Azure ao clicar nas configurações do Aplicativo e adicionar os pares chave/valor em "Configurações do aplicativo".

 ![Configurações do aplicativo do Azure][azure_app_settings]

 Em seguida, você pode acessá-las da seguinte forma:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

Os exemplos a seguir mostram como enviar uma mensagem de email usando a API da Web do SendGrid com um aplicativo de console.

    using System;
    using System.Threading.Tasks;
    using SendGrid;
    using SendGrid.Helpers.Mail;

    namespace Example
    {
        internal class Example
        {
            private static void Main()
            {
                Execute().Wait();
            }

            static async Task Execute()
            {
                var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
                var client = new SendGridClient(apiKey);
                var msg = new SendGridMessage()
                {
                    From = new EmailAddress("test@example.com", "DX Team"),
                    Subject = "Hello World from the SendGrid CSharp SDK!",
                    PlainTextContent = "Hello, Email!",
                    HtmlContent = "<strong>Hello, Email!</strong>"
                };
                msg.AddTo(new EmailAddress("test@example.com", "Test User"));
                var response = await client.SendEmailAsync(msg);
            }
        }
    }
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Como: Enviar email da API do ASP .NET Core usando a classe MailHelper

O exemplo abaixo pode ser usado para enviar um único email para várias pessoas da API do ASP .NET Core usando a classe `MailHelper` do namespace `SendGrid.Helpers.Mail`. Neste exemplo, estamos usando o ASP .NET Core 1.0. 

Neste exemplo, a chave de API foi armazenada no arquivo `appsettings.json`, que pode ser substituído no portal do Azure, conforme mostrado nos exemplos acima.

O conteúdo do arquivo `appsettings.json` deve ser semelhante ao seguinte:

    {
       "Logging": {
       "IncludeScopes": false,
       "LogLevel": {
       "Default": "Debug",
       "System": "Information",
       "Microsoft": "Information"
         }
       },
     "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }

Primeiro, precisamos adicionar o código abaixo no arquivo `Startup.cs` do projeto da API do .NET Core. Isso é necessário para que possamos acessar o `SENDGRID_API_KEY` do arquivo `appsettings.json` usando a injeção de dependência no controlador da API. A interface `IConfiguration` pode ser inserida no construtor do controlador após adicioná-la no método `ConfigureServices` abaixo. O conteúdo do arquivo `Startup.cs` é parecido com o seguinte depois de adicionar o código necessário:

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

No controlador, depois da injeção da interface `IConfiguration`, podemos usar o método `CreateSingleEmailToMultipleRecipients` da classe `MailHelper` para enviar um único email para vários destinatários. O método aceita um parâmetro booliano adicional denominado `showAllRecipients`. Esse parâmetro pode ser usado para controlar se os destinatários de email serão capazes de ver os endereços de email uns dos outros na seção Para do cabeçalho do email. O código de exemplo do controlador deve ser como mostrado abaixo 

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using SendGrid;
    using SendGrid.Helpers.Mail;
    using Microsoft.Extensions.Configuration;

    namespace SendgridMailApp.Controllers
    {
        [Route("api/[controller]")]
        public class NotificationController : Controller
        {
           private readonly IConfiguration _configuration;

           public NotificationController(IConfiguration configuration)
           {
             _configuration = configuration;
           }      
        
           [Route("SendNotification")]
           public async Task PostMessage()
           {
              var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
              var client = new SendGridClient(apiKey);
              var from = new EmailAddress("test1@example.com", "Example User 1");
              List<EmailAddress> tos = new List<EmailAddress>
              {
                  new EmailAddress("test2@example.com", "Example User 2"),
                  new EmailAddress("test3@example.com", "Example User 3"),
                  new EmailAddress("test4@example.com","Example User 4")
              };
            
              var subject = "Hello world email from Sendgrid ";
              var htmlContent = "<strong>Hello world with HTML content</strong>";
              var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
              var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
              var response = await client.SendEmailAsync(msg);
          }
       }
    }
    
## <a name="how-to-add-an-attachment"></a>Como: Adicionou um anexo
É possível adicionar anexos a uma mensagem chamando o método **AddAttachment** e especificando minimamente o nome do arquivo e o conteúdo codificado Base64 que você deseja anexar. Você pode incluir vários anexos chamando esse método uma vez para cada arquivo que você quiser anexar ou usando o método **AddAttachments**. O exemplo a seguir demonstra como adicionar um anexo a uma mensagem:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Como: Usar configurações de email para habilitar rodapés, acompanhamento e análise
O SendGrid fornece a funcionalidade adicional de email por meio do uso das configurações de email e de acompanhamento. Essas configurações podem ser adicionadas a uma mensagem de email para habilitar uma funcionalidade específica, como o Acompanhamento de Cliques, Google Analytics, Acompanhamento de Assinatura e assim por diante. Para obter uma lista completa de aplicativos, consulte a [documentação de configurações][settings-documentation].

Os aplicativos podem ser aplicados nas mensagens de email do **SendGrid** usando os métodos implementados como parte da classe **SendGridMessage**. Os exemplos a seguir demonstram os filtros de rodapé e de acompanhamento de cliques:

Os exemplos a seguir demonstram os filtros de rodapé e de acompanhamento de cliques:

### <a name="footer-settings"></a>Configurações de rodapé
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Rastreamento de cliques
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Como: Usar Serviços do SendGrid Adicionais
O SendGrid oferece várias APIs e webhooks que você pode usar para aproveitar a funcionalidade adicional em seu aplicativo do Azure. Para obter mais detalhes, consulte a [referência da API do SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Próximas etapas
Agora que você já conhece as noções básicas do serviço de email SendGrid, siga estes links para saber mais.

* Repositório de\# biblioteca SendGrid C: [SendGrid-Csharp][sendgrid-csharp]
* Documentação da API do SendGrid: <https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[serviço de email baseado em nuvem]: https://sendgrid.com/solutions
[entrega de email transacional]: https://sendgrid.com/use-cases/transactional-email

