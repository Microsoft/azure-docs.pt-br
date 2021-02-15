---
title: Azure Active Directory entrada sem senha (versão prévia)
description: Saiba mais sobre as opções de entrada sem senha para Azure Active Directory usando as chaves de segurança FIDO2 ou o aplicativo Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1b82dbe169e01b68e7d4b8a4c243cb72d3a3e8b
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012998"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Opções de autenticação com senha para Azure Active Directory

Recursos como a MFA (autenticação multifator) são uma ótima maneira de proteger sua organização, mas os usuários geralmente ficam frustrados com a camada de segurança adicional sobre a existência de se lembrar de suas senhas. Os métodos de autenticação sem senha são mais convenientes porque a senha é removida e substituída por algo que você tem, além de algo que você conhece ou algo que você sabe.

| Autenticação  | Algo que você tem | Algo que você está ou conhece |
| --- | --- | --- |
| Sem senha | Dispositivo, telefone ou chave de segurança do Windows 10 | Biometria ou PIN |

Cada organização tem necessidades diferentes quando se trata de autenticação. A Microsoft oferece as seguintes três opções de autenticação sem senha que se integram ao Azure Active Directory (Azure AD):

- Windows Hello para Empresas
- Aplicativo Microsoft Authenticator
- Chaves de segurança FIDO2

![Autenticação: segurança versus conveniência](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello para Empresas

O Windows Hello para empresas é ideal para operadores de informações que têm seu próprio computador Windows designado. As credenciais biométricas e de PIN estão diretamente ligadas ao computador do usuário, o que impede o acesso de qualquer pessoa que não seja o proprietário. Com a integração de PKI (infraestrutura de chave pública) e suporte interno para SSO (logon único), o Windows Hello para empresas fornece um método conveniente para acessar diretamente os recursos corporativos locais e na nuvem.

![Exemplo de uma entrada de usuário com o Windows Hello para empresas](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

As etapas a seguir mostram como o processo de entrada funciona com o Azure AD:

![Diagrama que descreve as etapas envolvidas para a entrada do usuário com o Windows Hello para empresas](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. Um usuário entra no Windows usando biométrica ou gesto de PIN. O gesto desbloqueia a chave privada do Windows Hello para empresas e é enviado para o provedor de suporte de segurança de autenticação de nuvem, chamado de *provedor de AP de nuvem*.
1. O provedor de AP de nuvem solicita um nonce (um número arbitrário aleatório que pode ser usado apenas uma vez) do Azure AD.
1. O Azure AD retorna um nonce válido por 5 minutos.
1. O provedor de AP de nuvem assina o nonce usando a chave privada do usuário e retorna o nonce assinado para o Azure AD.
1. O Azure AD valida o nonce assinado usando a chave pública registrada com segurança do usuário em relação à assinatura de nonce. Depois de validar a assinatura, o Azure AD validará o nonce assinado retornado. Quando o nonce é validado, o Azure AD cria um PRT (token de atualização principal) com a chave de sessão que é criptografada para a chave de transporte do dispositivo e a retorna para o provedor de AP de nuvem.
1. O provedor de AP de nuvem recebe o PRT criptografado com chave de sessão. Usando a chave de transporte privada do dispositivo, o provedor de AP de nuvem descriptografa a chave de sessão e protege a chave de sessão usando o Trusted Platform Module do dispositivo (TPM).
1. O provedor de AP de nuvem retorna uma resposta de autenticação bem-sucedida para o Windows. O usuário é então capaz de acessar o Windows, bem como aplicativos locais e na nuvem sem a necessidade de autenticar novamente (SSO).

O [Guia de planejamento](/windows/security/identity-protection/hello-for-business/hello-planning-guide) do Windows Hello para empresas pode ser usado para ajudá-lo a tomar decisões sobre o tipo de implantação do Windows Hello para empresas e as opções que você precisará considerar.

## <a name="microsoft-authenticator-app"></a>Aplicativo Microsoft Authenticator

Você também pode permitir que o telefone de seu funcionário se torne um método de autenticação com senha. Talvez você já esteja usando o aplicativo Microsoft Authenticator como uma opção de autenticação multifator conveniente, além de uma senha. Você também pode usar o aplicativo autenticador como uma opção com senha.

![Entrar no Microsoft Edge com o aplicativo Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

O aplicativo autenticador transforma qualquer telefone iOS ou Android em uma credencial forte e com senha. Os usuários podem entrar em qualquer plataforma ou navegador, obtendo uma notificação para seu telefone, correspondendo a um número exibido na tela para aquele em seu telefone e, em seguida, usando sua biométrica (toque ou face) ou PIN para confirmar. Consulte [baixar e instalar o aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) para obter detalhes de instalação.

A entrada sem senha com o aplicativo Microsoft Authenticator para o Azure AD está atualmente em versão prévia. Uso do aplicativo Microsoft Authenticator para autenticação secundária para autenticação multifator do Azure AD, redefinição de senha de autoatendimento (SSPR) ou tokens de software OATH é GA. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A autenticação com senha usando o aplicativo autenticador segue o mesmo padrão básico do Windows Hello para empresas. É um pouco mais complicado, pois o usuário precisa ser identificado para que o Azure AD possa encontrar a versão do aplicativo Microsoft Authenticator que está sendo usada:

![Diagrama que descreve as etapas envolvidas para a entrada do usuário com o aplicativo Microsoft Authenticator](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. O usuário insere seu nome de usuário.
1. O Azure AD detecta que o usuário tem uma credencial forte e inicia o fluxo de credenciais forte.
1. Uma notificação é enviada para o aplicativo via Apple Push Notification Service (APNS) em dispositivos iOS ou por meio do FCM (firebase Cloud Messaging) em dispositivos Android.
1. O usuário recebe a notificação por push e abre o aplicativo.
1. O aplicativo chama o Azure AD e recebe um desafio de prova de presença e um nonce.
1. O usuário conclui o desafio inserindo sua biométrica ou PIN para desbloquear a chave privada.
1. O nonce é assinado com a chave privada e enviado de volta para o Azure AD.
1. O Azure AD executa a validação de chave pública/privada e retorna um token.

Para começar a usar a entrada sem senha, conclua as seguintes instruções:

> [!div class="nextstepaction"]
> [Habilitar o sinal sem senha usando o aplicativo autenticador](howto-authentication-passwordless-phone.md)

## <a name="fido2-security-keys"></a>Chaves de segurança FIDO2

A Aliança do FIDO (Fast IDentity online) ajuda a promover os padrões de autenticação aberta e a reduzir o uso de senhas como uma forma de autenticação. FIDO2 é o padrão mais recente que incorpora o padrão de autenticação na Web (WebAuthn).

As chaves de segurança FIDO2 são um método de autenticação de senha com base em padrões não Phish que pode ser fornecido em qualquer fator forma. A FIDO (Fast Identity online) é um padrão aberto para autenticação com senha. O FIDO permite que usuários e organizações aproveitem o padrão para entrar em seus recursos sem nome de usuário ou senha usando uma chave de segurança externa ou uma chave de plataforma incorporada a um dispositivo.

Os usuários podem registrar e, em seguida, selecionar uma chave de segurança FIDO2 na interface de entrada como o principal meio de autenticação. Essas chaves de segurança FIDO2 normalmente são dispositivos USB, mas também podem usar Bluetooth ou NFC. Com um dispositivo de hardware que manipula a autenticação, a segurança de uma conta é aumentada, pois não há senha que possa ser exposta ou adivinhada.

As chaves de segurança do FIDO2 podem ser usadas para entrar em seus dispositivos Windows 10 do Azure AD ou híbridos ingressados no Azure AD e obter logon único em seus recursos locais e de nuvem. Os usuários também podem entrar em navegadores com suporte. As chaves de segurança do FIDO2 são uma ótima opção para empresas que são muito sensíveis à segurança ou que têm cenários ou funcionários que não estão dispostos ou podem usar seus telefones como um segundo fator.

A entrada com chaves de segurança FIDO2 para o Azure AD está atualmente em versão prévia. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Entrar no Microsoft Edge com uma chave de segurança](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

O processo a seguir é usado quando um usuário entra com uma chave de segurança FIDO2:

![Diagrama que descreve as etapas envolvidas para a entrada do usuário com uma chave de segurança FIDO2](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. O usuário conecta a chave de segurança FIDO2 em seu computador.
2. O Windows detecta a chave de segurança FIDO2.
3. O Windows envia uma solicitação de autenticação.
4. O Azure AD envia de volta um nonce.
5. O usuário conclui seu gesto para desbloquear a chave privada armazenada na enclave segura da chave de segurança do FIDO2.
6. A chave de segurança FIDO2 assina o nonce com a chave privada.
7. A solicitação de token de PRT (token de atualização principal) com nonce assinado é enviada ao Azure AD.
8. O Azure AD verifica o nonce assinado usando a chave pública FIDO2.
9. O Azure AD retorna o PRT para habilitar o acesso a recursos locais.

Embora existam muitas chaves que são FIDO2 certificadas pela Aliança de FIDO, a Microsoft exige que algumas extensões opcionais da especificação CTAP (FIDO2 Client-to-Authenticator Protocol) sejam implementadas pelo fornecedor para garantir a segurança máxima e a melhor experiência.

Uma chave de segurança **deve** implementar os seguintes recursos e extensões do protocolo FIDO2 CTAP para ser compatível com a Microsoft:

| # | Confiança de recurso/extensão | Por que esse recurso ou extensão é necessário? |
| --- | --- | --- |
| 1 | Chave residente | Esse recurso permite que a chave de segurança seja portátil, em que sua credencial é armazenada na chave de segurança. |
| 2 | PIN do cliente | Esse recurso permite que você proteja suas credenciais com um segundo fator e se aplica a chaves de segurança que não têm uma interface do usuário. |
| 3 | HMAC-segredo | Essa extensão garante que você possa entrar em seu dispositivo quando ele estiver offline ou no modo avião. |
| 4 | Várias contas por RP | Esse recurso garante que você possa usar a mesma chave de segurança em vários serviços, como conta da Microsoft e Azure Active Directory. |

### <a name="fido2-security-key-providers"></a>Provedores de chave de segurança FIDO2

Os provedores a seguir oferecem chaves de segurança FIDO2 de fatores forma diferentes que são conhecidos como compatíveis com a experiência sem senha. Incentivamos você a avaliar as propriedades de segurança dessas chaves contatando o fornecedor, bem como a FIDO Alliance.

| Provedor | Contact |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://ftsafe.us/pages/microsoft](https://ftsafe.us/pages/microsoft) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| Soluções de TrustKey | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (grupo Thales) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc. | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) |
| Hypersecu | [https://www.hypersecu.com/hyperfido](https://www.hypersecu.com/hyperfido) |
| VinCSS | [https://passwordless.vincss.net](https://passwordless.vincss.net) |
| KONA I | [https://konai.com/business/security/fido](https://konai.com/business/security/fido) |
| Excelsecu | [https://www.excelsecu.com/productdetail/esecufido2secu.html](https://www.excelsecu.com/productdetail/esecufido2secu.html) |
| Token2 Suíça | [https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key](https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key) |

> [!NOTE]
> Se você comprar e planejar usar chaves de segurança baseadas em NFC, precisará de um leitor de NFC com suporte para a chave de segurança. O leitor NFC não é um requisito ou limitação do Azure. Consulte o fornecedor da sua chave de segurança baseada em NFC para obter uma lista de leitores NFC com suporte.

Se você for um fornecedor e quiser obter seu dispositivo na lista de dispositivos com suporte, entre em contato com [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com) .

Para começar a usar as chaves de segurança do FIDO2, conclua as seguintes instruções:

> [!div class="nextstepaction"]
> [Habilitar o sinal sem senha usando chaves de segurança FIDO2](howto-authentication-passwordless-security-key.md)

## <a name="what-scenarios-work-with-the-preview"></a>Quais cenários funcionam com a versão prévia?

Os recursos de entrada sem senha do Azure AD estão atualmente em versão prévia. As seguintes considerações se aplicam:

- Os administradores podem habilitar métodos de autenticação com senha para seus locatários
- Os administradores podem direcionar todos os usuários ou Selecionar usuários/grupos dentro de seu locatário para cada método
- Os usuários finais podem registrar e gerenciar esses métodos de autenticação com senha em seu portal de conta
- Os usuários finais podem entrar com esses métodos de autenticação sem senha
   - Microsoft Authenticator aplicativo: funciona em cenários em que a autenticação do Azure AD é usada, inclusive em todos os navegadores, durante a instalação do OOBE (Windows 10) e com aplicativos móveis integrados em qualquer sistema operacional.
   - Chaves de segurança: trabalhe na tela de bloqueio para o Windows 10 e a Web em navegadores com suporte, como o Microsoft Edge (a borda herdada e nova).

## <a name="choose-a-passwordless-method"></a>Escolha um método com senha

A escolha entre essas três opções de senha depende dos requisitos de segurança, plataforma e aplicativo da sua empresa.

Aqui estão alguns fatores a serem considerados ao escolher a tecnologia com senha da Microsoft:

||**Windows Hello para empresas**|**Entrada sem senha com o aplicativo Microsoft Authenticator**|**Chaves de segurança FIDO2**|
|:-|:-|:-|:-|
|**Pré-requisito**| Windows 10, versão 1809 ou posterior<br>Azure Active Directory| Aplicativo Microsoft Authenticator<br>Telefone (dispositivos iOS e Android que executam o Android 6,0 ou superior.)|Windows 10, versão 1903 ou posterior<br>Azure Active Directory|
|**Modo**|Plataforma|Software|Hardware|
|**Sistemas e dispositivos**|PC com um Trusted Platform Module interno (TPM)<br>Reconhecimento de PIN e Biometria |Reconhecimento de PIN e biometria no telefone|Dispositivos de segurança FIDO2 que são compatíveis com a Microsoft|
|**Experiência do usuário**|Entre usando um PIN ou um reconhecimento biométrico (facial, íris ou impressão digital) com dispositivos Windows.<br>A autenticação do Windows Hello está vinculada ao dispositivo; o usuário precisa do dispositivo e de um componente de entrada, como um PIN ou um fator biométrico para acessar recursos corporativos.|Entre usando um telefone celular com verificação de impressão digital, reconhecimento facial ou íris ou PIN.<br>Os usuários entram na conta corporativa ou pessoal de seu PC ou telefone celular.|Entrar usando o dispositivo de segurança FIDO2 (biometria, PIN e NFC)<br>O usuário pode acessar o dispositivo com base nos controles da organização e autenticar com base no PIN, a biometria usando dispositivos como chaves de segurança USB e cartões inteligentes, chaves ou wearables habilitados para NFC.|
|**Cenários habilitados**| Experiência sem senha com o dispositivo Windows.<br>Aplicável a um PC de trabalho dedicado com capacidade de logon único para dispositivos e aplicativos.|Solução sem senha em qualquer lugar usando o telefone celular.<br>Aplicável para acessar aplicativos pessoais ou de trabalho na Web de qualquer dispositivo.|Experiência sem senha para trabalhadores que usam biometria, PIN e NFC.<br>Aplicável a PCs compartilhados e onde um telefone celular não é uma opção viável (por exemplo, para pessoal de suporte técnico, quiosque público ou equipe de hospital)|

Use a tabela a seguir para escolher qual método dará suporte a seus requisitos e usuários.

|Persona|Cenário|Ambiente|Tecnologia com senha|
|:-|:-|:-|:-|
|**Administrador**|Proteger o acesso a um dispositivo para tarefas de gerenciamento|Dispositivo Windows 10 atribuído|Chave de segurança do Windows Hello para empresas e/ou FIDO2|
|**Administrador**|Tarefas de gerenciamento em dispositivos que não são do Windows| Dispositivo móvel ou não Windows|Entrada sem senha com o aplicativo Microsoft Authenticator|
|**Operador de informações**|Trabalho de produtividade|Dispositivo Windows 10 atribuído|Chave de segurança do Windows Hello para empresas e/ou FIDO2|
|**Operador de informações**|Trabalho de produtividade| Dispositivo móvel ou não Windows|Entrada sem senha com o aplicativo Microsoft Authenticator|
|**Frente Worker**|Quiosques em uma fábrica, fábrica, varejo ou entrada de dados|Dispositivos Windows 10 compartilhados|Chaves de segurança do FIDO2|

## <a name="next-steps"></a>Próximas etapas

Para começar a usar sem senha no Azure AD, conclua um dos seguintes procedimentos:

* [Habilitar entrada sem senha da chave de segurança do FIDO2](howto-authentication-passwordless-security-key.md)
* [Habilitar a entrada sem senha baseada em telefone com o aplicativo autenticador](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Links externos

* [FIDO Alliance](https://fidoalliance.org/)
* [Especificação CTAP FIDO2](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
