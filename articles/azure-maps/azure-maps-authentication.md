---
title: Autenticação com mapas do Microsoft Azure
titleSuffix: Azure Maps
description: 'Saiba mais sobre duas maneiras de autenticar solicitações no Azure Maps: autenticação de chave compartilhada e autenticação do Azure Active Directory (AD do Azure).'
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9c973859c8b7a3d04693946f50377837c3538b85
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094088"
---
# <a name="authentication-with-azure-maps"></a>Autenticação com Azure Mapas

O mapas do Azure dá suporte a duas maneiras de autenticar solicitações: autenticação de chave compartilhada e autenticação [do Azure Active Directory (AD do Azure)](../active-directory/fundamentals/active-directory-whatis.md) . Este artigo explica os dois métodos de autenticação para ajudar a orientar sua implementação dos serviços do Azure Maps.

> [!NOTE]
> Para melhorar a comunicação segura com o Azure Maps, agora damos suporte ao protocolo TLS 1,2 e estamos desativando o suporte para TLS 1,0 e 1,1. Se você usa atualmente o TLS 1. x, avalie a preparação do TLS 1,2 e desenvolva um plano de migração com o teste descrito em [resolvendo o problema do tls 1,0](/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Autenticação de Chave Compartilhada

 As chaves primária e secundária são geradas depois que a conta do Azure Maps é criada. Você é incentivado a usar a chave primária como a chave de assinatura ao chamar o Azure Maps com autenticação de chave compartilhada. A autenticação de chave compartilhada passa uma chave gerada por uma conta do Azure Maps para um serviço do Azure Maps. Para cada solicitação para os serviços do Azure Maps, adicione a *chave de assinatura* como um parâmetro à URL. A chave secundária pode ser usada em cenários como alterações de chave sem interrupção.  

Para obter informações sobre como exibir suas chaves no portal do Azure, consulte [gerenciar a autenticação](./how-to-manage-authentication.md#view-authentication-details).

> [!TIP]
> Para fins de segurança, é recomendável que você gire entre as chaves primária e secundária. Para fazer a rotação das chaves, atualize seu aplicativo para usar a chave secundária, implante, em seguida, pressione o botão ciclo/atualizar ao lado da chave primária para gerar uma nova chave primária. A chave primária antiga será desabilitada. Para obter mais informações sobre a rotação de chaves, confira [Configurar o Azure Key Vault com a rotação de chaves e auditoria](../key-vault/secrets/tutorial-rotation-dual.md)

## <a name="azure-ad-authentication"></a>Autenticação do Azure AD

As assinaturas do Azure são fornecidas com um locatário do Azure AD para habilitar o controle de acesso refinado. O Azure Maps oferece autenticação para os serviços do Azure Maps usando o Azure AD. O Azure AD fornece autenticação baseada em identidade para usuários e aplicativos registrados no locatário do Azure AD.

Os Azure Mapas aceitam tokens de acesso **OAuth 2.0** para locatários do Azure AD associados à assinatura do Azure que contêm uma conta dos Azure Mapas. O Azure Maps também aceita tokens para:

* Usuários do Azure AD
* Aplicativos de parceiros que usam permissões delegadas por usuários
* Identidades gerenciadas dos recursos do Azure

Azure Mapas gera um *identificador exclusivo (ID do cliente)* para cada conta do Azure Mapas. Você pode solicitar tokens do Azure AD ao combinar essa ID de cliente com parâmetros adicionais.

Para obter mais informações sobre como configurar o Azure AD e solicitar tokens para os Azure Mapas, confira [Como gerenciar a autenticação nos Azure Mapas](./how-to-manage-authentication.md).

Para obter informações gerais sobre como autenticar com o Azure AD, consulte [o que é autenticação?](../active-directory/develop/authentication-vs-authorization.md).

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identidades gerenciadas para recursos do Azure e os Azure Mapas

[Identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) fornecem serviços do Azure com uma entidade de segurança baseada em aplicativo gerenciado automaticamente, que pode ser autenticada com o Azure AD. Com o Azure RBAC (controle de acesso baseado em função), a entidade de segurança de identidade gerenciada pode ser autorizada a acessar os serviços do Azure Maps. Alguns exemplos de identidades gerenciadas incluem: Azure App Service, Azure Functions e máquinas virtuais do Azure. Para obter uma lista de identidades gerenciadas, consulte [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="configuring-application-azure-ad-authentication"></a>Configurando a autenticação do Azure AD do aplicativo

Os aplicativos serão autenticados com o locatário do Azure AD usando um ou mais cenários com suporte fornecidos pelo Azure AD. Cada cenário de aplicativo do Azure AD representa requisitos diferentes com base nas necessidades de negócios. Alguns aplicativos podem exigir experiências de entrada do usuário e outros aplicativos podem exigir uma experiência de entrada do aplicativo. Para obter mais informações, consulte [fluxos de autenticação e cenários de aplicativos](../active-directory/develop/authentication-flows-app-scenarios.md).

Depois que o aplicativo recebe um token de acesso, o SDK e/ou o aplicativo envia uma solicitação HTTPS com o seguinte conjunto de cabeçalhos HTTP necessários, além de outros cabeçalhos HTTP da API REST:

| Nome do cabeçalho    | Valor               |
| :------------- | :------------------ |
| x-ms-client-id | 30d7cc….9f55        |
| Autorização  | Portador eyJ0e….HNIVN |

> [!NOTE]
> `x-ms-client-id` é o GUID baseado na conta dos Azure Mapas exibido na página de autenticação dos Azure Mapas.

Aqui está um exemplo de uma solicitação de rota do Azure Maps que usa um token de portador OAuth do Azure AD:

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

Para obter informações sobre como exibir a ID do cliente, consulte [Exibir detalhes de autenticação](./how-to-manage-authentication.md#view-authentication-details).

## <a name="authorization-with-role-based-access-control"></a>Autorização com controle de acesso baseado em função

O Azure Maps dá suporte ao acesso a todos os tipos principais para o Azure [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md) , incluindo: usuários individuais do Azure AD, grupos, aplicativos, recursos do Azure e identidades gerenciadas do Azure. Os tipos de entidade são concedidos a um conjunto de permissões, também conhecido como uma definição de função. Uma definição de função fornece permissões para ações da API REST. A aplicação de acesso a uma ou mais contas do Azure Maps é conhecida como um escopo. Ao aplicar uma entidade de segurança, definição de função e escopo, uma atribuição de função é criada. 

As seções a seguir discutem conceitos e componentes da integração do Azure Maps com o RBAC do Azure. Como parte do processo para configurar sua conta do Azure Maps, um diretório do AD do Azure é associado à assinatura do Azure que a conta do Azure Maps reside. 

Ao configurar o RBAC do Azure, você escolhe uma entidade de segurança e a aplica a uma atribuição de função. Para saber como adicionar atribuições de função no portal do Azure, consulte [atribuir funções do Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="picking-a-role-definition"></a>Escolhendo uma definição de função

Os tipos de definição de função a seguir existem para dar suporte a cenários de aplicativo.

| Definição de função do Azure       | Descrição                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Leitor de Dados do Azure Mapas      | Fornece acesso a APIs REST imutáveis do Azure Maps.                                                       |
| Colaborador de dados do Azure Maps | Fornece acesso a APIs REST mutáveis do Azure Maps. A imutabilidade é definida pelas ações: gravar e excluir. |
| Definição de função personalizada      | Crie uma função criada para habilitar o acesso restrito flexível às APIs REST do Azure Maps.                      |

Alguns serviços do Azure Maps podem exigir privilégios elevados para executar ações de gravação ou exclusão em APIs REST do Azure Maps. A função colaborador de dados do Azure Maps é necessária para serviços que fornecem ações de gravação ou exclusão. A tabela a seguir descreve a quais serviços o colaborador de dados do Azure Maps é aplicável ao usar ações de gravação ou exclusão no serviço em questão. Se apenas as ações de leitura forem usadas no serviço, o leitor de dados do Azure Maps poderá ser usado em vez do colaborador de dados do Azure Maps.

| Serviço de mapas do Azure | Definição de função do Azure Maps  |
| :----------------- | :-------------------------- |
| Dados               | Colaborador de dados do Azure Maps |
| Criador            | Colaborador de dados do Azure Maps |
| Espacial            | Colaborador de dados do Azure Maps |

Para obter informações sobre como exibir as configurações do RBAC do Azure, consulte [como configurar o Azure RBAC para mapas do Azure](./how-to-manage-authentication.md).

#### <a name="custom-role-definitions"></a>Definições de função personalizadas

Um aspecto da segurança do aplicativo é aplicar o princípio de privilégios mínimos. Esse princípio implica que a entidade de segurança só deve receber permissão de acesso necessário e não ter acesso adicional. A criação de definições de função personalizadas pode dar suporte a casos de uso que exigem maior granularidade para o controle de acesso. Para criar uma definição de função personalizada, você pode selecionar ações de dados específicas para incluir ou excluir para a definição.

A definição de função personalizada pode então ser usada em uma atribuição de função para qualquer entidade de segurança. Para saber mais sobre as definições de função personalizada do Azure, consulte [funções personalizadas do Azure](../role-based-access-control/custom-roles.md).

Aqui estão alguns cenários de exemplo em que as funções personalizadas podem melhorar a segurança do aplicativo.

| Cenário                                                                                                                                                                                                                 | Ação (ões) de dados de função personalizada                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| Uma página da Web voltada para o público ou de entrada interativa com blocos de mapa base e nenhuma outra API REST.                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| Um aplicativo que requer apenas geocodificação inversa e nenhuma outra API REST.                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| Uma função para uma entidade de segurança que solicita a leitura de dados de mapa baseados no criador do Azure Maps e APIs REST de bloco de mapa base.                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| Uma função para uma entidade de segurança que requer leitura, gravação e exclusão de dados de mapa baseados em criador. Isso pode ser definido como uma função de editor de dados de mapa, mas não permite o acesso a outras APIs REST, como blocos de mapa base. | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>Entendendo o escopo

Ao criar uma atribuição de função, ela é definida na hierarquia de recursos do Azure. Na parte superior da hierarquia há um [grupo de gerenciamento](../governance/management-groups/overview.md) e o mais baixo é um recurso do Azure, como uma conta do Azure Maps.
Atribuir uma atribuição de função a um grupo de recursos pode habilitar o acesso a várias contas ou recursos do Azure Maps no grupo.

> [!TIP]
> A recomendação geral da Microsoft é atribuir acesso ao escopo da conta do Azure Maps porque impede o **acesso não intencional a outras contas do Azure Maps** existentes na mesma assinatura do Azure.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o RBAC do Azure, consulte
> [!div class="nextstepaction"]
> [Controle de acesso baseado em função do Azure](../role-based-access-control/overview.md)

Para saber mais sobre como autenticar um aplicativo com o Azure AD e o Azure Maps, consulte
> [!div class="nextstepaction"]
> [Gerenciar autenticação no Azure Mapas](./how-to-manage-authentication.md)

Para saber mais sobre como autenticar o Controle de Mapeamento do Azure Maps com o Azure AD, consulte
> [!div class="nextstepaction"]
> [Usar o Controle de Mapeamento do Azure Mapas](./how-to-use-map-control.md)