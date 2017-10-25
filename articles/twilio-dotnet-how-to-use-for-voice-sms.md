---
title: Como usar o Twilio para Voz e SMS (.NET) | Microsoft Docs
description: "Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Amostras de código gravadas no .NET."
services: 
documentationcenter: .net
author: devinrader
manager: twilio
editor: 
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 1442e3af26ae87e645cf207228ed1197b2afdd4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Como usar o Twilio para funcionalidades de voz e SMS do Azure
Este guia demonstra como executar tarefas comuns de programação com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem fazer uma chamada telefônica e enviar uma mensagem serviço de mensagem curta (SMS). Para obter mais informações sobre o Twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [Próximas etapas](#NextSteps) .

## <a id="WhatIs"></a>O que é Twilio?
Twilio é alimentar o futuro das comunicações de negócios, que permite aos desenvolvedores incorporar voz, VoIP e mensagens em aplicativos. Eles virtualizam toda a infra-estrutura necessária em um ambiente baseado em nuvem, global, expondo-as por meio da plataforma de comunicações API Twilio. Os aplicativos são simples de criar e dimensionável. Aproveite a flexibilidade com preço pré-pago e se beneficie da confiabilidade da nuvem.

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio SMS** permite que seus aplicativos enviem e recebam mensagens SMS. **Cliente de Twilio** permite que você faça chamadas VoIP de qualquer telefone, tablet ou navegador e oferece suporte a WebRTC.

## <a id="Pricing"></a>Preços e ofertas especiais da Twilio
Os clientes do Azure recebem uma [oferta especial](http://www.twilio.com/azure): US$ 10 de cortesia de crédito da Twilio quando atualizam a sua conta da Twilio. Esse crédito de Twilio pode ser aplicado a qualquer uso de Twilio (equivalente a até 1.000 mensagens SMS de envio ou recebimento de até 1000 minutos de voz entrados, dependendo da localização do seu destino de chamada e mensagem ou número de telefone de crédito de US $10). Resgate esse crédito da Twilio e faça uma introdução em [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio é um serviço flexível. Não há nenhuma taxa de configuração e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [Preços da Twilio](http://www.twilio.com/voice/pricing).

## <a id="Concepts"></a>Conceitos
A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas. Para obter uma lista, consulte [Bibliotecas de API do Twilio][twilio_libraries].

Os principais aspectos da API do Twilio são os verbos Twilio e a TwiML (Linguagem de Marcação do Twilio).

### <a id="Verbs"></a>Verbos da Twilio
A API usa os verbos do Twilio; por exemplo, o verbo **&lt;Say&gt;** instrui o Twilio a fornecer de forma audível uma mensagem em uma chamada.

A seguir está uma lista de verbos do Twilio.  Saiba mais sobre os outros verbos e recursos na [Documentação da linguagem de marcação da Twilio](http://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;**: conecta o chamador com outro telefone.
* **&lt;Gather&gt;**: coleta os dígitos numéricos inseridos no teclado numérico de telefone.
* **&lt;Hangup&gt;**: termina uma chamada.
* **&lt;Play&gt;**: reproduz um arquivo de áudio.
* **&lt;Pause&gt;**: espera silenciosamente por um número especificado de segundos.
* **&lt;Record&gt;**: grava a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* **&lt;Redirect&gt;**: transfere o controle de uma chamada ou SMS para TwiML em um URL diferente.
* **&lt;Reject&gt;**: rejeita uma chamada recebida para o número do Twilio sem cobrar você
* **&lt;Say&gt;**: converte o texto em fala que é executada em uma chamada.
* **&lt;Sms&gt;**: envia uma mensagem SMS.

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções em XML com base nos verbos do Twilio que informam o Twilio como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML converteria a mensagem **Olá, mundo** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Quando o aplicativo chama a API Twilio, um dos parâmetros a API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidos Twilio para fornecer as respostas de TwiML usadas por seus aplicativos. Você também pode hospedar seus próprio URLs para produzir as respostas TwiML e outra opção é usar o **TwiMLResponse** objeto.

Para obter mais informações sobre os verbos do Twilio, seus atributos e o TwiML, consulte [TwiML][twiml]. Para obter mais informações sobre a API do Twilio, consulte [API do Twilio][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta na Twilio
Quando estiver pronto para obter uma conta do Twilio, inscreva-se em [Experimentar o Twilio][try_twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Quando você se inscrever para uma conta de Twilio, você receberá uma ID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. A ID de sua conta e o token de autenticação estão visíveis na [página da conta do Twilio][twilio_account], nos campos rotulados **ACCOUNT SID** e **AUTH TOKEN**, respectivamente.

## <a id="create_app"></a>Criar um aplicativo Azure
Um aplicativo do Azure que hospeda um aplicativo Twilio habilitado não é diferente de qualquer outro aplicativo do Azure. Adicione a biblioteca .NET do Twilio e configure a função para usar as bibliotecas .NET do Twilio.
Para obter informações sobre como criar um projeto inicial do Azure, consulte [Creating an Azure project with Visual Studio][vs_project] (Criando um projeto do Azure com o Visual Studio).

## <a id="configure_app"></a>Configurar seu aplicativo para usar as bibliotecas do Twilio
Twilio fornece um conjunto de bibliotecas do .NET auxiliar que encapsular vários aspectos de Twilio para fornecer maneiras simples e fáceis de interagir com o API REST de Twilio e Twilio cliente para gerar respostas de TwiML.

Twilio fornece cinco bibliotecas para desenvolvedores .NET:
Biblioteca|Descrição
---|---
Twilio.API|A biblioteca de Twilio de núcleo que encapsula a API REST Twilio em uma biblioteca .NET amigável. Essa biblioteca está disponível para o .NET, Silverlight e Windows Phone 7.
Twilio.TwiML|Fornece uma maneira amigável do .NET para gerar marcação de TwiML.
Twilio.MVC|Para os desenvolvedores que usam o ASP.NET MVC, esta biblioteca inclui um TwilioController, o TwiML ActionResult e o atributo de validação de solicitação.
Twilio.WebMatrix|Para os desenvolvedores usando a ferramenta de desenvolvimento do WebMatrix gratuita da Microsoft, esta biblioteca contém auxiliares de sintaxe do Razor para várias ações de Twilio.
Twilio.Client.Capability|Contém o gerador de token de capacidade para uso com o SDK do Twilio cliente JavaScript.

Observe que todas as bibliotecas requerem o .NET 3.5, Silverlight 4 ou Windows Phone 7 ou posterior.

Os exemplos fornecidos neste guia usam a biblioteca Twilio.API.

As bibliotecas podem ser [instaladas usando a extensão do gerenciador de pacotes NuGet](http://www.twilio.com/docs/csharp/install) disponíveis para o Visual Studio 2010 a 2015.  O código-fonte é hospedado no [GitHub][twilio_github_repo], que inclui um Wiki que contém a documentação completa para usar as bibliotecas.

Por padrão, o Microsoft Visual Studio 2010 instala a versão 1.2 do NuGet. As bibliotecas de Twilio a instalação requer a versão 1.6 do NuGet ou superior. Para obter mais informações sobre como instalar ou atualizar o NuGet, consulte [http://nuget.org/][nuget].

> [!NOTE]
> Para instalar a versão mais recente do NuGet, primeiro desinstale a versão carregada usando o Gerenciador de extensões do Visual Studio. Para fazer isso, você deve executar o Visual Studio como administrador. Caso contrário, o botão Desinstalar é desabilitado.
>
>

### <a id="use_nuget"></a>Para adicionar as bibliotecas Twilio ao seu projeto do Visual Studio:
1. Abra sua solução no Visual Studio.
2. Clique com o botão direito do mouse em **Referências**.
3. Clique em **Gerenciar Pacotes NuGet...**
4. Clique em **Online**.
5. Na caixa Pesquisar Online, digite *tiwlio*.
6. Clique em **Instalar** no pacote Twilio.

## <a id="howto_make_call"></a>Como fazer uma chamada externa
A seguir é demonstrado como fazer uma chamada externa usando a classe **CallResource**. Esse código também usa um site fornecido pelo Twilio para retornar a resposta TwiML (Linguagem de Marcação do Twilio). Substitua os valores dos números telefônicos **para** e **de** e certifique-se de verificar o número telefônico **de** da sua conta do Twilio antes de executar o código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    const string accountSID = "your_twilio_account";
    const string authToken = "your_twilio_authentication_token";

    // Initialize the TwilioClient.
    TwilioClient.Init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    var url = "http://twimlets.com/message";
    url = $"{url}?Message%5B0%5D=Hello%20World";

    // Set the call From, To, and URL values to use for the call.
    // This sample uses the sandbox number provided by
    // Twilio to make the call.
    var call = CallResource.Create(
        to: new PhoneNumber("+NNNNNNNNNN"),
        from: new PhoneNumber("NNNNNNNNNN"),
        url: new Uri(url));
        }

Para obter mais informações sobre os parâmetros passados para o método **CallResource.Create**, consulte [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Como mencionado, esse código utiliza um site fornecido pelo Twilio para retornar a resposta de TwiML. Em vez disso, você pode usar seu próprio site para fornecer a resposta TwiML. Para obter mais informações, consulte [Como fornecer respostas do TwiML de seu próprio site](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Como enviar uma mensagem de SMS
A seguinte captura de tela mostra como enviar uma mensagem de SMS usando a classe **MessageResource**. O número **de** é fornecido pelo Twilio para contas de avaliação para envio de mensagens SMS. O número **para** deve ser verificado para sua conta de Twilio antes de você executar o código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    const string accountSID = "your_twilio_account";
    const string authToken = "your_twilio_authentication_token";

    // Initialize the TwilioClient.
    TwilioClient.Init(accountSID, authToken);

    try
    {
        // Send an SMS message.
        var message = MessageResource.Create(
            to: new PhoneNumber("+12069419717"),
            from: new PhoneNumber("+14155992671"),
            body: "This is my SMS message.");
    }
    catch (TwilioException ex)
    {
        // An exception occurred making the REST call
        Console.WriteLine(ex.Message);
    }

## <a id="howto_provide_twiml_responses"></a>Como fornecer respostas TwiML de seu próprio site
Quando o aplicativo iniciar uma chamada para a API do Twilio, por exemplo, por meio do método **CallResource.Create**, o Twilio enviará a solicitação para uma URL que deverá retornar uma resposta em TwiML. O exemplo em [Como fazer uma chamada de saída](#howto_make_call) usa a URL fornecida pelo Twilio [http://twimlets.com/message][twimlet_message_url] para retornar a resposta.

> [!NOTE]
> Enquanto TwiML destina-se ao uso pelos serviços Web, é possível exibir o TwiML no seu navegador. Por exemplo, clique em [http://twimlets.com/message][twimlet_message_url] para ver um elemento &lt;Response&gt; vazio; como outro exemplo, clique em [http://twimlets.com/message?Message%5B0%5D=Hello%20World](http://twimlets.com/message?Message%5B0%5D=Hello%20World) para ver um elemento &lt;Response&gt; que contém um elemento &lt;Say&gt;.
>
>

Em vez de contar com a URL fornecida pela Twilio, você pode criar seu próprio site URL que retorna respostas HTTP. Você pode criar o site em qualquer linguagem que retorna respostas HTTP. Este tópico pressupõe que você irá hospedando a URL de um manipulador genérico do ASP.NET.

O seguinte manipulador de ASP.NET monta uma resposta de TwiML, diz **Hello World** na chamada.

    using System.Text;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {
            public void ProcessRequest(HttpContext context)
            {
                const string twiMLResponse =
                    "<Response><Say>Hello World.</Say></Response>";
                
                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.ContentEncoding = Encoding.UTF8;
                context.Response.Write(twiMLResponse);
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }
    
Como você pode ver no exemplo acima, a resposta de TwiML é simplesmente um documento XML. A biblioteca do Twilio.TwiML contém classes que irão gerar TwiML para você. O exemplo a seguir produz a resposta equivalente como mostrado acima, mas usa a classe **VoiceResponse**.

    using System.Web;
    using Twilio.TwiML;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                var twiml = new VoiceResponse();
                twiml.Say("Hello World.");

                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.Write(twiml.ToString());
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

Para obter mais informações sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

Depois que você configurar uma forma de fornecer respostas TwiML, você poderá passar essa URL para o método **CallResource.Create**. Por exemplo, se você tiver um aplicativo Web chamado MyTwiML implantado em um serviço de nuvem do Azure e o nome de seu manipulador ASP.NET for mytwiml.ashx, a URL poderá ser passada para **CallResource.Create** conforme mostrado no exemplo de código a seguir:

    // This sample uses the sandbox number provided by Twilio to make the call.
    // Place the call.
    var call = CallResource.Create(
        to: new PhoneNumber("+NNNNNNNNNN"),
        from: new PhoneNumber("NNNNNNNNNN"),
        url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
        }

Para obter mais informações sobre como usar o Twilio no Azure com o ASP.NET, consulte [Como fazer uma chamada telefônica usando o Twilio em uma função Web no Azure][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: http://twimlets.com/message

[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[vs_project]:http://msdn.microsoft.com/library/windowsazure/ee405487.aspx
[nuget]:http://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
