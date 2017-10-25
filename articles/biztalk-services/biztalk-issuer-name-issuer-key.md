---
title: "Nome do emissor e chave do emissor nos Serviços BizTalk | Microsoft Docs"
description: "Saiba como recuperar o nome do emissor e chave do emissor para barramento de serviço ou controle de acesso (ACS) nos serviços BizTalk. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: b9fd985c23558596408e78eadae00dd0f95c4214
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>Serviços BizTalk: nome e chave do emissor

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Os Serviços BizTalk do Azure usam o nome e a chave do emissor do Barramento de Serviço e o nome e a chave do emissor do Controle de Acesso. Especificamente:

| Tarefa | Qual nome e chave do emissor |
| --- | --- |
| Implantando seu aplicativo do Visual Studio |Nome e chave do emissor do Controle de Acesso |
| Configurando o Portal dos Serviços BizTalk do Azure |Nome e chave do emissor do Controle de Acesso |
| Criando retransmissões de LOB com os Serviços do Adaptador do BizTalk no Visual Studio |Nome e chave do emissor do Service Bus |

Este tópico lista as etapas para recuperar o Nome e a Chave do Emissor. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Nome e chave do emissor do Controle de Acesso
O nome e a chave do emissor do Controle de Acesso são usados pelo seguinte:

* Seu aplicativo do Serviço do BizTalk do Azure criado no Visual Studio: para implantar seu aplicativo do Serviço do BizTalk no Visual Studio para Azure, você insere o nome e a chave do emissor do Controle de Acesso. 
* O Portal de Serviços BizTalk do Azure: quando você cria um Serviço BizTalk e abre o Portal de Serviços BizTalk, o Nome de Emissor do Controle de Acesso e a Chave do Emissor são automaticamente registrados para implantações com os mesmos valores de Controle de Acesso.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Obtenha o Nome e a Chave do Emissor do Controle de Acesso

Para usar o ACS para autenticação e obter os valores de Nome do Emissor e Chave do Emissor, as etapas gerais incluem:

1. Instalar os [cmdlets do Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Adicionar sua conta do Azure: `Add-AzureAccount`
3. Retornar o nome da sua assinatura: `get-azuresubscription`
4. Selecionar sua assinatura: `select-azuresubscription <name of your subscription>` 
5. Criar um novo namespace: `new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    Exemplo:    `new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. Quando o novo namespace do ACS for criado (o que pode levar vários minutos), os valores de Nome do Emissor e Chave do Emissor serão listados na cadeia de conexão: 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

Resumidamente:  
Nome do Emissor = SharedSecretIssuer  
Chave do Emissor = SharedSecretKey

Mais no cmdlet [New-AzureSBNamespace](https://msdn.microsoft.com/library/dn495165.aspx). 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Nome e chave do emissor do Service Bus
O nome e a chave do emissor do Barramento de Serviço são usados pelos Serviços do Adaptador do BizTalk. Em seu projeto de Serviços BizTalk no Visual Studio, você usa os Serviços do Adaptador BizTalk para se conectar a um sistema de Linha de Negócios (LOB) local. Para se conectar, você cria a retransmissão de LOB e insere os detalhes do seu sistema de LOB. Ao fazer isso, você também insere o nome e a chave do emissor do Barramento de Serviço.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Para recuperar o nome e a chave do emissor do Barramento de Serviço
1. Entre no [portal clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **Barramento de Serviço**.
3. Selecione seu namespace. Na barra de tarefas, selecione **Informações da Conexão**. Isso exibe o **Emissor Padrão** (Nome do Emissor) e a **Chave Padrão** (Chave do Emissor). Os valores podem ser copiados.  

Resumidamente:  
Nome do Emissor = Emissor Padrão  
Chave do Emissor = Chave Padrão

## <a name="next"></a>Avançar
Tópicos adicionais dos Serviços BizTalk do Azure:

* [Instalando o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Tutoriais: Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Como começar a usar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Consulte também
* [Como usar o Serviço de Gerenciamento do ACS para configurar identidades de serviço](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Serviços BizTalk: provisionamento usando o portal clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [Serviços BizTalk: gráfico do status do provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [Serviços BizTalk: guias Painel, Monitor e Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Serviços BizTalk: backup e restauração](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Serviços BizTalk: limitação](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

