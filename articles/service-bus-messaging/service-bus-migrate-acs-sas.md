---
title: Migrar da autorização do Serviço de Controle de Acesso do Azure Active Directory para Assinatura de Acesso Compartilhado | Microsoft Docs
description: Migrar aplicativos do Serviço de Controle de Acesso para SAS
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: 746b19062c3014caa37c6668e6c41df054a47e25
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868155"
---
# <a name="migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Migrar da autorização do Serviço de Controle de Acesso do Azure Active Directory para Assinatura de Acesso Compartilhado

Anteriormente, os aplicativos do Barramento de Serviço podiam usar dois modelos de autorização diferentes: o modelo do token [SAS (Assinatura de Acesso Compartilhado)](service-bus-sas.md) fornecido diretamente pelo Barramento de Serviço, e um modelo federado no qual o gerenciamento de regras de autorização era feito internamente pelo ACS (Serviço de Controle de Acesso) do [Azure Active Directory](/azure/active-directory/), e os tokens obtidos do ACS eram passados para o Barramento de Serviço para autorização do acesso aos recursos desejados.

O modelo de autorização do ACS foi substituído há tempos pela [autorização SAS](service-bus-authentication-and-authorization.md) como o modelo preferido e toda a documentação, diretrizes e exemplos usam exclusivamente SAS hoje em dia. Além disso, não é mais possível criar novos namespaces do Barramento de Serviço emparelhados com o ACS.

O SAS tem a vantagem de não ser imediatamente dependente de outro serviço, mas pode ser usado diretamente de um cliente sem qualquer intermediário fornecendo ao cliente o acesso ao nome da regra e à chave da regra de SAS. A SAS também pode ser facilmente integrada a uma abordagem na qual um cliente tenha que passar primeiro por uma verificação de autorização com outro serviço para, depois, ter um token emitido. A segunda abordagem é semelhante ao padrão de uso do ACS, mas permite a emissão de tokens de acesso com base nas condições específicas do aplicativo, que são difíceis de expressar no ACS.

Para todos os aplicativos existentes que dependem do ACS, solicitamos que os clientes migrem seus aplicativos para se basearem em SAS em vez disso.

## <a name="migration-scenarios"></a>Cenários de migração

O ACS e o Barramento de Serviço são integrados por meio do conhecimento compartilhado de uma *chave de assinatura*. A chave de assinatura é usada por um namespace do ACS para assinar tokens de autorização, e é usada pelo Barramento de Serviço para verificar se o token foi emitido pelo namespace emparelhado do ACS. O namespace do ACS contém identidades de serviço e as regras de autorização. As regras de autorização definem qual identidade de serviço, ou token emitido pelo provedor de identidade externo, recebe qual tipo de acesso a uma parte do grafo de namespace do Barramento de Serviço, na forma de uma correspondência de prefixo mais longa.

Por exemplo, uma regra do ACS pode conceder a declaração **Enviar** no prefixo de caminho `/` a uma identidade de serviço, o que significa que um token emitido pelo ACS com base nessa regra concede ao cliente os direitos para enviar a todas as entidades no namespace. Se o prefixo de caminho for `/abc`, a identidade ficará restrita ao envio para entidades chamadas `abc` ou organizadas sob esse prefixo. Presume-se que os leitores destas diretrizes de migração já estejam familiarizados com esses conceitos.

Os cenários de migração se enquadram em três categorias amplas:

1.  **Padrões inalterados**. Alguns clientes usam um objeto [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider), passando a identidade de serviço **proprietário** gerada automaticamente, e sua chave secreta, ao namespace do ACS, emparelhado com o namespace do Barramento de Serviço, e não adicionam novas regras.

2.  **Identidades de serviço personalizadas com regras simples**. Alguns clientes adicionam novas identidades de serviço e concedem a cada nova identidade de serviço as permissões **Enviar**, **Escutar** e **Gerenciar** para uma entidade específica.

3.  **Identidades de serviço personalizadas com regras complexas**. Poucos clientes têm conjuntos de regras complexas nas quais os tokens emitidos externamente são mapeados para direitos em Retransmissão ou nas quais uma identidade de serviço individual receba direitos diferenciados em vários caminhos de namespace por meio de várias regras.

Para obter assistência com a migração de conjuntos de regras complexos, contate o [suporte do Azure](https://azure.microsoft.com/support/options/). Os outros dois cenários permitem a migração simples.

### <a name="unchanged-defaults"></a>Padrões inalterados

Se o seu aplicativo não tiver alterado os padrões do ACS, substitua todo uso de [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) por um objeto [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) e use o namespace pré-configurado **RootManageSharedAccessKey** em vez da conta **proprietário** do ACS. Observe que mesmo com a conta **proprietário** do ACS, essa configuração não era (e ainda não é) recomendada, pois essa conta/regra fornece a autoridade de gerenciamento completo sobre o namespace, incluindo permissão para excluir qualquer entidade.

### <a name="simple-rules"></a>Regras simples

Se o aplicativo usa identidades de serviço personalizadas com regras simples, a migração será bem simples no caso em que uma identidade de serviço do ACS tiver sido criada para fornecer controle de acesso em uma fila específica. Esse cenário é frequente em soluções com estilo SaaS, nos quais cada fila é usada como uma ponte para um site de locatário ou filial, e a identidade do serviço é criada para esse site específico. Nesse caso, a identidade de serviço respectiva pode ser migrada para uma regra de Assinatura de Acesso Compartilhado, diretamente na fila. O nome da identidade de serviço pode se tornar o nome da regra de SAS, e a chave de identidade do serviço pode se tornar a chave da regra de SAS. Em seguida, os direitos da regra de SAS são configurados de forma equivalente à regra de ACS aplicável para a entidade.

Você pode fazer essa configuração nova e adicional do SAS localmente, em qualquer namespace existente federado com o ACS e a migração para fora do ACS será executada posteriormente usando [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) em vez de [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). O namespace não precisa ser desvinculado do ACS.

### <a name="complex-rules"></a>Regras complexas

As regras de SAS não devem ser contas, mas são chaves de assinatura nomeadas associadas aos direitos. Assim, cenários nos quais o aplicativo cria várias identidades de serviço e lhes concede direitos de acesso a várias entidades ou ao namespace inteiro, ainda exigirão um intermediário de emissão de token. Você pode obter orientação com relação a esse intermediário [entrando em contato com o suporte](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a autenticação do Barramento de Serviço, confira os tópicos a seguir:

* [Autenticação e autorização do Barramento de Serviço](service-bus-authentication-and-authorization.md)
* [Autenticação do Barramento de Serviço com Assinaturas de Acesso Compartilhado](service-bus-sas.md)

