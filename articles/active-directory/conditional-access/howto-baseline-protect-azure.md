---
title: Política de linha de base exigir MFA para gerenciamento de serviços (visualização) – Azure Active Directory
description: Política de acesso condicional para exigir a MFA do Azure Resource Manager
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 203b752f9da67ebf60e373fe7ce0893b4fd7fcb5
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560956"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Política de linha de base: Exigir MFA para gerenciamento de serviços (visualização)

Você pode estar usando uma variedade de serviços do Azure em sua organização. Esses serviços podem ser gerenciados por meio da API do Azure Resource Manager:

* Portal do Azure
* Azure PowerShell
* CLI do Azure

Usando o Azure Resource Manager para gerenciar seus serviços é uma ação altamente privilegiada. O Azure Resource Manager pode alterar as configurações de todo o locatário, como configurações de serviço e a cobrança de assinatura. Autenticação de fator único é vulnerável a uma variedade de ataques, como spray de phishing e a senha. Portanto, é importante verificar a identidade dos usuários que desejam acessar o Azure Resource Manager e atualizar as configurações, exigindo a autenticação multifator antes de permitir acesso.

**Exigir MFA para gerenciamento de serviços** é um [política de linha de base](concept-baseline-protection.md) que exigirão MFA para qualquer usuário que está acessando o portal do Azure, Azure PowerShell ou CLI do Azure. Essa política se aplica a todos os usuários acessando o Azure Resource Manager, independentemente de se ele for um administrador.

Depois que essa política está habilitada em um locatário, todos os usuários que fizerem logon em recursos de gerenciamento do Azure serão solicitados com a autenticação multifator. Se o usuário não estiver registrado para MFA, o usuário precisará se registrar usando o aplicativo Microsoft Authenticator para continuar.

Para executar a interativo entrar usando [Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps), use o [Connect AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet.

```PowerShell
Connect-AzAccount
```

Quando executado, esse cmdlet apresentará uma cadeia de caracteres de token. Para entrar, copie essa cadeia de caracteres e cole-o na [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)  em um navegador. Sua sessão do PowerShell será autenticada para conectar o Azure.

Para executar a interativo entrar usando [CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest), execute o [login az](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) comando.

```azurecli
az login
```

Se a CLI pode abrir seu navegador padrão, ela irá fazê-lo e carregar uma página de entrada. Caso contrário, você precisará abrir uma página do navegador e siga as instruções na linha de comando para inserir um código de autorização depois de navegar até [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) no seu navegador. Em seguida, entre com suas credenciais de conta no navegador.

## <a name="deployment-considerations"></a>Considerações de implantação

Porque o **exigir MFA para gerenciamento de serviços** política se aplica a todos os usuários do Azure Resource Manager, várias considerações precisam ser feitas para assegurar uma implantação tranquila. Essas considerações incluem a identificação de usuários e entidades de serviço no Azure AD que não é possível ou não deve realizar a MFA, bem como aplicativos e clientes usados pela sua organização que não dão suporte a autenticação moderna.

## <a name="enable-the-baseline-policy"></a>Habilitar a política de linha de base

A política **política de linha de base: Exigir MFA para gerenciamento de serviços (visualização)** vem pré-configurada e aparecerá na parte superior quando você navega até a folha de acesso condicional no portal do Azure.

Para habilitar essa política e proteger seus administradores:

1. Entrar para o **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **do Azure Active Directory** > **acesso condicional**.
1. Na lista de políticas, selecione **política de linha de base: Exigir MFA para gerenciamento de serviços (visualização)** .
1. Definir **habilitar política** à **usar a política imediatamente**.
1. Clique em **salvar**.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte:

* [Políticas de proteção de linha de base de acesso condicional](concept-baseline-protection.md)
* [Cinco etapas para proteger a infraestrutura de identidade](../../security/azure-ad-secure-steps.md)
* [O que é acesso condicional no Azure Active Directory?](overview.md)