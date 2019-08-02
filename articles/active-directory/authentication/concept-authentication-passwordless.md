---
title: Active Directory sem senha de entrada no Azure (visualização)
description: Logon sem senha no AD do Azure usando chaves de segurança de FIDO2 ou o aplicativo Microsoft Authenticator (visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d80b359be0a6249327ba1ba1d51ffbc330bb073
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712079"
---
# <a name="what-is-passwordless"></a>O que é sem senha?

A autenticação multifator (MFA) é uma ótima maneira de proteger sua organização, mas os usuários obtêm frustrados com a camada adicional sobre ter de lembrar suas senhas. Métodos de autenticação sem senha são mais convenientes, porque a senha foi removida e substituída com algo que você tem mais alguma coisa que você está ou algo que você conhece.

|   | Algo que você tem | Algo que você está ou saber |
| --- | --- | --- |
| Sem senha | Chave de segurança ou telefone | Biometria ou PIN |

Reconhecemos que cada organização tem necessidades diferentes quando se trata de autenticação. A Microsoft atualmente oferece Windows Hello, nossa experiência sem senha principal para o PC do Windows. Estamos adicionando novas credenciais para a família sem senha: Aplicativo Microsoft Authenticator e chaves de segurança FIDO2.

## <a name="microsoft-authenticator-app"></a>Aplicativo Microsoft Authenticator

Permitir que o telefone dos seus funcionários a se tornar um método de autenticação sem senha. Você pode já estar usando o aplicativo Microsoft Authenticator como uma opção conveniente a autenticação multifator, além de uma senha. Mas, agora, está disponível como uma opção sem senha.

Ele transforma qualquer iOS ou telefone Android em uma credencial forte, sem senha permitindo que os usuários entrar em qualquer plataforma ou navegador, obtendo uma notificação para seu telefone, correspondência de um número exibido na tela para um no seu telefone e usando seus biométrica ( tocar ou enfrentam) ou PIN para confirmar.

## <a name="fido2-security-keys"></a>Chaves de segurança FIDO2

As chaves de segurança FIDO2 são um método de autenticação sem senha com base em padrões unphishable que pode ser fornecidos em qualquer fator forma. Rápido identidade FIDO (Online) é um padrão aberto para a autenticação sem senha. Ele permite que os usuários e organizações aproveitem o padrão entrar em seus recursos sem um nome de usuário e uma senha usando uma chave de segurança externa ou uma chave de plataforma em um dispositivo.

Para visualização pública, os funcionários podem usar chaves de segurança externos para entrar em suas máquinas do Azure Active Directory ingressado no Windows 10 (executando a versão 1809 ou superior) e obter o logon único em seus recursos de nuvem. Eles também podem entrar navegadores com suporte.

Embora existam muitas chaves que estão FIDO2 certificada pela FIDO Alliance, a Microsoft exige algumas extensões opcionais da especificação FIDO2 CTAP ser implementado pelo fornecedor para garantir a segurança máxima e a melhor experiência.

Uma chave de segurança **deve** implementar os seguintes recursos e extensões do protocolo FIDO2 CTAP para ser compatível com a Microsoft:

| # | Recurso / extensão de confiança | Por que isso é necessário? |
| --- | --- | --- |
| 1 | Chave residente | Esse recurso permite que a chave de segurança ser portátil, onde sua credencial é armazenada na chave de segurança. |
| 2 | Pin de cliente | Esse recurso permite que você proteja suas credenciais com um segundo fator e se aplica às chaves de segurança que não têm uma interface do usuário. |
| 3 | segredo do HMAC | Essa extensão garante que você pode entrar no seu dispositivo quando ele está offline ou no modo de avião. |
| 4 | Várias contas por RP | Esse recurso garante que você pode usar a mesma chave de segurança em vários serviços, como Account da Microsoft e do Active Directory do Azure. |

Os seguintes provedores oferecem FIDO2 chaves de segurança de diferentes fatores forma que são conhecidos por serem compatíveis com a experiência de paswordless. A Microsoft incentiva os clientes a avaliar as propriedades de segurança dessas chaves, entrando em contato com o fornecedor, bem como FIDO Alliance.

| Provedor | Contato |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

Se você for um fornecedor e deseja obter o dispositivo nessa lista, entre em contato com [ Fido2Request@Microsoft.com ](mailto:Fido2Request@Microsoft.com).

As chaves de segurança FIDO2 são uma ótima opção para empresas que são muito confidenciais de segurança ou ter cenários ou funcionários que não são disposto ou é possível usar o telefone como um segundo fator.

## <a name="what-scenarios-work-with-the-preview"></a>Quais cenários funcionam com a versão prévia?

1. Os administradores podem habilitar métodos de autenticação sem senha para seu locatário
1. Os administradores podem todos os usuários de destino ou selecionar usuários/grupos em seu locatário para cada método
1. Os usuários finais podem registrar e gerenciar esses métodos de autenticação sem senha no seu portal de conta
1. Os usuários finais podem entrar com esses métodos de autenticação sem senha
   1. Aplicativo Microsoft Authenticator: Será o trabalho em todos os cenários em que a autenticação do Azure AD é usada, incluindo em todos os navegadores, durante o Windows 10-Out de instalação da caixa (OOBE) e com integrado aplicativos móveis em qualquer sistema operacional.
   1. Chaves de segurança: Funcionará na tela de bloqueio para Windows 10 versão 1809 ou superior e web em navegadores com suporte, como o Microsoft Edge.

## <a name="next-steps"></a>Próximas etapas

[Habilitar opções sem senha na sua organização](howto-authentication-passwordless-enable.md)

### <a name="external-links"></a>Links externos

[FIDO Alliance](https://fidoalliance.org/)

[Especificação de FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
