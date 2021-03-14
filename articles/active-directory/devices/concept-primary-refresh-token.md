---
title: PRT (Token de atualização principal) e Azure AD – Azure Active Directory
description: Qual é a função do PRT (Token de atualização principal) e como podemos gerenciá-lo no Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46cc8ef1158c02190f905cbe8eb1d12ea7be50a2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644928"
---
# <a name="what-is-a-primary-refresh-token"></a>O que é um Token de atualização principal?

Um PRT (token de atualização principal) é um artefato importante da autenticação do Azure AD no Windows 10, Windows Server 2016 e versões posteriores, iOS e dispositivos Android. Trata-se de um JWT (Token Web JSON) emitido especialmente para agentes de token de terceiros da Microsoft para habilitar o SSO (logon único) nos aplicativos usados nesses dispositivos. Neste artigo, serão fornecidos detalhes sobre a emissão, o uso e a proteção de um PRT em dispositivos Windows 10.

Este artigo pressupõe que você já tenha entendido os diferentes estados de dispositivo disponíveis no Azure AD e como o SSO funciona no Windows 10. Para obter mais informações sobre dispositivos no Azure AD, consulte o artigo [O que é o gerenciamento de dispositivos no Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Principais termos e componentes

Os componentes do Windows mostrados a seguir desempenham um papel fundamental na solicitação e no uso de um PRT:

* **Provedor de Autenticação na Nuvem** (CloudAP): o CloudAP é o provedor de autenticação moderno para entrar no Windows, o qual verifica os usuários que estão fazendo logon em um dispositivo Windows 10. Ele fornece uma estrutura de plug-in que pode ser usada pelos provedores de identidade para habilitar a autenticação no Windows usando as credenciais do próprio provedor de identidade.
* **Gerenciador de Contas da Web** (WAM): o WAM é o agente de token padrão em dispositivos Windows 10. Ele também fornece uma estrutura de plug-in que pode ser usada pelos provedores de identidade para habilitar o SSO para os aplicativos que dependem desse provedor de identidade.
* **Plug-in CloudAP do Azure AD**: um plug-in específico do Azure AD criado na estrutura do CloudAP e que verifica as credenciais do usuário com o Azure AD durante a entrada no Windows.
* **Plug-in WAM do Azure AD**: um plug-in específico do Azure AD criado na estrutura do WAM e que habilita o SSO para aplicativos que dependem do Azure AD para autenticação.
* **Dsreg**: um componente específico do Azure AD no Windows 10 que opera o processo de registro de dispositivo em todos os estados de dispositivo.
* **Trusted Platform Module** (TPM): um TPM é um componente de hardware interno de um dispositivo que fornece funções de segurança baseadas em hardware para segredos de usuário e de dispositivo. Mais detalhes podem ser encontrados no artigo [Visão geral da tecnologia Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>O que um PRT contém?

Um PRT contém declarações que geralmente estão presentes em qualquer token de atualização do Azure AD. Além disso, o PRT também conta com algumas declarações específicas do dispositivo. Elas são as seguintes:

* **ID do dispositivo**: um PRT é emitido a um usuário em um dispositivo específico. A declaração de ID do dispositivo `deviceID` determina em qual dispositivo o PRT foi emitido ao usuário. Posteriormente, essa declaração é emitida a tokens obtidos por meio do PRT. A declaração de ID do dispositivo é usada para determinar a autorização de Acesso condicional baseada no estado ou na conformidade do dispositivo.
* **Chave da sessão**: a chave da sessão é uma chave simétrica criptografada que é gerada pelo serviço de autenticação do Azure AD e emitida como parte do PRT. A chave da sessão atuará como a prova de posse quando um PRT for usado para obter tokens para outros aplicativos.

### <a name="can-i-see-whats-in-a-prt"></a>Posso ver o que há dentro de um PRT?

Um PRT é um blob opaco enviado a partir do Azure AD e cujo conteúdo não é conhecido por nenhum componente cliente. Você não consegue ver o que está dentro de um PRT.

## <a name="how-is-a-prt-issued"></a>Como um PRT é emitido?

O registro do dispositivo é um pré-requisito para a autenticação baseada em dispositivo no Azure AD. Um PRT só é emitido para usuários em dispositivos registrados. Para obter detalhes mais aprofundados sobre o registro de dispositivos, consulte o artigo [Windows Hello para Empresas e registro de dispositivo](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Durante um registro de dispositivo, o componente dsreg gera dois conjuntos de pares de chaves de criptografia:

* Chave de dispositivo (dkpub/dkpriv)
* Chave de transporte (tkpub/tkpriv)

Caso o dispositivo tenha TPM válido e em funcionamento, as chaves privadas serão associadas a esse TPM; as chaves públicas, por sua vez, são enviadas ao Azure AD durante o processo de registro do dispositivo. Essas chaves são usadas para validar o estado do dispositivo durante a solicitação de um PRT.

Há dois cenários em que o PRT é emitido durante a autenticação do usuário em um dispositivo Windows 10:

* **Ingressado no Azure AD** ou  **ingressado no Azure AD híbrido**: Um PRT será emitido durante o logon no Windows quando um usuário entrar com suas credenciais da organização. Um PRT é emitido com todas as credenciais do Windows 10 que tenham suporte, como senhas e o Windows Hello para Empresas. Nesse cenário, o plug-in CloudAP do Azure AD é considerada a autoridade principal pelo PRT.
* **Dispositivo registrados no Azure AD**: um PRT será emitido quando um usuário adicionar uma conta corporativa secundária ao seu dispositivo Windows 10. Os usuários podem adicionar uma conta ao Windows 10 de duas formas:  
   * Adicionando uma conta usando o prompt **Usar essa conta em qualquer lugar neste dispositivo** depois de entrar em um aplicativo (por exemplo, Outlook)
   * Adicionando uma conta pelo caminho **Configurações** > **Contas** > **Acesso corporativo ou de estudante** > **Conectar**

Em cenários com dispositivos registrados do Azure AD, o plug-in WAM do Azure AD é considerado a autoridade principal pelo PRT, pois o logon do Windows não está sendo feito com essa conta do Azure AD.

> [!NOTE]
> Provedores de identidade de terceiros precisam ter suporte ao protocolo WS-Trust para habilitar a emissão do PRT em dispositivos Windows 10. Sem o WS-Trust, o PRT não pode ser emitido para usuários em dispositivos ingressados no Azure AD híbrido ou no Azure AD. No ADFS, somente os pontos de extremidade usernamemixed são necessários. O ADFS/Services/Trust/2005/windowstransport e o ADFS/Services/Trust/13/windowstransport devem ser habilitados como pontos de extremidade voltados para a intranet e **não devem ser expostos** como pontos de extremidade voltados para a extranet por meio do proxy de aplicativo Web

## <a name="what-is-the-lifetime-of-a-prt"></a>Qual é o tempo de vida de um PRT?

Depois de emitido, um PRT é válido por 14 dias, mas será renovado continuamente desde que o usuário use o dispositivo ativamente.  

## <a name="how-is-a-prt-used"></a>Como um PRT é usado?

Um PRT é usado por dois componentes fundamentais no Windows:

* **Plug-in CloudAP do Azure AD**: ao entrar no Windows, o plug-in CloudAP do Azure AD solicita um PRT do Azure AD usando as credenciais fornecidas pelo usuário. Ele também armazena o PRT em cache para habilitar a entrada armazenada em cache quando o usuário não tiver acesso a uma conexão com a Internet.
* **Plug-in WAM do Azure AD**: quando usuários tentam acessar aplicativos, o plug-in WAM do Azure AD usa o PRT para habilitar o SSO no Windows 10. O plug-in WAM do Azure AD usa o PRT para solicitar tokens de atualização e de acesso para aplicativos que dependem do WAM para solicitações de token. Ele também injeta o PRT nas solicitações do navegador para habilitar o SSO em navegadores. O SSO do navegador no Windows 10 tem suporte no Microsoft Edge (nativamente) e no Chrome (por meio das [contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?hl=en) ou extensões do [Office Online](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb?hl=en)).

## <a name="how-is-a-prt-renewed"></a>Como um PRT é renovado?

Um PRT é renovado por meio de dois métodos:

* **Plug-in CloudAP do Azure AD a cada 4 horas**: o plug-in CloudAP renova o PRT a cada 4 horas durante a entrada no Windows. Caso o usuário esteja sem uma conexão com a Internet nesse momento, o plug-in CloudAP renovará o PRT depois que o dispositivo estiver conectado à Internet.
* **Plug-in WAM do Azure AD durante solicitações de token de aplicativo**: o plug-in WAM habilita o SSO em dispositivos Windows 10 por meio da habilitação de solicitações de token silencioso para aplicativos. O plug-in WAM pode renovar o PRT durante essas solicitações de token de duas maneiras:
   * Um aplicativo solicita o WAM a um token de acesso silenciosamente, mas não há um token de atualização disponível para esse aplicativo. Nesse caso, o WAM usa o PRT para solicitar um token para o aplicativo e retorna um novo PRT na resposta.
   * Um aplicativo solicita o WAM para um token de acesso, mas o PRT é inválido ou o Azure AD requer autorização adicional (por exemplo, autenticação multifator do Azure AD). Nesse cenário, o WAM inicia um logon interativo que requer que o usuário seja reautenticado ou forneça uma verificação adicional, e um novo PRT é emitido na autenticação bem-sucedida.

Em um ambiente do ADFS, a linha de visão direta para o controlador de domínio não é necessária para renovar o PRT. A renovação de PRT requer apenas pontos de extremidade/ADFS/Services/Trust/2005/usernamemixed e/ADFS/Services/Trust/13/usernamemixed habilitados no proxy usando WS-Trust protocolo.

Os pontos de extremidade de transporte do Windows são necessários para a autenticação de senha somente quando uma senha é alterada, não para a renovação de PRT.

### <a name="key-considerations"></a>Considerações-chave

* Um PRT só é emitido e renovado durante uma autenticação de aplicativo nativo. Um PRT não é renovado nem emitido durante uma sessão do navegador.
* Nos dispositivos ingressados no Azure AD e no Azure AD híbrido, o plug-in CloudAP é considerado a autoridade principal de um PRT. Caso um PRT seja renovado durante uma solicitação de token baseada em WAM, o PRT será enviado de volta para o plug-in CloudAP, o qual verificará sua validade com o Azure AD antes de aceitá-lo.

## <a name="how-is-the-prt-protected"></a>Como o PRT é protegido?

Um PRT é protegido ao ser associado ao dispositivo no qual o usuário entrou. O Azure AD e o Windows 10 habilitam a proteção do PRT por meio dos seguintes métodos:

* **Durante a primeira entrada**: durante a primeira entrada, um PRT é emitido por meio de solicitações de assinatura usando a chave do dispositivo gerada criptograficamente durante o registro do dispositivo. Em um dispositivo com um TPM válido e em funcionamento, a chave do dispositivo é protegida pelo TPM, o que impede qualquer acesso mal-intencionado. Um PRT não será emitido se não for possível validar a assinatura da chave de dispositivo correspondente.
* **Durante solicitações e renovações de token**: quando um PRT é emitido, o Azure AD também emite uma chave da sessão criptografada para o dispositivo. Ele é criptografado com a chave de transporte pública (tkpub) gerada e enviada ao Azure AD como parte do registro do dispositivo. Essa chave da sessão só pode ser descriptografada pela chave de transporte privada (tkpriv) protegida pelo TPM. A chave da sessão é a chave PoP (Prova de posse) para todas as solicitações enviadas ao Azure AD.  A chave da sessão também é protegida pelo TPM, e nenhum outro componente do sistema operacional pode acessá-la. As solicitações de token ou de renovação de PRT são assinadas com segurança por essa chave da sessão por meio do TPM e, portanto, não podem ser adulteradas. O Azure AD invalidará quaisquer solicitações do dispositivo que não estejam assinadas pela chave da sessão correspondente.

Ao proteger essas chaves com o TPM, aprimoramos a segurança do PRT de atores mal-intencionados tentando roubar as chaves ou reproduzir o PRT.  Portanto, o uso de um TPM aprimora muito a segurança do Azure ad ingressado, o Azure AD híbrido e os dispositivos registrados no Azure AD contra roubo de credenciais. Para melhor desempenho e confiabilidade, o TPM 2.0 é a versão recomendada para todos os cenários de registro de dispositivo do Azure AD no Windows 10. Iniciando a atualização do Windows 10, 1903, o Azure AD não usa o TPM 1,2 para qualquer uma das chaves acima devido a problemas de confiabilidade. 

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Como tokens de aplicativo e cookies de navegador são protegidos?

**Tokens de aplicativo**: quando um aplicativo solicita um token por meio do WAM, o Azure AD emite um token de atualização e um token de acesso. Porém, o WAM retorna apenas o token de acesso para o aplicativo e protege o token de atualização em seu cache por meio da criptografia com a chave DPAPI (interface de programação de aplicativo de proteção de dados) do usuário. O WAM usa o token de atualização de modo seguro por meio da assinatura de solicitações com a chave da sessão para emitir mais tokens de acesso. A chave DPAPI é protegida por uma chave simétrica baseada no Azure AD presente no próprio Azure AD. Quando o dispositivo precisa descriptografar o perfil do usuário com a chave DPAPI, o Azure AD a fornece criptografada pela chave da sessão, e o plug-in CloudAP solicitará o TPM para descriptografá-la. Essa funcionalidade garante a consistência na proteção de tokens de atualização e evita que os aplicativos implementem seus próprios mecanismos de proteção.  

**Cookies do navegador**: no Windows 10, o Azure AD tem suporte nativo ao SSO do navegador no Internet Explorer e no Microsoft Edge e, no Google Chrome, esse suporte se dá por meio da extensão de contas do Windows 10. A segurança é criada para proteger os cookies e também os pontos de extremidade para os quais eles são enviados. Os cookies do navegador são protegidos da mesma maneira que um PRT, ou seja, usando a chave da sessão para assiná-los e protegê-los.

Quando um usuário inicia uma interação com o navegador, o navegador (ou a extensão) invoca um host cliente nativo do COM. O host do cliente nativo garante que a página seja oriunda de um dos domínios permitidos. O navegador poderia enviar outros parâmetros para o host do cliente nativo, inclusive um nonce, porém, o host do cliente nativo garante a validação do nome do host. O host do cliente nativo solicita um cookie do PRT do plug-in CloudAP, o qual o cria e o assina com a chave da sessão protegida do TPM. Como o PRT-cookie é assinado pela chave de sessão, é muito difícil de adulterar. Esse o cookie do PRT é incluído no cabeçalho de solicitação do Azure AD para validar o dispositivo do qual ele se origina. Caso esteja usando o navegador Chrome, apenas a extensão explicitamente definida no manifesto do host do cliente nativo poderá chamá-lo impedindo que extensões arbitrárias façam essas solicitações. Depois que o Azure AD validar o cookie do PRT, ele emitirá um cookie de sessão para o navegador. Esse cookie de sessão também contém a mesma chave da sessão emitida com um PRT. Durante as solicitações subsequentes, a chave da sessão será validada, associando efetivamente o cookie ao dispositivo e impedindo reproduções de outros locais.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Quando um PRT obtém uma declaração de MFA?

Um PRT consegue obter uma declaração de MFA (autenticação multifator) em cenários específicos. Quando um PRT baseado na MFA é usado para solicitar tokens para aplicativos, a declaração de MFA é transferida para esses tokens de aplicativo. Essa funcionalidade fornece uma experiência sem contratempos aos usuários ao evitar o desafio de MFA para cada aplicativo que o exigir. Um PRT consegue obter uma declaração de MFA das maneiras a seguir:

* **Entrar com o Windows Hello para Empresas**: O Windows Hello para Empresas substitui senhas e usa chaves criptográficas para fornecer uma autenticação forte de dois fatores. O Windows Hello para Empresas é específico a um usuário em um dispositivo e ele mesmo requer a MFA para provisionar. Quando um usuário faz logon usando o Windows Hello para Empresas, o PRT do usuário obtém uma declaração de MFA. Esse cenário também se aplica a usuários que fazem logon com cartões inteligentes caso a autenticação de cartões inteligentes produza uma declaração de MFA do ADFS.
   * Como o Windows Hello para Empresas é considerado uma autenticação multifator, a declaração MFA é atualizada quando o próprio PRT é atualizado, portanto, a duração da MFA se estenderá continuamente quando os usuários entrarem usando o Windows Hello para Empresas.
* **MFA durante uma entrada interativa do WAM**: durante uma solicitação de token por meio do WAM, caso um usuário seja solicitado a usar a MFA para acessar o aplicativo, o PRT renovado durante essa interação será impresso com uma declaração de MFA.
   * Nesse caso, a declaração de MFA não é atualizada continuamente, portanto, sua duração é baseada no tempo de vida definido no diretório.
   * Quando um PRT e um RT já existentes são usados para o acesso a um aplicativo, eles são considerados a primeira prova de autenticação. Um novo AT será requerido, com uma segunda prova e uma declaração de MFA impressa. Isso também emitirá um PRT e um RT novos.
* **MFA durante o registro do dispositivo**: caso um administrador tenha definido suas configurações de dispositivo no Azure AD para [requerer uma MFA para registrar dispositivos](device-management-azure-portal.md#configure-device-settings), o usuário precisará usar a MFA para concluir o registro. Durante esse processo, a declaração de MFA do PRT emitido para o usuário será obtida durante o registro. Essa funcionalidade só se aplicará ao usuário que tiver feito a operação de ingresso e não a outros usuários que entrarem nesse dispositivo.
   * De modo semelhante à entrada interativa do WAM, a declaração de MFA não é atualizada continuamente, portanto, sua duração é baseada no tempo de vida definido no diretório.

O Windows 10 mantém uma lista particionada de PRTs para cada credencial. Assim, há um PRT para cada Windows Hello para Empresas, senha ou cartão inteligente. Esse particionamento garante que as declarações de MFA sejam isoladas com base na credencial usada e que não sejam misturadas durante solicitações de token.

## <a name="how-is-a-prt-invalidated"></a>Como um PRT é invalidado?

Um PRT é invalidado nos seguintes cenários:

* **Usuário inválido**: se um usuário for excluído ou desabilitado no Azure AD, seu PRT será invalidado e não poderá ser usado para obter tokens para aplicativos. Se um usuário excluído ou desabilitado já tiver entrado em um dispositivo antes, a entrada armazenada em cache faria o logon até que CloudAP ficasse ciente do seu estado inválido. Assim que CloudAP determinar que o usuário é inválido, ele bloqueará logons subsequentes. A entrada de um usuário inválido será automaticamente bloqueada em novos dispositivos que não tenham suas credenciais armazenadas em cache.
* **Dispositivo inválido**: se um dispositivo for excluído ou desabilitado no Azure AD, o PRT obtido será invalidado e não poderá ser usado para obter tokens para outros aplicativos. Se um usuário estiver conectado em um dispositivo inválido, ele poderá continuar fazendo isso. Mas todos os tokens no dispositivo serão invalidados, e o usuário não terá o SSO para nenhum recurso desse dispositivo.
* **Alteração de senha**: quando um usuário alterar sua senha, o PRT obtido com a senha anterior será invalidado pelo Azure AD. Ao alterar a senha, o usuário receberá um novo PRT. Essa invalidação pode ocorrer de duas maneiras:
   * Caso o usuário entre no Windows com sua nova senha, o CloudAP descartará o PRT antigo e solicitará que o Azure AD emita um novo PRT com a nova senha. Caso o usuário não tenha uma conexão com a Internet, a nova senha não poderá ser validada, e o Windows poderá exigir que o usuário insira sua senha antiga.
   * Caso um usuário tenha feito logon com sua senha antiga ou tenha alterado sua senha depois de entrar no Windows, o PRT antigo será usado para qualquer solicitação de token baseada em WAM. Nesse cenário, o usuário é solicitado a fazer a reautenticação durante a solicitação de token do WAM, e um novo PRT é emitido.
* **Problemas do TPM**: às vezes, o TPM de um dispositivo pode apresentar problemas ou falhar, levando à inacessibilidade de chaves protegidas pelo TPM. Nesse caso, o dispositivo não consegue obter um PRT nem solicitar tokens usando um PRT existente, pois ele não pode provar a posse das chaves de criptografia. Como resultado, todos os PRTs existentes são invalidados pelo Azure AD. Ao detectar uma falha, o Windows 10 inicia um fluxo de recuperação para registrar novamente o dispositivo com novas chaves de criptografia. Com o ingresso do Azure AD híbrido, assim como o registro inicial, a recuperação ocorre silenciosamente, sem a entrada do usuário. Para dispositivos ingressados ou registrados no Azure AD, a recuperação precisa ser executada por um usuário que tenha privilégios de administrador no dispositivo. Nesse cenário, o fluxo de recuperação é iniciado por um prompt do Windows que guia o usuário a recuperar o dispositivo com sucesso.

## <a name="detailed-flows"></a>Fluxos detalhados

Os diagramas a seguir ilustram os detalhes subjacentes na emissão, na renovação e no uso de um PRT para solicitar um token de acesso para um aplicativo. Além disso, essas etapas também descrevem como os mecanismos de segurança supramencionados são aplicados durante essas interações.

### <a name="prt-issuance-during-first-sign-in"></a>Emissão de PRT durante a primeira entrada

![Fluxo detalhado da emissão de PRT durante a primeira entrada](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Em dispositivos ingressados ao Azure AD, essa troca ocorre de forma síncrona para emitir um PRT antes que o usuário consiga fazer logon no Windows. Em dispositivos ingressados no Azure AD híbrido, o Active Directory local é a autoridade principal. Dessa forma, o usuário só está aguardando até que possa adquirir um TGT para fazer logon, enquanto a emissão do PRT ocorre de forma assíncrona. Esse cenário não se aplica a dispositivos registrados no Azure AD, pois o logon não usa as credenciais do Azure AD.

| Etapa | Descrição |
| :---: | --- |
| Um | O usuário insere sua senha na interface do usuário de entrada. A LogonUI passa as credenciais em um buffer de autenticação para o LSA, que, por sua vez, passa-as internamente para o CloudAP. O CloudAP encaminha essa solicitação ao plug-in CloudAP. |
| B | O plug-in CloudAP inicia uma solicitação de descoberta de realm para identificar o provedor de identidade do usuário. Caso o locatário do usuário tenha uma configuração de provedor de federação, o Azure AD retornará o ponto de extremidade MEX (Metadata Endpoint) do provedor de federação. Caso contrário, o Azure AD retorna que o usuário é gerenciado, indicando que ele pode se autenticar com o Azure AD. |
| C | Caso o usuário seja gerenciado, o CloudAP receberá o nonce do Azure AD. Caso o usuário seja federado, o plug-in CloudAP solicitará um token SAML do provedor de federação com as credenciais do usuário. Depois de ter recebido, o token SAML solicita um nonce do Azure AD. |
| D | O plug-in CloudAP constrói a solicitação de autenticação com as credenciais do usuário, o nonce e um escopo do agente, assina a solicitação com a chave do dispositivo (dkpriv) e a envia ao Azure AD. Em um ambiente federado, o plug-in CloudAP usa o token SAML retornado pelo provedor de federação em vez das credenciais do usuário. |
| E | O Azure AD valida as credenciais do usuário, o nonce e a assinatura do dispositivo, depois verifica se o dispositivo é válido no locatário e emite o PRT criptografado. Juntamente com o PRT, o Azure AD também emite uma chave simétrica, chamada de chave da sessão criptografada pelo Azure AD usando a chave de transporte (tkpub). Além disso, a chave da sessão também é inserida no PRT. Essa chave da sessão atua como a chave PoP para solicitações subsequentes com o PRT. |
| F | O plug-in CloudAP passa o PRT criptografado e a chave da sessão para o CloudAP. O CloudAP solicita ao TPM que descriptografe a chave da sessão usando a chave de transporte (tkpriv) e a recriptografe usando a própria chave do TPM. O CloudAP armazena a chave da sessão criptografada em seu cache, junto com o PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>Renovação do PRT em logons subsequentes

![Renovação do PRT em logons subsequentes](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Etapa | Descrição |
| :---: | --- |
| Um | O usuário insere sua senha na interface do usuário de entrada. A LogonUI passa as credenciais em um buffer de autenticação para o LSA, que, por sua vez, passa-as internamente para o CloudAP. O CloudAP encaminha essa solicitação ao plug-in CloudAP. |
| B | Caso o usuário tenha feito logon anteriormente ao usuário, o Windows iniciará a entrada em cache e validará as credenciais para fazer o logon do usuário. A cada 4 horas, o plug-in CloudAP inicia a renovação do PRT de forma assíncrona. |
| C | O plug-in CloudAP inicia uma solicitação de descoberta de realm para identificar o provedor de identidade do usuário. Caso o locatário do usuário tenha uma configuração de provedor de federação, o Azure AD retornará o ponto de extremidade MEX (Metadata Endpoint) do provedor de federação. Caso contrário, o Azure AD retorna que o usuário é gerenciado, indicando que ele pode se autenticar com o Azure AD. |
| D | Caso o usuário seja federado, o plug-in CloudAP solicitará um token SAML do provedor de federação com as credenciais do usuário. Depois de ter recebido, o token SAML solicita um nonce do Azure AD. Caso o usuário seja gerenciado, o CloudAP receberá diretamente o nonce do Azure AD. |
| E | O plug-in CloudAP constrói a solicitação de autenticação com as credenciais do usuário, o nonce e o PRT existente, assina a solicitação com a chave da sessão e a envia ao Azure AD. Em um ambiente federado, o plug-in CloudAP usa o token SAML retornado pelo provedor de federação em vez das credenciais do usuário. |
| F | O Azure AD valida a assinatura de chave da sessão comparando-a com a chave da sessão inserida no PRT, valida o nonce e, depois, verifica se o dispositivo é válido no locatário e emite um novo PRT. Como visto anteriormente, o PRT é novamente acompanhado pela chave da sessão criptografada pela chave de transporte (tkpub). |
| G | O plug-in CloudAP passa o PRT criptografado e a chave da sessão para o CloudAP. O CloudAP solicita ao TPM que descriptografe a chave da sessão usando a chave de transporte (tkpriv) e a recriptografe usando a própria chave do TPM. O CloudAP armazena a chave da sessão criptografada em seu cache, junto com o PRT. |

> [!NOTE]
> Um PRT pode ser renovado externamente sem a necessidade de uma conexão VPN quando os pontos de extremidade usernamemixed são habilitados externamente.

### <a name="prt-usage-during-app-token-requests"></a>Uso do PRT durante solicitações de token de aplicativo

![Uso do PRT durante solicitações de token de aplicativo](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Etapa | Descrição |
| :---: | --- |
| Um | Um aplicativo (por exemplo, Outlook, OneNote etc.) inicia uma solicitação de token para o WAM. O WAM, por sua vez, solicita ao plug-in WAM do Azure AD que atenda à solicitação de token. |
| B | Caso um token de atualização do aplicativo já esteja disponível, o plug-in WAM do Azure AD o usará para solicitar um token de acesso. Para fornecer uma prova de associação ao dispositivo, o plug-in WAM assinará a solicitação com a chave da sessão. O Azure AD valida a chave da sessão e emite um token de acesso e um novo token de atualização para o aplicativo, criptografados pela chave da sessão. O plug-in WAM solicita que o plug-in CloudAP descriptografe os tokens, o qual, por sua vez, solicita que o TPM descriptografe usando a chave da sessão, o que resulta no plug-in WAM obtendo ambos os tokens. Em seguida, o plug-in WAM fornece apenas o token de acesso para o aplicativo enquanto recriptografa o token de atualização com a DPAPI e o armazena em seu próprio cache.  |
| C |  Caso um token de atualização do aplicativo não esteja disponível, o plug-in WAM do Azure AD usará o PRT para solicitar um token de acesso. Para fornecer uma prova de posse, o plug-in WAM assinará a solicitação contendo o PRT com a chave da sessão. O Azure AD valida a assinatura de chave da sessão comparando-a com a chave da sessão inserida no PRT, verifica se o dispositivo é válido e emite um token de acesso e um token de atualização para o aplicativo. Além disso, o Azure AD pode emitir novos PRTs (baseados no ciclo de atualização), todos eles criptografados pela chave da sessão. |
| D | O plug-in WAM solicita que o plug-in CloudAP descriptografe os tokens, o qual, por sua vez, solicita que o TPM descriptografe usando a chave da sessão, o que resulta no plug-in WAM obtendo ambos os tokens. Em seguida, o plug-in WAM fornece apenas o token de acesso para o aplicativo enquanto recriptografa o token de atualização com a DPAPI e o armazena em seu próprio cache. O plug-in WAM usará o token de atualização nesse aplicativo desse momento em diante. O plug-in WAM também retorna o novo PRT ao plug-in de CloudAP, o qual valida o PRT com o Azure AD antes de atualizá-lo em seu próprio cache. O plug-in CloudAP usará o novo PRT desse momento em diante. |
| E | O WAM fornece o token de acesso emitido recentemente ao WAM, o qual, por sua vez, o fornece de volta para o aplicativo de chamada.|

### <a name="browser-sso-using-prt"></a>SSO do navegador usando o PRT

![SSO do navegador usando o PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Etapa | Descrição |
| :---: | --- |
| Um | O usuário faz logon no Windows com suas próprias credenciais para obter um PRT. Assim que o usuário abrir o navegador, o navegador (ou extensão) carregará as URLs a partir do registro. |
| B | Quando um usuário abre uma URL de logon do Azure AD, o navegador ou a extensão valida a URL com aquelas obtidas a partir do registro. Caso elas sejam correspondentes, o navegador invocará o host do cliente nativo para obter um token. |
| C | O host de cliente nativo valida que as URLs pertencem aos provedores de identidade da Microsoft (conta Microsoft ou Azure AD), extrai um nonce enviado a partir da URL e faz uma chamada para o plug-in CloudAP para obter um cookie do PRT. |
| D | O plug-in CloudAP criará o cookie do PRT, entrará com a chave da sessão associada ao TPM e a enviará de volta para o host do cliente nativo. |
| E | O host do cliente nativo retornará esse cookie do PRT ao navegador, o qual o incluirá como parte do cabeçalho de solicitação chamado x-ms-RefreshTokenCredential e solicitará tokens do Azure AD. |
| F | O Azure AD valida a assinatura da chave da sessão no cookie do PRT, valida o nonce, verifica se o dispositivo é válido no locatário e emite um token de ID para a página da Web e um cookie de sessão criptografado para o navegador. |

> [!NOTE]
> O fluxo de SSO do navegador descrito nas etapas acima não se aplica a sessões em modos privados, como InPrivate no Microsoft Edge, ou Incognito no Google Chrome (ao usar a extensão de contas da Microsoft).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como solucionar problemas relacionados ao PRT, consulte o artigo [Solucionar problemas de dispositivos Windows 10 e Windows Server 2016 ingressados no Azure Active Directory híbrido](troubleshoot-hybrid-join-windows-current.md).
