---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: Saiba como o Azure AD Identity Protection permite limitar a capacidade de um invasor de explorar uma identidade ou um dispositivo comprometidos ou um dispositivo que sofreu comprometimento conhecido ou suspeito anteriormente.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 01/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99a542d3208d5871d88c966fffc65cf16e0fbeee
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335400"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>O que é Azure Active Directory Identity Protection?

O Azure Active Directory [Identity](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology) Protection permite que as organizações configurem respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário.

## <a name="get-started"></a>Introdução

A Microsoft protege identidades baseadas em nuvem há mais de uma década. Com o Azure Active Directory Identity Protection, você pode usar os mesmos sistemas de proteção em seu ambiente que a Microsoft usa para proteger as identidades.

A grande maioria das violações de segurança ocorre quando os invasores conseguem acessar a um ambiente roubando a identidade de um usuário. Nos últimos anos, os invasores têm se tornado cada vez mais eficazes em aproveitar as violações de terceiros e usar ataques de phishing sofisticados. Assim que um invasor obtém acesso até mesmo a uma conta de usuário com privilégios baixos, é relativamente fácil para ele conseguir acessar recursos importantes da empresa por meio de movimentação lateral.

Como consequência, você precisa:

- Proteger todas as identidades, independentemente do nível de privilégio
- Agir proativamente para evitar o uso de identidades comprometidas

Descobrir identidades comprometidas não é uma tarefa fácil. O Azure Active Directory usa algoritmos de aprendizado de máquina e heurística adaptáveis para detectar anomalias e incidentes suspeitos que indicam identidades potencialmente comprometidas. Usando esses dados, o Identity Protection gera relatórios e alertas que permitem avaliar os problemas detectados e tomar as devidas ações de mitigação ou correção.

O Azure Active Directory Identity Protection é mais do que apenas uma ferramenta de monitoramento e criação de relatórios. Para proteger as identidades da sua organização, você pode configurar políticas de risco que respondem automaticamente a problemas detectados quando um nível de risco especificado foi alcançado. Essas políticas, entre outros Controles de Acesso condicional fornecidos pelo Azure Active Directory e pelo EMS ([Enterprise Mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/)), podem bloquear ou iniciar automaticamente ações de correção adaptáveis que incluem redefinições de senha e a imposição de autenticação multifator.

### <a name="identity-protection-capabilities"></a>Recursos do Identity Protection

**Detecção de vulnerabilidades e contas de risco:**  

- Fornecer recomendações personalizadas para melhorar a postura de segurança geral ao realçar as vulnerabilidades
- Calcular os níveis de risco de entrada
- Calcular os níveis de risco do usuário

**Investigação dos eventos de risco:**

- Enviar notificações para eventos de risco
- Investigar os eventos de risco usando informações relevantes e contextuais
- Fornecer fluxos de trabalho básicos para acompanhar as investigações
- Fornecer acesso fácil às ações de correção, tais como redefinição de senha

**Políticas de Acesso Condicional baseadas em risco:**

- Política para reduzir entradas arriscadas ao bloquear entradas ou exigir desafios de autenticação multifator
- Política para bloquear ou proteger contas de usuário arriscadas
- Política para exigir o registro para autenticação multifator

## <a name="identity-protection-roles"></a>Funções da proteção de identidade

Para equilibrar as atividades de gerenciamento em torno de sua implementação da proteção de identidade, você pode atribuir várias funções. O Azure AD Identity Protection dá suporte a três funções do diretório:

| Função | O que ele pode fazer | O que não pode fazer |
| :-- | --- | --- |
| Administrador global | Acesso completo à Proteção de Identidade, Proteção de Identidade integrada| |
| Administrador de segurança | Acesso total à proteção de identidade | Proteção de Identidade integrada, redefinir senhas para um usuário |
| Leitor de segurança | Acesso somente leitura para o Identity Protection | Integrar Proteção de Identidade, corrigir usuários, configurar políticas, redefinir senhas |

Para saber mais detalhes, consulte [Atribuindo funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

## <a name="detection"></a>Detecção

### <a name="vulnerabilities"></a>Vulnerabilidades

O Azure Active Directory Identity Protection analisa sua configuração e detecta vulnerabilidades que podem ter impacto nas identidades do usuário. Para saber mais, confira [Vulnerabilidades detectadas pelo Azure Active Directory Identity Protection](vulnerabilities.md).

### <a name="risk-events"></a>Eventos de risco

O Azure Active Directory usa algoritmos de aprendizado de máquina e heurística adaptáveis para detectar ações suspeitas relacionadas a identidades do usuário. O sistema cria um registro para cada ação suspeita detectada. Esses registros também são conhecidos como eventos de risco.  
Para obter mais detalhes, veja [Eventos de risco do Azure Active Directory](../active-directory-identity-protection-risk-events.md).

## <a name="investigation"></a>Investigação

Sua jornada pelo Identity Protection normalmente inicia no Painel do Identity Protection.

![Correção](./media/overview/1000.png "Correção")

O painel concede acesso a:

- Relatórios, como **Usuários sinalizados por riscos**, **Eventos de risco** e **Vulnerabilidades**
- Configurações como a definição das suas **Políticas de Segurança**, **Notificações** e **registro de autenticação multifator**

Este é normalmente seu ponto de partida para investigação, que é o processo de revisão de atividades, logs e outras informações relevantes relacionadas a um evento de risco para decidir se as etapas de correção e mitigação são necessárias, como a identidade foi comprometida e entender como ela foi usada.

Você pode vincular suas atividades de investigação para as [notificações](notifications.md) que o Azure Active Directory Protection envia por email.

## <a name="policies"></a>Políticas

Para implementar as respostas automatizadas, o Azure Active Directory Identity Protection fornece três políticas:

- [Política de registro de Autenticação Multifator](howto-mfa-policy.md)
- [Política de usuário de risco](howto-user-risk-policy.md)
- [Política de risco de entrada](howto-sign-in-risk-policy.md)

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>Próximas etapas

- [Canal 9: Azure AD e Identity Show: Versão Prévia do Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [Habilitando o Azure Active Directory Identity Protection](enable.md)
