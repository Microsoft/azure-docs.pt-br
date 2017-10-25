---
title: "Como fazer uma chamada telefônica do Twilio (Java) | Microsoft Docs"
description: "Saiba como fazer uma chamada telefônica de uma página da Web usando o Twilio em um aplicativo Java no Azure."
services: 
documentationcenter: java
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 04ecb80a2a9e15b549b47138caf71c7e64bda500
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Como fazer uma chamada telefônica usando o Twilio em um aplicativo Java no Azure
O exemplo a seguir mostra como você pode usar a Twilio para fazer uma chamada de uma página da web hospedada no Azure. O aplicativo resultante solicitará valores de chamada telefônica ao usuário, conforme mostrado na seguinte captura de tela.

![Formulário de chamada do Azure usando a Twilio e o Java][twilio_java]

Você precisará fazer o seguinte para usar o código deste tópico:

1. Obter uma conta e um token de autenticação da Twilio. Para uma introdução ao Twilio, avalie os preços em [http://www.twilio.com/pricing][twilio_pricing]. Você pode se inscrever no [https://www.twilio.com/try-twilio][try_twilio]. Para obter informações sobre a API fornecida pela Twilio, veja [http://www.twilio.com/api][twilio_api].
2. Obter o JAR doa Twilio. Em [https://github.com/twilio/twilio-java][twilio_java_github], você pode baixar as fontes GitHub e criar seu próprio JAR ou baixar um JAR predefinido (com ou sem dependências).
   O código deste tópico foi escrito usando o JAR TwilioJava 3.3.8 pré-criado com dependências.
3. Adicione o JAR a seu caminho de compilação de Java.
4. Se estiver usando o Eclipse para criar esse aplicativo Java, inclua o JAR da Twilio em seu arquivo de implantação de aplicativo (WAR) usando o recurso do assembly de implantação do Eclipse. Se não estiver usando o Eclipse para criar esse aplicativo Java, verifique se o JAR da Twilio está incluído na mesma função do Azure que seu aplicativo Java e adicionado ao caminho de classe de seu aplicativo.
5. Verifique se seu keystore de cacerts contém o certificado de autoridade da Equifax Secure Certificate com impressão digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:F4:CF e a impressão digital SHA1 é D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Esse é o certificado de AC (autoridade de certificação) do serviço [https://api.twilio.com][twilio_api_service], que é chamado quando você usa APIs da Twilio. Para obter informações sobre como adicionar este certificado de autoridade de certificação para o repositório de cacerts de JDK, veja [Adicionando um certificado ao repositório de certificados de autoridade de certificação Java][add_ca_cert].

Além disso, é altamente recomendável você se familiarizar com as informações em [Criando um aplicativo Olá, Mundo usando o Kit de Ferramentas do Azure para Eclipse][azure_java_eclipse_hello_world] ou com outras técnicas de hospedagem de aplicativos Java no Azure se você não estiver usando o Eclipse.

## <a name="create-a-web-form-for-making-a-call"></a>Criar um formulário da web para fazer uma chamada
O código a seguir mostra como criar um formulário da web para recuperar dados do usuário para fazer uma chamada. Para o objetivo deste exemplo, um novo projeto Web dinâmico, chamado **TwilioCloud**, foi criado e **callform.jsp** foi adicionado como um arquivo JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Como criar o código para fazer a chamada
O código a seguir, que é chamado quando o usuário preenche o formulário mostrado por callform.jsp, cria a mensagem de chamada e gera a chamada. Para o objetivo deste exemplo, o arquivo JSP é chamado **makecall.jsp** e foi adicionado ao projeto **TwilioCloud**. (Use sua conta e o token de autenticação da Twilio em vez dos valores de espaço reservado atribuídos a **accountSID** e **authToken** no código a seguir.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";

         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");

         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");

         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;

         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);

         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Além de fazer a chamada, o makecall.jsp exibe o ponto de extremidade da Twilio, a versão da API e o status da chamada. A captura de tela a seguir é um exemplo:

![Resposta de chamada do Azure usando a Twilio e o Java][twilio_java_response]

## <a name="run-the-application"></a>Executar o aplicativo
As seguintes são as etapas de alto nível para executar seu aplicativo. Os detalhes dessas etapas podem ser localizados em [Criando um aplicativo Olá, Mundo usando o Kit de Ferramentas do Azure para Eclipse][azure_java_eclipse_hello_world].

1. Exporte o WAR TwilioCloud para a pasta **approot** do Azure. 
2. Modifique o **startup.cmd** para descompactar seu WAR TwilioCloud.
3. Compile o aplicativo para o emulador de computação.
4. Inicie a implantação no emulador de computação.
5. Abra um navegador e execute o **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Insira os valores no formulário, clique em **Fazer esta chamada**e, em seguida, veja os resultados em makecall.jsp.

Quando estiver pronto para implantar no Azure, recompile para implantação na nuvem, implante no Azure e execute http://*seu_nome_hospedado*.cloudapp.net/TwilioCloud/callform.jsp no navegador (substitua seu valor por *seu_nome_hospedado*).

## <a name="next-steps"></a>Próximas etapas
Esse código foi fornecido para mostrar a funcionalidade básica usando a Twilio no Java no Azure. Antes de implantar o Azure na produção, convém adicionar mais tratamento de erros ou outros recursos. Por exemplo:

* Em vez de usar um formulário da web, você pode usar os blobs de armazenamento ou o Banco de Dados SQL do Azure para armazenar números de telefone e texto de chamada. Para obter informações sobre como usar os blobs de armazenamento do Azure no Java, consulte [Como usar o serviço de armazenamento de blobs do Java][howto_blob_storage_java]. Para obter informações sobre como usar o Banco de Dados SQL no Java, consulte [Usando o Banco de Dados SQL no Java][howto_sql_azure_java].
* Você pode usar **RoleEnvironment.getConfigurationSettings** para recuperar a ID da conta e o token de autenticação da Twilio nas definições da configuração da implantação, em vez de embutir valores no código no makecall.jsp. Para obter informações sobre a classe **RoleEnvironment**, consulte [Como usar a biblioteca de tempo de execução de serviço do Azure no JSP][azure_runtime_jsp] e a documentação do pacote de Tempo de Execução de Serviço do Azure em [http://dl.windowsazure.com/javadoc][azure_javadoc].
* O código makecall.jsp atribui uma URL fornecida pelo Twilio, [http://twimlets.com/message][twimlet_message_url], para a variável **Url**. Esta URL fornece uma resposta da linguagem TwiML que informa a Twilio como proceder com a chamada. Por exemplo, a TwiML que é retornada pode conter um verbo **&lt;Say&gt;** que resulta em texto que está sendo falado para o destinatário da chamada. Em vez de usar a URL fornecida pela Twilio, você pode criar seu próprio serviço para responder à solicitação da Twilio. Para obter mais informações, consulte [Como usar a Twilio para obter recursos de voz e SMS em Java][howto_twilio_voice_sms_java]. Mais informações sobre a TwiML podem ser encontradas em [http://www.twilio.com/docs/api/twiml][twiml] e mais informações sobre **&lt;Say&gt;** e outros verbos da Twilio podem ser encontradas em [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Leia as diretrizes de segurança da Twilio em [https://www.twilio.com/docs/security][twilio_docs_security].

Para obter informações adicionais sobre a Twilio, consulte [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Consulte também
* [Como usar a Twilio para obter recursos de voz e SMS no Java][howto_twilio_voice_sms_java]
* [Adicionar um certificado ao repositório de Certificados de Autoridade de Certificação Java][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
