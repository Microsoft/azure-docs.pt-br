---
title: Como usar o SendGrid no Azure Functions | Microsoft Docs
description: Mostra como usar o SendGrid no Azure Functions
services: functions
documentationcenter: na
author: rachelappel
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/31/2017
ms.author: rachelap
ms.openlocfilehash: 563bdac30176de131f6d8fd194713f482ceb75d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-sendgrid-in-azure-functions"></a>Como usar o SendGrid no Azure Functions

## <a name="sendgrid-overview"></a>Visão geral do SendGrid

O Azure Functions dá suporte às saídas de associações do SendGrid para habilitar funções para envio de mensagens de email com algumas linhas de código e uma conta do SendGrid.

Para usar a API do SendGrid em um Azure Function, você deve ter uma [conta do SendGrid](http://SendGrid.com). Além disso, você deve ter uma chave de API do SendGrid. Faça logon em sua conta do SendGrid e clique em **Configurações** e depois em **Chave de API** para gerar uma chave de API. Mantenha essa chave disponível, pois você a usará em uma etapa futura.

Agora você está pronto para criar um aplicativo de funções do Azure.

## <a name="create-an-azure-function-app"></a>Criar um Aplicativo de funções do Azure 

Aplicativos do Azure Function são contêineres para uma ou mais Azure Functions. Azure Functions são exatamente isso, uma função. Cada função do Azure está ligada a um gatilho, que é um evento que faz com que a função seja executada.
Cada função pode conter qualquer número de associações de entrada ou saída. Associações são serviços que podem ser usados em uma função. O SendGrid é uma associação de saída que você pode usar para enviar email. 

1. Faça logon no portal do Azure e [crie um Aplicativo de Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) ou abra um Aplicativo de funções existente. 
2. Crie uma função do Azure. Para mantê-la simples, escolha um gatilho manual e C#. 

 ![Criar uma função do Azure](./media/functions-how-to-use-sendgrid/functions-new-function-manual-trigger-page.png)

## <a name="configure-sendgrid-for-use-in-an-azure-function-app"></a>Configure o SendGrid para uso em um aplicativo de funções do Azure

Você deve armazenar sua chave de API do SendGrid como uma configuração de aplicativo para usá-la em uma função. O campo ApiKey não é sua chave de API do SendGrid real, mas uma configuração de aplicativo que você define e que representa sua chave de API real. Armazenar a chave dessa maneira é para segurança, pois ela é separada de qualquer código ou arquivos que possam ser verificados no controle do código-fonte.

- Crie uma chave **AppSettings** nas **Configurações de Aplicativo** de seu aplicativo de funções.

 ![Criar uma função do Azure](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-api-key-app-settings.png)

## <a name="configure-sendgrid-output-bindings"></a>Configurar associações de saída do SendGrid

O SendGrid está disponível como uma associação de saída da função do Azure. Para criar uma associação de saída do SendGrid:

1. Vá para a guia **Integrar** da função no Portal do Azure.
2. Clique em **Nova Saída** para criar uma associação de saída do SendGrid.
3. Preencha as propriedades **Chave de API** e **Nome do parâmetro de mensagem**. Se desejar, você poderá inserir as outras propriedades agora ou então codificá-las. Essas configurações podem ser usadas como padrões.

 ![Configurar associações de saída do SendGrid](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-output-bindings.png)

Adicionar uma associação a uma função cria um arquivo chamado **function.json** na pasta da função. Esse arquivo contém as mesmas informações que você vê na guia **Integrar** da função do Azure, mas no formato Json. Configurar os campos **ApiKey**, **message** e **from** criará as seguintes entradas no arquivo **function.json**: 

```json
{
  "bindings": [    
    {
      "type": "sendGrid",
      "name": "message",
      "apiKey": "SendGridKey",
      "direction": "out",
      "from": "azure@contoso.com"
    }
  ],
  "disabled": false
}
```

Se preferir, você poderá modificar esse arquivo diretamente, por conta própria.

Agora que você criou e configurou a função e o Aplicativo de funções, você pode escrever o código para enviar um email.

## <a name="write-code-that-creates-and-sends-email"></a>Escrever um código que cria e envia um email

A API do SendGrid contém todos os comandos de que você precisa para criar e enviar um email.  

- Substitua o código na função pelo código a seguir:

```cs
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
    message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    // change to email of recipient
    personalization.AddTo(new Email("MoreEmailPlease@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

Observe que a primeira linha contém a diretiva ```#r``` que faz referência ao assembly SendGrid. Depois disso, você pode usar uma instrução ```using``` para acessar mais facilmente os objetos nesse namespace. No código, crie instâncias de ```Mail```, ```Personalization``` e ```Content``` objetos da API do SendGrid que compõem o email. Quando você retorna a mensagem, o SendGrid a entrega. 

A assinatura da função também contém um parâmetro de saída adicional do tipo ```Mail``` chamado ```message```. Ambas as associações de entrada e saída expressam a si mesmas como parâmetros de função no código. 

2. Teste seu código clicando em **Testar** e insira uma mensagem no campo **Corpo da solicitação**, então clique no botão **Executar**.

 ![Testar seu código](./media/functions-how-to-use-sendgrid/functions-develop-test-sendgrid.png)

3. Verifique o email para confirmar se o SendGrid enviou o email. Ele deve ir para o endereço no código da etapa 1 e conter a mensagem do **Corpo da solicitação**.

## <a name="next-steps"></a>Próximas etapas
Este artigo demonstrou como usar o serviço SendGrid para criar e enviar email. Para saber mais sobre como usar o Azure Functions em seus aplicativos, consulte os tópicos a seguir: 

- [Melhores Práticas para o Azure Functions](functions-best-practices.md) lista algumas melhores práticas para usar ao criar Azure Functions.

- [Referência do desenvolvedor do Azure Functions](functions-reference.md) Referência do programador para a codificação de funções e a definição de gatilhos e de associações.

- [Testando o Azure Functions](functions-test-a-function.md) Descreve várias ferramentas e técnicas para testar suas funções.