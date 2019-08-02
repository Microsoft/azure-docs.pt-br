---
title: O Token de atualização principal (PRT) e o Azure AD - Azure Active Directory
description: Qual é a função do e como podemos gerenciar o primário atualizar Token PRT () no Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e195a93209875b9eabfaa2ad00772281922443c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476106"
---
# <a name="what-is-a-primary-refresh-token"></a>O que é um primário Token de atualização?

Um principal de atualização Token PRT () é um artefato de chave de autenticação do AD do Azure em dispositivos Android, iOS e Windows 10. É um JSON Web Token (JWT) emitidos especialmente para Microsoft primeiro terceiros token corretores para habilitar o logon único (SSO) entre os aplicativos usados nesses dispositivos. Neste artigo, forneceremos detalhes sobre como um PRT é emitido, usado e protegido em dispositivos Windows 10.

Este artigo pressupõe que você já compreende os estados de dispositivo diferentes disponíveis no Azure AD e como único logon funciona no Windows 10. Para obter mais informações sobre os dispositivos no Azure AD, consulte o artigo [o que é gerenciamento de dispositivo no Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Componentes e terminologia principal

Os seguintes componentes do Windows desempenham um papel importante na solicitação e usando um PRT:

* **Provedor de autenticação de nuvem** (CloudAP): CloudAP é o provedor de autenticação moderna para o Windows entrar, que verifica se os usuários que fizerem logon em um dispositivo Windows 10. CloudAP fornece uma estrutura de plug-in essa identidade provedores podem se basear para habilitar a autenticação Windows usando credenciais do provedor de identidade.
* **Gerente de conta da Web** (WAM): WAM é o agente de token padrão em dispositivos Windows 10. WAM também fornece uma estrutura de plug-in essa identidade provedores podem criar e habilitar o SSO para seus aplicativos que dependem desse provedor de identidade.
* **Plug-in do Azure AD CloudAP**: Um Azure AD específico plug-in baseado no framework CloudAP, que verifica as credenciais do usuário com o Azure AD durante a entrada Windows.
* **Plug-in do Azure AD WAM**: Um Azure AD específico plug-in baseado no framework WAM, que habilita o SSO para aplicativos que dependem do Azure AD para autenticação.
* **Dsreg**: Um componente específico do AD do Azure no Windows 10, que manipula o processo de registro de dispositivo para todos os estados de dispositivo.
* **Trusted Platform Module** (TPM): Um TPM é um componente de hardware em um dispositivo, que fornece funções de segurança com base em hardware para segredos de usuário e dispositivo. Mais detalhes podem ser encontrados no artigo [Trusted Platform Module visão geral da tecnologia](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>O que o PRT contém?

Um PRT contém declarações geralmente contidas em qualquer token de atualização do AD do Azure. Além disso, há algumas declarações específicas de dispositivo incluídas no PRT. Elas são as seguintes:

* **ID do dispositivo**: Um PRT é emitido para um usuário em um dispositivo específico. A declaração de ID de dispositivo `deviceID` determina o PRT foi emitido para o usuário no dispositivo. Posteriormente, essa declaração é emitida para tokens obtidos por meio de PRT. A declaração de ID do dispositivo é usada para determinar a autorização para acesso condicional com base no estado do dispositivo ou conformidade.
* **Chave de sessão**: A chave de sessão é uma chave simétrica criptografada, gerado pelo serviço de autenticação do AD do Azure, emitido como parte do PRT. A chave de sessão atua como a prova de posse quando um PRT é usado para obter tokens para outros aplicativos.

### <a name="can-i-see-whats-in-a-prt"></a>Pode ver o que está em um PRT?

Um PRT é um blob opaco enviado do Azure AD cujo conteúdo não é conhecido para todos os componentes cliente. Você não pode ver o que está dentro de um PRT.

## <a name="how-is-a-prt-issued"></a>Como um PRT é emitido?

O registro de dispositivo é um pré-requisito para a autenticação de dispositivo com base no Azure AD. Um PRT é emitido para usuários somente em dispositivos registrados. Para obter mais detalhes sobre o registro de dispositivo, consulte o artigo [Windows Hello for Business e o registro de dispositivo](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Durante o registro de dispositivo, o componente dsreg gera dois conjuntos de pares de chaves de criptografia:

* Chave do dispositivo (dkpub/dkpriv)
* Chave de transporte (tkpub/tkpriv)

As chaves privadas são vinculadas ao TPM do dispositivo se o dispositivo tiver um TPM válido em funcionamento, enquanto as chaves públicas são enviadas para o Azure AD durante o processo de registro do dispositivo. Essas chaves são usadas para validar o estado do dispositivo durante solicitações PRT.

O PRT é emitida durante a autenticação de usuário em um dispositivo Windows 10 em dois cenários:

* **Ingressado no Azure AD** ou **híbrido**: Um PRT é emitido durante o logon do Windows, quando um usuário entra com suas credenciais da organização. Por exemplo, um PRT é emitido com todas as credenciais do Windows 10 com suporte, senha e o Windows Hello para empresas. Nesse cenário, o plug-in do Azure AD CloudAP é a autoridade primária para o PRT.
* **Dispositivos registrados no Azure AD**: Um PRT é emitido quando um usuário adiciona uma conta de trabalho secundário para seu dispositivo Windows 10. Os usuários podem adicionar uma conta para o Windows 10 de duas maneiras diferentes-  
   * Adicionando uma conta por meio de **Use essa conta em qualquer lugar neste dispositivo** prompt depois de entrar um aplicativo (por exemplo, Outlook)
   * Adicionando uma conta do **as configurações** > **contas** > **acessar trabalho ou escola** > **Connect**

Em cenários de dispositivos registrados no Azure AD, o plug-in do Azure AD WAM é a autoridade primária para o PRT como logon do Windows não está acontecendo com essa conta do AD do Azure.

> [!NOTE]
> 3º provedores de identidade de terceiros precisam oferecer suporte ao protocolo WS-Trust, para permitir a emissão de PRT em dispositivos Windows 10. Sem o WS-Trust, PRT não podem ser emitidas para os usuários no Azure híbrido ingressado no AD ou AD do Azure ingressado em dispositivos

## <a name="what-is-the-lifetime-of-a-prt"></a>O que é o tempo de vida de um PRT?

Depois de emitido, um PRT é válido por 14 dias e é renovada continuamente desde que o usuário usa ativamente o dispositivo.  

## <a name="how-is-a-prt-used"></a>Como um PRT é usado?

Um PRT é usado por dois componentes principais no Windows:

* **Plug-in do Azure AD CloudAP**: Durante a inscrição do Windows no, o plug-in do Azure AD CloudAP solicita um PRT do AD do Azure usando as credenciais fornecidas pelo usuário. Ele também armazena em cache o PRT para habilitar a entrada em cache quando o usuário não tem acesso a uma conexão de internet.
* **Plug-in do Azure AD WAM**: Quando os usuários tentam acessar os aplicativos, o plug-in do Azure AD WAM usa o PRT para habilitar o SSO no Windows 10. Plug-in do Azure AD WAM usa o PRT para solicitar tokens de acesso e à atualização para aplicativos que dependem de WAM para solicitações de token. Ele também permite SSO em navegadores, injetando o PRT em solicitações do navegador. Navegador SSO no Windows 10 tem suporte no Microsoft Edge (nativo) e Chrome (através da extensão de contas do Windows 10 ou no Office Online).

## <a name="how-is-a-prt-renewed"></a>Como um PRT é renovado?

Um PRT é renovado em dois métodos diferentes:

* **Azure AD CloudAP plug-in cada 4 horas**: O plug-in CloudAP renova o PRT cada 4 horas durante a entrada Windows. Se o usuário não tem conexão de internet durante esse período, plug-in do CloudAP renovará a PRT depois que o dispositivo está conectado à internet.
* **Plugin WAM do AD do Azure durante solicitações de token do aplicativo**: O plug-in WAM habilita o SSO em dispositivos Windows 10, permitindo que solicitações de token silenciosas para aplicativos. O plug-in WAM pode renovar o PRT durante essas solicitações de token de duas maneiras diferentes:
   * Um aplicativo solicita silenciosamente WAM para um token de acesso, mas nenhum token de atualização está disponível para o aplicativo. Nesse caso, WAM usa o PRT para solicitar um token para o aplicativo e obtém de volta um PRT novo na resposta.
   * Um aplicativo solicita WAM para um token de acesso, mas o PRT é inválido ou Azure AD requer autorização adicional (por exemplo, autenticação multifator do Azure). Nesse cenário, WAM inicia um logon interativo, exigir que o usuário a autenticar novamente ou fornecer verificação adicional e um PRT nova é emitida em uma autenticação bem-sucedida.

### <a name="key-considerations"></a>Considerações-chave

* Um PRT só é emitido e renovada durante a autenticação do aplicativo nativo. Um PRT não é renovado ou emitido durante uma sessão do navegador.
* No Azure AD associado e dispositivos de ingressados no Azure AD híbrido, o plug-in CloudAP é a autoridade primária para um PRT. Se um PRT for renovada durante uma solicitação de token com base em WAM, o PRT é enviado para CloudAP plug-in do, que verifica a validade de PRT com o Azure AD antes de aceitá-lo.

## <a name="how-is-the-prt-protected"></a>Como o PRT está protegido?

Um PRT é protegido por associação para o dispositivo, o usuário tiver se conectado. Azure AD e Windows 10 habilitar a proteção de PRT por meio dos seguintes métodos:

* **Durante a primeira entrada**: Durante a primeira entrada, um PRT é emitido por solicitações usando a chave do dispositivo gerada criptograficamente durante o registro de dispositivos de assinatura. Em um dispositivo com um TPM válido em funcionamento, a chave do dispositivo é protegida pelo TPM, impedindo que qualquer acesso mal-intencionado. Um PRT não será emitido se a assinatura de chave de dispositivo correspondente não pode ser validada.
* **Durante a renovação e solicitações de token**: Quando um PRT é emitido, o Azure AD também emite uma chave de sessão criptografada para o dispositivo. Ela é criptografada com a chave pública de transporte (tkpub) gerados e enviados para o Azure AD como parte do registro de dispositivo. Essa chave de sessão só pode ser descriptografada pela chave privada de transporte (tkpriv) protegida pelo TPM. A chave de sessão é a chave de prova de posse (POP) para todas as solicitações enviadas ao Azure AD.  A chave de sessão também é protegida pelo TPM e nenhum outro componente do sistema operacional pode acessá-lo. Solicitações de token ou solicitações de renovação PRT com segurança são assinadas por essa chave de sessão por meio do TPM e, portanto, a não podem ser violadas. Azure AD invalidará todas as solicitações do dispositivo que não são assinadas pela correspondente chave de sessão.

Protegendo essas chaves com o TPM, atores mal-intencionado não podem roubar as chaves nem o PRT de reprodução em outro lugar como o TPM estiver inacessível, mesmo se um invasor tiver posse física do dispositivo.  Assim, usar um TPM bastante aprimora a segurança da ingressado no Azure AD, Azure AD híbrido, e os dispositivos contra roubo de credenciais registrados no Azure AD. Para desempenho e confiabilidade, o TPM 2.0 é a versão recomendada para todos os cenários de registro de dispositivo do Azure AD no Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Como os tokens de aplicativo e os cookies do navegador protegidos?

**Tokens de aplicativo**: Quando um aplicativo solicita um token por meio de WAM, o Azure AD emite um token de atualização e um token de acesso. No entanto, WAM só retorna o token de acesso para o aplicativo e protege o token de atualização em seu cache, criptografando-a com a chave do usuário data protection aplicativo programação DPAPI (interface). WAM com segurança usa o token de atualização por solicitações de assinatura com a chave de sessão para ainda mais emitir tokens de acesso. A chave do DPAPI é protegida por uma chave simétrica do Azure AD com base no Azure AD em si. Quando o dispositivo precisa descriptografar o perfil do usuário com a chave da DPAPI, o Azure AD fornece a chave DPAPI criptografada pela chave de sessão, quais plug-in CloudAP solicita o TPM para descriptografar. Essa funcionalidade garante a consistência na segurança dos tokens de atualização e evita implementar seus próprios mecanismos de proteção de aplicativos.  

**Os cookies do navegador**: No Windows 10, Azure AD dá suporte ao navegador o SSO no Internet Explorer e Microsoft Edge modo nativo ou no Google Chrome através da extensão de contas do Windows 10. A segurança foi construída não só para proteger os cookies, mas também os pontos de extremidade para o qual os cookies são enviados. Os cookies do navegador são protegidos da mesma maneira é um PRT, utilizando a chave para assinar e proteger os cookies de sessão.

Quando um usuário inicia uma interação com o navegador, o navegador (ou extensão) invoca um host de cliente nativo COM. O host de cliente nativo garante que a página é proveniente de um dos domínios permitidos. O navegador pode enviar que outros parâmetros para o cliente nativo de host, incluindo um nonce, no entanto, o host de cliente nativo garante validação do nome do host. O host de cliente nativo solicita um cookie de PRT do plugin CloudAP, que cria e assina com a chave de sessão protegidas por TPM. Como o cookie de PRT é assinado pela chave de sessão, ele não pode ser violado. Esse cookie de PRT está incluído no cabeçalho da solicitação para o Azure AD validar o dispositivo, que ela se origina. Se usar o navegador Chrome, apenas a extensão de explicitamente definida no manifesto do host de cliente nativo pode invocá-lo impedindo extensões arbitrárias de fazer essas solicitações. Depois que o Azure AD valida o cookie PRT, ele emite um cookie de sessão para o navegador. Esse cookie de sessão também contém a mesma chave de sessão emitida com um PRT. Durante as solicitações subsequentes, a chave de sessão é validada com eficiência o cookie de associação para o dispositivo e impedindo reproduções de qualquer outro lugar.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Quando um PRT chegar a uma declaração MFA?

Um PRT pode obter uma declaração de autenticação multifator (MFA) em cenários específicos. Quando um PRT com base no MFA é usado para solicitar tokens para aplicativos, a declaração de MFA é transferida para os tokens de aplicativo. Essa funcionalidade fornece uma experiência perfeita aos usuários, impedindo que o desafio de MFA para todos os aplicativos que precisa dele. Um PRT pode obter uma declaração MFA das seguintes maneiras:

* **Entrar com o Windows Hello para empresas**: Windows Hello para empresas substitui as senhas e usa chaves de criptografia para fornecer autenticação de dois fatores forte. Windows Hello para empresas é específico a um usuário em um dispositivo e, em si requer a MFA para provisionar. Quando o usuário se conecta com o Windows Hello para empresas, PRT do usuário obtém uma declaração de MFA. Este cenário também se aplica aos usuários fazer logon com cartões inteligentes, se a autenticação de cartão inteligente produz uma declaração MFA do ADFS.
   * Como o Windows Hello para empresas é considerado a autenticação multifator, a declaração de MFA é atualizada quando o PRT em si é atualizada, portanto, a duração MFA serão estendidos continuamente quando os usuários entram com o WIndows Hello para empresas
* **MFA durante a entrada interativa WAM**: Durante uma solicitação de token por meio de WAM, se um usuário é necessário fazer o MFA para acessar o aplicativo, o que é renovado durante essa interação PRT é imprinted com uma declaração de MFA.
   * Nesse caso, a declaração de MFA não é atualizada continuamente, portanto, a duração do MFA se baseia no tempo de vida definido no diretório.
* **MFA durante o registro de dispositivo**: Se um administrador tiver configurado as configurações do dispositivo no Azure AD [exigir MFA registrar dispositivos](device-management-azure-portal.md#configure-device-settings), o usuário precisará fazer o MFA para concluir o registro. Durante esse processo, o que é emitido para o usuário PRT tem a declaração de MFA obtida durante o registro. Esse recurso se aplica somente ao usuário que fez a operação de junção, não a outros usuários que entram nesse dispositivo.
   * Assim como o WAM interativo de entrada, a declaração de MFA não é atualizada continuamente, portanto, a duração do MFA se baseia no tempo de vida definido no diretório.

Windows 10 mantém uma lista particionada de PRTs para cada credencial. Portanto, há um PRT para cada do Windows Hello para empresas, senha ou cartão inteligente. Esse particionamento assegurará que as declarações MFA são isoladas com base na credencial usada e não misturados durante solicitações de token.

## <a name="how-is-a-prt-invalidated"></a>Como um PRT é invalidado?

Um PRT é invalidado nos seguintes cenários:

* **Usuário inválido**: Se um usuário for excluído ou desabilitado no AD do Azure, seu PRT é invalidado e não pode ser usado para obter tokens para aplicativos. Se um usuário desabilitado ou excluído tiver entrado em um dispositivo antes, entrar em cache seria registrá-los, até que CloudAP está ciente do seu estado inválido. Depois que CloudAP determina que o usuário é inválido, ele bloqueia logons subsequentes. Um usuário inválido é automaticamente impedido de entrar em novos dispositivos que não têm suas credenciais armazenadas em cache.
* **Dispositivo inválido**: Se um dispositivo for excluído ou desabilitado no AD do Azure, o PRT obtido em que o dispositivo é invalidado e não pode ser usado para obter tokens para outros aplicativos. Se um usuário já está conectado a um dispositivo inválido, eles podem continuar a fazê-lo. Mas todos os tokens no dispositivo serão invalidados e o usuário não tem o SSO para todos os recursos do dispositivo.
* **Alteração de senha**: Depois que um usuário altera sua senha, o PRT obtido com a senha anterior é invalidada pelo AD do Azure. Resultados de alteração de senha do usuário obtendo um PRT novo. Essa invalidação pode ocorrer de duas maneiras diferentes:
   * Se o usuário entra no Windows com sua nova senha, o CloudAP descarta o PRT antigo e solicitações do Azure AD para emitir um novo PRT com sua nova senha. Se o usuário não tem uma conexão de internet, a nova senha não pode ser validada, o Windows podem exigir que o usuário a digitar sua senha antiga.
   * Se um usuário conectado com a senha antiga ou alterou sua senha depois de entrar no Windows, o antigo PRT é usado para qualquer solicitação de token com base em WAM. Nesse cenário, o usuário é solicitado a autenticar novamente durante a solicitação de token WAM e um novo PRT é emitido.
* **Problemas com o TPM**: Às vezes, TPM um dispositivo pode falhas no ou falharem, levando a incapacidade de acesso de chaves protegidas pelo TPM. Nesse caso, o dispositivo é capaz de obter um PRT ou solicitar tokens usando um PRT existente, pois ele não é possível provar a posse das chaves de criptografia. Como resultado, qualquer PRT existente é invalidada pelo AD do Azure. Quando o Windows 10 detecta uma falha, ele inicia um fluxo de recuperação para registrar o dispositivo novamente com novas chaves de criptografia. Com associação híbrida do Azure Ad, assim como o registro inicial, a recuperação ocorra silenciosamente sem entrada do usuário. Para dispositivos registrados no Azure AD associados ou o Azure AD, a recuperação precisa ser executada por um usuário que tenha privilégios de administrador no dispositivo. Nesse cenário, o fluxo de recuperação é iniciado por um prompt do Windows que orienta o usuário para recuperar com êxito o dispositivo.

## <a name="detailed-flows"></a>Fluxos detalhados

Os diagramas a seguir ilustram os detalhes subjacentes na emissão, renovação e usando um PRT para solicitar um token de acesso para um aplicativo. Além disso, essas etapas também descrevem como os mecanismos de segurança mencionados acima são aplicados durante essas interações.

### <a name="prt-issuance-during-first-sign-in"></a>Emissão de PRT durante a primeira entrada

![Emissão de PRT durante a primeira entrada fluxo detalhado](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> No AD do Azure ingressado em dispositivos, essa troca acontece de forma síncrona para emitir um PRT antes que o usuário pode efetuar logon no Windows. Em híbrido do Azure AD ingressado em dispositivos, do Active Directory local é a autoridade principal. Portanto, o usuário só está aguardando até que eles possam adquirir um TGT para logon, enquanto a emissão de PRT ocorre de maneira assíncrona. Esse cenário não se aplica a dispositivos registrados no Azure AD como o logon não usa credenciais do Azure AD.

| Etapa | DESCRIÇÃO |
| :---: | --- |
| O | Usuário insere sua senha na interface do usuário de entrada. LogonUI passa as credenciais em um buffer de autenticação para LSA, que passa internamente para CloudAP em sequências. CloudAP encaminha essa solicitação para o plug-in CloudAP. |
| b | Plug-in do CloudAP inicia uma solicitação de descoberta de realm para identificar o provedor de identidade do usuário. Se o locatário do usuário tiver uma instalação do provedor de federação, o Azure AD retorna Metadata Exchange do provedor de Federação (MEX) ponto de extremidade. Se não estiver, o Azure AD retorna que o usuário for gerenciado que indica que o usuário pode autenticar com o Azure AD. |
| C | Se o usuário for gerenciado, CloudAP obterá o nonce do AD do Azure. Se o usuário for federado, o plug-in do CloudAP solicita um token SAML do provedor de federação com as credenciais do usuário. Uma vez que recebe, o token SAML, ele solicita um nonce do AD do Azure. |
| D | Plug-in do CloudAP constrói a solicitação de autenticação com as credenciais do usuário, o nonce e um escopo de agente, assina a solicitação com a chave do dispositivo (dkpriv) e o envia para o Azure AD. Em um ambiente federado, o plug-in do CloudAP usa o token SAML retornado pelo provedor de Federação em vez do usuário ' credenciais. |
| E | O Azure AD valida as credenciais do usuário, o nonce e assinatura do dispositivo, verifica se o dispositivo é válido no locatário e emite o PRT criptografado. Juntamente com o PRT, o Azure AD também emite uma chave simétrica, chamada de chave de sessão criptografada pelo Azure AD usando a chave de transporte (tkpub). Além disso, a chave de sessão também é inserida no PRT. Essa chave de sessão atua como a chave de prova de posse (PoP) para solicitações subsequentes com o PRT. |
| F | Plug-in do CloudAP passa a chave de sessão e PRT criptografada para CloudAP. CloudAP solicitar o TPM para descriptografar a chave de sessão usando a chave de transporte (tkpriv) e criptografar novamente usando a chave do TPM. CloudAP armazena a chave de sessão criptografada em seu cache, juntamente com o PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>Renovação PRT em logons subsequentes

![Renovação PRT em logons subsequentes](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Etapa | DESCRIÇÃO |
| :---: | --- |
| O | Usuário insere sua senha na interface do usuário de entrada. LogonUI passa as credenciais em um buffer de autenticação para LSA, que passa internamente para CloudAP em sequências. CloudAP encaminha essa solicitação para o plug-in CloudAP. |
| b | Se o usuário tem logon anteriormente para o usuário, inicia do Windows armazenados em cache entrar e valida as credenciais para o usuário. Cada 4 horas, o plug-in CloudAP inicia a renovação PRT assincronamente. |
| C | Plug-in do CloudAP inicia uma solicitação de descoberta de realm para identificar o provedor de identidade do usuário. Se o locatário do usuário tiver uma instalação do provedor de federação, o Azure AD retorna Metadata Exchange do provedor de Federação (MEX) ponto de extremidade. Se não estiver, o Azure AD retorna que o usuário for gerenciado que indica que o usuário pode autenticar com o Azure AD. |
| D | Se o usuário for federado, o plug-in do CloudAP solicita um token SAML do provedor de federação com as credenciais do usuário. Uma vez que recebe, o token SAML, ele solicita um nonce do AD do Azure. Se o usuário for gerenciado, CloudAP obterá diretamente o nonce do AD do Azure. |
| E | Plug-in do CloudAP constrói a solicitação de autenticação com as credenciais do usuário, o nonce e o PRT existente, assina a solicitação com a chave de sessão e o envia para o Azure AD. Em um ambiente federado, o plug-in do CloudAP usa o token SAML retornado pelo provedor de Federação em vez do usuário ' credenciais. |
| F | Azure AD valida a assinatura de chave de sessão, comparando-a com a chave de sessão inserida no PRT, valida o nonce e verifica se o dispositivo é válido no locatário e emite um PRT novo. Como visto anteriormente, o PRT novamente é acompanhado com a chave de sessão criptografada pela chave de transporte (tkpub). |
| G | Plug-in do CloudAP passa a chave de sessão e PRT criptografada para CloudAP. CloudAP solicita o TPM para descriptografar a chave de sessão usando a chave de transporte (tkpriv) e criptografar novamente usando a chave do TPM. CloudAP armazena a chave de sessão criptografada em seu cache, juntamente com o PRT. |

### <a name="prt-usage-during-app-token-requests"></a>Uso PRT durante solicitações de token de aplicativo

![Uso PRT durante solicitações de token de aplicativo](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Etapa | DESCRIÇÃO |
| :---: | --- |
| O | Um aplicativo (por exemplo, Outlook, OneNote etc.) inicia uma solicitação de token para WAM. WAM, por sua vez, solicita que o plug-in do Azure AD WAM para atender à solicitação de token. |
| b | Se um token de atualização para o aplicativo já estiver disponível, o plug-in do Azure AD WAM a usa para solicitar um token de acesso. Para fornecer uma prova de associação de dispositivo, o plug-in do WAM assina a solicitação com a chave de sessão. O Azure AD valida a chave de sessão e emite um token de acesso e um novo token de atualização para o aplicativo, criptografado pela chave de sessão. Plug-in do WAM solicitações AP da nuvem plug-in para descriptografar tokens, que, por sua vez, solicita o TPM para descriptografar usando a chave de sessão, resultando no plug-in WAM obter ambos os tokens. Em seguida, o plug-in do WAM fornece apenas o token de acesso ao aplicativo, enquanto ele criptografa novamente o token de atualização com DPAPI e os armazena no seu próprio cache  |
| C |  Se um token de atualização para o aplicativo não estiver disponível, o plug-in do Azure AD WAM usa o PRT para solicitar um token de acesso. Para fornecer uma prova de posse, o plug-in do WAM assina a solicitação de contendo o PRT com a chave de sessão. Azure AD valida a assinatura de chave de sessão, comparando-a com a chave de sessão inserida no PRT, verifica se o dispositivo é válido e emite um token de acesso e um token de atualização para o aplicativo. Além disso, o Azure AD pode emitir um novo PRT (com base no ciclo de atualização), todos eles criptografada pela chave de sessão. |
| D | Plug-in do WAM solicitações AP da nuvem plug-in para descriptografar tokens, que, por sua vez, solicita o TPM para descriptografar usando a chave de sessão, resultando no plug-in WAM obter ambos os tokens. Em seguida, o plug-in do WAM fornece apenas o token de acesso ao aplicativo, enquanto ele criptografa novamente o token de atualização com DPAPI e os armazena no seu próprio cache. Plug-in do WAM usará o token de atualização no futuro para este aplicativo. Plug-in do WAM também devolve PRT o novo para o plugin do AP da nuvem, que valida o PRT com o Azure AD antes de atualizá-la em seu próprio cache. Plug-in de ponto de acesso de nuvem usará o novo PRT no futuro. |
| E | WAM fornece o token de acesso emitidos recentemente para WAM, que por sua vez, fornece-lo de volta para o aplicativo de chamada|

### <a name="browser-sso-using-prt"></a>Navegador SSO usando PRT

![Navegador SSO usando PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Etapa | DESCRIÇÃO |
| :---: | --- |
| O | Usuário faz logon no Windows com suas credenciais para obter um PRT. Depois que o usuário abre o navegador, o navegador (ou extensão) carrega as URLs do registro. |
| b | Quando um usuário abre uma URL de logon do AD do Azure, o navegador ou a extensão valida a URL com aqueles obtido do registro. Se elas corresponderem, o navegador invoca o host de cliente nativo para obter um token. |
| C | O host de cliente nativo valida que as URLs pertencem aos provedores de identidade do Microsoft (conta da Microsoft ou do Azure AD), extrai um nonce enviado da URL e faz uma chamada para o plug-in do CloudAP para obter um cookie PRT. |
| D | O plug-in CloudAP criar o cookie PRT, entrar com a chave de sessão vinculada ao TPM e enviará de volta para o host de cliente nativo. Como o cookie é assinado pela chave de sessão, ele não pode ser violado. |
| E | O host de cliente nativo retornará esse cookie PRT ao navegador, que irá incluí-lo como parte do cabeçalho da solicitação, chamado x-ms-RefreshTokenCredential e solicitar tokens do AD do Azure. |
| F | Azure AD valida a assinatura de chave de sessão no cookie de PRT, valida o nonce, verifica se o dispositivo é válido no locatário e emite um token de ID para a página da web e um cookie de sessão criptografada para o navegador. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como solucionar problemas relacionados à PRT, consulte o artigo [híbrido de solução de problemas do Azure Active Directory ingressado em dispositivos Windows 10 e Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).
