---
title: Bloquear autenticação herdada - Azure Active Directory
description: Saiba como melhorar sua postura de segurança bloqueando a autenticação herdada usando o Acesso Condicional do Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09f98e3d6c7997d9cae2737b25f4323021e29bfb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98892432"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Como fazer: Bloquear autenticação herdada para Azure AD com Acesso Condicional   

Para fornecer aos usuários acesso fácil aos aplicativos na nuvem, o Azure AD (Azure Active Directory) dá suporte a uma ampla variedade de protocolos de autenticação, incluindo a autenticação herdada. No entanto, protocolos herdados não dão suporte para MFA (autenticação multifator). Em muitos ambientes, a MFA é um requisito comum para lidar com roubo de identidade. 

Alex Weinert, diretor de segurança de identidade na Microsoft, em sua postagem de blog em 12 de março de 2020 [Novas ferramentas para bloquear a autenticação herdada em sua organização](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#), enfatiza por que as organizações devem bloquear a autenticação herdada e quais ferramentas adicionais a Microsoft fornece para realizar essa tarefa:

> Para que o MFA seja eficaz, você também precisa bloquear a autenticação herdada. Isso ocorre porque os protocolos de autenticação herdados como POP, SMTP, IMAP e MAPI não podem impor o MFA, tornando-os pontos de entrada preferenciais para os adversários que atacam sua organização...
> 
>...Os números na autenticação herdada de uma análise do tráfego do Azure Active Directory (Azure AD) são fortes:
> 
> - Mais de 99% dos ataques de pulverização de senha usam protocolos de autenticação herdados
> - Mais de 97% dos ataques de preenchimento de credenciais usam autenticação herdada
> - As contas do Azure AD em organizações que desabilitaram a autenticação herdada experimentaram 67% menos comprometimentos do que aquelas em que a autenticação herdada está habilitada
>

Se o ambiente estiver pronto para bloquear a autenticação herdada para melhorar a proteção do locatário, você poderá atingir essa meta com acesso condicional. Este artigo explica como é possível configurar políticas de Acesso Condicional que bloqueiam autenticação herdada para locatário.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com os [conceitos básicos](overview.md) do acesso condicional do Azure AD.

## <a name="scenario-description"></a>Descrição do cenário

O Azure AD dá suporte para vários dos protocolos de autenticação e autorização mais amplamente utilizados, incluindo a autenticação herdada. Autenticação herdada refere-se a protocolos que usam autenticação básica. Normalmente, esses protocolos não podem impor nenhum tipo de autenticação de segundo fator. Exemplos de aplicativos baseados em autenticação herdada são:

- Aplicativos mais antigos do Microsoft Office
- Aplicativos que usam protocolos de email como POP, IMAP e SMTP

Autenticação de fator único (por exemplo, nome de usuário e senha) atualmente não é suficiente. Senhas são ruins porque são fáceis de adivinhar e nós (humanos) dificilmente escolhemos boas senhas. Senhas também são vulneráveis a uma variedade de ataques, como pulverização de senha e phishing. Uma das coisas mais fáceis que você pode fazer para se proteger contra ameaças à senha é implementar a MFA (autenticação multifator). Com MFA, mesmo se um invasor possuir a senha de um usuário, somente a senha não será suficiente para autenticar e acessar os dados com êxito.

Como é possível impedir que aplicativos usando autenticação herdada acessem os recursos do locatário? A recomendação é apenas bloqueá-los com uma política de Acesso Condicional. Se necessário, você permite que apenas determinados usuários e locais de rede específicos usem aplicativos baseados em autenticação herdada.

As políticas de Acesso Condicional são impostas após a conclusão da autenticação multifator. Portanto, o Acesso Condicional não funciona como uma primeira linha de defesa para cenários como ataques de DoS (ataque de negação de serviço), mas pode utilizar os sinais desses eventos (por exemplo, o nível de risco de entrada, a localização da solicitação e assim por diante) para determinar o acesso.

## <a name="implementation"></a>Implementação

Esta seção explica como configurar uma política de Acesso Condicional para bloquear a autenticação herdada. 

### <a name="legacy-authentication-protocols"></a>Protocolos de autenticação herdados

As opções a seguir são consideradas protocolos de autenticação herdados

- SMTP autenticado-usado por clientes POP e IMAP para enviar mensagens de email.
- Descoberta automática - usada pelos clientes do Outlook e do EAS para localizar e conectar-se às caixas de correio no Exchange Online.
- Exchange ActiveSync (EAS) – usado para conectar-se às caixas de correio no Exchange Online.
- Exchange Online PowerShell - usado para se conectar ao Exchange Online com o PowerShell remoto. Se você bloquear a autenticação básica para o Exchange Online PowerShell, será necessário usar o módulo do PowerShell do Exchange Online para se conectar. Para obter instruções, confira [Conectar ao Exchange Online PowerShell usando a autenticação multifator](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Serviços Web do Exchange (EWS) - uma interface de programação usada pelo Outlook, pelo Outlook para Mac e por aplicativos de terceiros.
- IMAP4 – usado por clientes de email IMAP.
- MAPI sobre HTTP (MAPI/HTTP) – usado pelo Outlook 2010 e posterior.
- OAB (catálogo de endereços offline) – uma cópia das coleções de listas de endereços que são baixadas e usadas pelo Outlook.
- Outlook em Qualquer Lugar (RPC por HTTP) - usado pelo Outlook 2016 e anterior.
- Serviço do Outlook – usado pelo aplicativo de email e calendário para Windows 10.
- POP3 - usado por clientes de email POP.
- Serviços Web de relatórios - usados para recuperar dados de relatório no Exchange Online.
- Outros clientes - outros protocolos identificados que utilizam a autenticação herdada.

Para saber mais sobre esses protocolos e serviços de autenticação, confira [Relatórios de atividade de entrada no portal do Azure Active Directory](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Identificar uso de autenticação herdada

Antes de poder bloquear a autenticação herdada em seu diretório, primeiro você precisará entender se os usuários têm aplicativos que usam autenticação herdada e como ele afeta o diretório geral. Os logs de entrada do Azure AD podem ser usados para entender se você está usando a autenticação herdada.

1. Navegue até o **portal do Azure** > **Azure Active Directory** > **Entradas**.
1. Adicione a coluna Aplicativo cliente se ela não for exibida clicando em **Colunas** > **Aplicativo cliente**.
1. **Adicionar filtros**  >  > de **aplicativo cliente** selecione todos os protocolos de autenticação herdados. Selecione fora da caixa de diálogo filtragem para aplicar suas seleções e feche a caixa de diálogo.
1. Se você ativou a [nova visualização de relatórios de atividade de entrada](../reports-monitoring/concept-all-sign-ins.md), repita as etapas acima também na guia entradas de **usuário (não interativa)** .

A filtragem mostrará apenas as tentativas de entrada feitas por protocolos de autenticação herdados. Clicar em cada tentativa de entrada individual mostrará detalhes adicionais. O campo **Aplicativo cliente** na guia **Informações básicas** indicarão qual protocolo de autenticação herdado foi usado.

Esses logs indicarão quais usuários ainda estão dependendo da autenticação herdada e quais aplicativos estão usando protocolos herdados para fazer solicitações de autenticação. Para usuários que não aparecem nesses logs e são confirmados por não usar a autenticação herdada, implemente uma política de Acesso Condicional somente para esses usuários.

## <a name="block-legacy-authentication"></a>Bloquear a autenticação herdada 

Há duas maneiras de usar políticas de acesso condicional para bloquear a autenticação herdada.

- [Bloqueando diretamente a autenticação herdada](#directly-blocking-legacy-authentication)
- [Bloqueio indireto de autenticação herdada](#indirectly-blocking-legacy-authentication)
 
### <a name="directly-blocking-legacy-authentication"></a>Bloqueando diretamente a autenticação herdada

A maneira mais fácil de bloquear a autenticação herdada em toda a organização é Configurando uma política de acesso condicional que se aplica especificamente aos clientes de autenticação herdados e bloqueia o acesso. Ao atribuir usuários e aplicativos à política, certifique-se de excluir usuários e contas de serviço que ainda precisam entrar usando a autenticação herdada. Configure a condição de aplicativos cliente selecionando **clientes do Exchange ActiveSync** e **outros clientes**. Para bloquear o acesso para esses aplicativos cliente, configure os controles de acesso para bloquear o acesso.

![Condição de aplicativos cliente configurada para bloquear autenticação herdada](./media/block-legacy-authentication/client-apps-condition-configured-yes.png)

### <a name="indirectly-blocking-legacy-authentication"></a>Bloqueio indireto de autenticação herdada

Mesmo que sua organização não esteja pronta para bloquear a autenticação herdada em toda a organização, você deve garantir que as entradas usando autenticação herdada não ignorem políticas que exijam controles de concessão, como a exigência de autenticação multifator ou dispositivos ingressados no Azure AD híbrido ou em conformidade. Durante a autenticação, os clientes de autenticação herdados não dão suporte ao envio de informações de MFA, conformidade do dispositivo ou estado de ingresso para o Azure AD. Portanto, aplique políticas com controles de concessão a todos os aplicativos cliente para que as entradas com base em autenticação herdadas que não podem atender aos controles de concessão sejam bloqueadas. Com a disponibilidade geral da condição de aplicativos cliente em agosto de 2020, as políticas de acesso condicional recém-criadas se aplicam a todos os aplicativos cliente por padrão.

![Configuração padrão da condição de aplicativos cliente](./media/block-legacy-authentication/client-apps-condition-configured-no.png)

## <a name="what-you-should-know"></a>O que você deve saber

Bloquear o acesso usando **Outros clientes** também bloqueia o Exchange Online PowerShell e o Dynamics 365 usando a autenticação básica.

A configuração de uma política para **Outros clientes** bloqueia determinados clientes, como SPConnect, para toda a organização. Esse bloqueio acontece porque os clientes mais antigos são autenticados de maneiras inesperadas. O problema não se aplica aos principais aplicativos do Office, como os antigos clientes do Office.

Pode levar até 24 horas para que a política entre em vigor.

É possível selecionar todos os controles de concessão disponíveis para a condição de **Outros clientes**, no entanto, a experiência do usuário final será sempre a mesma - acesso bloqueado.

### <a name="sharepoint-online-and-b2b-guest-users"></a>Usuários convidados do SharePoint Online e B2B

Para bloquear o acesso de usuário B2B por meio da autenticação herdada ao SharePoint Online, as organizações devem desabilitar a autenticação herdada no SharePoint usando o `Set-SPOTenant` comando do PowerShell e definindo o `-LegacyAuthProtocolsEnabled` parâmetro como `$false` . Mais informações sobre como definir esse parâmetro podem ser encontradas no documento de referência do SharePoint PowerShell sobre [set-SPOTenant](/powershell/module/sharepoint-online/set-spotenant)

## <a name="next-steps"></a>Próximas etapas

- [Determinar o impacto usando o modo somente relatório de Acesso Condicional](howto-conditional-access-insights-reporting.md)
- Se você ainda não estiver familiarizado com a configuração de políticas de Acesso Condicional, confira [Exigir MFA para aplicativos específicos com Acesso Condicional do Azure Active Directory](../authentication/tutorial-enable-azure-mfa.md), para obter um exemplo.
- Para saber mais sobre suporte de autenticação moderna, veja [Como funciona a autenticação moderna para os aplicativos cliente do Office 2013 e do Office 2016](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
- [Como configurar um dispositivo ou aplicativo multifuncional para enviar email usando Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)