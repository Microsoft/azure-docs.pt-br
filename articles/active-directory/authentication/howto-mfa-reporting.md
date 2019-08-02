---
title: Relatórios de acesso e uso para o Azure MFA - Azure Active Directory
description: Descreve como usar o recurso de relatórios da Autenticação Multifator do Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd4c6952aab1581a968fc671141792c0e16a7d46
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536960"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Relatórios na Autenticação Multifator do Azure

A Autenticação Multifator do Azure fornece vários relatórios que podem ser usados por você e sua organização, acessíveis através do portal do Azure. A tabela a seguir lista os relatórios possíveis:

| Relatório | Location | DESCRIÇÃO |
|:--- |:--- |:--- |
| Histórico de usuário bloqueado | Microsoft Azure AD > Servidor MFA > Bloquear/desbloquear usuários | Mostra o histórico de solicitações para bloquear ou desbloquear usuários. |
| Alertas de fraudes e uso | Microsoft Azure AD > Entradas | Fornece informações sobre o uso geral, resumo do usuário e detalhes do usuário, assim como um histórico de alertas de fraude enviados durante o intervalo de datas especificado. |
| Uso para componentes locais | Microsoft Azure AD > Servidor MFA > Relatórios de Atividade | Fornece informações sobre o uso geral do MFA por meio da extensão do NPS, ADFS e do servidor MFA. |
| Histórico de usuário desviado | Microsoft Azure AD > Servidor MFA > Bypass avulso | Fornece um histórico de solicitações para ignorar a Autenticação Multifator para um usuário. |
| Status do servidor | Microsoft Azure AD > Servidor MFA > Status do Servidor | Exibe o status dos Servidores de Autenticação Multifator associado à sua conta. |

## <a name="view-mfa-reports"></a>Exibir relatórios de MFA

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Servidor MFA**.
3. Selecione o relatório que você deseja exibir.

   ![Relatório de status do servidor MFA server no portal do Azure](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Relatório de entradas do AD do Azure

Com o **relatório de atividades de logins** no [Portal do Azure](https://portal.azure.com), você pode obter as informações necessárias para determinar o desempenho do seu ambiente.

O relatório de logins pode fornecer informações sobre o uso de aplicativos gerenciados e atividades de login do usuário, o que inclui informações sobre o uso da autenticação multifator (MFA). Os dados de MFA oferecem ideias sobre como a MFA está funcionando em sua organização. Isso permite que você responda perguntas como:

- A entrada foi desafiada com a MFA?
- Como o usuário concluiu o MFA?
- Por que o usuário não conseguiu concluir a MFA?
- Quantos usuários são desafiados para MFA?
- Quantos usuários não conseguiram concluir o desafio da MFA?
- Quais são os problemas comuns de MFA que os usuários finais estão encontrando?

Esses dados estão disponíveis por meio do [Portal do Azure](https://portal.azure.com) e da [API de relatórios](../reports-monitoring/concept-reporting-api.md).

![Relatório de entradas do Azure AD no portal do Azure](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Estrutura de relatório de entradas

Os relatórios de atividade de entrada para MFA fornecem acesso às seguintes informações:

**MFA obrigatório:** Indica se o MFA é ou não obrigatório para as credenciais. MFA pode ser exigida devido a MFA por usuário, acesso condicional ou por outros motivos. Os valores possíveis são **Sim** ou **Não**.

**Resultado do MFA:** Mais informações sobre se o MFA foi atendido ou negado:

- Se a MFA tiver sido atendida, esta coluna fornecerá mais informações sobre como a MFA foi atendida.
   - Autenticação Multifator do Azure
      - concluído na nuvem
      - expirou devido às políticas configuradas no locatário
      - registro solicitado
      - satisfeito por declaração no token
      - satisfeita por declaração fornecida pelo provedor externo
      - satisfeita por autenticação forte
      - ignorado porque o fluxo exercitado era o fluxo de logon de agente do Windows
      - ignorado devido a senha de aplicativo
      - ignorado devido ao local
      - ignorado devido a dispositivo registrado
      - ignorado devido a dispositivo lembrado
      - concluído com êxito
   - Redirecionado para um provedor externo para autenticação multifator

- Se a MFA tiver sido negada, esta coluna fornecerá o motivo da negação.
   - Autenticação Multifator do Azure negada;
      - autenticação em andamento
      - tentativa de autenticação duplicada
      - foi inserido código incorreto muitas vezes
      - autenticação inválida
      - Código de verificação do aplicativo móvel inválido
      - configuração incorreta
      - chamada telefônica caiu na caixa postal
      - o número de telefone tem um formato inválido
      - erro de serviço
      - não é possível acessar o telefone do usuário
      - não é possível enviar a notificação de aplicativo móvel no dispositivo
      - não é possível enviar a notificação de aplicativo móvel
      - usuário recusou a autenticação
      - o usuário não respondeu à notificação do aplicativo móvel
      - o usuário não tem nenhum método de verificação registrado
      - código incorreto inserido por usuário
      - PIN incorreto inserido pelo usuário
      - o usuário desligou a chamada telefônica sem obter êxito na autenticação
      - usuário está bloqueado
      - o usuário nunca inseriu o código de verificação
      - usuário não encontrado
      - código de verificação já usado uma vez

**Método de autenticação do MFA:** O método de autenticação utilizado pelo usuário para concluir o MFA. Os valores possíveis incluem:

- mensagem de texto
- Notificação de aplicativo móvel
- Chamada telefônica (telefone de autenticação)
- Código de verificação do aplicativo móvel
- Chamada telefônica (telefone comercial)
- Chamada telefônica (telefone de autenticação alternativo)

**Detalhes de autenticação do MFA:** Versão limpa do número de telefone, por exemplo: + X XXXXXXXX64.

**Acesso condicional** encontrar informações sobre as políticas de acesso condicional que afetava o tentativa de logon, incluindo:

- Nome da política
- Controles de concessão
- Controles de sessão
- Result

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell para gerar relatórios sobre usuários registrados para MFA

Primeiro, certifique-se de que você tenha o [módulo V1 do MSOnline PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) instalado.

Identifique os usuários que se registraram para MFA usando o PowerShell a seguir.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifique os usuários que não se registraram para MFA usando o PowerShell a seguir.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>Possíveis resultados em relatórios de atividade

A tabela a seguir pode ser usada para solucionar problemas de autenticação multifator usando a versão baixada do relatório de atividade de autenticação multifator. Eles não serão exibidos diretamente no portal do Azure.

| Resultado da chamada | DESCRIÇÃO | Ampla descrição |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN digitado | O usuário inseriu um PIN.  Se a autenticação for bem-sucedida, em seguida, ele inseriu o PIN correto.  Se a autenticação for negada, em seguida, ele inseriu um PIN incorreto ou o usuário está definido para o modo padrão. |
| SUCCESS_NO_PIN | Apenas os # inserido | Se o usuário está definido para o modo PIN e a autenticação é negada, isso significa que o usuário não inseriu o PIN e só digitou #.  Se o usuário está definido para o modo padrão e a autenticação for bem-sucedida isso significa que o usuário só digitou #, que é a ação correta no modo padrão. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Não pressionado após entrada | O usuário não enviou os dígitos DTMF, pois não inseriu #.  Outros dígitos inseridos não são enviados, a menos que # é inserido para indicar a conclusão da entrada. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Nenhuma entrada de telefone - atingiu o tempo limite | A chamada foi atendida, mas não houve resposta.  Isso geralmente indica que a chamada foi recebida pelo correio de voz. |
| SUCCESS_PIN_EXPIRED | PIN expirado e não alterado | O PIN do usuário expirou e foi solicitado a alterá-lo, mas a alteração do PIN não foi concluída com êxito. |
| SUCCESS_USED_CACHE | Cache usado | Autenticação bem-sucedida sem uma chamada da autenticação multifator, desde que uma autenticação bem-sucedida anterior para o mesmo nome de usuário ocorreu dentro do período de cache configurado. |
| SUCCESS_BYPASSED_AUTH | Autenticação ignorada | Autenticação bem-sucedida usando um Bypass descartável iniciado para o usuário.  Consulte o relatório de histórico de usuários ignorados para obter mais detalhes sobre o bypass. |
| SUCCESS_USED_IP_BASED_CACHE | Cache usado com base em IP | A autenticação foi bem-sucedida sem uma chamada da autenticação multifator, desde uma autenticação bem-sucedida anterior para o mesmo nome de usuário, o tipo de autenticação, o nome do aplicativo e IP ocorreu dentro do período de cache configurado. |
| SUCCESS_USED_APP_BASED_CACHE | Cache usado com base no aplicativo | A autenticação foi bem-sucedida sem uma chamada da autenticação multifator, desde uma autenticação bem-sucedida anterior para o mesmo nome de usuário, o tipo de autenticação e o nome do aplicativo dentro do período de cache configurado. |
| SUCCESS_INVALID_INPUT | Entrada de telefone inválido | A resposta enviada pelo telefone não é válida.  Isso pode ser de um aparelho de fax ou modem ou o usuário pode ter inserido * como parte de seu PIN. |
| SUCCESS_USER_BLOCKED | O usuário está bloqueado | Número de telefone do usuário está bloqueado.  Um número bloqueado pode ser iniciado pelo usuário durante uma chamada de autenticação ou por um administrador usando o portal do Azure. <br> OBSERVAÇÃO:   Um número bloqueado é um subproduto de um alerta de fraude. |
| SUCCESS_SMS_AUTHENTICATED | Mensagem de texto autenticada | Para a mensagem de texto bidirecional, o usuário respondeu corretamente com sua senha única (OTP) ou OTP + PIN. |
| SUCCESS_SMS_SENT | Mensagem de texto enviada | Para a mensagem de texto, a mensagem de texto que contém a senha única (OTP) foi enviada com êxito.  O usuário irá inserir o OTP ou OTP + PIN no aplicativo para concluir a autenticação. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Aplicativos móveis autenticados | O usuário autenticado com êxito por meio do aplicativo móvel. |
| SUCCESS_OATH_CODE_PENDING | Código OATH pendente | O usuário foi solicitado a fornecer o código OATH, mas não respondeu. |
| SUCCESS_OATH_CODE_VERIFIED | Código OATH verificado | O usuário inseriu um código OATH válido quando solicitado. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Código OATH de fallback verificado | O usuário foi autenticação negado usando seu método primário da autenticação multifator e, em seguida, forneceu um código OATH válido para fallback. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Perguntas de segurança de fallback respondidas | O usuário foi autenticação negado usando seu método primário da autenticação multifator e respondeu às perguntas de segurança corretamente para fallback. |
| FAILED_PHONE_BUSY | Autenticação já em andamento | A autenticação multifator já está processando uma autenticação para este usuário.  Isso geralmente é causado por clientes RADIUS que enviam várias solicitações de autenticação durante o mesmo logon. |
| CONFIG_ISSUE | Telefone inacessível | Chamada foi tentada, mas não pôde ser realizada ou não foi atendida.  Isso inclui o sinal de ocupado, sinal de ocupado rápido (desconectado), tri-tons (número não mais em serviço), atingiu o tempo limite ao toque, etc. |
| FAILED_INVALID_PHONENUMBER | Formato de número de telefone inválido | O número de telefone tem um formato inválido.  Números de telefone devem ser numéricos e devem ter 10 dígitos para o código do país + 1 (Estados Unidos e Canadá). |
| FAILED_USER_HUNGUP_ON_US | Usuário desligou o telefone | O usuário atendeu ao telefone, mas desligou sem pressionar quaisquer botões. |
| FAILED_INVALID_EXTENSION | Extensão inválida | A extensão contém caracteres inválidos.  Apenas dígitos, vírgulas, *, e # são permitidos.  Um prefixo @ também pode ser usado. |
| FAILED_FRAUD_CODE_ENTERED | Código de fraude inserido | O usuário escolhido relatar fraude durante a chamada, resultando em uma autenticação negada e um número de telefone, bloqueado.| 
| FAILED_SERVER_ERROR | Não é possível fazer chamada | O serviço de autenticação multifator não pôde fazer a chamada. |
| FAILED_SMS_NOT_SENT | Não foi possível enviar a mensagem de texto | A mensagem de texto não pôde ser enviada.  A autenticação é negada. |
| FAILED_SMS_OTP_INCORRECT | Mensagem de texto OTP incorreto | O usuário inseriu uma senha incorreta única (OTP) da mensagem de texto que receberam.  A autenticação é negada. |
| FAILED_SMS_OTP_PIN_INCORRECT | Texto da mensagem OTP + PIN incorreto | O usuário inseriu uma senha incorreta única (OTP) e/ou um PIN do usuário incorreta.  A autenticação é negada. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Excedeu as tentativas OTP de mensagem de texto máximo | O usuário excedeu o número máximo de tentativas de senha única (OTP). |
| FAILED_PHONE_APP_DENIED | Aplicativos móveis negados | O usuário negou a autenticação no aplicativo móvel, pressionando o botão Negar. |
| FAILED_PHONE_APP_INVALID_PIN | PIN inválido do aplicativo móvel | O usuário inseriu um PIN inválido durante a autenticação no aplicativo móvel. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Não foi alterado de PIN do aplicativo móvel | O usuário não foi concluída com êxito uma alteração PIN necessária no aplicativo móvel. |
| FAILED_FRAUD_REPORTED | Fraude relatada | O usuário relatou uma fraude no aplicativo móvel. |
| FAILED_PHONE_APP_NO_RESPONSE | Aplicativo móvel sem resposta | O usuário não respondeu à solicitação de autenticação de aplicativo móvel. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Aplicativo todos os dispositivos móvel bloqueados | Os dispositivos de aplicativo móvel para este usuário não estão mais respondendo às notificações e foram bloqueados. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Falha na notificação do aplicativo móvel | Ocorreu uma falha ao tentar enviar uma notificação para o aplicativo móvel no dispositivo do usuário. |
| FAILED_PHONE_APP_INVALID_RESULT | Resultado inválido do aplicativo móvel | O aplicativo móvel retornou um resultado inválido. |
| FAILED_OATH_CODE_INCORRECT | Código OATH incorreto | O usuário inseriu um código OATH incorreto.  A autenticação é negada. |
| FAILED_OATH_CODE_PIN_INCORRECT | Código OATH + PIN incorreto | O usuário inseriu um código OATH incorreto e/ou um PIN do usuário incorreta.  A autenticação é negada. |
| FAILED_OATH_CODE_DUPLICATE | Duplicar código OATH | O usuário inseriu um código OATH usado anteriormente.  A autenticação é negada. |
| FAILED_OATH_CODE_OLD | Código OATH desatualizado | O usuário inseriu um código OATH que precede um código OATH usado anteriormente.  A autenticação é negada. |
| FAILED_OATH_TOKEN_TIMEOUT | Tempo limite de resultado do código OATH | O usuário levou muito tempo para inserir o código OATH e a tentativa de autenticação multifator já tinha atingiu o tempo limite. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Tempo limite de resultado de perguntas de segurança | O usuário levou muito tempo para inserir a resposta para perguntas de segurança e a tentativa de autenticação multifator já tinha atingiu o tempo limite. |
| FAILED_AUTH_RESULT_TIMEOUT | Tempo limite de resultado de autenticação | O usuário levou muito tempo para concluir a tentativa de autenticação multifator. |
| FAILED_AUTHENTICATION_THROTTLED | Autenticação limitada | A tentativa de autenticação multifator foi limitada pelo serviço. |

## <a name="next-steps"></a>Próximas etapas

* [Uso do SSPR e MFA e relatórios de insights](howto-authentication-methods-usage-insights.md)
* [Para usuários](../user-help/multi-factor-authentication-end-user.md)
* [Onde implantar](concept-mfa-whichversion.md)
