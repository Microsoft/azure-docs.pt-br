---
title: Detalhes do evento de entrada para a autenticação multifator do Azure AD – Azure Active Directory
description: Saiba como exibir a atividade de entrada para eventos de autenticação multifator do Azure AD e mensagens de status.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/15/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5f78b70599d6d0ae8825accf4cc55cdc1c01d9ce
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861231"
---
# <a name="use-the-sign-ins-report-to-review-azure-ad-multi-factor-authentication-events"></a>Use o relatório de entradas para examinar os eventos da autenticação multifator do Azure AD

Para examinar e entender os eventos da autenticação multifator do Azure AD, você pode usar o relatório de entradas do Azure Active Directory (Azure AD). Este relatório mostra detalhes de autenticação de eventos quando você recebe uma solicitação de Autenticação Multifator e se as políticas de acesso condicional foram usadas. Para obter informações detalhadas sobre o relatório de entradas, confira a [visão geral dos relatórios de atividade de entrada no Azure AD](../reports-monitoring/concept-sign-ins.md).

Este artigo mostra como exibir o relatório de entradas do Azure AD no portal do Azure e no módulo MSOnline V1 PowerShell.

## <a name="view-the-azure-ad-sign-ins-report"></a>Veja o relatório de entradas do AD do Azure

O relatório de entradas fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário, o que inclui informações sobre o uso da autenticação multifator (MFA). Os dados de MFA oferecem ideias sobre como a MFA está funcionando em sua organização. Esses dados permitem responder perguntas como as seguintes:

- A entrada foi desafiada com a MFA?
- Como o usuário concluiu o MFA?
- Por que o usuário não conseguiu concluir a MFA?
- Quantos usuários são desafiados para MFA?
- Quantos usuários não conseguiram concluir o desafio da MFA?
- Quais são os problemas comuns de MFA que os usuários finais estão encontrando?

Para exibir o relatório de atividade de entrada no [portal do Azure](https://portal.azure.com), conclua as etapas a seguir. Você também pode consultar [dados usando a API de relatório](../reports-monitoring/concept-reporting-api.md).

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta com permissões de *administrador global*.
1. Procure e selecione **Azure Active Directory**. Em seguida, escolha **Usuários** no menu no lado esquerdo.
1. Em *Atividade* no menu esquerdo, escolha **Entradas**.
1. Uma lista de eventos de entrada será mostrada, incluindo o status. Você pode escolher um evento para exibir mais detalhes.

    A guia *Detalhes de Autenticação* ou *Acesso Condicional* dos detalhes do evento mostra o código de status ou qual política acionou o prompt de MFA.

    [![Captura de tela do exemplo de relatório de entradas do Azure Active Directory no portal do Azure](media/howto-mfa-reporting/sign-in-report-cropped.png)](media/howto-mfa-reporting/sign-in-report.png#lightbox)

Se disponível, a autenticação será mostrada, como mensagem de texto, notificação do aplicativo Microsoft Authenticator ou chamada telefônica.

Os detalhes a seguir são mostrados na janela *Detalhes de Autenticação* para um evento de entrada que mostra se a solicitação de MFA foi atendida ou negada:

* Se a MFA tiver sido atendida, esta coluna fornecerá mais informações sobre como a MFA foi atendida.
   * concluído na nuvem
   * expirou devido às políticas configuradas no locatário
   * registro solicitado
   * satisfeito por declaração no token
   * satisfeita por declaração fornecida pelo provedor externo
   * satisfeita por autenticação forte
   * ignorado porque o fluxo exercitado era o fluxo de logon de agente do Windows
   * ignorado devido a senha de aplicativo
   * ignorado devido ao local
   * ignorado devido a dispositivo registrado
   * ignorado devido a dispositivo lembrado
   * concluído com êxito

* Se a MFA tiver sido negada, esta coluna fornecerá o motivo da negação.
   * autenticação em andamento
   * tentativa de autenticação duplicada
   * foi inserido código incorreto muitas vezes
   * autenticação inválida
   * Código de verificação do aplicativo móvel inválido
   * configuração incorreta
   * chamada telefônica caiu na caixa postal
   * o número de telefone tem um formato inválido
   * erro de serviço
   * não é possível acessar o telefone do usuário
   * não é possível enviar a notificação de aplicativo móvel no dispositivo
   * não é possível enviar a notificação de aplicativo móvel
   * usuário recusou a autenticação
   * o usuário não respondeu à notificação do aplicativo móvel
   * o usuário não tem nenhum método de verificação registrado
   * código incorreto inserido por usuário
   * PIN incorreto inserido pelo usuário
   * o usuário desligou a chamada telefônica sem obter êxito na autenticação
   * usuário está bloqueado
   * o usuário nunca inseriu o código de verificação
   * usuário não encontrado
   * código de verificação já usado uma vez

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Relatórios do PowerShell sobre usuários registrados para MFA

Primeiro, verifique se você instalou o [módulo MSOnline V1 PowerShell ](/powershell/azure/active-directory/overview).

Identifique os usuários que se registraram para MFA usando o PowerShell a seguir. Esse conjunto de comandos exclui usuários desabilitados, pois essas contas não podem se autenticar no Azure AD:

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identifique os usuários que não se registraram para MFA usando o PowerShell a seguir. Esse conjunto de comandos exclui usuários desabilitados, pois essas contas não podem se autenticar no Azure AD:

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0 -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identifique os usuários e métodos de saída registrados:

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="downloaded-activity-reports-result-codes"></a>Códigos de resultado dos relatórios de atividades baixados

A tabela a seguir pode ajudar a solucionar problemas de eventos usando a versão baixada do relatório de atividade das etapas do portal anterior ou comandos do PowerShell. Esses códigos de resultado não são exibidos diretamente no portal do Azure.

| Resultado da chamada | Descrição | Descrição geral |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN inserido | O usuário inseriu um PIN.   Se a autenticação foi bem-sucedida, ele inseriu o PIN correto.   Se a autenticação foi negada, ele inseriu um PIN incorreto ou o usuário está configurado para o modo Padrão. |
| SUCCESS_NO_PIN | Só digitou # | Se o usuário estiver configurado para o modo PIN e a autenticação for negada, isso significa que ele não inseriu o PIN e só digitou #.  Se o usuário estiver configurado para o modo Padrão e a autenticação for bem-sucedida, isso significa que ele só digitou #, que é a ação correta no modo Padrão. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Não Pressionado Após Entrada | O usuário não enviou os dígitos DTMF porque não inseriu #.   Se # não for inserido para indicar a conclusão da entrada, os outros dígitos inseridos não serão enviados. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Nenhuma Entrada de Telefone - Tempo Esgotado | A chamada foi atendida, mas não houve resposta.   Isso geralmente indica que a chamada foi recebida pelo correio de voz. |
| SUCCESS_PIN_EXPIRED | PIN Expirado e Não Alterado | O PIN do usuário expirou e o usuário foi solicitado a alterar o PIN, mas não concluiu a alteração com êxito. |
| SUCCESS_USED_CACHE | Cache Usado | A autenticação foi bem-sucedida sem uma chamada de Autenticação Multifator, pois uma autenticação bem-sucedida anterior para o mesmo nome de usuário ocorreu dentro do período de cache configurado. |
| SUCCESS_BYPASSED_AUTH | Autenticação Ignorada | A autenticação foi feita usando um Bypass Descartável iniciado para o usuário.  Consulte o Relatório Histórico de Usuários Ignorados para obter mais detalhes. |
| SUCCESS_USED_IP_BASED_CACHE | Cache baseado em IP usado | A autenticação foi bem-sucedida sem uma chamada de Autenticação Multifator, pois uma autenticação bem-sucedida anterior para o mesmo nome de usuário, tipo de autenticação, nome de aplicativo e IP ocorreu dentro do período de cache configurado. |
| SUCCESS_USED_APP_BASED_CACHE | Cache baseado em aplicativo usado | A autenticação foi bem-sucedida sem uma chamada de Autenticação Multifator, pois uma autenticação bem-sucedida anterior para o mesmo nome de usuário, tipo de autenticação e nome de aplicativo ocorreu dentro do período de cache configurado. |
| SUCCESS_INVALID_INPUT | Entrada de Telefone Inválida | A resposta enviada pelo telefone não é válida.   Ela pode ter vindo de um fax ou modem, ou o usuário pode ter inserido * como parte de seu PIN. |
| SUCCESS_USER_BLOCKED | Usuário está bloqueado | O número de telefone do usuário está bloqueado.   Um número bloqueado pode ser iniciado pelo usuário durante uma chamada de autenticação ou por um administrador usando o portal do Azure. <br> OBSERVAÇÃO:   Um número bloqueado é um subproduto de um Alerta de Fraude. |
| SUCCESS_SMS_AUTHENTICATED | Mensagem de texto autenticada | Para uma mensagem de texto bidirecional, o usuário respondeu corretamente com sua senha de uso único (OTP) ou OTP + PIN. |
| SUCCESS_SMS_SENT | Mensagem de texto enviada | Para uma mensagem de texto, a mensagem de texto contendo a senha de uso único (OTP) foi enviada com êxito.   O usuário insere OTP ou OTP + PIN no aplicativo para concluir a autenticação. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Aplicativos móveis autenticados | O usuário foi autenticado com êxito por meio do aplicativo móvel. |
| SUCCESS_OATH_CODE_PENDING | Código OATH Pendente | O usuário foi solicitado a fornecer seu código OATH, mas não respondeu. |
| SUCCESS_OATH_CODE_VERIFIED | Código OATH Verificado | O usuário digitou um código OATH válido quando solicitado. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Código OATH de Fallback Verificado | O usuário teve a autenticação negada usando o método de Autenticação Multifator primário e forneceu um código OATH válido para fallback. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Perguntas de Segurança de Fallback Respondidas | O usuário teve a autenticação negada usando o método de Autenticação Multifator primário e respondeu as perguntas de segurança corretamente para fallback. |
| FAILED_PHONE_BUSY | Autenticação em Andamento | A Autenticação Multifator já está processando uma autenticação para esse usuário.   Isso normalmente é causado por clientes de RADIUS que enviam várias solicitações de autenticação durante o mesmo acesso. |
| CONFIG_ISSUE | Telefone Inacessível | O usuário tentou efetuar a chamada, mas não pôde completá-la ou ela não foi respondida.   Isso inclui sinal de ocupado, sinal de ocupado rápido (desconectado), três tons (número fora de serviço), tempo limite atingido ao tocar, etc. |
| FAILED_INVALID_PHONENUMBER | Formato de Número de Telefone Inválido | O número de telefone tem um formato inválido.   Os números de telefone devem ser numéricos e ter 10 dígitos para o código do país + 1 (Estados Unidos e Canadá). |
| FAILED_USER_HUNGUP_ON_US | Usuário Desligou o Telefone | O usuário atendeu ao telefone, mas desligou sem pressionar quaisquer botões. |
| FAILED_INVALID_EXTENSION | Ramal Inválido | O ramal contém caracteres inválidos.   Apenas dígitos, vírgulas, * e # são permitidos.   O prefixo @ também pode ser usado. |
| FAILED_FRAUD_CODE_ENTERED | Código de Fraude Inserido | O usuário escolheu relatar uma fraude durante a chamada, fazendo com que a autenticação fosse negada e o número de telefone, bloqueado.| 
| FAILED_SERVER_ERROR | Não é Possível Fazer Chamada | O serviço de Autenticação Multifator não pôde fazer a chamada. |
| FAILED_SMS_NOT_SENT | Não foi possível enviar uma mensagem de texto | Não foi possível enviar a mensagem de texto.   A autenticação foi negada. |
| FAILED_SMS_OTP_INCORRECT | OTP da mensagem de texto incorreta | O usuário inseriu uma senha de uso único (OTP) incorreta na mensagem de texto recebida.   A autenticação foi negada. |
| FAILED_SMS_OTP_PIN_INCORRECT | OTP + PIN da mensagem de texto incorretos | O usuário inseriu uma senha de uso único (OTP) incorreta e/ou um PIN de usuário incorreto.   A autenticação foi negada. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Excedidas as Tentativas Máximas de OTP de Mensagem de Texto | O usuário excedeu o número máximo de tentativas de senha de uso único (OTP). |
| FAILED_PHONE_APP_DENIED | Aplicativos móveis negados | O usuário negou a autenticação no aplicativo móvel ao pressionar o botão Negar. |
| FAILED_PHONE_APP_INVALID_PIN | PIN do Aplicativo Móvel Inválido | O usuário inseriu um PIN inválido durante a autenticação no aplicativo móvel. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | PIN do Aplicativo Móvel Não Alterado | O usuário não concluiu com êxito uma alteração de PIN necessária no aplicativo móvel. |
| FAILED_FRAUD_REPORTED | Fraude Relatada | O usuário relatou uma fraude no aplicativo móvel. |
| FAILED_PHONE_APP_NO_RESPONSE | Sem Resposta do Aplicativo Móvel | O usuário não respondeu à solicitação de autenticação do aplicativo móvel. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Todos os Dispositivos do Aplicativo Móvel Bloqueados | Os dispositivos do aplicativo móvel desse usuário não estão mais respondendo às notificações e foram bloqueados. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Falha na Notificação do Aplicativo Móvel | Ocorreu uma falha ao tentar enviar uma notificação ao aplicativo móvel no dispositivo do usuário. |
| FAILED_PHONE_APP_INVALID_RESULT | Resultado Inválido do Aplicativo Móvel | O aplicativo móvel retornou um resultado inválido. |
| FAILED_OATH_CODE_INCORRECT | Código OATH Incorreto | O usuário inseriu um código OATH incorreto.  A autenticação foi negada. |
| FAILED_OATH_CODE_PIN_INCORRECT | Código OATH + PIN Incorreto | O usuário inseriu um código OATH incorreto e/ou um PIN de usuário incorreto.  A autenticação foi negada. |
| FAILED_OATH_CODE_DUPLICATE | Duplicar Código OATH | O usuário inseriu um código OATH usado anteriormente.  A autenticação foi negada. |
| FAILED_OATH_CODE_OLD | Código OATH Desatualizado | O usuário inseriu um código OATH que precede um código OATH usado anteriormente.  A autenticação foi negada. |
| FAILED_OATH_TOKEN_TIMEOUT | Tempo Limite de Resultado de Código OATH | O usuário demorou muito para inserir o código OATH e atingiu o tempo limite da tentativa de Autenticação Multifator. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Tempo Limite de Resultado de Perguntas sobre Segurança | O usuário demorou muito para inserir a resposta das perguntas de segurança e atingiu o tempo limite da tentativa de Autenticação Multifator. |
| FAILED_AUTH_RESULT_TIMEOUT | Tempo Limite de Resultado de Autenticação | O usuário demorou muito para concluir a tentativa de Autenticação Multifator. |
| FAILED_AUTHENTICATION_THROTTLED | Autenticação limitada | A tentativa de Autenticação Multifator foi limitada pelo serviço. |

## <a name="additional-mfa-reports"></a>Relatórios adicionais de MFA

As informações e relatórios adicionais a seguir estão disponíveis para eventos de MFA, incluindo para o servidor de MFA:

| Relatório | Location | Descrição |
|:--- |:--- |:--- |
| Histórico de usuário bloqueado | Azure AD > Segurança > MFA > Bloquear/desbloquear usuários | Mostra o histórico de solicitações para bloquear ou desbloquear usuários. |
| Uso para componentes locais | Azure AD > Segurança > MFA > Relatório de atividade | Fornece informações sobre o uso geral do servidor de MFA por meio do ramal de NPS, ADFS e servidor de MFA. |
| Histórico de usuário desviado | Azure AD > Segurança > MFA > Bypass avulso | Fornece um histórico das solicitações do servidor de MFA para ignorar a MFA de um usuário. |
| Status do servidor | Azure AD > Segurança > MFA > Status do servidor | Exibe o status dos Servidores de MFA associado à sua conta. |

## <a name="next-steps"></a>Próximas etapas

Este artigo forneceu uma visão geral do relatório de atividade de entradas. Para obter informações mais detalhadas sobre o que esse relatório contém e entender os dados, confira [Relatórios de atividade de entradas no Azure AD](../reports-monitoring/concept-sign-ins.md).
