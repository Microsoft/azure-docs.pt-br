---
title: Como usar o serviço de email SendGrid (Node.js) | Microsoft Docs
description: Saiba como enviar email com o serviço de email SendGrid no Azure. Exemplos de código escritos usando a API do Node. js.
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: f2d653441598a47986913d525057672eed24b435
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60931697"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Como enviar emails usando o SendGrid do Node.js

Este guia demonstra como executar tarefas comuns de programação com o serviço de email SendGrid no Windows Azure. As amostras são gravadas usando a API do Node.js. Os cenários abordados incluem a **construção de emails**, o **envio de emails**, a **adição de anexos**, o **uso de filtros** e a **atualização de propriedades**. Para obter mais informações sobre o SendGrid e o envio de e-mails, consulte a seção [Próximas etapas](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de email SendGrid?

SendGrid é um [serviço de email baseado em nuvem] que fornece uma [entrega de email transacional], escalabilidade e análise em tempo real confiáveis com APIs flexíveis que facilitam a integração personalizada. Os cenários comuns de uso do SendGrid incluem:

* Envio automático de recibos para os clientes
* Administração de listas de distribuição para enviar aos clientes mensalmente panfletos eletrônicos e ofertas especiais
* Coleta de métrica em tempo real para, por exemplo, email bloqueado e capacidade de resposta do cliente
* Geração de relatórios para ajudar a identificar as tendências
* Encaminhamento de consultas dos clientes
* Notificações de email de seu aplicativo

Para obter mais informações, consulte [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Criar uma conta do SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Referenciar o módulo do Node.js SendGrid

O módulo de SendGrid para o Node.js pode ser instalado através do gerenciador de pacotes do nó (npm) usando o seguinte comando:

```bash
npm install sendgrid
```

Após a instalação, você pode exigir o módulo em seu aplicativo, usando o código a seguir:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

O módulo de SendGrid exporta as funções **SendGrid** e **Email**.
**SendGrid** é responsável por enviar email por meio da API da Web, enquanto **Email** encapsula uma mensagem de email.

## <a name="how-to-create-an-email"></a>Como: Criar um email

Criar uma mensagem de e-mail usando o módulo SendGrid envolve criar primeiro uma mensagem de e-mail usando a função de E-mail e, em seguida, enviá-la usando a função SendGrid. Este é um exemplo da criação de uma nova mensagem usando a função de Email:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Você também pode especificar uma mensagem de HTML a clientes que dão suporte a ela, definindo a propriedade de html. Por exemplo: 

```javascript
html: This is a sample <b>HTML<b> email message.
```

Definir propriedades de texto e de html fornece retorno normal ao conteúdo de texto para clientes que não podem dar suporte a mensagens em HTML.

Para obter mais informações sobre todas as propriedades compatíveis com a função de Email, consulte [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Como: Envie um email

Depois de criar uma mensagem de email usando a função Email, você pode enviá-la usando a API da Web fornecida pelo SendGrid. 

### <a name="web-api"></a>API Web

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Enquanto os exemplos acima mostram a passagem em uma função de retorno de chamada e de objeto de email, você pode invocar diretamente as funções de envio especificando diretamente as propriedades do email. Por exemplo:   
> 
> ```javascript
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> ```
>

## <a name="how-to-add-an-attachment"></a>Como: Adicionar um anexo
Os anexos podem ser adicionados a uma mensagem, especificando o(s) nome(s) e caminho(s) dos arquivos na propriedade **arquivos**. O exemplo a seguir demonstra como enviar um anexo:

```javascript
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.',
    files: [
        {
            filename:     '',           // required only if file.content is used.
            contentType:  '',           // optional
            cid:          '',           // optional, used to specify cid for inline content
            path:         '',           //
            url:          '',           // == One of these three options is required
            content:      ('' | Buffer) //
        }
    ],
});
```

> [!NOTE]
> Ao usar a propriedade **arquivos**, o arquivo deve estar acessível por meio de [fs.readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Se o arquivo que você deseja anexar é hospedado no armazenamento do Azure, como um contêiner de Blob, você deve primeiro copiar o arquivo para o armazenamento local ou para uma unidade do Azure antes de ser enviado como um anexo usando a propriedade **arquivos**.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Como: Usar filtros para habilitar rodapés e acompanhamento

O SendGrid fornece a funcionalidade adicional de email por meio do uso de filtros. Essas são as configurações que podem ser adicionadas a uma mensagem de email para habilitar uma funcionalidade específica, como habilitar rastreamento de cliques, Google analytics, rastreamento de assinatura e assim por diante. Para obter uma lista completa de filtros, consulte [Configurações de filtro][Filter Settings].

Os filtros podem ser aplicados a uma mensagem pela propriedade **filtros**.
Cada filtro é especificado por um hash que possui configurações específicas de filtro.
Os exemplos a seguir demonstram os filtros de rodapé e de acompanhamento de cliques:

### <a name="footer"></a>Rodapé

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'footer': {
        'settings': {
            'enable': 1,
            'text/plain': 'This is a text footer.'
        }
    }
});

sendgrid.send(email);
```

### <a name="click-tracking"></a>Acompanhamento de cliques

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'clicktrack': {
        'settings': {
            'enable': 1
        }
    }
});

sendgrid.send(email);
```

## <a name="how-to-update-email-properties"></a>Como: Atualizar propriedades de Email

Algumas propriedades de email podem ser substituídas usando **setProperty** ou anexadas usando **addProperty**. Por exemplo, você pode adicionar destinatários adicionais ao usar

```javascript
email.addTo('jeff@contoso.com');
```

ou definir um filtro usando

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Para obter mais informações, consulte [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Como: Usar Serviços do SendGrid Adicionais

O SendGrid oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do SendGrid do aplicativo do Azure. Para obter detalhes completos, consulte a [Documentação da API do SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Próximas etapas

Agora que você já conhece as noções básicas do serviço de email SendGrid, siga estes links para saber mais.

* Repositório do módulo SendGrid Node.js: [sendgrid-nodejs][sendgrid-nodejs]
* Documentação da API do SendGrid: <https://sendgrid.com/docs>
* Oferta especial do SendGrid para clientes do Azure: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[serviço de email baseado em nuvem]: https://sendgrid.com/email-solutions
[entrega de email transacional]: https://sendgrid.com/transactional-email
