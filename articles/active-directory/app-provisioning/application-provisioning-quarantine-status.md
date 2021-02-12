---
title: Status de provisionamento de aplicativo de quarentena | Microsoft Docs
description: Quando você configurou um aplicativo para provisionamento automático de usuário, saiba o que é um status de provisionamento de quarentena e como limpá-lo.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/24/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: d997c85f96fa9f87ca6d017cb555b3732007e21c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256298"
---
# <a name="application-provisioning-in-quarantine-status"></a>Provisionamento de aplicativo no status de quarentena

O serviço de provisionamento do Azure AD monitora a integridade de sua configuração. Ele também coloca aplicativos não íntegros em um estado de "quarentena". Se a maioria, ou todas, das chamadas feitas em relação ao sistema de destino falharem consistentemente, o trabalho de provisionamento será marcado como em quarentena. Um exemplo de uma falha é um erro recebido devido a credenciais de administrador inválidas.

Em quarentena:
- A frequência de ciclos incrementais é reduzida gradualmente para uma vez por dia.
- O trabalho de provisionamento é removido da quarentena depois que todos os erros são corrigidos e o próximo ciclo de sincronização é iniciado. 
- Se o trabalho de provisionamento permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento será desabilitado (interromperá a execução).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Como fazer saber se meu aplicativo está em quarentena?

Há três maneiras de verificar se um aplicativo está em quarentena:
  
- Na portal do Azure, navegue até **Azure Active Directory**  >  **aplicativos empresariais**  >  &lt; *nome do aplicativo* &gt;  >  **provisionamento** e examine a barra de progresso de uma mensagem de quarentena.   

  ![Barra de status de provisionamento mostrando o status de quarentena](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- No portal do Azure, navegue até **Azure Active Directory**  >  **logs de auditoria** > filtrar em **atividade: quarentena** e examine o histórico de quarentena. A exibição na barra de progresso, conforme descrito acima, mostra se o provisionamento está em quarentena no momento. Os logs de auditoria mostram o histórico de quarentena de um aplicativo. 

- Use a solicitação de Microsoft Graph [obter synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta&preserve-view=true) para obter programaticamente o status do trabalho de provisionamento:

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- Verifique seu email. Quando um aplicativo é colocado em quarentena, um email de notificação de uso único é enviado. Se o motivo da quarentena for alterado, um email atualizado será enviado mostrando o novo motivo para quarentena. Se você não vir um email:

  - Verifique se você especificou um **email de notificação** válido na configuração de provisionamento para o aplicativo.
  - Verifique se não há nenhuma filtragem de spam na caixa de entrada de email de notificação.
  - Certifique-se de que você não cancelou a assinatura de emails.
  - Verificar emails de `azure-noreply@microsoft.com`

## <a name="why-is-my-application-in-quarantine"></a>Por que meu aplicativo está em quarentena?

|Descrição|Ação recomendada|
|---|---|
|**Problema de conformidade do scim:** Uma resposta HTTP/404 não encontrada foi retornada em vez da resposta HTTP/200 OK esperada. Nesse caso, o serviço de provisionamento do Azure AD fez uma solicitação para o aplicativo de destino e recebeu uma resposta inesperada.|Verifique a seção credenciais de administrador. Veja se o aplicativo requer a especificação da URL do locatário e se a URL está correta. Se você não vir um problema, entre em contato com o desenvolvedor do aplicativo para garantir que seu serviço seja compatível com SCIM. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Credenciais inválidas:** Ao tentar autorizar, acessar o aplicativo de destino, recebemos uma resposta do aplicativo de destino que indica que as credenciais fornecidas são inválidas.|Navegue até a seção credenciais de administrador da interface do usuário de configuração de provisionamento e autorize o acesso novamente com credenciais válidas. Se o aplicativo estiver na Galeria, examine o tutorial de configuração do aplicativo para obter mais etapas necessárias.|
|**Funções duplicadas:** As funções importadas de determinados aplicativos, como Salesforce e zendesk, devem ser exclusivas. |Navegue até o [manifesto](../develop/reference-app-manifest.md) do aplicativo no portal do Azure e remova a função duplicada.|

 Uma solicitação de Microsoft Graph para obter o status do trabalho de provisionamento mostra o seguinte motivo para quarentena:
- `EncounteredQuarantineException` indica que foram fornecidas credenciais inválidas. O serviço de provisionamento não pode estabelecer uma conexão entre o sistema de origem e o sistema de destino.
- `EncounteredEscrowProportionThreshold` indica que o provisionamento excedeu o limite de caução. Essa condição ocorre quando mais de 40% dos eventos de provisionamento falharam. Para obter mais informações, consulte detalhes do limite de caução abaixo.
- `QuarantineOnDemand` significa que detectamos um problema com seu aplicativo e o definimos manualmente como quarentena.

**Limites de caução**

Se o limite de caução proporcional for atendido, o trabalho de provisionamento entrará em quarentena. Essa lógica está sujeita a alterações, mas funciona aproximadamente conforme descrito abaixo: 

Um trabalho pode entrar em quarentena independentemente das contagens de falha para problemas como credenciais de administrador ou conformidade SCIM. No entanto, em geral, 5.000 falhas são o mínimo para começar a avaliar se deseja colocar em quarentena devido a muitas falhas. Por exemplo, um trabalho com falhas de 4.000 não entrará em quarentena. Mas, um trabalho com falhas de 5.000 dispararia uma avaliação. Uma avaliação usa os seguintes critérios:  
- Se mais de 40% dos eventos de provisionamento falharem ou houver mais de 40.000 falhas, o trabalho de provisionamento entrará em quarentena. Falhas de referência não serão contadas como parte do limite de 40% ou de 40.000. Por exemplo, falha ao atualizar um gerente ou um membro do grupo é uma falha de referência.
- Um trabalho em que 45.000 usuários não foram provisionados com êxito resultaria em quarentena, pois excede o limite de 40.000.
- Um trabalho em que 30.000 os usuários com falha no provisionamento e 5.000 foram bem-sucedidos resultaria em quarentena, pois ela excede o limite de 40% e o mínimo de 5.000.
- Um trabalho com falhas de 20.000 e 100.000 de sucesso não entraria em quarentena porque ele não excedeu o limite de falha de 40% ou a falha máxima de 40.000.  
- Há um limite absoluto de 60.000 falhas que conta para falhas de referência e de não referência. Por exemplo, 40.000 usuários não puderam ser provisionados e 21.000 falha nas atualizações do Gerenciador. O total é de 61.000 falhas e excede o limite de 60.000.


## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Como fazer obter meu aplicativo fora de quarentena?

Primeiro, resolva o problema que fez com que o aplicativo fosse colocado em quarentena.

- Verifique as configurações de provisionamento do aplicativo para certificar-se de que você [inseriu credenciais de administrador válidas](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). O Azure AD deve estabelecer uma relação de confiança com o aplicativo de destino. Verifique se você inseriu credenciais válidas e se sua conta tem as permissões necessárias.

- Examine os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para investigar melhor quais erros estão causando a quarentena e resolver o erro. Vá até **Azure Active Directory** &gt; **aplicativos empresariais** &gt; **Provisionando logs (versão prévia)** na seção **atividade** .

Depois de resolver o problema, reinicie o trabalho de provisionamento. Determinadas alterações nas configurações de provisionamento do aplicativo, como mapeamentos de atributo ou filtros de escopo, reiniciarão automaticamente o provisionamento para você. A barra de progresso na página de **provisionamento** do aplicativo indica quando o provisionamento foi iniciado pela última vez. Se você precisar reiniciar o trabalho de provisionamento manualmente, use um dos seguintes métodos:  

- Use o portal do Azure para reiniciar o trabalho de provisionamento. Na página de **provisionamento** do aplicativo em **configurações**, selecione **limpar estado e reiniciar sincronização** e defina o **status de provisionamento** como **ativado**. Essa ação reinicia completamente o serviço de provisionamento, o que pode levar algum tempo. Um ciclo inicial completo será executado novamente, o que limpa as caução, remove o aplicativo da quarentena e limpa as marcas d' água.

- Use Microsoft Graph para [reiniciar o trabalho de provisionamento](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). Você terá controle total sobre o que reinicia. Você pode optar por limpar as caução (para reiniciar o contador de caução que se acumula para o status de quarentena), limpar a quarentena (para remover o aplicativo da quarentena) ou limpar as marcas d' água. Envie a seguinte solicitação:
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

Substitua "{ID}" pelo valor da ID do aplicativo e substitua "{jobId}" pela [ID do trabalho de sincronização](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta&preserve-view=true#list-synchronization-jobs-in-the-context-of-the-service-principal).
