---
title: Modelo de identidade
titleSuffix: An Azure Communication Services concept
description: Saiba mais sobre as identidades e tokens de acesso
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: dd2ffacb176ed3733acba8699d4e870b15dd3c42
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888701"
---
# <a name="identity-model"></a>Modelo de identidade

Os serviços de comunicação do Azure são um serviço independente de identidade. Esse design oferece vários benefícios:

- Reutiliza as identidades existentes do seu sistema de gerenciamento de identidade
- Fornece flexibilidade para cenários de integração
- Mantém suas identidades privadas nos serviços de comunicação do Azure

Em vez de duplicar as informações em seu sistema, você manterá a relação de mapeamento que seu caso comercial exige. Por exemplo, você pode mapear identidades 1:1, 1: N, N:1, N:M. Identificadores externos, como números de telefone, usuários, dispositivos, aplicativos e GUIDs, não podem ser usados para identidade nos serviços de comunicação do Azure. Os tokens de acesso gerados para uma identidade dos serviços de comunicação do Azure são usados para acessar primitivos como chat ou chamada.

## <a name="identity"></a>Identidade

Você pode criar identidades usando a biblioteca de administração dos serviços de comunicação do Azure. Uma identidade serve como um identificador em conversas. Ele é usado para criar tokens de acesso. A mesma identidade pode participar de várias sessões simultâneas em vários dispositivos. Uma identidade pode ter vários tokens de acesso ativos ao mesmo tempo. 

A exclusão de uma identidade, recurso ou assinatura invalida todos os seus tokens de acesso. Essa ação também exclui todos os dados que são armazenados para a identidade. Uma identidade excluída não pode criar novos tokens de acesso ou acessar dados armazenados anteriormente (por exemplo, mensagens de chat). 

Você não é cobrado pelo número de identidades que tem. Em vez disso, você será cobrado pelo uso de primitivos. O número de suas identidades não precisa restringir como você mapeia as identidades de seu aplicativo para as identidades dos serviços de comunicação do Azure. 

Com a liberdade de mapeamento vem a responsabilidade da privacidade. Se um usuário quiser ser excluído do seu sistema, você precisará excluir todas as identidades associadas a esse usuário.

Os serviços de comunicação do Azure não fornecem identidades especiais para usuários anônimos. Ele não mantém o mapeamento entre os usuários e as identidades e não pode determinar se uma identidade é anônima. Você pode criar o conceito de identidade para atender às suas necessidades. Nossa recomendação é criar uma nova identidade para cada usuário anônimo em cada aplicativo. 

Qualquer pessoa que tenha um token de acesso válido pode acessar o conteúdo de identidade atual. Por exemplo, os usuários podem acessar mensagens de bate-papo que enviaram. O acesso é restrito apenas a escopos que fazem parte do token de acesso. Para obter mais informações, consulte a seção [tokens de acesso](#access-tokens) neste artigo.

### <a name="identity-mapping"></a>Mapeamento de identidade

Os serviços de comunicação do Azure não replicam a funcionalidade do sistema de gerenciamento de identidade do Azure. Ele não fornece uma maneira para os clientes usarem identidades específicas do cliente. Por exemplo, os clientes não podem usar um número de telefone ou endereço de email. Em vez disso, os serviços de comunicação do Azure fornecem identificadores exclusivos. Você pode atribuir esses identificadores exclusivos às identidades do seu aplicativo. Os serviços de comunicação do Azure não armazenam qualquer tipo de informação que possa revelar a identidade real dos usuários.

Para evitar a duplicação de informações em seu sistema, planeje como mapear usuários de seu domínio de identidade para identidades dos serviços de comunicação do Azure. Você pode seguir qualquer tipo de padrão. Por exemplo, você pode usar 1:1, 1: N, N:1 ou M:N. Decida se um único usuário está mapeado para uma única identidade ou para várias identidades. 

Quando uma nova identidade é criada, armazene seu mapeamento para o usuário ou usuários do seu aplicativo. Como as identidades exigem tokens de acesso para usar primitivos, a identidade precisa ser conhecida pelo usuário ou usuários do seu aplicativo.

Se você usar um banco de dados relacional para armazenar informações do usuário, poderá ajustar seu design com base no seu cenário de mapeamento. Para cenários que mapeiam 1:1 ou N:1, talvez você queira adicionar uma `CommunicationServicesId` coluna à tabela para armazenar sua identidade dos serviços de comunicação do Azure. Em cenários que usam a relação 1: N ou N:M, você pode considerar a criação de uma tabela separada no banco de dados relacional.

## <a name="access-tokens"></a>Tokens de acesso

Um token de acesso é um JWT (token Web JSON) que pode ser usado para obter acesso aos primitivos do serviço de comunicação do Azure. Um token de acesso emitido tem proteção de integridade. Ou seja, suas declarações não podem ser alteradas após serem emitidas. Portanto, uma alteração manual de propriedades, como identidade, expiração ou escopos, invalidará o token de acesso. Se os primitivos forem usados com Tokens invalidados, o acesso será negado aos primitivos. 

As propriedades de um token de acesso são:
* Identidade.
* Expiration.
* Escopos.

Um token de acesso é sempre válido por 24 horas. Depois de expirar, o token de acesso é invalidado e não pode ser usado para acessar nenhum primitivo. 

Uma identidade precisa de uma maneira de solicitar um novo token de acesso de um serviço do lado do servidor. O parâmetro *Scope* define um conjunto não vazio de primitivos que podem ser usados. Os serviços de comunicação do Azure dão suporte aos seguintes escopos para tokens de acesso.

|Nome|Descrição|
|---|---|
|Chat|  Concede a capacidade de participar de um chat|
|VoIP|  Concede a capacidade de chamar identidades e números de telefone|


Para revogar um token de acesso antes do tempo de expiração, use a biblioteca de administração dos serviços de comunicação do Azure. A revogação de token não é imediata. Leva até 15 minutos para se propagar. A remoção de uma identidade, um recurso ou uma assinatura revoga todos os tokens de acesso. 

Se você quiser remover a capacidade do usuário de acessar a funcionalidade específica, revogue todos os tokens de acesso. Em seguida, emita um novo token de acesso que tenha um conjunto mais limitado de escopos.

Nos serviços de comunicação do Azure, uma rotação de chaves de acesso revoga todos os tokens de acesso ativos que foram criados usando uma tecla de acesso anterior. Todas as identidades perdem o acesso aos serviços de comunicação do Azure e devem emitir novos tokens de acesso. 

É recomendável emitir tokens de acesso em seu serviço do lado do servidor e não no aplicativo do cliente. O raciocínio é que a emissão exige uma chave de acesso ou uma identidade gerenciada. Por motivos de segurança, o compartilhamento de chaves de acesso com o aplicativo do cliente não é recomendado. 

O aplicativo cliente deve usar um ponto de extremidade de serviço confiável que possa autenticar seus clientes. O ponto de extremidade deve emitir tokens de acesso em seu nome. Para obter mais informações, consulte [arquitetura de cliente e servidor](./client-and-server-architecture.md).

Se você armazenar em cache tokens de acesso a um repositório de backup, recomendamos o uso da criptografia. Um token de acesso são dados confidenciais. Ele poderá ser usado para atividades mal-intencionadas se não estiver protegido. Alguém que tem um token de acesso pode iniciar o SDK e acessar a API. A API acessível é restrita apenas com base nos escopos que o token de acesso tem. É recomendável emitir tokens de acesso que tenham apenas os escopos necessários.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução ao gerenciamento de tokens de acesso, consulte [criar e gerenciar tokens de acesso](../quickstarts/access-tokens.md).
* Para obter uma introdução à autenticação, consulte [autenticar para os serviços de comunicação do Azure](./authentication.md).
* Para obter uma introdução à privacidade e à residência de dados, consulte [disponibilidade de região e residência de dados](./privacy.md).