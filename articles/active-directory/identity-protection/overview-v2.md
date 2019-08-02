---
title: O que é Azure Active Directory Identity Protection (atualizado)? | Microsoft Docs
description: O que é Azure Active Directory Identity Protection (atualizado)?
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 10/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5d82f665cc837e5dbd2d7efbf16c7ae94e1b2b7
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333904"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>O que é Azure Active Directory Identity Protection (atualizado)?

A experiência do Identity Protection foi atualizada para proteger melhor as identidades da organização. Essa experiência atualizada oferece:

- Experiência de administração reprojetada que gira em torno do risco mais relevante para você: risco do usuário e risco de entrada
- Experiência de investigações poderosa compatível com filtragem, classificação e downloads inteligentes
- Cálculo de risco do usuário aprimorado para ajudar você a priorizar seus esforços para os usuários com maior probabilidade de comprometimento
- Nova compatibilidade com API para habilitar o acesso programático aos dados de risco
- Processo de feedback da administração simplificado que permite que você proteja imediatamente seus usuários
- Novo aprendizado de máquina supervisionado para melhorar a precisão das avaliações de risco

A segurança é uma grande preocupação para as organizações atualmente. A maioria das violações de segurança ocorre quando os invasores conseguem acesso a um ambiente roubando a identidade de um usuário. Nos últimos anos, os invasores têm se tornado cada vez mais eficazes em aproveitar as violações de terceiros e usar ataques de phishing sofisticados. Assim que os invasores obtêm acesso a até mesmo a uma conta de usuário com privilégios baixos, é relativamente fácil para eles conseguirem acessar recursos importantes da empresa por meio de movimentação lateral. 

Para responder a essas ameaças, o Azure AD Identity Protection capacita você para: 

- Agir proativamente para evitar o uso de identidades comprometidas 
- Atenuar o risco automaticamente quando a atividade suspeita é detectada 
- Investigar as entradas e os usuários arriscados para abordar possíveis vulnerabilidades  
- Ser alertado quando o risco de um usuário atinge um limite especificado 

O Azure AD Identity Protection é um recurso do Azure Active Directory Premium P2 que permite que você configure políticas para responder automaticamente quando a identidade de um usuário é comprometida ou quando alguém diferente do proprietário da conta está tentando entrar usando sua identidade. Essas políticas, entre outros Controles de Acesso condicional fornecidos pelo Azure AD, podem bloquear o acesso ou iniciar automaticamente as ações de mitigação, como a redefinição de senha ou a imposição da autenticação multifator. Além disso, o Identity Protection fornece recursos de monitoramento e relatórios para obter insights mais aprofundados sobre o risco e possíveis comprometimentos na sua organização. 

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWsS6Q]

## <a name="risk-events"></a>Eventos de risco

O Azure AD Identity Protection detecta os seguintes eventos de risco: 

| Tipo de evento de risco | DESCRIÇÃO | Tipo de detecção |
| --- | --- | --- |
| Viagem atípica | Entrada de uma localização atípica com base nas entradas recentes do usuário. | Off-line |
| Endereço IP anônimo | Entrada de um endereço IP anônimo (por exemplo: navegador Tor, VPNs para anonimato). | Tempo real |
| Propriedades de entrada desconhecidas | Entrada com propriedades que não vimos recentemente para o usuário especificado. | Tempo real |
| Endereço IP vinculado a malware | Entrada de um endereço IP vinculado a malware | Off-line |
| Credenciais vazadas | Este evento de risco indica que as credenciais válidas do usuário foram vazadas | Off-line |

## <a name="types-of-risk"></a>Tipos de risco 

A Proteção de identidade se baseia em dois tipos de risco:

- Risco de entrada
- Risco do usuário

### <a name="sign-in-risk"></a>Risco de entrada

Um risco de entrada representa a probabilidade de uma determinada solicitação de autenticação não estar autorizada pelo proprietário da identidade.

Há duas avaliações do risco de entrada: 

- **Risco de entrada (em tempo real)** : o risco de entrada (em tempo real) se baseia em todas as detecções em tempo real que disparam durante o processamento da entrada.  
- **Risco de entrada (agregação)** : o risco de entrada (agregação) é o risco total de uma entrada. Ele é calculado por um modelo de machine learning que considera:
   - Detecções em tempo real (descritas acima)
   - Detecções offline (que são acionadas depois que a entrada ocorreu) 
   - Todos os outros recursos de entrada

### <a name="user-risk"></a>Risco do usuário

Um risco do usuário representa a probabilidade de uma determinada identidade estar comprometida. 

O risco do usuário é calculado considerando todos os riscos associados ao usuário:

- Todos as entradas arriscadas
- Todos os eventos de risco não vinculados a uma entrada
- O risco do usuário atual
- Qualquer ação de correção ou descarte realizada no usuário até a data

## <a name="how-identity-protection-detects-risk"></a>Como o Identity Protection detecta riscos  

O Azure AD usa o aprendizado de máquina para detectar anomalias e atividades suspeitas usando os dois sinais detectados em tempo real durante as entradas, bem como os sinais que não estão em tempo real relacionados aos usuários e suas atividades de entrada. Usando esses dados, o Identity Protection calcula um risco de entrada em tempo real sempre que um usuário é autenticado, bem como determina um nível de risco geral do usuário para cada usuário. O Identity Protection permite que você tome providências automaticamente nessas detecções de risco configurando o risco de usuário do Identity Protection e as políticas de risco de entrada.  

Para entender como o Identity Protection detecta riscos, há dois conceitos importantes: risco do usuário e o risco de entrada. O risco de entrada reflete a probabilidade de uma determinada solicitação de autenticação não estar autorizada pelo proprietário da identidade. Há dois tipos de riscos de entrada: total e em tempo real. O risco de entrada em tempo real é detectado no momento da tentativa de entrada especificada (por exemplo, entradas de endereços IP anônimos). O risco de entrada total é a agregação dos riscos de entrada em tempo real, bem como quaisquer eventos de risco não em tempo real subsequentes associados com as entradas do usuário (como viagem impossível). O risco do usuário reflete a possibilidade geral de um ator mal-intencionado ter comprometido uma identidade fornecida. O risco do usuário contém todas as atividades de risco de um determinado usuário, incluindo:

- Risco de entrada em tempo real
- Risco de entrada subsequente
- Detecções de usuário arriscados.   

 ![Flow](./media/overview-v2/01.png)

O fluxo de linha de base para a detecção de risco e resposta do Identity Protection para qualquer entrada fornecida está resumido no gráfico acima.  

## <a name="common-scenarios"></a>Cenários comuns 

Vamos ver um exemplo de um funcionário da Contoso. 

1. Um funcionário tenta entrar no Exchange Online do navegador Tor. No momento da entrada, o Azure AD detecta eventos de risco em tempo real. 
2. O Azure AD detecta que o funcionário está entrando de um endereço IP anônimo, disparando um nível de risco de entrada médio. 
3. O funcionário é desafiado por um prompt de MFA, porque o administrador de TI da Contoso configurou a política de Acesso Condicional de risco de entrada do Identity Protection. A política exige o MFA para um risco de entrada médio ou mais alto. 
4. O funcionário passa o prompt do MFA e acessa o Exchange Online e seu nível de risco de usuário não é alterado. 

O que aconteceu nos bastidores? A tentativa de logon do navegador Tor disparou um risco de entrada em tempo real no Azure AD para o endereço IP anônimo. Como o Azure AD processou a solicitação, ele aplicou a política de risco de entrada configurada no Identity Protection porque o nível de risco de entrada do funcionário atendeu ao limite (Médio). Como o funcionário se registrou anteriormente no MFA, ele pôde responder e passar no desafio do MFA. Sua capacidade de passar com sucesso no desafio do MFA sinalizou para o Azure AD que provavelmente ele é o proprietário da identidade legítima e seu nível de risco do usuário não aumenta. 

Mas e se não era o funcionário que estava tentando entrar? 

1. Um ator mal-intencionado com as credenciais do funcionário tenta entrar na sua conta do Exchange Online pelo navegador Tor, já que está tentando ocultar o endereço IP. 
2. O Azure AD detecta que a tentativa de entrada é de um endereço IP anônima, disparando um risco de entrada em tempo real. 
3. O ator mal-intencionado é desafiado por um prompt de MFA, porque o administrador de TI da Contoso configurou a política de acesso condicional de risco de entrada do Identity Protection para exigir o MFA quando o risco de entrada for médio ou mais alto. 
4. O ator mal-intencionado falhará no desafio de MFA e não conseguirá acessar a conta do Exchange Online do funcionário. 
5. O prompt de MFA com falha disparou um evento de risco a ser registrado, aumentando o risco do usuário para entradas futuras. 

Agora que um ator mal-intencionado tentou acessar a conta do funcionário, vamos ver o que acontece na próxima vez que ele tenta entrar. 

1. O funcionário tenta entrar no Exchange Online pelo Outlook. No momento da entrada, o Azure AD detecta eventos de risco em tempo real, bem como risco de usuário anterior. 
2. O Azure AD não detecta nenhum risco de entrada em tempo real, mas detecta o risco do usuário alto devido à atividade arriscada anterior nos cenários anteriores.  
3. O funcionário é desafiado por uma solicitação de redefinição de senha, pois o administrador de TI da Contoso configurou a política de risco do usuário do Identity Protection para exigir a alteração de senha quando um usuário com risco alto fizer logon. 
4. Como o funcionário está registrado para SSPR e MFA, ele redefinirá sua senha com êxito. 
5. Ao redefinir a senha, as credenciais do funcionário não estão mais comprometidas e sua identidade retorna para um estado seguro. 
6. Os eventos de risco anteriores do funcionário são resolvidos e seu nível de risco é automaticamente redefinido como uma resposta para atenuar o comprometimento das credenciais. 

## <a name="how-do-i-configure-identity-protection"></a>Como configurar o Identity Protection? 

Para começar a trabalhar com o Identity Protection, configure primeiro uma política de risco do usuário e uma política de risco de entrada. Depois que essas políticas são configuradas e aplicadas a um grupo de teste, você pode simular eventos de risco para entender como o Identity Protection responderá em seu ambiente. O guia de início rápido abaixo fornece um passo a passo sobre como configurar as políticas mencionadas anteriormente e testar em seu ambiente. 

O Identity Protection dá suporte a três funções no Azure AD para equilibrar as atividades de gerenciamento em torno da sua implantação: 

| Função | O que ele pode fazer | O que não pode fazer |
| --- | --- | --- |
| Administrador global | Acesso completo à Proteção de Identidade, Proteção de Identidade integrada | |
| Administrador de segurança | Acesso total à proteção de identidade | Proteção de Identidade integrada, redefinir senhas para um usuário |
| Leitor de segurança | Acesso somente leitura para o Identity Protection | Integrar Proteção de Identidade, corrigir usuários, configurar políticas, redefinir senhas| 

Para saber mais informações, consulte [Atribuindo funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
 
## <a name="licensing"></a>Licenciamento

>[!NOTE]
> Durante a versão prévia pública do Identity Protection (atualizada), somente os clientes do Azure AD Premium P2 terão acesso ao relatório de usuários arriscados e ao relatório de entradas arriscadas.

| Recurso | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Básico/Gratuito |
| --- | --- | --- | --- |
| Política de risco do usuário | Sim | Não | Não |
| Política de risco de entrada | Sim | Não | Não |
| Relatório de usuários arriscados | Acesso completo | Informações limitadas | Informações limitadas |
| Relatório de entradas arriscadas | Acesso completo | Informações limitadas | Informações limitadas |
| Política de registro de MFA | Sim | Não | Não |

## <a name="next-steps"></a>Próximas etapas 

Para se familiarizar com o Identity Protection, confira [Configurar a política de risco de entrada](quickstart-sign-in-risk-policy.md). 
