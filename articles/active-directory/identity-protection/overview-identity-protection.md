---
title: O que é Azure Active Directory Identity Protection?
description: Detectar, corrigir, investigar e analisar o risco com o Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 01/05/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.custom: contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e274d35fde6a3d55c05bcb5a9f22e75a37aa3c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97955392"
---
# <a name="what-is-identity-protection"></a>O que é proteção de identidade?

Com a ferramenta Identity Protection, as organizações podem executar três tarefas importantes:

- Automatizar a detecção e a correção de riscos baseados em identidade.
- Investigar os riscos usando os dados no portal.
- Exportar os dados de detecção de riscos a utilitários de terceiros para análise adicional.

O Identity Protection usa os aprendizados que a Microsoft adquiriu com sua posição em organizações com o Azure AD, com o espaço do consumidor com as contas Microsoft e com os jogos do Xbox para proteger seus usuários. A Microsoft analisa 6,5 trilhões de sinais por dia para identificar e proteger os clientes contra ameaças.

Os sinais gerados pelo Identidade Protection e fornecidos a ele podem ser alimentados ainda mais em ferramentas como o Acesso Condicional para tomar decisões de acesso ou retroalimentar uma ferramenta SIEM (gerenciamento de evento e informações de segurança) para uma maior investigação com base nas políticas impostas por sua organização.

## <a name="why-is-automation-important"></a>Por que a automação é importante?

Em sua [postagem no blog em outubro de 2018](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843), Alex Weinert, líder da equipe de Segurança e Proteção de Identidade da Microsoft, explica por que a automação é tão importante ao lidar com o volume de eventos:

> Todos os dias, nossos sistemas de aprendizado de máquina e heurística fornecem pontuações de risco para 18 bilhões de tentativas de logon para mais de 800 milhões de contas diferentes, das quais 300 milhões são perceptivelmente feitas por adversários (entidades como criminosos e hackers).
>
> No Ignite do ano passado, falei sobre os três principais ataques em nossos sistemas de identidade. Este é o volume recente desses ataques
>   
>   - **Repetição de violação**: 4,6 bilhões de ataques detectados em maio de 2018
>   - **Pulverização de senha**: 350 mil em abril de 2018
>   - **Phishing**: é difícil quantificar exatamente, mas vimos 23 milhões de eventos de risco em março de 2018, sendo que muitos deles estão relacionados a phishing

## <a name="risk-detection-and-remediation"></a>Detecção e correção de riscos

O Identity Protection identifica riscos nas seguintes classificações:

| Tipo de detecção de risco | Descrição |
| --- | --- |
| Endereço IP anônimo | Entrada de um endereço IP anônimo (por exemplo: navegador Tor, VPNs para anonimato). |
| Viagem atípica | Entrada proveniente de uma localização atípica com base nas conexões recentes do usuário. |
| Endereço IP vinculado a malware | Entrada de um endereço IP vinculado a malware. |
| Propriedades de entrada desconhecidas | Entrada com propriedades que não vimos recentemente para o usuário especificado. |
| Credenciais vazadas | Indica que as credenciais válidas do usuário foram vazadas. |
| Pulverização de senha | Indica que vários nomes de usuário estão sendo atacados por meio de senhas comuns de maneira unificada, por força bruta. |
| Inteligência contra ameaças do Azure AD | As fontes internas e externas de inteligência contra ameaças da Microsoft identificaram um padrão de ataque conhecido. |
| Novo país | Essa detecção é descoberta pelo [MCAS (Microsoft Cloud App Security)](/cloud-app-security/anomaly-detection-policy#activity-from-infrequent-country). |
| Atividade de um endereço IP anônimo | Essa detecção é descoberta pelo [MCAS (Microsoft Cloud App Security)](/cloud-app-security/anomaly-detection-policy#activity-from-anonymous-ip-addresses). |
| Encaminhamento suspeito da caixa de entrada | Essa detecção é descoberta pelo [MCAS (Microsoft Cloud App Security)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-forwarding). |

Mais detalhes sobre esses riscos e como/quando são calculados podem ser encontrados no artigo, [O que é risco](concept-identity-protection-risks.md).

Os sinais de risco podem disparar esforços de remediação, como exigir que os usuários: executem a Autenticação Multifator do Azure AD, redefinam sua senha usando a redefinição de senha por autoatendimento ou bloqueiem até que um administrador executa uma ação.

## <a name="risk-investigation"></a>Investigação de riscos

Os administradores podem examinar as detecções e executar uma ação manual sobre elas, se necessário. Há três importantes relatórios que os administradores usam para investigações no Identity Protection:

- Usuários de risco
- Entradas de risco
- Detecções de risco

Mais informações podem ser encontradas no artigo, [Como investigar o risco](howto-identity-protection-investigate-risk.md).

### <a name="risk-levels"></a>Níveis de risco

O Identity Protection categoriza os riscos em três camadas: baixa, média e alta. 

Embora a Microsoft não forneça detalhes específicos sobre como o risco é calculado, diremos que cada nível traz uma maior confiança de que a entrada ou o usuário está comprometido. Por exemplo, algo como uma instância de propriedades de entrada desconhecidas para um usuário pode não ser tão ameaçador quanto as credenciais vazadas para outro usuário.

## <a name="exporting-risk-data"></a>Exportar dados de risco

Os dados do Identity Protection podem ser exportados para outras ferramentas para serem arquivados, investigados ainda mais e correlacionados. As APIs baseadas no Microsoft Graph permitem que as organizações coletem esses dados para processamento adicional em uma ferramenta como o SIEM. Informações sobre como acessar a API do Identity Protection podem ser encontradas no artigo, [Introdução ao Azure Active Directory Identity Protection e ao Microsoft Graph](howto-identity-protection-graph-api.md)

Informações sobre integrar o Identity Protection ao Azure Sentinel podem ser encontradas no artigo, [Conectar dados do Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Permissões

O Identity Protection requer que os usuários sejam um Leitor de Segurança, Operador de Segurança, Administrador da Segurança, Leitor Global ou Administrador Global para acessarem.

| Função | O que ele pode fazer | O que não pode fazer |
| --- | --- | --- |
| Administrador global | Acesso total à proteção de identidade |   |
| Administrador de segurança | Acesso total à proteção de identidade | Redefinir senha para um usuário |
| Operador de segurança | Exibir todos os relatórios da Proteção de Identidade e a folha de Visão Geral <br><br> Ignorar o risco do usuário, confirmar a entrada segura, confirmar o comprometimento | Configurar ou alterar políticas <br><br> Redefinir senha para um usuário <br><br> Configurar alertas |
| Leitor de segurança | Exibir todos os relatórios da Proteção de Identidade e a folha de Visão Geral | Configurar ou alterar políticas <br><br> Redefinir senha para um usuário <br><br> Configurar alertas <br><br> Fornecer comentários sobre as detecções |

Atualmente, a função de operador de segurança não pode acessar o relatório de entradas suspeitas.

Os administradores do Acesso Condicional também podem criar políticas que consideram o risco de entrada como uma condição. Encontre mais informações no artigo [Acesso Condicional: Condições](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk).

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Recurso | Detalhes  | Aplicativos do Azure AD Gratuito / Microsoft 365 | Azure AD Premium P1|Azure AD Premium P2 |
| --- | --- | --- | --- | --- |
| Políticas de risco | Política de risco do usuário (por meio do Identity Protection)  | Não | Não |Sim | 
| Políticas de risco | Política de risco de entrada (por meio do Identity Protection ou do acesso condicional)  | Não |  Não |Sim |
| Relatórios de segurança | Visão geral |  Não | Não |Sim |
| Relatórios de segurança | Usuários de risco  | Informações limitadas. Somente os usuários com risco médio e alto são mostrados. Sem gaveta de detalhes ou histórico de riscos. | Informações limitadas. Somente os usuários com risco médio e alto são mostrados. Sem gaveta de detalhes ou histórico de riscos. | Acesso completo|
| Relatórios de segurança | Entradas de risco  | Informações limitadas. Nenhum detalhe ou nível de risco é mostrado. | Informações limitadas. Nenhum detalhe ou nível de risco é mostrado. | Acesso completo|
| Relatórios de segurança | Detecções de risco   | Não | Informações limitadas. Sem gaveta de detalhes.| Acesso completo|
| Notificações | Alertas de usuários em risco detectados  | Não | Não |Sim |
| Notificações | Resumo semanal| Não | Não | Sim | 
| | Política de registro de MFA | Não | Não | Sim |

Mais informações sobre esses relatórios avançados podem ser encontradas no artigo [Instruções: investigar o risco](howto-identity-protection-investigate-risk.md#navigating-the-reports).

## <a name="next-steps"></a>Próximas etapas

- [Visão geral de segurança](concept-identity-protection-security-overview.md)

- [O que é risco](concept-identity-protection-risks.md)

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)
