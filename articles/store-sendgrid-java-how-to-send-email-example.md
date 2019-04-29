---
title: store-sendgrid-java-how-to-send-email-example
description: Como enviar email usando o SendGrid do Java em uma implantação do Azure
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: af096a73-6985-4350-92e4-ce1722c8d72f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.openlocfilehash: 79cb9bb82862f5720d5ec2262ba30dbbcf3e3f66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60930110"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Como enviar email usando o SendGrid do Java em uma implantação do Azure
O exemplo a seguir mostra como você pode usar o SendGrid para enviar emails de uma página da web hospedada no Azure. O aplicativo resultante solicitará que o usuário para valores de email, conforme mostrado na seguinte captura de tela.

![Formulário de email][emailform]

O email resultante será semelhante à seguinte captura de tela.

![Mensagem de email][emailsent]

Você precisará fazer o seguinte para usar o código deste tópico:

1. Obter os JARs javax.mail, por exemplo, de <https://www.oracle.com/technetwork/java/javamail/index.html>.
2. Adicione os JARs ao seu caminho de compilação do Java.
3. Se estiver usando o Eclipse para criar esse aplicativo Java, você poderá incluir as bibliotecas do SendGrid no seu arquivo de implantação de aplicativo (WAR) usando o recurso do assembly de implantação do Eclipse. Se não estiver usando o Eclipse para criar esse aplicativo Java, verifique se as bibliotecas estão incluídas na mesma função do Azure que o seu aplicativo Java e adicionadas ao caminho de classe do seu aplicativo.

Você também deve ter seu próprio nome de usuário e senha do SendGrid para poder enviar o email. Para começar a usar o SendGrid, consulte [Como enviar email usando o SendGrid do Java](store-sendgrid-java-how-to-send-email.md).

Além disso, é altamente recomendável você se familiarizar com as informações de [Criando um aplicativo Hello World para o Azure no Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app?view=azure-java-stable)ou com outras técnicas de hospedagem de aplicativos Java no Azure se você não estiver usando o Eclipse.

## <a name="create-a-web-form-for-sending-email"></a>Criar um formulário da web para enviar email
O código a seguir mostra como criar um formulário da web para recuperar dados do usuário para enviar email. Para fins deste conteúdo, o arquivo JSP é denominado **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>Criar o código para enviar o email
O código a seguir, que é chamado quando você preenche o formulário em emailform.jsp, cria a mensagem de email e a envia. Para fins deste conteúdo, o arquivo JSP é denominado **sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%

     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");

     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;

            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {

         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";

         Properties properties;

         properties = new Properties();

         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");

         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");

         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();

         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);

         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);

         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 

         message = new MimeMessage(mailSession);

         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            

         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);

         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");

         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");

         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();

        out.println("<p>Email processing completed.</p>");

     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>

    </body>
    </html>

Além de enviar o email, o emailform. jsp fornece um resultado para o usuário; um exemplo é a seguinte captura de tela:

![Resultado de envio de email][emailresult]

## <a name="next-steps"></a>Próximas etapas
Implantar o aplicativo no emulador de computação e, em um navegador, executar emailform.jsp, inserir valores no formulário, clicar em **Enviar este email**e, em seguida, ver os resultados em sendemail.jsp.

Esse código foi fornecido para lhe mostrar como usar o SendGrid do Java no Azure. Antes de implantar o Azure na produção, convém adicionar mais tratamento de erros ou outros recursos. Por exemplo: 

* Você pode usar os blobs de armazenamento ou o Banco de Dados SQL do Azure para armazenar endereços de email e mensagens de email, em vez de usar um formulário da web. Para obter informações sobre como usar os blobs de armazenamento do Azure no Java, consulte [Como usar o serviço de armazenamento de blob do Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Para obter informações sobre como usar o Banco de Dados SQL no Java, consulte [Usando o Banco de Dados SQL no Java](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-java).
* Para obter mais informações sobre como usar o SendGrid no Java, consulte [Como enviar email usando o SendGrid do Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
