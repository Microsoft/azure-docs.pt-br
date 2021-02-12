---
title: Abordagens de migração de usuário
titleSuffix: Azure AD B2C
description: Migre contas de usuário de outro provedor de identidade para Azure AD B2C usando os métodos pré-migração de migração ou migração prévia.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 34bc50f5f95725b59c0d2b30b529e12abb6aa7fa
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661146"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Migrar usuários para Azure AD B2C

Migrar de outro provedor de identidade para Azure Active Directory B2C (Azure AD B2C) também pode exigir a migração de contas de usuário existentes. Dois *métodos de migração* são discutidos aqui, pré-migração migração e *migração ininterrupta*. Com qualquer abordagem, você precisa escrever um aplicativo ou script que usa a [API Microsoft Graph](microsoft-graph-operations.md) para criar contas de usuário no Azure ad B2C.

## <a name="pre-migration"></a>Pré-migração

No fluxo de pré-migração, seu aplicativo de migração executa estas etapas para cada conta de usuário:

1. Leia a conta de usuário do provedor de identidade antigo, incluindo suas credenciais atuais (nome de usuário e senha).
1. Crie uma conta correspondente no diretório Azure AD B2C com as credenciais atuais.

Use o fluxo de pré-migração em uma destas duas situações:

- Você tem acesso às credenciais de texto não criptografado de um usuário (seu nome e senha).
- As credenciais são criptografadas, mas você pode descriptografá-las.

Para obter informações sobre como criar contas de usuário programaticamente, consulte [gerenciar contas de usuário Azure ad B2C com Microsoft Graph](microsoft-graph-operations.md).

## <a name="seamless-migration"></a>Migração direta

Use o fluxo de migração contínuo se as senhas de texto não criptografado no provedor de identidade antigo não estiverem acessíveis. Por exemplo, quando:

- A senha é armazenada em um formato criptografado unidirecional, como com uma função de hash.
- A senha é armazenada pelo provedor de identidade herdado de forma que você não possa acessar o. Por exemplo, quando o provedor de identidade valida as credenciais chamando um serviço Web.

O fluxo de migração contínuo ainda requer a migração prévia de contas de usuário, mas, em seguida, usa uma [política personalizada](custom-policy-get-started.md) para consultar uma [API REST](custom-policy-rest-api-intro.md) (que você cria) para definir a senha de cada usuário na primeira entrada.

Assim, o fluxo de migração contínuo tem duas fases: *pré-migração* e *definir credenciais*.

### <a name="phase-1-pre-migration"></a>Fase 1: pré-migração

1. O aplicativo de migração lê as contas de usuário do provedor de identidade antigo.
1. O aplicativo de migração cria contas de usuário correspondentes no diretório Azure AD B2C, mas não *define senhas*.

### <a name="phase-2-set-credentials"></a>Fase 2: definir credenciais

Após a conclusão da migração das contas, sua política personalizada e a API REST executarão o seguinte quando um usuário entrar:

1. Leia a conta de usuário Azure AD B2C correspondente ao endereço de email inserido.
1. Verifique se a conta está sinalizada para migração avaliando um atributo de extensão booliano.
    - Se o atributo de extensão retornar `True` , chame sua API REST para validar a senha em relação ao provedor de identidade herdado.
      - Se a API REST determinar que a senha está incorreta, retorne um erro amigável ao usuário.
      - Se a API REST determinar que a senha está correta, grave a senha na conta de Azure AD B2C e altere o atributo de extensão booliano para `False` .
    - Se o atributo de extensão booliano retornar `False` , continue o processo de entrada normalmente.

Para ver um exemplo de política personalizada e API REST, consulte o [exemplo de migração de usuário contínuo](https://aka.ms/b2c-account-seamless-migration) no github.

![Diagrama de fluxograma da abordagem de migração direta para a migração do usuário](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagrama: fluxo de migração contínuo*

## <a name="best-practices"></a>Práticas recomendadas

### <a name="security"></a>Segurança

A abordagem de migração direta usa sua própria API REST personalizada para validar as credenciais de um usuário no provedor de identidade herdado.

**Você deve proteger sua API REST contra ataques de força bruta.** Um invasor pode enviar várias senhas na esperança de eventualmente adivinhar as credenciais de um usuário. Para ajudar a anular esses ataques, pare de atender solicitações à sua API REST quando o número de tentativas de entrada passa por um certo limite. Além disso, proteja a comunicação entre Azure AD B2C e sua API REST. Para saber como proteger suas APIs RESTful para produção, confira [Proteger API RESTful](secure-rest-api.md).

### <a name="user-attributes"></a>Atributos de usuário

Nem todas as informações no provedor de identidade herdado devem ser migradas para seu diretório Azure AD B2C. Identifique o conjunto apropriado de atributos de usuário para armazenar em Azure AD B2C antes de migrar.

- **Armazenar no** Azure ad B2C
  - Nome de usuário, senha, endereços de email, números de telefone, números de associação/identificadores.
  - Marcadores de consentimento para política de privacidade e contratos de licença de usuário final.
- **Não** armazenar no Azure ad B2C
  - Dados confidenciais, como números de cartão de crédito, SSN (cadastro de seguridade social), registros médicos ou outros dados regulamentados por órgãos governamentais ou de conformidade do setor.
  - Preferências de marketing ou de comunicação, comportamentos do usuário e informações.

### <a name="directory-clean-up"></a>Limpeza de diretório

Antes de iniciar o processo de migração, aproveite a oportunidade de limpar seu diretório.

- Identifique o conjunto de atributos de usuário a ser armazenado em Azure AD B2C e migre apenas o que você precisa. Se necessário, você pode criar [atributos personalizados](user-flow-custom-attributes.md) para armazenar mais dados sobre um usuário.
- Se você estiver migrando de um ambiente com várias fontes de autenticação (por exemplo, cada aplicativo tem seu próprio diretório de usuário), migre para uma conta unificada no Azure AD B2C.
- Se vários aplicativos tiverem nomes de usuário diferentes, você poderá armazená-los em uma conta do Azure AD B2C, usando a coleção de identidades. Em relação à senha, permita que o usuário escolha um e defina-o no diretório. Por exemplo, com a migração direta, somente a senha escolhida deve ser armazenada na conta de Azure AD B2C.
- Remova as contas de usuário não usadas antes da migração ou não migre contas obsoletas.

### <a name="password-policy"></a>Política de senha

Se as contas que você está migrando tiverem uma intensidade de senha mais fraca do que a [força de senha forte](../active-directory/authentication/concept-sspr-policy.md) imposta pelo Azure ad B2C, você poderá desabilitar o requisito de senha forte. Para obter mais informações, consulte [propriedade de política de senha](user-profile-attributes.md#password-policy-attribute).

## <a name="next-steps"></a>Próximas etapas

O repositório [Azure-ad-B2C/User-Migration](https://github.com/azure-ad-b2c/user-migration) no GitHub contém um exemplo de política personalizada de migração direta e uma amostra de código da API REST:

[Política personalizada de migração de usuário direta & exemplo de código da API REST](https://aka.ms/b2c-account-seamless-migration)
