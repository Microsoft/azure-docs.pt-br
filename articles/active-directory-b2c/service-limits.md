---
title: Limites e restrições do serviço Azure AD B2C
titleSuffix: Azure AD B2C
description: Referência para limites de serviço e restrições para Azure Active Directory B2C serviço.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/02/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 190d88e62069a34b61017a0079f75696d67f6c82
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979905"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Limites e restrições do serviço Azure Active Directory B2C

Este artigo contém as restrições de uso e outros limites de serviço para o serviço de Azure Active Directory B2C (Azure AD B2C).

## <a name="end-userconsumption-related-limits"></a>Limites relacionados ao usuário final/consumo

Os seguintes limites de serviço relacionados ao usuário final se aplicam a todos os protocolos de autenticação e autorização com suporte pelo Azure AD B2C, incluindo SAML, Open ID Connect, OAuth2 e ROPC.

|Categoria |Limite    |
|---------|---------|
|Número de solicitações por endereço IP por locatário de Azure AD B2C       |6000/5 min          |
|Número total de solicitações por locatário de Azure AD B2C     |12000/min          |

O número de solicitações pode variar dependendo do número de leituras e gravações de diretório que ocorrem durante o percurso do usuário Azure AD B2C. Por exemplo, uma jornada de entrada simples que lê a partir do diretório consiste em 1 solicitação. Se a jornada de entrada também precisar atualizar o diretório, essa operação será contada como uma solicitação adicional.

## <a name="azure-ad-b2c-configuration-limits"></a>Limites de configuração de Azure AD B2C

A tabela a seguir lista os limites de configuração administrativa no serviço de Azure AD B2C.

|Categoria  |Limite  |
|---------|---------|
|Número de escopos por aplicativo        |1000          |
|Número de [atributos personalizados](user-profile-attributes.md#extension-attributes)   por usuário <sup>1</sup>       |100         |
|Número de URLs de redirecionamento por aplicativo       |100         |
|Número de URLs de saída por aplicativo        |1          |
|Limite de cadeia de caracteres por atributo      |caracteres de 250          |
|Número de locatários B2C por assinatura      |20         |
|Níveis de [herança](custom-policy-overview.md#inheritance-model) em políticas personalizadas     |10         |
|Número de políticas por locatário de Azure AD B2C      |200          |
|Tamanho máximo do arquivo de política      |400 KB          |

<sup>1</sup> consulte também [limites e restrições de serviço do Azure ad](../active-directory/enterprise-users/directory-service-limits-restrictions.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [diretrizes de limitação do Microsoft Graph](/graph/throttling) 
- Saiba mais sobre as [diferenças de validação para aplicativos Azure ad B2C](../active-directory/develop/supported-accounts-validation.md)













