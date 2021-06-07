---
title: Barramento de serviço do Azure-expiração de mensagem
description: Este artigo explica a expiração e a vida útil das mensagens do barramento de serviço do Azure. Após esse prazo, a mensagem não é mais entregue.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: 5d60d84bdc0d437d97c369296a414d55beda4167
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952313"
---
# <a name="message-expiration-time-to-live"></a>Expiração da mensagem (vida útil)
O conteúdo de uma mensagem ou um comando ou consulta que uma mensagem transmite para um destinatário, está quase sempre sujeito a alguma forma de prazo de expiração de nível de aplicativo. Após o prazo, o conteúdo não é mais entregue ou a operação solicitada não é mais executada.

Para ambientes de desenvolvimento e teste em que as consultas e tópicos são frequentemente usadas no contexto de execuções parciais de aplicativos ou partes de aplicativos, também é desejável que as mensagens de teste presas sejam automaticamente coletadas no lixo para que a próxima execução de teste possa começar limpa.

A expiração de qualquer mensagem individual pode ser controlada definindo a propriedade do sistema de **vida** útil, que especifica uma duração relativa. A expiração se torna um instante absoluto quando a mensagem é enfileirada na entidade. Nesse momento, a propriedade **expire-at-UTC** usa o valor **enfileirado-time-UTC**  +  **time-to-Live**. A configuração de tempo de vida (TTL) em uma mensagem orientada não é imposta quando não há clientes ouvindo ativamente.

Após o instante **expirado em UTC** , as mensagens se tornam inqualificadas para recuperação. A expiração não afeta as mensagens que estão bloqueadas para entrega no momento. Essas mensagens ainda são tratadas normalmente. Se o bloqueio expirar ou se a mensagem for abandonada, a expiração entrará em vigor imediatamente.

Enquanto a mensagem está em um bloqueio, o aplicativo pode estar em posse de uma mensagem que expirou. Se o aplicativo está disposto a prosseguir com o processamento ou opta por abandonar a mensagem cabe ao implementador.

O TTL extremamente baixo na ordem de milissegundos ou segundos pode fazer com que as mensagens expirem antes que os aplicativos receptores a recebam. Considere o TTL mais alto que funciona para seu aplicativo.

## <a name="entity-level-expiration"></a>Expiração de nível de entidade
Todas as mensagens enviadas para uma fila ou tópico estão sujeitas a uma expiração padrão definida no nível de entidade. Ele também pode ser definido no portal durante a criação e ajustado posteriormente. A expiração padrão é usada para todas as mensagens enviadas para a entidade em que a vida útil não está definida explicitamente. A expiração padrão também funciona como um teto para o valor de vida útil. As mensagens que têm uma expiração de tempo de vida maior do que o valor padrão são silenciosamente ajustadas para o valor de vida útil da mensagem padrão antes de serem enfileiradas.

> [!NOTE]
> O valor de vida útil padrão para uma mensagem orientada é o maior valor possível para um único inteiro de 64 bits, se não for especificado de outra forma.
>
> Para entidades de mensagens (filas e tópicos), o tempo de expiração padrão também é o maior valor possível para um único inteiro de 64 bits para as camadas Standard e Premium do barramento de serviço. Para a camada **básica** , o tempo de expiração padrão (também máximo) é de **14 dias**.

As mensagens expiradas podem opcionalmente ser movidas para uma fila de mensagens [mortas](service-bus-dead-letter-queues.md). Você pode definir essa configuração programaticamente ou usando o portal do Azure. Se a opção estiver desabilitada, as mensagens expiradas serão descartadas. As mensagens expiradas movidas para a fila de mensagem mortas podem ser diferenciadas de outras mensagens mortas avaliando a propriedade [motivo de mensagens mortas](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) que o agente armazena na seção Propriedades do usuário. 

No caso mencionado acima em que a mensagem está protegida contra expiração enquanto está bloqueada e se o sinalizador está definido na entidade, a mensagem é movida para a fila de mensagens mortas assim que o bloqueio é abandonado ou expira. No entanto, ele não será movido se a mensagem for liquidada com êxito, o que pressupõe que o aplicativo o tratou com êxito, apesar da expiração nominal.

A combinação de vida útil e automática (e transacional) de mensagens mortas na expiração é uma ferramenta valiosa para estabelecer confiança em se um trabalho dado a um manipulador ou um grupo de manipuladores em um prazo é recuperado para processamento à medida que o prazo é atingido.

Por exemplo, considere um site da Web que precisa executar trabalhos de forma confiável em um back-end com restrição de escala e que ocasionalmente enfrente picos de tráfego ou deseje ser isolado contra episódios de disponibilidade de tal back-end. No caso normal, o manipulador do lado do servidor para os dados de usuário enviados envia por push as informações em uma fila e subsequentemente recebe uma resposta confirmando o tratamento bem-sucedido da transação em uma fila de resposta. Se houver um pico de tráfego e o manipulador de back-end não puder processar seus itens de lista de pendências no tempo, os trabalhos expirados serão retornados na fila de mensagens mortas. O usuário interativo pode ser notificado de que a operação solicitada levará um pouco mais do que o normal e a solicitação poderá então ser colocada em uma fila diferente para um caminho de processamento em que o resultado do processamento eventual é enviado para o usuário por email. 


## <a name="temporary-entities"></a>Entidades temporárias

As filas, os tópicos e as assinaturas do barramento de serviço podem ser criados como entidades temporárias, que são automaticamente removidas quando não foram usadas por um período de tempo especificado.
 
A limpeza automática é útil em cenários de desenvolvimento e teste nos quais as entidades são criadas dinamicamente e não são limpas após o uso, devido a alguma interrupção do teste ou da execução da depuração. Isso também é útil quando um aplicativo cria entidades dinâmicas, como uma fila de resposta, para receber respostas de volta para um processo do servidor Web ou em outro objeto de duração relativamente curta, no qual é difícil limpar essas entidades de forma confiável quando a instância do objeto desaparece.

O recurso é habilitado usando a propriedade **exclusão automática em ociosidade** no namespace. Essa propriedade é definida como a duração para a qual uma entidade pode estar ociosa (não utilizada) antes de ser excluída automaticamente. O valor mínimo dessa propriedade é 5 minutos.
 
## <a name="idleness"></a>Ociosidade

Aqui está o que é considerado ociosidade de entidades (filas, tópicos e assinaturas):

- Filas
    - Não há envios  
    - Não recebe  
    - Não há atualizações para a fila  
    - Não há mensagens agendadas  
    - Não pesquisar/inspecionar 
- Tópicos  
    - Não há envios  
    - Não há atualizações para o tópico  
    - Não há mensagens agendadas 
- Assinaturas
    - Não recebe  
    - Não há atualizações para a assinatura  
    - Não há novas regras adicionadas à assinatura  
    - Não pesquisar/inspecionar  
 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
