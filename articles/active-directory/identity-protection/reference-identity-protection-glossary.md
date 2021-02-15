---
title: Glossário do Azure Active Directory Identity Protection
description: Glossário do Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a17f52e519a84c58c9ed5b75259cc0b96d0a175
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88949167"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Glossário do Azure Active Directory Identity Protection

### <a name="at-risk-user"></a>Em risco (Usuário)
Um usuário com uma ou mais detecções de risco ativas. 

### <a name="atypical-sign-in-location"></a>Local de entrada atípico
Uma entrada de um local geográfico incomum para o usuário específico, usuários semelhantes ou o locatário.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Um módulo de segurança do Azure Active Directory que fornece uma visão consolidada das detecções de risco e das possíveis vulnerabilidades que afetam as identidades de uma organização.

### <a name="conditional-access"></a>Acesso condicional
Uma política para proteger o acesso aos recursos. As regras de acesso condicional são armazenadas no Azure Active Directory e são avaliadas pelo Azure AD antes de conceder acesso ao recurso.  As regras de exemplo incluem restrição baseada na localização do usuário, integridade do dispositivo ou método de autenticação do usuário.

### <a name="credentials"></a>Credenciais
Informações que incluem a identificação e prova de identificação usadas para obter acesso ao local e aos recursos da rede. Exemplos de credenciais são nomes de usuário e senhas, cartões inteligentes e certificados.

### <a name="event"></a>Evento
Um registro de uma atividade no Azure Active Directory.

### <a name="false-positive-risk-detection"></a>Falso-positivo (detecção de risco)
Um status de detecção de risco definido manualmente por um usuário de proteção de identidade, indicando que a detecção de risco foi investigada e foi sinalizada incorretamente como uma detecção de risco.

### <a name="identity"></a>Identidade
Uma pessoa ou entidade que deve ser verificada por meio de autenticação com base em critérios como senha ou certificado.

### <a name="identity-risk-detection"></a>Detecção de risco de identidade
Evento do Azure AD que foi sinalizado como anômala pela proteção de identidade e pode indicar que uma identidade foi comprometida.

### <a name="ignored-risk-detection"></a>Ignorado (detecção de risco)
Um status de detecção de risco definido manualmente por um usuário de proteção de identidade, indicando que a detecção de risco está fechada sem realizar uma ação de correção.

### <a name="impossible-travel-from-atypical-locations"></a>Viagem impossível de locais atípicos
Uma detecção de risco disparada quando duas entradas para o mesmo usuário são detectadas, em que pelo menos uma delas é de um local de entrada do atípicos e onde o tempo entre as entradas é menor do que o tempo mínimo necessário para viajar fisicamente entre esses locais.  

### <a name="investigation"></a>Investigação
O processo de revisar as atividades, os logs e outras informações relevantes relacionadas a uma detecção de risco para decidir se as etapas de correção ou mitigação são necessárias, entender se e como a identidade foi comprometida e entender como a identidade comprometida foi usada.

### <a name="leaked-credentials"></a>Credenciais vazadas
Uma detecção de risco disparada quando as credenciais do usuário atual (nome de usuário e senha) são encontradas publicadas publicamente na Web escura por nossos pesquisadores.

### <a name="mitigation"></a>Atenuação
Uma ação que visa limitar ou eliminar a capacidade de um invasor explorar uma identidade ou um dispositivo comprometidos sem restaurá-los para um estado seguro. Uma mitigação não resolve as detecções de riscos anteriores associadas à identidade ou ao dispositivo.

### <a name="multi-factor-authentication"></a>Autenticação multifator
Um método de autenticação que exige dois ou mais métodos de autenticação, que podem incluir algo que o usuário tem, como um certificado; algo que o usuário conhece, como nomes de usuário, senhas ou frases secretas; atributos físicos, como uma impressão digital; e atributos pessoais, como uma assinatura pessoal.

### <a name="offline-detection"></a>Detecção offline
A detecção de anomalias e a avaliação do risco de um evento, tal como uma tentativa de entrada após o ocorrido, para um evento que já aconteceu.

### <a name="policy-condition"></a>Condição da política
Uma parte de uma política de segurança que define as entidades (grupos, usuários, aplicativos, plataformas de dispositivos, Estados de dispositivo, intervalos de IP e tipos de cliente) incluídas na política ou excluídas dela.

### <a name="policy-rule"></a>Regra de política
A parte de uma política de segurança que descreve as circunstâncias que dispararia a política e as ações tomadas quando a política é disparada.

### <a name="prevention"></a>Prevenção
Uma ação para evitar danos à organização por abuso de uma identidade ou dispositivo que sofreu comprometimento conhecido ou suspeito. Uma ação de prevenção não protege o dispositivo ou a identidade e não resolve as detecções de riscos anteriores.

### <a name="privileged-user"></a>Privilegiado (usuário)
Um usuário que, no momento de uma detecção de risco, tinha permissões de administrador permanentes ou temporárias para um ou mais recursos no Azure Active Directory, como um administrador global, administrador de cobrança, administrador de serviços, administrador de usuários e administrador de senha. 

### <a name="real-time"></a>Tempo real
Consulte Detecção em tempo real.

### <a name="real-time-detection"></a>Detecção em tempo real.
A detecção de anomalias e a avaliação do risco de um evento, tal como uma tentativa de entrada antes de permitir que o evento prossiga.

### <a name="remediated-risk-detection"></a>Corrigido (detecção de risco)
Um status de detecção de risco definido automaticamente pela proteção de identidade, indicando que a detecção de risco foi corrigida usando a ação de correção padrão para esse tipo de detecção de risco. Por exemplo, quando a senha do usuário é redefinida, muitas detecções de risco que indicam que a senha anterior foi comprometida são automaticamente corrigidas.

### <a name="remediation"></a>Remediação
Uma ação que visa proteger uma identidade ou um dispositivo que sofreu comprometimento conhecido ou suspeito anteriormente. Uma ação de correção restaura a identidade ou o dispositivo para um estado seguro e resolve as detecções de risco anteriores associadas à identidade ou ao dispositivo.

### <a name="resolved-risk-detection"></a>Resolvido (detecção de risco)
Um status de detecção de risco definido manualmente por um usuário de proteção de identidade, indicando que o usuário realizou uma ação de correção apropriada fora da proteção de identidade e que a detecção de risco deve ser considerada fechada.

### <a name="risk-detection-status"></a>Status de detecção de risco
Uma propriedade de uma detecção de risco, que indica se o evento está ativo e, se fechado, o motivo para fechá-lo.

### <a name="risk-detection-type"></a>Tipo de detecção de risco
Uma categoria para a detecção de risco, que indica o tipo de anomalia que fez com que o evento fosse considerado arriscado.

### <a name="risk-level-risk-detection"></a>Nível de risco (detecção de risco)
Uma indicação (alta, média ou baixa) da severidade da detecção de risco para ajudar os usuários de proteção de identidade a priorizar as ações que eles levam para reduzir o risco para sua organização. 

### <a name="risk-level-sign-in"></a>Nível de risco (entrada)
Uma indicação (Alta, Média ou Baixa) da probabilidade de que outra pessoa esteja tentando usar a identidade do usuário para uma entrada específica.

### <a name="risk-level-user-compromise"></a>Nível de risco (comprometimento do usuário)
Uma indicação (Alta, Média ou Baixa) da probabilidade de uma identidade ter sido comprometida.

### <a name="risk-level-vulnerability"></a>Nível de risco (vulnerabilidade)
Uma indicação (Alta, Média ou Baixa) da severidade da vulnerabilidade para ajudar os usuários do Identity Protection a priorizar as ações tomadas para reduzir o risco para a organização.

### <a name="secure-identity"></a>Proteger (identidade)
Realizar uma ação de correção como uma alteração de senha ou reimplantação da imagem no computador para restaurar uma identidade potencialmente comprometida para um estado não comprometido.

### <a name="security-policy"></a>Política de segurança
Uma coleção de regras e condição da política. Uma política pode ser aplicada a entidades como usuários, grupos, aplicativos, dispositivos, plataformas de dispositivos, estados de dispositivo, intervalos de IP e tipos de cliente do Auth2.0. Quando uma política está habilitada, ela é avaliada sempre que um token para um recurso é emitido para uma entidade incluída na política.

### <a name="sign-in-v"></a>Entrar (v)
Autenticar-se em uma identidade no Azure Active Directory.

### <a name="sign-in-n"></a>Entrada (n)
O processo ou a ação de autenticar uma identidade no Azure Active Directory e o evento que captura essa operação.

### <a name="sign-in-from-anonymous-ip-address"></a>Entrada de um endereço IP anônimo
Uma detecção de risco disparada após uma entrada bem-sucedida do endereço IP que foi identificada como um endereço IP de proxy anônimo.

### <a name="sign-in-from-infected-device"></a>Entrada de um dispositivo infectado
Uma detecção de risco disparada quando uma entrada é originada de um endereço IP, que é conhecido por ser usado por um ou mais dispositivos comprometidos, que estão ativamente tentando se comunicar com um servidor de bot.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Entrada de um endereço IP com atividade suspeita
Uma detecção de risco disparada após uma entrada bem-sucedida de um endereço IP com um número alto de tentativas de logon com falha em várias contas de usuário em um curto período de tempo.

### <a name="sign-in-from-unfamiliar-location"></a>Entrada de um local desconhecido
Uma detecção de risco disparada quando um usuário entra com êxito de um novo local (IP, latitude/longitude e ASN).

### <a name="sign-in-risk"></a>Risco de entrada
Consulte Nível de risco (entrada)

### <a name="sign-in-risk-policy"></a>Política de risco de entrada
Uma política de acesso condicional que avalia o risco para uma entrada específica e aplica mitigações com base em condições e regras predefinidas.

### <a name="user-compromise-risk"></a>Risco de comprometimento do usuário
Consulte Nível de risco (comprometimento do usuário)

### <a name="user-risk"></a>Risco do usuário
Consulte Nível de risco (comprometimento do usuário).

### <a name="user-risk-policy"></a>Política de risco do usuário
Uma política de acesso condicional que considera a entrada e aplica mitigações com base em condições e regras predefinidas.

### <a name="users-flagged-for-risk"></a>Usuários sinalizados por risco
Usuários que têm detecções de risco, que são ativas ou corrigidas

### <a name="vulnerability"></a>Vulnerabilidade
Uma configuração ou condição no Azure Active Directory que torna o diretório suscetível a vulnerabilidades ou ameaças.

## <a name="see-also"></a>Confira também

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)