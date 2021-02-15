---
title: Visão geral das chaves de política-Azure Active Directory B2C
description: Saiba mais sobre os tipos de chaves de política de criptografia que podem ser usadas em Azure Active Directory B2C para assinatura e validação de tokens, segredos de cliente, certificados e senhas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30348d7ca12ded2d1f4b0522a7cabeadf0553a07
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953348"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Visão geral das chaves de política no Azure Active Directory B2C

O Azure Active Directory B2C (Azure AD B2C) armazena os segredos e certificados na forma de chaves de política para estabelecer confiança com os serviços com os quais ele se integra. Essas relações de confiança consistem em:

- Provedores de identidade externos
- Conectando-se com os [serviços de API REST](restful-technical-profile.md)
- Criptografia e assinatura de token

 Este artigo discute o que você precisa saber sobre as chaves de política que são usadas pelo Azure AD B2C.

> [!NOTE]
> Atualmente, a configuração de chaves de política é limitada apenas a [políticas personalizadas](./custom-policy-get-started.md) .

Você pode configurar segredos e certificados para estabelecer confiança entre serviços no portal do Azure no menu de **chaves de política** . As chaves podem ser simétricas ou assimétricas. A criptografia *simétrica* ou a criptografia de chave privada é onde um segredo compartilhado é usado para criptografar e descriptografar os dados. A criptografia *assimétrica* , ou criptografia de chave pública, é um sistema criptográfico que usa pares de chaves, que consistem em chaves públicas que são compartilhadas com o aplicativo de terceira parte confiável e chaves privadas que são conhecidas apenas para Azure ad B2C.

## <a name="policy-keyset-and-keys"></a>Chave e conjunto de chaves de política

O recurso de nível superior para chaves de política no Azure AD B2C é o contêiner do conjunto de **chaves** . Cada conjunto de chaves contém pelo menos uma **chave**. Uma chave tem os seguintes atributos:

| Atributo |  Obrigatório | Comentários |
| --- | --- |--- |
| `use` | Yes | Uso: identifica o uso pretendido da chave pública. Criptografar dados `enc` ou verificar a assinatura nos dados `sig` .|
| `nbf`| No | Data e hora de ativação. |
| `exp`| No | Data e hora de expiração. |

É recomendável definir os valores de ativação e de validade de chave de acordo com seus padrões de PKI. Talvez seja necessário girar esses certificados periodicamente por motivos de segurança ou política. Por exemplo, você pode ter uma política para girar todos os seus certificados todos os anos.

Para criar uma chave, você pode escolher um dos seguintes métodos:

- **Manual** -crie um segredo com uma cadeia de caracteres que você define. O segredo é uma chave simétrica. Você pode definir as datas de ativação e validade.
- **Gerado** -gerar uma chave automaticamente. Você pode definir as datas de ativação e validade. Há duas opções:
  - **Secret** – gera uma chave simétrica.
  - **RSA** – gera um par de chaves (chaves assimétricas).
- **Carregar** – carregar um certificado ou uma chave PKCS12. O certificado deve conter as chaves pública e privada (chaves assimétricas).

## <a name="key-rollover"></a>Substituição de chave

Para fins de segurança, Azure AD B2C pode sobrepor chaves periodicamente ou imediatamente em caso de emergência. Qualquer aplicativo, provedor de identidade ou API REST que se integre com Azure AD B2C deve estar preparado para lidar com um evento de substituição de chave, independentemente da frequência que possa ocorrer. Caso contrário, se seu aplicativo ou Azure AD B2C tentar usar uma chave expirada para executar uma operação criptográfica, a solicitação de entrada falhará.

Se um conjunto de chaves Azure AD B2C tiver várias chaves, somente uma das chaves estará ativa a qualquer momento, com base nos seguintes critérios:

- A ativação da chave é baseada na **data de ativação**.
  - As chaves são classificadas por data de ativação em ordem crescente. As chaves com datas de ativação ainda mais no futuro aparecem mais abaixo na lista. As chaves sem uma data de ativação estão localizadas na parte inferior da lista.
  - Quando a data e a hora atuais forem maiores que a data de ativação de uma chave, Azure AD B2C ativará a chave e interromperá o uso da chave ativa anterior.
- Quando o tempo de expiração da chave atual tiver decorrido e o contêiner de chave contiver uma nova chave com os tempos de validade e *não antes* e de *expiração* , a nova chave se tornará ativa automaticamente.
- Quando o tempo de expiração da chave atual tiver decorrido e o contêiner de chave *não* contiver uma nova chave com os tempos de *expiração* e *não antes* e de vencimento, Azure ad B2C não poderá usar a chave expirada. Azure AD B2C gerará uma mensagem de erro dentro de um componente dependente de sua política personalizada. Para evitar esse problema, você pode criar uma chave padrão sem datas de ativação e expiração como uma rede de segurança.
- O ponto de extremidade da chave (URI JWKS) do ponto de extremidade de configuração bem conhecido do OpenId Connect reflete as chaves configuradas no contêiner de chave, quando a chave é referenciada no [perfil técnico do JwtIssuer](./jwt-issuer-technical-profile.md). Um aplicativo que usa uma biblioteca OIDC irá buscar automaticamente esses metadados para garantir que ele use as chaves corretas para validar os tokens. Para obter mais informações, saiba como usar a [biblioteca de autenticação da Microsoft](../active-directory/develop/msal-b2c-overview.md), que sempre busca automaticamente as chaves de assinatura de token mais recentes.

## <a name="policy-key-management"></a>Gerenciamento de chaves de política

Para obter a chave ativa atual em um contêiner de chave, use o ponto de extremidade Microsoft Graph API [getActiveKey](/graph/api/trustframeworkkeyset-getactivekey) .

Para adicionar ou excluir chaves de criptografia e assinatura:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, procure e selecione **Azure AD B2C**.
1. Na página Visão Geral, em **Políticas**, selecione **Identity Experience Framework**.
1. Selecionar **chaves de política** 
    1. Para adicionar uma nova chave, selecione **Adicionar**.
    1. Para remover uma nova chave, selecione a chave e, em seguida, selecione **excluir**. Para excluir a chave, digite o nome do contêiner de chave a ser excluído. Azure AD B2C excluirá a chave e criará uma cópia da chave com o sufixo. bak.

### <a name="replace-a-key"></a>Substituir uma chave

As chaves em um conjunto de chaves não são substituíveis ou removíveis. Se você precisar alterar uma chave existente:

- É recomendável adicionar uma nova chave com a **data de ativação** definida para a data e hora atuais. Azure AD B2C ativará a nova chave e irá parar de usar a chave ativa anterior.
- Como alternativa, você pode criar um novo conjunto de chaves com as chaves corretas. Atualize sua política para usar o novo conjunto de chaves e, em seguida, remova o conjunto de chaves antigo. 

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar o Microsoft Graph para automatizar um [conjunto](microsoft-graph-operations.md#trust-framework-policy-keyset) de chaves e a implantação de [chave de política](microsoft-graph-operations.md#trust-framework-policy-key) .