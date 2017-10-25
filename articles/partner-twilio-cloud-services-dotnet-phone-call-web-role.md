---
title: "Como fazer uma chamada telefônica do Twilio (.NET) | Microsoft Docs"
description: "Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Amostras de código gravadas no .NET."
services: 
documentationcenter: .net
author: devinrader
manager: timlt
editor: 
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 0899a49cbfda775017dab7fc6d8963bbeb86d74c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Como fazer uma chamada telefônica usando a Twilio em uma função web no Azure
Este guia demonstra como usar a Twilio para fazer uma chamada de uma página da web hospedada no Azure. O aplicativo resultante solicita ao usuário para fazer uma chamada com o número e mensagem determinados, conforme mostrado na seguinte captura de tela.

![Formulário de chamada do Azure usando a Twilio e o ASP.NET][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Pré-requisitos
Você precisará fazer o seguinte para usar o código deste tópico:

1. Adquirir uma conta e um token de autenticação da Twilio do seu [Console do Twilio][twilio_console]. Para começar com a Twilio, inscreva-se em [https://www.twilio.com/try-twilio][try_twilio]. Você pode avaliar os preços em [http://www.twilio.com/pricing][twilio_pricing]. Para obter informações sobre a API fornecida pela Twilio, veja [http://www.twilio.com/voice/api][twilio_api].
2. Adicione a *biblioteca do .NET da Twilio* à sua função web. Consulte **Para adicionar as bibliotecas da Twilio ao seu projeto de função web**, posteriormente neste tópico.

Você deve estar familiarizado com a criação de uma [Função web básica no Azure][azure_webroles_get_started].

## <a name="howtocreateform"></a>Como criar um formulário Web para fazer uma chamada
<a id="use_nuget"></a>Para adicionar as bibliotecas Twilio ao seu projeto de função Web:

1. Abra sua solução no Visual Studio.
2. Clique com o botão direito do mouse em **Referências**.
3. Clique em **Gerenciar Pacotes NuGet**
4. Clique em **Online**.
5. Na caixa Pesquisar Online, digite *tiwlio*.
6. Clique em **Instalar** no pacote Twilio.

O código a seguir mostra como criar um formulário da web para recuperar dados do usuário para fazer uma chamada. Neste exemplo, uma Função web do ASP.NET chamada **TwilioCloud** é criada.

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a id="howtocreatecode"></a>Como criar o código para fazer a chamada
O código a seguir, que é chamado quando o usuário preenche o formulário, cria a mensagem de chamada e gera a chamada. Neste exemplo, o código é executado no manipulador de eventos onclick do botão no formulário. (Use sua conta e o token de autenticação da Twilio em vez dos valores de espaço reservado atribuídos a `accountSID` e `authToken` no código a seguir.)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call porcessing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"http://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

A chamada é feita, e o ponto de extremidade da Twilio, a versão da API e o status da chamada são exibidos. A captura de tela a seguir mostra a saída de uma execução de exemplo.

![Resposta de chamada do Azure usando a Twilio e o ASP.NET][twilio_dotnet_basic_form_output]

Mais informações sobre TwiML podem ser localizadas em [http://www.twilio.com/docs/api/twiml][twiml]. Mais informações sobre &lt;Say&gt; e outros verbos da Twilio podem ser localizadas em [http://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a id="nextsteps"></a>Próximas etapas
Esse código foi fornecido para mostrar a funcionalidade básica usando a Twilio em uma função web do ASP.NET no Azure. Antes de implantar o Azure na produção, convém adicionar mais tratamento de erros ou outros recursos. Por exemplo:

* Em vez de usar um formulário da web, você pode usar o armazenamento de Blob do Azure ou uma instância do Banco de Dados SQL do Azure para armazenar números de telefone e texto de chamada. Para obter mais informações sobre como usar blobs no Azure, consulte [Como usar o serviço de Armazenamento de Blobs do Azure no .NET][howto_blob_storage_dotnet]. Para obter informações sobre como usar o Banco de Dados SQL, consulte [Como usar o Banco de Dados SQL do Azure em aplicativos .NET][howto_sql_azure_dotnet].
* Você pode usar `RoleEnvironment.getConfigurationSettings` para recuperar a ID da conta e o token de autenticação da Twilio nas definições da configuração da implantação, em vez de embutir valores no código de seu formulário. Para obter informações sobre a classe `RoleEnvironment`, consulte [Microsoft.WindowsAzure.ServiceRuntime Namespace (Namespace Microsoft.WindowsAzure.ServiceRuntime)][azure_runtime_ref_dotnet].
* Leia as Diretrizes de segurança da Twilio em [https://www.twilio.com/docs/security][twilio_docs_security].
* Saiba mais sobre a Twilio em [https://www.twilio.com/docs][twilio_docs].

## <a name="seealso"></a>Consulte também
* [Como usar o Twilio para funcionalidades de voz e SMS do Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
[azure_webroles_get_started]: https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-dotnet-get-started
