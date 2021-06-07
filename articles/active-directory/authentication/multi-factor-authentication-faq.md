---
title: Perguntas frequentes sobre autenticação multifator do Azure AD-Azure Active Directory
description: Perguntas frequentes e respostas relacionadas à autenticação multifator do Azure AD.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a58fbf874339bf287ce1af62e1edb39ec6227d99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257095"
---
# <a name="frequently-asked-questions-about-azure-ad-multi-factor-authentication"></a>Perguntas frequentes sobre a autenticação multifator do Azure AD

Estas perguntas frequentes respondem a perguntas comuns sobre a autenticação multifator do Azure AD e usando o serviço de autenticação multifator. Ele é dividido em perguntas sobre o serviço em geral, modelos, experiências do usuário, de cobrança e solução de problemas.

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o servidor MFA para novas implantações. Os novos clientes que desejarem exigir a autenticação multifator de seus usuários devem usar a autenticação multifator do Azure AD baseada em nuvem. Os clientes existentes que ativaram o servidor MFA antes de 1º de julho poderão baixar a versão mais recente, atualizações futuras e gerar credenciais de ativação como de costume.
>
> As informações compartilhadas abaixo em relação à Servidor de Autenticação Multifator do Azure só são aplicáveis a usuários que já têm o servidor MFA em execução.
>
> O licenciamento baseado em consumo não está mais disponível para novos clientes em vigor em 1 de setembro de 2018.
> A partir de 1º de setembro de 2018, novos provedores de autenticação podem não ser mais criados. Os provedores de autenticação existentes podem continuar sendo usados e atualizados. A Autenticação Multifator continuará sendo um recurso disponível nas licenças do Azure AD Premium.

## <a name="general"></a>Geral

* [Como o Azure Servidor de Autenticação Multifator lida com os dados do usuário?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Quais códigos curtos do SMS são usados para enviar mensagens SMS para meus usuários?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Como o Servidor de Autenticação Multifator do Azure lida com os dados do usuário?

Com Servidor de Autenticação Multifator, os dados do usuário são armazenados somente nos servidores locais. Nenhum dado de usuário persistente é armazenado na nuvem. Quando o usuário executa a verificação em duas etapas, Servidor de Autenticação Multifator envia dados para o serviço de nuvem da autenticação multifator do Azure AD para autenticação. A comunicação entre o Servidor de Autenticação Multifator e o serviço de nuvem da Autenticação Multifator usa o protocolo SSL (Secure Sockets Layer) ou TLS (Transport Layer Security) pela porta de saída 443.

Quando solicitações de autenticação são enviadas ao serviço de nuvem, dados são coletados para relatórios de autenticação e uso. Os campos de dados a seguir estão incluídos em logs de verificação em duas etapas:

* **ID Exclusiva** (ou o nome de usuário, ou a ID do Servidor de Autenticação Multifator local)
* **Nome e sobrenome** (opcional)
* **Endereço de email** (opcional)
* **Número de Telefone** (ao usar uma chamada de voz ou autenticação SMS)
* **Token de Dispositivo** (ao usar a autenticação de aplicativos móveis)
* **Modo de Autenticação**
* **Resultado da autenticação**
* **Nome do Servidor de Autenticação Multifator**
* **IP do Servidor de Autenticação Multifator**
* **IP do Cliente** (se disponível)

Os campos opcionais podem ser configurados no Servidor de Autenticação Multifator.

O resultado de verificação (sucesso ou negação) e o motivo se ele foi negado, é armazenado com os dados de autenticação. Esses dados estão disponíveis em relatórios de uso e de autenticação.

Para obter mais informações, consulte [residência de dados e dados do cliente para autenticação multifator do Azure ad](concept-mfa-data-residency.md).

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Quais códigos curtos de SMS são usados para enviar mensagens SMS aos meus usuários?

No Estados Unidos, usamos os seguintes códigos curtos do SMS:

* *97671*
* *69829*
* *51789*
* *99399*

No Canadá, usamos os seguintes códigos curtos do SMS:

* *759731*
* *673801*

Não há nenhuma garantia de entrega de prompt de autenticação multifator baseada em voz ou SMS consistente pelo mesmo número. Pensando no melhor para nossos usuários, nós podemos adicionar ou remover códigos curtos a qualquer momento, pois fazemos ajustes de rota para melhorar a capacidade de entrega de SMS.

Não há suporte para códigos curtos para países ou regiões além do Estados Unidos e do Canadá.

## <a name="billing"></a>Cobrança

A maioria das perguntas de cobrança pode ser respondida referindo-se à [página de preços da autenticação multifator](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) ou à documentação para [as versões de autenticação multifator do Azure AD e planos de consumo](concept-mfa-licensing.md).

* [Minha organização é cobrada pelo envio de chamadas telefônicas e mensagens de texto que são usadas para autenticação?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [O modelo de cobrança por usuário cobra por todos os usuários habilitados ou apenas aqueles que executaram a verificação em duas etapas?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Como funciona a cobrança da autenticação multifator?](#how-does-multi-factor-authentication-billing-work)
* [Há uma versão gratuita da autenticação multifator do Azure AD?](#is-there-a-free-version-of-azure-ad-multi-factor-authentication)
* [Minha organização pode alternar entre modelos de cobrança de consumo por usuário e por autenticação a qualquer momento?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [Minha organização pode alternar entre cobrança e assinaturas baseadas em consumo (um modelo baseado em licença) a qualquer momento?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [Minha organização precisa usar e sincronizar identidades para usar a autenticação multifator do Azure AD?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>A minha organização é cobrada por enviar as mensagens de texto e realizar as chamadas telefônicas usadas para autenticação?

Não, você não será cobrado por chamadas telefônicas individuais colocadas ou mensagens de texto enviadas aos usuários por meio da autenticação multifator do Azure AD. Se você usar um provedor MFA por autenticação, será cobrado para cada autenticação, mas não para o método usado.

Os usuários podem ser cobrados por chamadas telefônicas ou mensagens de texto recebidas, de acordo com seu serviço de telefone pessoal.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>O modelo de cobrança por usuário me cobra por todos os usuários habilitados ou somente pelos que executaram a verificação em duas etapas?

A cobrança se baseia no número de usuários configurados para usar a Autenticação Multifator, independentemente deles terem executado uma verificação de duas etapas naquele mês.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Como funciona a cobrança de Autenticação Multifator?

Quando você cria um provedor MFA por usuário ou por autenticação, a assinatura do Azure da sua organização é cobrada mensalmente com base no uso. Esse modelo de cobrança é semelhante a como o Azure cobra pelo uso de máquinas virtuais e aplicativos Web.

Quando você adquire uma assinatura para a autenticação multifator do Azure AD, sua organização paga apenas a taxa de licença anual para cada usuário. As licenças MFA e os pacotes Microsoft 365, Azure AD Premium ou Enterprise Mobility + Security são cobrados dessa maneira.

Para obter mais informações, consulte [como obter a autenticação multifator do Azure ad](concept-mfa-licensing.md).

### <a name="is-there-a-free-version-of-azure-ad-multi-factor-authentication"></a>Há uma versão gratuita da autenticação multifator do Azure AD?

Os padrões de segurança podem ser habilitados na camada de Azure AD Gratuito. Com os padrões de segurança, todos os usuários são habilitados para autenticação multifator usando o aplicativo Microsoft Authenticator. Não há a possibilidade de usar a verificação por telefone ou mensagem de texto com padrões de segurança, apenas o aplicativo Microsoft Authenticator.

Para obter mais informações, consulte [o que são padrões de segurança?](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>Minha organização pode alternar entre os modelos de cobrança de consumo por usuário e por autenticação a qualquer momento?

Se sua organização adquirir o MFA como um serviço autônomo de cobrança com base em consumo, escolha um modelo de cobrança ao criar um provedor MFA. Você não pode alterar o modelo de cobrança depois que um provedor de MFA é criado. 

Se o seu provedor de MFA *não* estiver vinculado a um locatário do Azure ad ou se você vincular o novo provedor de MFA a um locatário do Azure ad diferente, as configurações de usuário e as opções de configuração não serão transferidas. Além disso, os servidores MFA existentes do Azure precisarão ser reativados usando as credenciais de ativação geradas por meio do novo provedor de MFA. Reativar os servidores MFA para vinculá-los para o novo provedor de MFA não afete de telefonema e autenticação de mensagens de texto, mas as notificações de aplicativo móvel deixará de funcionar para todos os usuários até que eles reativarem o aplicativo móvel.

Saiba mais sobre provedores MFA em [Introdução a um provedor de Autenticação Multifator do Azure](concept-mfa-authprovider.md).

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>Minha organização pode trocar alternar entre a cobrança baseada no consumo e assinaturas (um modelo baseado em licença) a qualquer momento?

Em alguns casos, sim.

Se o diretório tiver um provedor de Autenticação Multifator do Azure *por usuário*, você pode adicionar licenças do MFA. Os usuários com licenças não são contados na cobrança baseada em consumo por usuário. Os usuários sem licenças ainda podem ser habilitados para MFA através do provedor MFA. Se você comprar e atribuir licenças para todos os usuários configurados para usar a Autenticação Multifator, poderá excluir o provedor de Autenticação Multifator do Azure. Você sempre pode criar outro provedor MFA por usuário, se você tiver mais usuários do que licenças no futuro.

Se o diretório tiver um provedor de autenticação multifator do Azure *por autenticação* , você sempre será cobrado por cada autenticação, desde que o provedor de MFA esteja vinculado à sua assinatura. Você pode atribuir licenças MFA para os usuários, mas você ainda será cobrado para cada solicitação de verificação em duas etapas, se se trata de uma pessoa com uma licença MFA atribuída ou não.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication"></a>Minha organização precisa usar e sincronizar identidades para usar a autenticação multifator do Azure AD?

Se a sua organização usa um modelo de cobrança baseado em consumo, o Azure Active Directory é opcional, mas não é necessário. Se o seu provedor de MFA não estiver vinculado a um locatário do Azure AD, você só poderá implantar o Azure Servidor de Autenticação Multifator local.

O Azure Active Directory é necessário para o modelo de licença porque as licenças são adicionadas ao locatário do Azure AD quando você compra e as atribui aos usuários no diretório.

## <a name="manage-and-support-user-accounts"></a>Gerenciar e dar suporte a contas de usuário

* [O que devo dizer aos meus usuários para fazer se não receberem uma resposta em seu telefone?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [O que devo fazer se um dos meus usuários não conseguir entrar em sua conta?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [O que devo fazer se um dos meus usuários perder um telefone que está usando senhas de aplicativo?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [E se um usuário não conseguir entrar em aplicativos sem navegador?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [Meus usuários dizem que às vezes eles não recebem a mensagem de texto ou a verificação atinge o tempo limite.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Posso alterar a quantidade de tempo que meus usuários têm para inserir o código de verificação de uma mensagem de texto antes de o sistema expirar?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Posso usar tokens de hardware com o Azure Servidor de Autenticação Multifator?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Posso usar o Azure Servidor de Autenticação Multifator para proteger os serviços de terminal?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Configurei a ID do chamador no servidor MFA, mas meus usuários ainda recebem chamadas de autenticação multifator de um chamador anônimo.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Por que meus usuários estão sendo solicitados a registrar suas informações de segurança?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>O que devo dizer aos meus usuários para fazer se não receberem uma resposta em seu telefone?

Faça com que os usuários tentem até cinco vezes em 5 minutos para obter uma chamada telefônica ou SMS para autenticação. A Microsoft usa vários provedores para entregar mensagens SMS e chamadas. Se essa abordagem não funcionar, abra um caso de suporte para solucionar os problemas.

Os aplicativos de segurança de terceiros também podem bloquear a mensagem de texto do código de verificação ou a chamada telefônica. Se estiver usando um aplicativo de segurança de terceiros, tente desabilitar a proteção e, em seguida, solicite outro código de verificação de MFA.

Se as etapas acima não funcionarem, verifique se os usuários estão configurados para mais de um método de verificação. Tente entrar novamente, mas selecione um método de verificação diferente na página de entrada.

Para obter mais informações, consulte o [Guia de solução de problemas do usuário final](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>O que devo fazer se um dos meus usuários não conseguir acessar a própria conta?

Você pode redefinir a conta do usuário fazendo com que ele refaça o processo de registro. Saiba mais sobre como [gerenciar configurações de usuário e dispositivo com a autenticação multifator do Azure ad na nuvem](howto-mfa-userdevicesettings.md).

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>O que devo fazer se um dos meus usuários perder um telefone que está usando senhas de aplicativo?

Para evitar acesso não autorizado, exclua as senhas do todos os usuários aplicativo. Depois que o usuário tiver outro dispositivo, ele poderá recriar as senhas. Saiba mais sobre como [gerenciar configurações de usuário e dispositivo com a autenticação multifator do Azure ad na nuvem](howto-mfa-userdevicesettings.md).

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>E se um usuário não conseguir entrar em aplicativos que não são acessados por navegador?

Se sua organização usa ainda clientes herdados e você [permitido o uso de senhas de aplicativo](howto-mfa-app-passwords.md), em seguida, os usuários não conseguem entrar nesses clientes herdados com seu nome de usuário e senha. Em vez disso, eles precisam [configurar senhas de aplicativo](../user-help/multi-factor-authentication-end-user-app-passwords.md). Os usuários devem limpar (excluir) suas informações de logon, reinicie o aplicativo e entre com seu nome de usuário e *senha de aplicativo* em vez de regulares de senha.

Se sua organização não tiver clientes herdados, você não deverá permitir que os usuários criem senhas de aplicativo.

> [!NOTE]
> **Autenticação moderna para clientes do Office 2013**
>
> Senhas de aplicativo são necessárias apenas para aplicativos que não suportam autenticação moderna. Clientes do Office 2013 oferece suporte aos protocolos de autenticação moderna, mas precisam ser configurados. A autenticação moderna está disponível para qualquer cliente que execute a atualização de março de 2015 ou posterior para o Office 2013. Para obter mais informações, consulte a postagem no blog [atualização da autenticação moderna do Office 365](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>Meus usuários dizem que às vezes eles não recebem a mensagem de texto ou a verificação atinge o tempo limite.

A entrega de mensagens SMS não é garantida porque existem fatores não controláveis que podem afetar a confiabilidade do serviço. Esses fatores incluem o país ou a região de destino, a operadora de celular e a intensidade do sinal.

Os aplicativos de segurança de terceiros também podem bloquear a mensagem de texto do código de verificação ou a chamada telefônica. Se estiver usando um aplicativo de segurança de terceiros, tente desabilitar a proteção e, em seguida, solicite outro código de verificação de MFA.

Se os usuários costumam ter problemas com o recebimento confiável de mensagens de texto, solicite que eles usem o método de chamada de telefone ou aplicativo Microsoft Authenticator em vez disso. O Microsoft Authenticator pode receber notificações em conexões celular e de Wi-Fi. Além disso, o aplicativo móvel pode gerar códigos de verificação mesmo quando o dispositivo não tem sinal. O aplicativo Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [Ios](https://go.microsoft.com/fwlink/?Linkid=825073)e [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Posso alterar a quantidade de tempo que meus usuários precisam inserir o código de verificação de uma mensagem de texto antes do sistema expira?

Em alguns casos, Sim.

Para o SMS unidirecional com o Servidor MFA v7.0 ou posterior do Azure, você pode configurar o tempo limite de configuração definindo uma chave do registro. Depois que o serviço de nuvem MFA envia a mensagem de texto, o código de verificação (ou a senha de uso único) é retornada para o Servidor MFA. O Servidor MFA armazena o código na memória para 300 segundos por padrão. Se o usuário insere seu código depois de 300 segundos, a autenticação será negada. Siga estas etapas para alterar a configuração de tempo limite padrão:

1. Ir para `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`.
2. Crie uma chave de registro **DWORD** chamada *pfsvc_pendingSmsTimeoutSeconds* e defina o tempo em segundos que você deseja que o servidor do Azure MFA armazene senhas de uso único.

>[!TIP]
>
> Se você tiver vários Servidores MFA, apenas aquele que processam a solicitação de autenticação original sabem o código de verificação que foi enviado ao usuário. Quando o usuário insere o código, a solicitação de autenticação para validação deve ser enviada para o mesmo servidor. Se a validação de código é enviada para um servidor diferente, a autenticação será negada.

Se os usuários não responderem ao SMS dentro do período de tempo limite definido, a autenticação será negada.

Para o SMS unidirecional com o Azure AD MFA na nuvem (incluindo o adaptador AD FS ou a extensão do servidor de políticas de rede), você não pode definir a configuração de tempo limite. O Azure AD armazena o código de verificação durante 180 segundos.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Posso usar tokens de hardware com o Servidor de Autenticação Multifator do Azure?

Se você estiver usando o Azure Servidor de Autenticação Multifator, poderá importar tokens de TOTP (senha de uso único) com base em tempo de autenticação aberta (OATH), e depois usá-los para a verificação em duas etapas.

Será possível usar tokens ActiveIdentity que sejam TOTP OATH se você colocar a chave secreta em um arquivos CSV e o importar para o Servidor de Autenticação Multifator do Azure. É possível usar tokens OATH com os Serviços de Federação do Active Directory (ADFS), a autenticação baseada em formulários do Servidor de Informações da Internet (IIS) e o serviço RADIUS, quando o sistema cliente aceita entradas do usuário.

Você pode importar tokens OATH TOTP de terceiros com os seguintes formatos:  

- PSKC (Contêiner Portátil de Chave Simétrica)
- CSV se o arquivo contiver um número de série, uma chave secreta no formato de Base 32 e um intervalo de tempo

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Posso usar o Servidor de Autenticação Multifator do Azure para proteger os Serviços de Terminal?

Sim, mas se você estiver usando o Windows Server 2012 R2 ou posterior, só poderá proteger os serviços de terminal usando Área de Trabalho Remota gateway (Gateway RD).

As alterações de segurança no Windows Server 2012 R2 mudaram a forma como o Servidor de Autenticação Multifator do Azure se conecta ao pacote de segurança LSA (Autoridade de Segurança Local) no Windows Server 2012 e versões anteriores. Para versões dos Serviços de Terminal no Windows Server 2012 ou anteriores, você pode [proteger um aplicativo com a Autenticação do Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se você estiver usando o Windows Server 2012 R2, precisará do gateway de área de trabalho remota.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>Eu configurei uma ID de Chamadas no Servidor MFA, mas os usuários ainda recebem chamadas de Autenticação Multifator de um chamador anônimo.

Quando as chamadas da Autenticação Multifator são feitas por meio de rede telefônica pública, às vezes, elas são roteadas por uma operadora que não é compatível com a ID de chamadas. Devido a esse comportamento da operadora, a ID do chamador não é garantida, mesmo que o sistema de autenticação multifator sempre a envie.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Por que meus usuários estão sendo solicitados para registrar as informações de segurança?

Há vários motivos que os usuários podem ser solicitados para registrar as informações de segurança:

- O usuário foi habilitado para MFA pelo administrador no Azure AD, mas não tem informações de segurança ainda registradas para sua conta.
- O usuário ativou autoatendimento para redefinição de senha no Azure AD. As informações de segurança ajudará a redefinir sua senha no futuro, se ele esquecerem.
- O usuário acessou um aplicativo que tem uma política de acesso condicional para exigir MFA e não foi registrado anteriormente para MFA.
- O usuário está registrando um dispositivo com o Azure AD (incluindo o ingresso no Azure AD) e sua organização requer MFA para registro de dispositivo, mas o usuário não foi registrado anteriormente para MFA.
- O usuário está gerando o Windows Hello para empresas no Windows 10 (que requer MFA) e não foi registrado anteriormente para MFA.
- A organização criou e ativado uma diretiva de registro de MFA que foi aplicada ao usuário.
- O usuário registrado para MFA anteriormente, mas escolher um método de verificação que um administrador como desabilitado. O usuário, portanto, deve passar pelo registro MFA novamente para selecionar um novo método de verificação padrão.

## <a name="errors"></a>Errors

* [O que os usuários devem fazer se receberem uma mensagem de erro "a solicitação de autenticação não é para uma conta ativada" ao usar notificações de aplicativo móvel?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [O que os usuários devem fazer se receberem uma mensagem de erro 0x800434D4L ao entrar em um aplicativo sem navegador?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>O que os usuários devem fazer se receberem uma mensagem de erro "a solicitação de autenticação não é para uma conta ativada" ao usar notificações de aplicativo móvel?

Peça ao usuário para concluir o procedimento a seguir para remover sua conta da Microsoft Authenticator e, em seguida, adicioná-la novamente:

1. Acesse [seu perfil de portal do Azure](https://account.activedirectory.windowsazure.com/profile/) e entre com uma conta institucional.
2. Selecione **verificação de segurança adicional**.
3. Remova a conta existente do aplicativo Microsoft Authenticator.
4. Clique em **Configurar** e siga as instruções para reconfigurar o Microsoft Authenticator.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>O que os usuários deverão fazer se receberem uma mensagem de erro 0x800434D4L ao entrar em um aplicativo que não é de navegador?

O erro *0x800434D4L* ocorre quando você tenta entrar em um aplicativo sem navegador, instalado em um computador local, que não funciona com contas que exigem a verificação em duas etapas.

Uma solução alternativa para esse erro é ter contas de usuário separadas para operações administrativas e não administrativas. Posteriormente, você pode vincular caixas de correio entre a conta administrativa e a conta não administrativa para que seja possível entrar no Outlook usando a conta não administrativa. Para obter mais detalhes sobre isso, saiba como [fornecer a um administrador a capacidade de abrir e exibir o conteúdo da caixa de correio de um usuário](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Próximas etapas

Se sua pergunta não for respondida aqui, as seguintes opções de suporte estarão disponíveis:

* Pesquise a [Base de Dados de Conhecimento de Suporte da Microsoft](https://support.microsoft.com) para obter soluções para problemas técnicos comuns.
* Pesquise e procure perguntas e respostas técnicas da Comunidade ou faça sua própria pergunta no [Azure Active Directory Q&A](/answers/topics/azure-active-directory.html).
* Entre em contato com o suporte do Microsoft Professional por meio [do Azure servidor de autenticação multifator](https://support.microsoft.com/oas/default.aspx?prid=14947). Ao entrar em contato conosco, é útil incluir o máximo possível de informações sobre o problema. As informações que você pode fornecer incluem a página em que viu o erro, o código de erro específico, a ID da sessão específica e a ID do usuário que viu o erro.
* Se você for um cliente herdado do PhoneFactor e tiver dúvidas ou precisar de ajuda para redefinir uma senha, use o [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) endereço de email para abrir um caso de suporte.
