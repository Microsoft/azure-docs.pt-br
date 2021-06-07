---
title: Usar atributos de extensão de esquema do Azure AD em declarações
titleSuffix: Microsoft identity platform
description: Descreve como usar atributos de extensão de esquema de diretório para enviar dados de usuário para aplicativos em declarações de token.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 0127c8d796126d1e99b1fa38a9506df477c7eb49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755728"
---
# <a name="using-directory-schema-extension-attributes-in-claims"></a>Usando atributos de extensão de esquema de diretório em declarações

Os atributos de extensão do esquema de diretório fornecem uma maneira de armazenar dados adicionais em Azure Active Directory objetos de usuário e outros objetos de diretório, como grupos, detalhes de locatário, entidades de serviço.  Somente atributos de extensão em objetos de usuário podem ser usados para emitir declarações para aplicativos. Este artigo descreve como usar atributos de extensão de esquema de diretório para enviar dados de usuário para aplicativos em declarações de token.

> [!NOTE]
> Microsoft Graph fornece dois outros mecanismos de extensão para personalizar objetos de grafo. Elas são conhecidas como extensões Microsoft Graph abertas e extensões de esquema de Microsoft Graph. Consulte a [documentação do Microsoft Graph](/graph/extensibility-overview) para obter detalhes. Os dados armazenados em objetos Microsoft Graph usando esses recursos não estão disponíveis como fontes de declarações em tokens.

Os atributos de extensão de esquema de diretório sempre são associados a um aplicativo no locatário e são referenciados pelo *ApplicationId* do aplicativo em seu nome.

O identificador de um atributo de extensão de esquema de diretório está no formato *Extension_xxxxxxxxx_AttributeName*.  Em que *xxxxxxxxx* é a *ApplicationId* do aplicativo para o qual a extensão foi definida.

## <a name="registering-and-using-directory-schema-extensions"></a>Registrando e usando extensões de esquema de diretório
Os atributos de extensão do esquema de diretório podem ser registrados e preenchidos de uma das duas maneiras:

- Ao configurar o AD Connect para criá-los e sincronizar dados neles no AD local. Consulte [Azure ad Connect as extensões de diretório de sincronização](../hybrid/how-to-connect-sync-feature-directory-extensions.md).
- Usando Microsoft Graph para registrar, definir os valores de e ler de extensões de [esquema](/graph/extensibility-overview). Os [cmdlets do PowerShell](/powershell/azure/active-directory/using-extension-attributes-sample) também estão disponíveis.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-with-ad-connect"></a>Emitindo declarações com dados de atributos de extensão de esquema de diretório criados com o AD Connect
Os atributos de extensão de esquema de diretório criados e sincronizados usando o AD Connect sempre estão associados à ID do aplicativo usada pelo AD Connect. Eles podem ser usados como uma fonte para declarações Configurando-os como declarações na configuração de **aplicativos empresariais** na interface do usuário do portal para aplicativos SAML registrados usando a galeria ou a experiência de configuração de aplicativo não galeria em **aplicativos empresariais** e por meio de uma política de mapeamento de declarações para aplicativos registrados por meio da experiência de registro do aplicativo.  Depois que um atributo de extensão de diretório criado por meio do AD Connect estiver no diretório, ele será exibido na interface do usuário de configuração de declarações de SSO do SAML.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-for-an-application-using-graph-or-powershell"></a>Emitindo declarações com dados de atributos de extensão de esquema de diretório criados para um aplicativo usando o Graph ou o PowerShell
Se um atributo de extensão de esquema de diretório for registrado para um aplicativo usando Microsoft Graph ou PowerShell (por meio de uma configuração inicial de aplicativos ou etapa de provisionamento para a instância), o mesmo aplicativo poderá ser configurado no Azure Active Directory para receber dados nesse atributo de um objeto de usuário em uma declaração quando o usuário entrar.  O aplicativo pode ser configurado para receber dados em extensões de esquema de diretório registradas no mesmo aplicativo usando [declarações opcionais](active-directory-optional-claims.md#configuring-directory-extension-optional-claims).  Eles podem ser definidos no manifesto do aplicativo.  Isso permite que um aplicativo multilocatário Registre atributos de extensão de esquema de diretório para seu próprio uso. Quando o aplicativo é provisionado em um locatário, as extensões de esquema de diretório associadas ficam disponíveis para serem definidas nos usuários desse locatário e para serem consumidas.  Depois de configurado no locatário e no consentimento concedido, ele pode ser usado para armazenar e recuperar dados via grafo e mapear para declarações em tokens que a plataforma de identidade da Microsoft emite para aplicativos.

Os atributos de extensão do esquema de diretório podem ser registrados e preenchidos para qualquer aplicativo.

Se um aplicativo precisar enviar declarações com dados de um atributo de extensão registrado em um aplicativo diferente, uma [política de mapeamento de declarações](active-directory-claims-mapping.md) deverá ser usada para mapear o atributo de extensão para a declaração.  Um padrão comum para gerenciar atributos de extensão de esquema de diretório é criar um aplicativo especificamente para ser o ponto de registro de todas as extensões de esquema de que você precisa.  Não precisa ser um aplicativo real e essa técnica significa que todas as extensões têm a mesma ID de aplicativo em seu nome.

Por exemplo, aqui está uma política de mapeamento de declarações para emitir uma única declaração de um atributo de extensão de esquema de diretório em um token OAuth/OIDC:

```json
{
    "ClaimsMappingPolicy": {
        "Version": 1,
        "IncludeBasicClaimSet": "false",
        "ClaimsSchema": [{
                "Source": "User",
                "ExtensionID": "extension_xxxxxxx_test",
                "JWTClaimType": "http://schemas.contoso.com/identity/claims/exampleclaim"
            },
        ]
    }
}
```

Em que *xxxxxxx* é a ID do aplicativo com a qual a extensão foi registrada.

> [!TIP]
> A consistência de maiúsculas e minúsculas é importante ao definir atributos de extensão de diretório em objetos.  Os nomes de atributo de extensão não diferenciam casos quando são configurados, mas diferenciam maiúsculas de minúsculas quando são lidos do diretório pelo serviço de token.  Se um atributo de extensão for definido em um objeto de usuário com o nome "Legacyid" e em outro objeto de usuário com o nome "legacyid", quando o atributo for mapeado para uma declaração usando o nome "Legacyid", os dados serão recuperados com êxito e a declaração incluída no token para o primeiro usuário, mas não o segundo.
>
> O parâmetro "ID" no esquema de declarações usado para atributos de diretório internos é "ExtensionId" para atributos de extensão de diretório.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [adicionar declarações personalizadas ou adicionais aos tokens SAML 2,0 e JSON Web tokens (JWT)](active-directory-optional-claims.md).
- Saiba como [Personalizar declarações emitidas em tokens para um aplicativo específico](active-directory-claims-mapping.md).