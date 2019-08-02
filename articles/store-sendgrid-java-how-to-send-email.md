---
title: Como usar o serviço de email SendGrid (Java) | Microsoft Docs
description: Saiba como enviar email com o serviço de email SendGrid no Azure. Exemplos de códigos escritos em Java.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: 8ae948e9c79cff4cd0c896b250743fd9dc521752
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876520"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Como enviar emails usando o SendGrid com Java
Este guia demonstra como executar tarefas comuns de programação com o serviço de email SendGrid no Windows Azure. As amostras são gravadas em Java. Os cenários abordados incluem a **construção de emails**, o **envio de emails**, a **adição de anexos**, o **uso de filtros** e a **atualização de propriedades**. Para obter mais informações sobre o SendGrid e o envio de emails, consulte a seção [Próximas etapas](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de email SendGrid?
SendGrid é um [serviço de email baseado em nuvem] que fornece uma [entrega de email transacional], escalabilidade e análise em tempo real confiáveis com APIs flexíveis que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid incluem:

* Envio automático de recibos para os clientes
* Administração de listas de distribuição para enviar aos clientes mensalmente panfletos eletrônicos e ofertas especiais
* Coleta de métrica em tempo real para, por exemplo, email bloqueado e capacidade de resposta do cliente
* Geração de relatórios para ajudar a identificar as tendências
* Encaminhamento de consultas dos clientes
* Notificações de email de seu aplicativo

Para obter mais informações, consulte <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Criar uma conta do SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Como: Usar as bibliotecas javax. mail
Obtenha as bibliotecas javax.mail, por exemplo, a partir de <https://www.oracle.com/technetwork/java/javamail> e importe-as para o seu código. Em um alto nível, o processo de usar a biblioteca javax.mail para enviar emails usando SMTP é fazer o seguinte:

1. Especificar os valores de SMTP, incluindo o servidor SMTP, que, para SendGrid, é smtp.sendgrid.net.

```
        import java.util.Properties;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

           public static void main(String[] args) throws Exception{
               new MyEmailer().SendMail();
           }

           public void SendMail() throws Exception
           {
              Properties properties = new Properties();
                 properties.put("mail.transport.protocol", "smtp");
                 properties.put("mail.smtp.host", SMTP_HOST_NAME);
                 properties.put("mail.smtp.port", 587);
                 properties.put("mail.smtp.auth", "true");
                 // …
```

1. Estenda a classe *javax.mail.Authenticator* e, na implementação do método *getPasswordAuthentication*, retorne seu nome de usuário e sua senha do SendGrid.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Criar uma sessão de email autenticada por meio de um objeto *javax.mail.Session* .  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Criar sua mensagem e atribuir valores de conteúdo, **Para**, **De** e **Assunto**. Isso é mostrado na [seguinte forma: Criar uma seção](#how-to-create-an-email) de email.
4. Enviar a mensagem por meio de um objeto *javax.mail.Transport* . Isso é mostrado na seção [como: Enviar uma seção de email] [#how para enviar-um-email].

## <a name="how-to-create-an-email"></a>Como: Criar um email
O código a seguir mostra como especificar valores para um email.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hello,</p>
        <p>Your Contoso order has <b>shipped</b>.</p>
        <p>Thank you,<br>John</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="how-to-send-an-email"></a>Como: Enviar um email
O código a seguir mostra como enviar um email.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Como: Adicionou um anexo
O código a seguir mostra como adicionar um anexo.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\";
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Como: Usar filtros para habilitar rodapés, rastreamento e análise
O SendGrid fornece a funcionalidade adicional de email por meio do uso de *filtros*. Essas são as configurações que podem ser adicionadas a uma mensagem de email para habilitar uma funcionalidade específica, como habilitar rastreamento de cliques, Google analytics, rastreamento de assinatura e assim por diante. Para obter uma lista completa de filtros, consulte [Configurações de filtro][Filter Settings].

* O código a seguir mostra como inserir um filtro de rodapés que faça com que o texto HTML exibido na parte inferior do email seja enviado.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Outro exemplo de um filtro é o acompanhamento de cliques. Digamos que o texto do email contém um hiperlink, como o seguinte, e você deseja acompanhar a taxa de cliques:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* Para habilitar o acompanhamento de cliques, use o código a seguir:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Como: Atualizar propriedades de email
Algumas propriedades de email podem ser substituídas usando **set Property** ou acrescentado usando **add Property**.

Por exemplo, para especificar endereços para **ReplyTo** , use o seguinte:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Para adicionar um destinatário a **Cc** , use o seguinte:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Como: Usar serviços SendGrids adicionais
O SendGrid oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do SendGrid do aplicativo do Azure. Para obter detalhes completos, consulte a [Documentação da API do SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Próximas etapas
Agora que você já conhece as noções básicas do serviço de email SendGrid, siga estes links para saber mais.

* Exemplo que demonstra como usar SendGrid em uma implantação do Azure: [Como enviar emails usando o SendGrid do Java em uma implantação do Azure](store-sendgrid-java-how-to-send-email-example.md)
* SDK de Java do SendGrid: <https://sendgrid.com/docs/Code_Examples/java.html>
* Documentação da API do SendGrid: <https://sendgrid.com/docs/API_Reference/index.html>
* Oferta especial do SendGrid para clientes do Azure: <https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[serviço de email baseado em nuvem]: https://sendgrid.com/email-solutions
[entrega de email transacional]: https://sendgrid.com/transactional-email
