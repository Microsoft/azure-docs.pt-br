---
title: O que é risco? Azure AD Identity Protection
description: Explicando o risco no Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18e504579c750caf452ef74844c4a388ec96448a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97954478"
---
# <a name="what-is-risk"></a>O que é risco?

As detecções de risco no Azure AD Identity Protection incluem quaisquer ações suspeitas identificadas relacionadas a contas de usuário no diretório.

A proteção de identidade fornece às organizações acesso a recursos avançados para ver e responder rapidamente a essas ações suspeitas. 

![Visão geral de segurança mostrando usuários e entradas arriscadas](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

> [!NOTE]
> A proteção de identidade gera detecções de risco somente quando as credenciais corretas são usadas. Se credenciais incorretas forem usadas em uma entrada, isso não representará o risco de comprometimento de credenciais.

## <a name="risk-types-and-detection"></a>Tipos de risco e detecção

Há dois tipos de **usuário** de risco e **entrada** e dois tipos de detecção ou cálculo em **tempo real** e **offline**.

As detecções em tempo real podem não aparecer no relatório de cinco a dez minutos. As detecções offline podem não aparecer no relatório de duas a vinte e quatro horas.

### <a name="user-risk"></a>Risco do usuário

Um risco de usuário representa a probabilidade de que uma determinada identidade ou conta seja comprometida. 

Esses riscos são calculados offline usando fontes de inteligência contra ameaças internas e externas da Microsoft, incluindo pesquisadores de segurança, profissionais de imposição de leis, equipes de segurança da Microsoft e outras fontes confiáveis.

| Detecção de riscos | Descrição |
| --- | --- |
| Credenciais vazadas | Esse tipo de detecção de risco indica que as credenciais válidas do usuário foram vazadas. Quando criminosos cibernéticos comprometem senhas válidas de usuários legítimos, geralmente compartilham essas credenciais. Normalmente, esse compartilhamento é feito postando-se publicamente na Web escura, colando sites ou negociando e vendendo as credenciais no mercado preto. Quando o serviço de credenciais vazadas da Microsoft adquire credenciais de usuário da Web escura, colar sites ou outras fontes, eles são verificados em relação às credenciais válidas atuais dos usuários do Azure AD para encontrar correspondências válidas. Para obter mais informações sobre credenciais vazadas, consulte [perguntas comuns](#common-questions). |
| Inteligência contra ameaças do Azure AD | Esse tipo de detecção de risco indica a atividade do usuário que é incomum para o usuário determinado ou é consistente com padrões de ataque conhecidos com base nas fontes de inteligência contra ameaças internas e externas da Microsoft. |

### <a name="sign-in-risk"></a>Risco de entrada

Um risco de entrada representa a probabilidade de uma determinada solicitação de autenticação não estar autorizada pelo proprietário da identidade. 

Esses riscos podem ser calculados em tempo real ou calculados offline usando fontes de inteligência contra ameaças internas e externas da Microsoft, incluindo pesquisadores de segurança, profissionais de imposição de leis, equipes de segurança da Microsoft e outras fontes confiáveis.

| Detecção de riscos | Tipo de detecção | Descrição |
| --- | --- | --- |
| Endereço IP anônimo | Tempo real | Esse tipo de detecção de risco indica entradas de um endereço IP anônimo (por exemplo, navegador Tor ou VPN anônima). Normalmente, esses endereços IP são usados por atores que desejam ocultar sua telemetria de logon (endereço IP, localização, dispositivo etc.) para, potencialmente, más intenções. |
| Viagem atípica | Offline | Esse tipo de detecção de risco identifica duas entradas provenientes de locais geograficamente distantes, em que pelo menos um dos locais também pode ser atípicos para o usuário, dado o comportamento passado. Entre muitos outros fatores, esse algoritmo de aprendizado de máquina leva em consideração o tempo entre as duas entradas e o tempo que seria necessário para o usuário ir do primeiro até o segundo local, indicando que um usuário diferente está usando as mesmas credenciais. <br><br> Esse algoritmo ignora “falsos positivos” óbvios que contribuem para condições impossíveis de viagem, como VPNs e locais regularmente usados por outros usuários na organização. O sistema tem um período de aprendizado inicial do anterior de 14 dias ou 10 logons, durante o qual ele aprende o comportamento de entrada de um novo usuário. |
| Endereço IP vinculado a malware | Offline | Esse tipo de detecção de risco indica entradas de endereços IP infectados com malware que é conhecido por se comunicar ativamente com um servidor de bot. Essa detecção é determinada pela correlação de endereços IP do dispositivo do usuário em endereços IP que estavam em contato com um servidor de bot enquanto o servidor de bot estava ativo. |
| Propriedades de entrada desconhecidas | Tempo real | Esse tipo de detecção de risco considera o histórico de entrada anterior (IP, latitude/longitude e ASN) para procurar por entradas anormais. O sistema armazena informações sobre os locais anteriores usados por um usuário e considera esses locais "familiares". A detecção de risco é disparada quando a entrada ocorre de um local que ainda não está na lista de locais familiares. Os usuários recém-criados estarão no "modo de aprendizado" por um período em que as propriedades de entrada desconhecidas as detecções de risco serão desativadas enquanto nossos algoritmos aprendem o comportamento do usuário. A duração do modo de aprendizado é dinâmica e depende de quanto tempo leva o algoritmo para reunir informações suficientes sobre os padrões de entrada do usuário. A duração mínima é de cinco dias. Um usuário pode voltar para o modo de aprendizado após um longo período de inatividade. O sistema também ignora entradas de dispositivos conhecidos e locais que são geograficamente próximos de uma localização familiar. <br><br> Também podemos executar essa detecção para a autenticação Básica (ou protocolos herdados). Como esses protocolos não têm propriedades modernas como a ID do cliente, há uma telemetria limitada para reduzir os falsos positivos. Recomendamos que nossos clientes mudem para a autenticação moderna. |
| Usuário comprometido confirmado pelo administrador | Offline | Essa detecção indica que um administrador selecionou ' confirmar o usuário comprometido ' na interface do usuário de usuários arriscados ou usando a API riskyUsers. Para ver qual administrador confirmou que esse usuário está comprometido, verifique o histórico de risco do usuário (por meio da interface de usuário ou API). |
| Endereço IP mal-intencionado | Offline | Essa detecção indica a entrada de um endereço IP mal-intencionado. Um endereço IP é considerado mal-intencionado com base em taxas de falha altas devido a credenciais inválidas recebidas do endereço IP ou outras fontes de reputação de IP. |
| Regras de manipulação de caixa de entrada suspeita | Offline | Essa detecção é descoberta pelo [MCAS (Microsoft Cloud App Security)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Essa detecção analisa o ambiente e dispara alertas quando regras suspeitas que excluem ou movem mensagens ou pastas são definidas na caixa de entrada de um usuário. Essa detecção pode indicar que a conta do usuário está comprometida, que as mensagens estão sendo intencionalmente ocultas e que a caixa de correio está sendo usada para distribuir spam ou malware na sua organização. |
| Pulverização de senha | Offline | Um ataque de irrigação de senha é onde vários nomes de usuários são atacados usando senhas comuns em uma maneira de força bruta unificada para obter acesso não autorizado. Essa detecção de risco é disparada quando um ataque de irrigação de senha é executado. |
| Viagem impossível | Offline | Essa detecção é descoberta pelo [MCAS (Microsoft Cloud App Security)](/cloud-app-security/anomaly-detection-policy#impossible-travel). Essa detecção identifica duas atividades do usuário (é uma única ou várias sessões) provenientes de locais geograficamente distantes em um período de tempo menor que o tempo que teria levado para o usuário viajar do primeiro local para o segundo, indicando que um usuário diferente está usando as mesmas credenciais. |
| Novo país | Offline | Essa detecção é descoberta pelo [MCAS (Microsoft Cloud App Security)](/cloud-app-security/anomaly-detection-policy#activity-from-infrequent-country). Essa detecção considera os locais de atividades anteriores para determinar os locais novos e pouco frequentes. O mecanismo de detecção de anomalias armazena informações sobre locais anteriores usados por usuários na organização. |
| Atividade de um endereço IP anônimo | Offline | Essa detecção é descoberta pelo [MCAS (Microsoft Cloud App Security)](/cloud-app-security/anomaly-detection-policy#activity-from-anonymous-ip-addresses). Essa detecção identifica que os usuários estavam ativos com base em um endereço IP que foi identificado como um endereço IP de proxy anônimo. |
| Encaminhamento suspeito da caixa de entrada | Offline | Essa detecção é descoberta pelo [MCAS (Microsoft Cloud App Security)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-forwarding). Essa detecção procura regras de encaminhamento de email suspeito, por exemplo, se um usuário criou uma regra de caixa de entrada que encaminha uma cópia de todos os emails para um endereço externo. |

### <a name="other-risk-detections"></a>Outras detecções de risco

| Detecção de riscos | Tipo de detecção | Descrição |
| --- | --- | --- |
| Risco adicional detectado | Em tempo real ou offline | Essa detecção indica que uma das detecções Premium acima foi detectada. Como as detecções Premium são visíveis apenas para clientes Azure AD Premium P2, elas são intituladas "risco adicional detectado" para clientes sem licenças Azure AD Premium P2. |

## <a name="common-questions"></a>Perguntas comuns

### <a name="risk-levels"></a>Níveis de risco

O Identity Protection categoriza os riscos em três camadas: baixa, média e alta. Ao configurar [políticas personalizadas de proteção de identidade](./concept-identity-protection-policies.md#custom-conditional-access-policy), você também pode configurá-lo para disparar **sem nenhum** nível de risco. Sem risco significa que não há nenhuma indicação ativa de que a identidade do usuário foi comprometida.

Embora a Microsoft não forneça detalhes específicos sobre como o risco é calculado, diremos que cada nível traz uma maior confiança de que a entrada ou o usuário está comprometido. Por exemplo, algo como uma instância de propriedades de entrada desconhecidas para um usuário pode não ser tão ameaçador quanto as credenciais vazadas para outro usuário.

### <a name="password-hash-synchronization"></a>Sincronização de hash de senha

As detecções de risco como credenciais vazadas e a irrigação de senha exigem a presença de hashes de senha para que a detecção ocorra. Para obter mais informações sobre a sincronização de hash de senha, consulte o artigo [implementar a sincronização de hash de senha com Azure ad Connect sincronização](../hybrid/how-to-connect-password-hash-synchronization.md).

### <a name="leaked-credentials"></a>Credenciais vazadas

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Onde a Microsoft encontra credenciais vazadas?

A Microsoft encontra credenciais vazadas em uma variedade de locais, incluindo:

- Public cola sites como pastebin.com e paste.ca em que os atores inválidos normalmente lançam tal material. Esse local é a primeira parada dos atores mais ruins em sua busca de encontrar credenciais roubadas.
- Agências de aplicação de leis.
- Outros grupos da Microsoft fazendo pesquisas escuras na Web.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Por que não vejo credenciais vazadas?

As credenciais vazadas são processadas sempre que a Microsoft encontra um novo lote disponível publicamente. Devido à natureza confidencial, as credenciais vazadas são excluídas logo após o processamento. Somente novas credenciais vazadas encontradas depois que você habilita a sincronização de hash de senha (PHS) serão processadas em seu locatário. A confirmação com os pares de credenciais encontrados anteriormente não é feita. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Não vi nenhum evento de risco de credencial vazado há algum tempo?

Se você não viu nenhum evento de risco de credencial vazado, isso ocorre devido aos seguintes motivos:

- Você não tem o PHS habilitado para seu locatário.
- A Microsoft não encontrou pares de credenciais vazados que correspondam aos seus usuários.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Com que frequência a Microsoft processa novas credenciais?

As credenciais são processadas imediatamente depois de serem encontradas, normalmente em vários lotes por dia.

## <a name="next-steps"></a>Próximas etapas

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)
- [Visão geral de segurança](concept-identity-protection-security-overview.md)