---
title: Avaliação de acesso contínuo no Azure AD
description: Respondendo a alterações no estado do usuário mais rapidamente com avaliação de acesso contínuo no Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562c90dcc4f802290b0ed8b4d544fce9d526fa10
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524661"
---
# <a name="continuous-access-evaluation"></a>Avaliação contínua de acesso

A expiração do token e a atualização são um mecanismo padrão do setor. Quando um aplicativo cliente como o Outlook se conecta a um serviço como o Exchange Online, as solicitações de API são autorizadas usando tokens de acesso OAuth 2,0. Por padrão, esses tokens de acesso são válidos por uma hora, quando expiram, o cliente é Redirecionado de volta para o Azure AD para atualizá-los. Esse período de atualização fornece uma oportunidade para reavaliar as políticas de acesso do usuário. Por exemplo: podemos optar por não atualizar o token devido a uma política de acesso condicional ou porque o usuário foi desabilitado no diretório. 

Os clientes expressou preocupações sobre o atraso entre quando as condições mudam para o usuário, como o local da rede ou roubo de credenciais, e quando as políticas podem ser impostas relacionadas a essa alteração. Experimentamos a abordagem "objeto de moderação" de tempos de vida reduzidos de token, mas descobrimos que eles podem prejudicar as experiências e a confiabilidade do usuário sem eliminar os riscos.

A resposta oportuna a violações de política ou problemas de segurança realmente requer uma "conversa" entre o emissor do token, como o Azure AD e a terceira parte confiável, como o Exchange Online. Essa conversa bidirecional nos dá dois recursos importantes. A terceira parte confiável pode observar quando as coisas foram alteradas, como um cliente proveniente de um novo local e informam ao emissor do token. Ele também fornece ao emissor do token uma maneira de dizer à terceira parte confiável para parar de respeitar os tokens de um determinado usuário devido a comprometimento da conta, à desabilitação ou a outras preocupações. O mecanismo para essa conversa é a Continuous Access Evaluation (CAE). O objetivo é que a resposta seja quase em tempo real, mas, em alguns casos, a latência de até 15 minutos pode ser observada devido ao tempo de propagação do evento.

A implementação inicial da avaliação de acesso contínuo se concentra no Exchange, nas equipes e no SharePoint Online.

Para preparar seus aplicativos para usar o CAE, consulte [como usar APIs habilitadas para avaliação de acesso contínuo em seus aplicativos](../develop/app-resilience-continuous-access-evaluation.md).

### <a name="key-benefits"></a>Principais benefícios

- Término do usuário ou alteração/redefinição de senha: a revogação da sessão do usuário será imposta quase em tempo real.
- Alteração de local de rede: as políticas de local de acesso condicional serão impostas quase em tempo real.
- A exportação de tokens para um computador fora de uma rede confiável pode ser impedida com políticas de local de acesso condicional.

## <a name="scenarios"></a>Cenários 

Há dois cenários que compõem a avaliação de acesso contínuo, avaliação crítica de eventos e avaliação de política de acesso condicional.

### <a name="critical-event-evaluation"></a>Avaliação de evento crítico

A avaliação de acesso contínuo é implementada habilitando serviços, como o Exchange Online, o SharePoint Online e as equipes, para assinar eventos críticos no Azure AD para que esses eventos possam ser avaliados e aplicados quase em tempo real. A avaliação crítica de eventos não depende de políticas de acesso condicional, portanto está disponível em qualquer locatário. Os seguintes eventos são avaliados no momento:

- A conta de usuário foi excluída ou desabilitada
- A senha de um usuário é alterada ou redefinida
- A autenticação multifator está habilitada para o usuário
- O administrador revoga explicitamente todos os tokens de atualização para um usuário
- Alto risco de usuário detectado por Azure AD Identity Protection

Esse processo habilita o cenário em que os usuários perdem o acesso a arquivos, email, calendário ou tarefas organizacionais do SharePoint, e equipes de Microsoft 365 aplicativos cliente dentro de minutos após um desses eventos críticos. 

### <a name="conditional-access-policy-evaluation-preview"></a>Avaliação da política de acesso condicional (versão prévia)

O Exchange e o SharePoint são capazes de sincronizar as políticas de acesso condicional de chave para que possam ser avaliadas dentro do próprio serviço.

Esse processo habilita o cenário em que os usuários perdem o acesso a arquivos organizacionais, email, calendário ou tarefas de Microsoft 365 aplicativos cliente ou SharePoint Online imediatamente após as alterações no local de rede.

> [!NOTE]
> Nem todas as combinações de provedor de recursos e aplicativos têm suporte. Consulte a tabela abaixo. O Office se refere ao Word, Excel e PowerPoint

| | Outlook Web | Win32 do Outlook | IOS do Outlook | Android do Outlook | Mac do Outlook |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Com suporte | Com suporte | Sem suporte | Sem suporte | Com suporte |
| **Exchange Online** | Com suporte | Com suporte | Com suporte | Com suporte | Com suporte |

| | Office Web Apps | Aplicativos Win32 do Office | Office para iOS | Office para Android | Office para Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Sem suporte | Com suporte | Com suporte | Com suporte | Com suporte |
| **Exchange Online** | Sem suporte | Com suporte | Com suporte | Com suporte | Com suporte |

### <a name="client-side-claim-challenge"></a>Desafio de declaração do lado do cliente

Antes da avaliação de acesso contínuo, os clientes sempre tentarão repetir o token de acesso de seu cache, desde que ele não tenha expirado. Com o CAE, estamos introduzindo um novo caso que um provedor de recursos possa rejeitar um token, mesmo quando ele não tiver expirado. Para informar os clientes para ignorarem seu cache, mesmo que os tokens em cache não tenham expirado, apresentamos um mecanismo chamado **Challenge de declaração** para indicar que o token foi rejeitado e um novo token de acesso precisa ser emitido pelo Azure AD. O CAE requer uma atualização do cliente para entender o desafio da declaração. A versão mais recente dos seguintes aplicativos abaixo dá suporte ao desafio de declaração:

- Janelas do Outlook
- IOS do Outlook
- Android do Outlook
- Mac do Outlook
- Outlook Web App
- Teams para Windows (somente para recursos de equipes)
- Equipes iOS (somente para recursos de equipes)
- Equipes Android (somente para recursos de equipes)
- Mac das equipes (somente para recursos de equipes)
- Word/Excel/PowerPoint para Windows
- Word/Excel/PowerPoint para iOS
- Word/Excel/PowerPoint para Android
- Word/Excel/PowerPoint para Mac

### <a name="token-lifetime"></a>Tempo de vida do Token

Como o risco e a política são avaliados em tempo real, os clientes que negociam sessões de reconhecimento de avaliação de acesso contínuo dependerão de CAE em vez de políticas de tempo de vida de token de acesso estático existentes, o que significa que a política de tempo de vida de token configurável não será mais respeitada para clientes compatíveis com CAE que negociem sessões com reconhecimento de

O tempo de vida do token é aumentado para ser de longa duração, até 28 horas, em sessões CAE. A revogação é orientada por eventos críticos e avaliação de política, não apenas um período de tempo arbitrário. Essa alteração aumenta a estabilidade dos aplicativos sem afetar a postura de segurança. 

Se você não estiver usando clientes compatíveis com CAE, o tempo de vida do token de acesso padrão permanecerá em 1 hora, a menos que você tenha configurado o tempo de vida do token de acesso com o recurso de visualização da [CTL](../develop/active-directory-configurable-token-lifetimes.md)

## <a name="example-flows"></a>Fluxos de exemplo

### <a name="user-revocation-event-flow"></a>Fluxo de eventos de revogação do usuário:

![Fluxo de eventos de revogação do usuário](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. Um cliente com capacidade de CAE apresenta credenciais ou um token de atualização para o Azure AD solicitando um token de acesso para algum recurso.
1. Um token de acesso é retornado junto com outros artefatos para o cliente.
1. Um administrador [revoga explicitamente todos os tokens de atualização para o usuário](/powershell/module/azuread/revoke-azureaduserallrefreshtoken). Um evento de revogação será enviado para o provedor de recursos do Azure AD.
1. Um token de acesso é apresentado ao provedor de recursos. O provedor de recursos avalia a validade do token e verifica se há qualquer evento de revogação para o usuário. O provedor de recursos usa essas informações para decidir conceder acesso ao recurso ou não.
1. Nesse caso, o provedor de recursos nega o acesso e envia um desafio de declaração 401 + de volta para o cliente.
1. O cliente com capacidade de CAE compreende o desafio de declaração 401 +. Ele ignora os caches e volta para a etapa 1, enviando seu token de atualização junto com o desafio de declaração de volta para o Azure AD. O Azure AD reavaliará todas as condições e solicitará que o usuário se autentique novamente nesse caso.

### <a name="user-condition-change-flow-preview"></a>Fluxo de alteração de condição do usuário (visualização):

No exemplo a seguir, um administrador de acesso condicional configurou uma política de acesso condicional com base na localização para permitir somente o acesso de intervalos de IP específicos:

![Fluxo de eventos da condição do usuário](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. Um cliente com capacidade de CAE apresenta credenciais ou um token de atualização para o Azure AD solicitando um token de acesso para algum recurso.
1. O Azure AD avalia todas as políticas de acesso condicional para ver se o usuário e o cliente atendem às condições.
1. Um token de acesso é retornado junto com outros artefatos para o cliente.
1. O usuário sai de um intervalo de IP permitido
1. O cliente apresenta um token de acesso ao provedor de recursos de fora de um intervalo de IP permitido.
1. O provedor de recursos avalia a validade do token e verifica a política de localização sincronizada do Azure AD.
1. Nesse caso, o provedor de recursos nega o acesso e envia um desafio de declaração 401 + de volta para o cliente porque ele não está vindo do intervalo de IP permitido.
1. O cliente com capacidade de CAE compreende o desafio de declaração 401 +. Ele ignora os caches e volta para a etapa 1, enviando seu token de atualização junto com o desafio de declaração de volta para o Azure AD. O Azure AD reavalia todas as condições e negará o acesso, nesse caso.

## <a name="enable-or-disable-cae-preview"></a>Habilitar ou desabilitar CAE (versão prévia)

1. Entre no **portal do Azure** como administrador de acesso condicional, administrador de segurança ou administrador global
1. Navegue até **Azure Active Directory**  >    >  **avaliação de acesso contínuo à** segurança.
1. Escolha **habilitar visualização**.

Nessa página, você pode, opcionalmente, limitar os usuários e grupos que estarão sujeitos à versão prévia.

![Habilitando a visualização CAE no portal do Azure](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>Solução de problemas

### <a name="supported-location-policies"></a>Políticas de localização com suporte

Para CAE, só temos informações sobre locais nomeados baseados em IP nomeados. Não temos informações sobre outras configurações de local como [IPs confiáveis MFA](../authentication/howto-mfa-mfasettings.md#trusted-ips) ou locais baseados em países. Quando o usuário vem de um IP confiável MFA ou locais confiáveis que incluem IPs confiáveis de MFA ou local de país, o CAE não será imposto depois que o usuário mudar para um local diferente. Nesses casos, emitiremos um token CAE de 1 hora sem verificação instantânea de imposição de IP.

> [!IMPORTANT]
> Ao configurar locais para avaliação de acesso contínuo, use apenas a [condição de local de acesso condicional baseado em IP](../conditional-access/location-condition.md#preview-features) e configure todos os endereços IP, **incluindo IPv4 e IPv6**, que podem ser vistos pelo provedor de identidade e provedor de recursos. Não use condições de localização de país ou o recurso de IPs confiáveis que está disponível na página de configurações de serviço da autenticação multifator do Azure AD.

### <a name="ip-address-configuration"></a>Configuração do endereço IP

O provedor de identidade e os provedores de recursos podem ver endereços IP diferentes. Essa incompatibilidade pode ocorrer devido a implementações de proxy de rede em sua organização ou configurações de IPv4/IPv6 incorretas entre o provedor de identidade e o provedor de recursos. Por exemplo:

- Seu provedor de identidade vê um endereço IP do cliente.
- O provedor de recursos vê um endereço IP diferente do cliente depois de passar por um proxy.
- O endereço IP que seu provedor de identidade vê faz parte de um intervalo de IP permitido na política, mas o endereço IP do provedor de recursos não é.

Se esse cenário existir em seu ambiente para evitar loops infinitos, o Azure AD emitirá um token de uma hora CAE e não imporá a alteração do local do cliente. Mesmo nesse caso, a segurança é melhorada em comparação com os tokens de hora tradicionais, pois ainda estamos avaliando os [outros eventos](#critical-event-evaluation) , além dos eventos de alteração de local do cliente.

### <a name="office-and-web-account-manager-settings"></a>Configurações do Office e do Gerenciador de contas da Web

| Canal de atualização do Office | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Canal Empresarial Semestral | Se definido como Enabled ou 1, CAE não terá suporte. | Se definido como Enabled ou 1, CAE não terá suporte. |
| Canal Atual <br> ou <br> Canal Empresarial Mensal | CAE tem suporte independentemente da configuração | CAE tem suporte independentemente da configuração |

Para obter uma explicação dos canais de atualização do Office, consulte [visão geral dos canais de atualização para aplicativos Microsoft 365](/deployoffice/overview-update-channels). É recomendável que as organizações não desabilitem o Gerenciador de contas da Web (WAM).

### <a name="policy-change-timing"></a>Tempo de alteração de política

Devido ao potencial de atraso de replicação entre o Azure AD e os provedores de recursos, as alterações de política feitas pelos administradores podem levar até duas horas para serem efetivas para o Exchange Online.

Exemplo: o administrador adiciona uma política para impedir que um intervalo de endereços IP acesse email às 11:00 A.M., um usuário que vem desse intervalo de IPS antes de poder continuar a acessar o email até 1:00 PM.

### <a name="coauthoring-in-office-apps"></a>Coautoria em aplicativos do Office

Quando vários usuários estão colaborando no mesmo documento ao mesmo tempo, o acesso do usuário ao documento pode não ser imediatamente revogado pelo CAE com base na revogação do usuário ou em eventos de alteração de política. Nesse caso, o usuário perde o acesso completamente após, fechando o documento, fechando o Word, o Excel ou o PowerPoint, ou após um período de 10 horas.

Para reduzir esse tempo, um administrador do SharePoint pode, opcionalmente, reduzir a vida útil máxima de sessões de coautoria para documentos armazenados no SharePoint Online e no OneDrive for Business, [Configurando uma política de local de rede no SharePoint Online](/sharepoint/control-access-based-on-network-location). Depois que essa configuração for alterada, o tempo de vida máximo de sessões de coautoria será reduzido para 15 minutos e poderá ser ajustado ainda mais usando o comando "Set-SPOTenant – IPAddressWACTokenLifetime" do PowerShell do SharePoint Online

### <a name="enable-after-a-user-is-disabled"></a>Habilitar depois que um usuário for desabilitado

Se você habilitar um usuário logo após ele ser desabilitado. Haverá alguma latência para que a conta possa ser habilitada. SPO e times terão um atraso de 15 minutos. O atraso é de 35-40 minutos para EXO.

## <a name="faqs"></a>Perguntas frequentes

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Como o CAE funcionará com a frequência de entrada?

A frequência de entrada será respeitada com ou sem CAE.

## <a name="next-steps"></a>Próximas etapas

[Anunciando avaliação de acesso contínuo](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
