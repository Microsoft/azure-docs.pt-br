---
title: Glossário do Azure Active Directory Identity Protection | Microsoft Docs
description: Glossário do Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6751fe74bfd9b8a07aec2263582d9f1a4bc0b2ac
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333974"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Glossário do Azure Active Directory Identity Protection

### <a name="at-risk-user"></a>Em risco (Usuário)
Um usuário com um ou mais eventos de risco ativos. 

### <a name="atypical-sign-in-location"></a>Local de entrada atípico
Uma entrada de um local geográfico incomum para o usuário específico, usuários semelhantes ou o locatário.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Um módulo de segurança do Azure Active Directory que fornece uma exibição consolidada dos eventos de risco e das possíveis vulnerabilidades que afetam as identidades de uma organização.

### <a name="conditional-access"></a>Acesso Condicional
Uma política para proteger o acesso aos recursos. As regras de acesso condicional são armazenadas no Azure Active Directory e são avaliadas pelo Azure AD antes de conceder acesso ao recurso.  As regras de exemplo incluem restrição baseada na localização do usuário, integridade do dispositivo ou método de autenticação do usuário.

### <a name="credentials"></a>Credenciais
Informações que incluem a identificação e prova de identificação usadas para obter acesso ao local e aos recursos da rede. Exemplos de credenciais são nomes de usuário e senhas, cartões inteligentes e certificados.

### <a name="event"></a>evento
Um registro de uma atividade no Azure Active Directory.

### <a name="false-positive-risk-event"></a>Falsos positivos (evento de risco)
Um status de evento de risco definido manualmente por um usuário do Identity Protection que indica que o evento foi investigado e foi marcado incorretamente como um evento de risco.

### <a name="identity"></a>Identidade
Uma pessoa ou entidade que deve ser verificada por meio de autenticação com base em critérios como senha ou certificado.

### <a name="identity-risk-event"></a>Evento de risco de identidade
Um evento AAD que foi marcado como anômalo pelo Identity Protection e pode indicar que uma identidade foi comprometida.

### <a name="ignored-risk-event"></a>Ignorado (evento de risco)
Um status de evento de risco definido manualmente por um usuário do Identity Protection que indica que o evento foi fechado sem realizar uma ação de correção.

### <a name="impossible-travel-from-atypical-locations"></a>Viagem impossível de locais atípicos
Um evento de risco disparado quando duas entradas para o mesmo usuário são detectadas, sendo pelo menos uma delas de um local de entrada atípico e em que o tempo entre as entradas é menor do que o tempo mínimo necessário para viajar fisicamente entre esses locais.  

### <a name="investigation"></a>Investigação
O processo de revisão de atividades, logs e outras informações relevantes relacionadas a um evento de risco para decidir se as etapas de correção e mitigação são necessárias, como a identidade foi comprometida e entender como ela foi usada.

### <a name="leaked-credentials"></a>Credenciais vazadas
Um evento de risco disparado quando as credenciais do usuário atual (nome de usuário e senha) são encontradas como postadas publicamente por nossos pesquisadores na Dark Web.

### <a name="mitigation"></a>Redução
Uma ação que visa limitar ou eliminar a capacidade de um invasor explorar uma identidade ou um dispositivo comprometidos sem restaurá-los para um estado seguro. Uma mitigação não resolve eventos de risco anteriores associados à identidade ou ao dispositivo.

### <a name="multi-factor-authentication"></a>Autenticação multifator
Um método de autenticação que exige dois ou mais métodos de autenticação, que podem incluir algo que o usuário tem, como um certificado; algo que o usuário conhece, como nomes de usuário, senhas ou frases secretas; atributos físicos, como uma impressão digital; e atributos pessoais, como uma assinatura pessoal.

### <a name="offline-detection"></a>Detecção offline
A detecção de anomalias e a avaliação do risco de um evento, tal como uma tentativa de entrada após o ocorrido, para um evento que já aconteceu.

### <a name="policy-condition"></a>Condição da política
Uma parte de uma política de segurança que define as entidades (grupos, usuários, aplicativos, plataformas de dispositivos, Estados de dispositivo, intervalos de IP e tipos de cliente) incluídas na política ou excluídas dela.

### <a name="policy-rule"></a>Regra de política
A parte de uma política de segurança que descreve as circunstâncias que vai disparar a política e as ações executadas quando ela é acionada.

### <a name="prevention"></a>Prevenção
Uma ação para evitar danos à organização por abuso de uma identidade ou dispositivo que sofreu comprometimento conhecido ou suspeito. Uma ação de prevenção não protege o dispositivo ou a identidade, e não resolve eventos de risco anteriores.

### <a name="privileged-user"></a>Privilegiado (usuário)
Um usuário que, no momento de um evento de risco, tinha permissões de administrador permanentes ou temporárias a um ou mais recursos no Azure Active Directory, como um Administrador Global, Administrador de Cobrança, Administrador de Serviços, Administrador de Usuários e Administrador de Senha. 

### <a name="real-time"></a>Tempo real
Consulte Detecção em tempo real.

### <a name="real-time-detection"></a>Detecção em tempo real.
A detecção de anomalias e a avaliação do risco de um evento, tal como uma tentativa de entrada antes de permitir que o evento prossiga.

### <a name="remediated-risk-event"></a>Corrigido (evento de risco)
Um status de evento de risco definido automaticamente pelo Identity Protection, que indica que o evento foi corrigido usando a ação de correção padrão para esse tipo de evento de risco. Por exemplo, quando a senha do usuário é redefinida, muitos eventos de risco que indicam que a senha anterior foi comprometida são corrigidos automaticamente.

### <a name="remediation"></a>Correção
Uma ação que visa proteger uma identidade ou um dispositivo que sofreu comprometimento conhecido ou suspeito anteriormente. Uma correção restaura a identidade ou dispositivo para um estado seguro e resolve eventos de risco anteriores associados à identidade ou ao dispositivo.

### <a name="resolved-risk-event"></a>Resolvido (evento de risco)
Um status de evento de risco definido manualmente por um usuário do Identity Protection que indica que o usuário executou uma ação de correção apropriada fora do Identity Protection e que o evento deve ser considerado fechado.

### <a name="risk-event-status"></a>Status de evento de risco
Uma propriedade de um evento de risco que indica se o evento está ativo e, se fechado, o motivo para estar fechado.

### <a name="risk-event-type"></a>Tipo de evento de risco
Uma categoria para o evento de risco que indica o tipo de anomalia que fez com que o evento fosse considerado arriscado.

### <a name="risk-level-risk-event"></a>Nível de risco (evento de risco)
Uma indicação (Alta, Média ou Baixa) da severidade do evento de risco para ajudar os usuários do Identity Protection a priorizar as ações tomadas para reduzir o risco para a organização. 

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
Um evento de risco disparado após uma entrada bem-sucedida de um endereço IP que foi identificado como um endereço IP de proxy anônimo.

### <a name="sign-in-from-infected-device"></a>Entrada de um dispositivo infectado
Um evento de risco disparado em que uma entrada origina um endereço IP conhecido por ser usado por um ou mais dispositivos comprometidos, que estão tentando ativamente comunicar-se com um servidor de bot.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Entrada de um endereço IP com atividade suspeita
Um evento de risco acionado depois de uma entrada bem-sucedida de um endereço IP com um grande número de tentativas de logon com falha em várias contas de usuário em um curto período de tempo.

### <a name="sign-in-from-unfamiliar-location"></a>Entrada de um local desconhecido
Um evento de risco disparado quando um usuário entra com êxito de um novo local (IP, Latitude/Longitude e ASN).

### <a name="sign-in-risk"></a>Risco de entrada
Consulte Nível de risco (entrada)

### <a name="sign-in-risk-policy"></a>Política de risco de entrada
Uma política de acesso condicional que avalia o risco para uma entrada específica e aplica mitigações com base em condições e regras predefinidas.

### <a name="user-compromise-risk"></a>Risco de comprometimento do usuário
Consulte Nível de risco (comprometimento do usuário)

### <a name="user-risk"></a>Risco de usuário
Consulte Nível de risco (comprometimento do usuário).

### <a name="user-risk-policy"></a>Política de risco do usuário
Uma política de acesso condicional que considera a entrada e aplica mitigações com base em condições e regras predefinidas.

### <a name="users-flagged-for-risk"></a>Usuários sinalizados para risco
Usuários que têm eventos de risco ativos ou corrigidos

### <a name="vulnerability"></a>Vulnerabilidade
Uma configuração ou condição no Azure Active Directory que torna o diretório suscetível a vulnerabilidades ou ameaças.

## <a name="see-also"></a>Consulte também
* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
