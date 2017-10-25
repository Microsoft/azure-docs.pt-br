---
title: "Segurança para Hubs de notificação"
description: "Este tópico explica a segurança para hubs de notificação do Azure."
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: 
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 7c3283799806135060bb8ca57ea398c93d1106bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="security"></a>Segurança
## <a name="overview"></a>Visão geral
Este tópico descreve o modelo de segurança dos Hubs de notificação do Azure. Como os Hubs de notificação são uma entidade do barramento de serviço, eles implementam o mesmo modelo de segurança que o barramento de serviço. Para mais informações, consulte os tópicos de [Autenticação do barramento de serviço](https://msdn.microsoft.com/library/azure/dn155925.aspx) .

## <a name="shared-access-signature-security-sas"></a>Segurança de assinatura de acesso compartilhado (SAS)
Os hubs de notificação implementam um esquema de segurança de nível de entidade chamado SAS (assinatura de acesso compartilhado). Esse esquema permite que as entidades de mensagens declarem até 12 regras de autorização nas suas descrições que concedem direitos naquela entidade.

Cada regra contém um nome, um valor de chave (segredo compartilhado) e um conjunto de direitos, conforme explicado na seção “Declarações de segurança”. Ao criar um Hub de notificação, duas regras são automaticamente criadas: uma com direitos de escuta (que usa o aplicativo cliente) e outra com todos os direitos (que usa o back-end).

Ao realizar o gerenciamento de registro dos aplicativos clientes, se as informações enviadas por meio de notificações não forem confidenciais (por exemplo, atualizações de clima), uma maneira comum de acessar um Hub de notificação é fornecer o valor da chave da regra de acesso de somente escuta para o aplicativo cliente e fornecer o valor de chave da regra de acesso completo para o back-end do aplicativo.

Não é recomendável que você insira o valor da chave em aplicativos cliente da Windows Store. Uma maneira de evitar a inserção do valor chave é recuperar o aplicativo cliente do back-end do aplicativo na inicialização.

É importante entender que a chave de acesso de escuta permite que um aplicativo cliente seja registrado para qualquer marca. Se seu aplicativo tiver que restringir registros de marcas específicas para clientes específicos (por exemplo, quando as marcas representam IDs de usuário), o back-end do aplicativo deverá executar os registros. Para mais informações, consulte a seção Gerenciamento de registro. Observe que, dessa maneira, o aplicativo cliente não terá acesso direto aos Hubs de notificação.

## <a name="security-claims"></a>Declarações de segurança
Semelhantes a outras entidades, as operações de Hub de notificação são permitidas para três declarações de segurança: ouvir, enviar e gerenciar.

| Declaração | Descrição | Operações permitidas |
| --- | --- | --- |
| Escutar |Criar/atualizar, ler e excluir registros simples |Criar/Atualizar o registro<br><br>Ler registro<br><br>Ler todos os registros para um identificador<br><br>Excluir registro |
| Enviar |Enviar mensagens ao hub de notificação |Enviar mensagem |
| Gerenciar |CRUDs nos Hubs de notificação (incluindo atualização de credenciais PNS e chaves de segurança) e ler registros baseados em marcas |Criar/Atualizar/Ler/Excluir hubs de notificação<br><br>Ler registros por marca |

Os hubs de notificação aceitam declarações concedidas pelos tokens de controle de acesso do Microsoft Azure e por tokens de assinatura gerados com chaves compartilhadas configuradas diretamente no hub de notificação.

