---
title: Como bloquear autenticação herdados para o Azure Active Directory (Azure AD) com acesso condicional | Microsoft Docs
description: Saiba como melhorar sua postura de segurança bloqueando autenticação herdados usando o acesso condicional do Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60fc168a6c15e7cb8d6e4271154d99fb4136226e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509027"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Como: Autenticação herdados do bloco para o Azure AD com acesso condicional   

Para fornecer aos usuários acesso fácil aos aplicativos na nuvem, o Azure AD (Azure Active Directory) dá suporte a uma ampla variedade de protocolos de autenticação, incluindo a autenticação herdada. No entanto, protocolos herdados não dão suporte para MFA (autenticação multifator). Em muitos ambientes, a MFA é um requisito comum para lidar com roubo de identidade. 

Se seu ambiente está preparado para o bloco de autenticação herdados para melhorar a proteção do seu locatário, você pode atingir essa meta com acesso condicional. Este artigo explica como você pode configurar políticas de acesso condicional que bloqueiam autenticação herdados para seu locatário.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com: 

- O [conceitos básicos](overview.md) de acesso condicional do Azure AD 
- O [práticas recomendadas](best-practices.md) para configurar políticas de acesso condicional no portal do Azure

## <a name="scenario-description"></a>Descrição do cenário

O Azure AD dá suporte para vários dos protocolos de autenticação e autorização mais amplamente utilizados, incluindo a autenticação herdada. Autenticação herdada refere-se a protocolos que usam autenticação básica. Normalmente, esses protocolos não podem impor nenhum tipo de autenticação de segundo fator. Exemplos de aplicativos baseados em autenticação herdada são:

- Aplicativos mais antigos do Microsoft Office
- Aplicativos que usam protocolos de email como POP, IMAP e SMTP

Autenticação de fator único (por exemplo, nome de usuário e senha) atualmente não é suficiente. Senhas são ruins porque são fáceis de adivinhar e nós (humanos) dificilmente escolhemos boas senhas. Senhas também são vulneráveis a uma variedade de ataques, como pulverização de senha e phishing. Uma das medidas mais fáceis que você pode tomar para proteção contra ameaças de senha é implementar MFA. Com MFA, mesmo se um invasor possuir a senha de um usuário, somente a senha não será suficiente para autenticar e acessar os dados com êxito.

Como é possível impedir que aplicativos usando autenticação herdada acessem os recursos do locatário? A recomendação é simplesmente bloqueá-los com uma política de acesso condicional. Se necessário, você permite que apenas determinados usuários e locais de rede específicos usem aplicativos baseados em autenticação herdada.

Políticas de acesso condicional são impostas após a autenticação multifator primeiro ter sido concluída. Portanto, o acesso condicional não serve como uma primeira linha de defesa para cenários como negação de serviço (DoS) ataques, mas podem utilizar os sinais desses eventos (por exemplo, o nível de risco de entrada, o local da solicitação e assim por diante) para determinar o acesso.

## <a name="implementation"></a>Implementação

Esta seção explica como configurar uma política de acesso condicional para o bloco de autenticação herdados. 

### <a name="identify-legacy-authentication-use"></a>Identificar o uso de autenticação herdados

Antes de você pode bloquear autenticação herdados em seu diretório, você precisa primeiro entender se os usuários têm aplicativos que usam autenticação herdados e como ela afeta seu diretório geral. Logs de entrada do AD do Azure podem ser usados para entender se você estiver usando autenticação herdados.

1. Navegue até a **portal do Azure** > **do Azure Active Directory** > **entradas**.
1. Adicionar a coluna de aplicativo cliente se ele não for exibido ao clicar no **colunas** > **aplicativo cliente**.
1. Filtrar por **aplicativo cliente** > **outros clientes** e clique em **aplicar**.

Filtragem somente para mostrar a você entrar tentativas feitas por protocolos de autenticação herdados. Clicar em cada tentativa de logon individual mostrará detalhes adicionais. O **aplicativo cliente** campo sob o **informações básicas de** guia indicará qual protocolo de autenticação herdada foi usado.

Esses logs indica que os usuários ainda dependem de autenticação herdados e quais aplicativos estão usando os protocolos herdados para fazer solicitações de autenticação. Para usuários que não aparecem nesses logs e confirmados que não esteja usando autenticação herdados, implemente uma política de acesso condicional para apenas esses usuários.

### <a name="block-legacy-authentication"></a>Bloquear a autenticação herdada 

Em uma política de acesso condicional, você pode definir uma condição que está vinculada aos aplicativos cliente que são usados para acessar seus recursos. A condição de aplicativos cliente permite restringir o escopo a aplicativos usando autenticação herdada, selecionando **Outros clientes** para **Aplicativos móveis e clientes de desktop**.

![Outros clientes](./media/block-legacy-authentication/01.png)

Para bloquear o acesso a esses aplicativos, é necessário selecionar **Bloquear acesso**.

![Acesso bloqueado](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Selecione usuários e aplicativos na nuvem

Se você quiser bloquear a autenticação herdada para sua organização, provavelmente pressupõe que é possível fazer isso, selecionando:

- Todos os usuários
- Todos os aplicativos em nuvem
- Acesso bloqueado

![Atribuições](./media/block-legacy-authentication/03.png)

O Azure tem um recurso de segurança que impede que você crie uma política assim porque essa configuração viola a [práticas recomendadas](best-practices.md) políticas de acesso condicional.
 
![Sem suporte para configuração de política](./media/block-legacy-authentication/04.png)

O recurso de segurança é necessário porque *bloqueia todos os usuários e todos os aplicativos na nuvem* e tem o potencial de impedir que toda a organização faça autenticação no locatário. É necessário excluir pelo menos um usuário para satisfazer o requisito mínimo de melhor prática. Também é possível excluir uma função de diretório.

![Sem suporte para configuração de política](./media/block-legacy-authentication/05.png)

Você pode satisfazer esse recurso de segurança, excluindo um usuário da sua política. O ideal, é definir algumas [contas administrativas de acesso para emergência no Azure AD](../users-groups-roles/directory-emergency-access.md) e excluí-las da política.

## <a name="policy-deployment"></a>Implantação de política

Antes de colocar a política em produção, atente-se para:
 
- **Contas de serviço** - Identifique as contas de usuário que são usadas como contas de serviço ou por dispositivos, como telefones de sala de conferência. Certifique-se de que essas contas têm senhas fortes e adicione-as a um grupo excluído.
- **Relatórios de entradas** - Analise o relatório de entrada e procure **outro tráfego de cliente**. Identifique o uso principal e investigue por que ele está em uso. Normalmente, o tráfego é gerado por clientes mais antigos do Office que não usam autenticação moderna ou alguns aplicativos de email de terceiros. Faça um plano para afastar o uso desses aplicativos ou, se o impacto for baixo, notifique os usuários de que eles não podem mais usar esses aplicativos.
 
Para obter mais informações, consulte [Como implantar uma nova política?](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>O que você deve saber

Bloqueio de acesso usando **outros clientes** também bloqueia o PowerShell do Exchange Online usando a autenticação básica.

A configuração de uma política para **Outros clientes** bloqueia determinados clientes, como SPConnect, para toda a organização. Esse bloqueio acontece porque os clientes mais antigos são autenticados de maneiras inesperadas. O problema não se aplica aos principais aplicativos do Office, como os antigos clientes do Office.

Pode levar até 24 horas para que a política entre em vigor.

Você pode selecionar todos os controles de concessão disponível para o **outros clientes** condição; no entanto, a experiência do usuário final é sempre o mesmo - bloqueou o acesso.

Se você bloquear o uso de autenticação herdados a **outros clientes** condição, você também pode definir a condição de plataforma e o local do dispositivo. Por exemplo, se você quiser bloquear apenas a autenticação herdada para dispositivos móveis, defina a condição **plataformas de dispositivo** selecionando:

- Android
- iOS
- Windows Phone

![Sem suporte para configuração de política](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Próximas etapas

- Se você não estiver familiarizado com a configuração de políticas de acesso condicional ainda, consulte [exigir MFA para aplicativos específicos com acesso condicional do Azure Active Directory](app-based-mfa.md) para obter um exemplo.
- Para obter mais informações sobre o suporte a autenticação moderna, consulte [como a autenticação moderna funciona para aplicativos de cliente do Office 2013 e Office 2016](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
