---
title: Modelo de segurança dos Hubs de Notificação
description: Saiba mais sobre o modelo de segurança para os hubs de notificação do Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: 07600b1fe0cb7420989fbbfbe55c2f1a4197d2fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100548243"
---
# <a name="notification-hubs-security"></a>Segurança dos hubs de notificação

## <a name="overview"></a>Visão geral

Este tópico descreve o modelo de segurança dos Hubs de notificação do Azure.

## <a name="shared-access-signature-security"></a>Segurança de assinatura de acesso compartilhado

Os hubs de notificação implementam um esquema de segurança em nível de entidade chamado de SAS ( *assinatura de acesso compartilhado* ). Cada regra contém um nome, um valor de chave (segredo compartilhado) e um conjunto de direitos, conforme explicado posteriormente em [declarações de segurança](#security-claims). 

Ao criar um Hub, duas regras são criadas automaticamente: uma com direitos de **escuta** (que o aplicativo cliente usa) e outra com **todos os** direitos (que o back-end do aplicativo usa):

- **DefaultListenSharedAccessSignature**: concede somente a permissão **Listen** .
- **DefaultFullSharedAccessSignature**: concede permissões de **escuta**, **Gerenciamento** e **envio** . Essa política deve ser usada somente no back-end do aplicativo. Não usá-lo em aplicativos cliente; Use uma política somente com acesso de **escuta** . Para criar uma nova política de acesso personalizada com um novo token SAS, consulte [tokens SAS para políticas de acesso](#sas-tokens-for-access-policies) mais adiante neste artigo.

Ao realizar o gerenciamento de registro dos aplicativos clientes, se as informações enviadas por meio de notificações não forem confidenciais (por exemplo, atualizações de clima), uma maneira comum de acessar um Hub de notificação é fornecer o valor da chave da regra de acesso de somente escuta para o aplicativo cliente e fornecer o valor de chave da regra de acesso completo para o back-end do aplicativo.

Os aplicativos não devem inserir o valor de chave em aplicativos cliente da Windows Store; em vez disso, faça com que o aplicativo cliente a recupere do back-end do aplicativo na inicialização.

A chave com acesso de **escuta** permite que um aplicativo cliente se registre em qualquer marca. Se seu aplicativo precisar restringir registros a marcas específicas para clientes específicos (por exemplo, quando as marcas representam IDs de usuário), o back-end do aplicativo deve executar os registros. Para obter mais informações, consulte [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md). Observe que, dessa maneira, o aplicativo cliente não terá acesso direto aos Hubs de notificação.

## <a name="security-claims"></a>Declarações de segurança

Semelhante a outras entidades, as operações do hub de notificação são permitidas para três declarações de segurança: **escutar**, **Enviar** e **gerenciar**.

| Declaração   | Descrição                                          | Operações permitidas |
| ------- | ---------------------------------------------------- | ------------------ |
| Escutar  | Criar/atualizar, ler e excluir registros simples | Criar/Atualizar o registro<br><br>Ler registro<br><br>Ler todos os registros para um identificador<br><br>Excluir registro |
| Enviar    | Enviar mensagens para o Hub de notificação                | Enviar mensagem |
| Gerenciar  | CRUDs nos Hubs de notificação (incluindo atualização de credenciais PNS e chaves de segurança) e ler registros baseados em marcas |Criar/atualizar/ler/excluir hubs<br><br>Ler registros por marca |

Os hubs de notificação aceitam tokens SAS gerados com chaves compartilhadas configuradas diretamente no Hub.

Não é possível enviar uma notificação para mais de um namespace. Os namespaces são contêineres lógicos para hubs de notificação e não estão envolvidos no envio de notificações.

Usar as políticas de acesso no nível de namespace (credenciais) para operações em nível de namespace; por exemplo: listando hubs, criando ou excluindo hubs, etc. Somente as políticas de acesso no nível do Hub permitem enviar notificações.

### <a name="sas-tokens-for-access-policies"></a>Tokens SAS para políticas de acesso

Para criar uma nova declaração de segurança ou para exibir as chaves SAS existentes, faça o seguinte:

1. Entre no portal do Azure.
2. Selecione **Todos os recursos**.
3. Selecione o nome do hub de notificação para o qual você deseja criar a declaração ou exibir a chave SAS.
4. No menu à esquerda, selecione políticas de **acesso**.
5. Selecione **nova política** para criar uma nova declaração de segurança. Dê um nome à política e selecione as permissões que deseja conceder. Depois, selecione **OK**.
6. A cadeia de conexão completa (incluindo a nova chave SAS) é exibida na janela políticas de acesso. Você pode copiar essa cadeia de caracteres para a área de transferência para uso posterior.

Para extrair a chave SAS de uma política específica, selecione o botão **copiar** ao lado da política que contém a chave SAS desejada. Cole esse valor em um local temporário e copie a parte da chave SAS da cadeia de conexão. Este exemplo usa um namespace de hubs de notificação chamado **mytestnamespace1** e uma política chamada **policy2**. A chave SAS é o valor próximo ao final da cadeia de caracteres, especificado por **SharedAccessKey**:

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![Obter chaves SAS](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos hubs de notificação](notification-hubs-push-notification-overview.md)
