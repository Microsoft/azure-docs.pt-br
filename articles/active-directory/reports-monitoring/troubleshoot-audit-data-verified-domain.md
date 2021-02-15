---
title: Solucionar problemas de dados de auditoria de alteração de domínio verificada | Microsoft Docs
description: Fornece informações que serão exibidas na Azure Active Directory logs de atividades quando você alterar um domínio verificado pelos usuários.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/22/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3c9ec3b1e96e47dbf46c6acb2c81147b614d069
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87117421"
---
# <a name="troubleshoot-audit-data-on-verified-domain-change"></a>Solução de problemas: auditar dados em alteração de domínio verificado 


## <a name="i-have-a-lot-of-changes-to-my-users-and-i-am-not-sure-what-the-cause-of-it-is"></a>Tenho muitas alterações aos meus usuários e não tenho certeza de qual é a causa dele.

### <a name="symptoms"></a>Sintomas

Verifico os logs de auditoria do Azure AD e vejo várias atualizações de usuário ocorrendo em meu locatário do Azure AD. Esses eventos de **atualização de usuário** não exibem informações de **ator** , o que causa incerteza sobre o que/quem disparou as alterações em massa para os usuários. 

### <a name="cause"></a>Causa

 Um motivo comum por trás das alterações de objetos em massa é uma operação de back-end não síncrona chamada **ProxyCalc**.  **ProxyCalc** é a lógica que determina os endereços de **userPrincipalName** e **proxy**apropriados, que são atualizados nos usuários, grupos ou contatos do Azure AD. O design por trás do **ProxyCalc** é garantir que todos os **endereços** de **userPrincipalName** e proxy sejam consistentes no Azure ad a qualquer momento. **ProxyCalc** deve ser disparado por uma alteração explícita como uma alteração de domínio verificada e não é executado de forma perpétua em segundo plano como uma tarefa. 

  

#### <a name="what-does-userprincipalname-consistency-mean"></a>O que significa consistência de UserPrincipalName? 

Para usuários somente na nuvem, a consistência significa que o **userPrincipalName** está definido como um sufixo de domínio verificado. Quando um **userPrincipalName** inconsistente for processado, o **ProxyCalc** o converterá para o sufixo onmicrosoft.com padrão, por exemplo: username@Contoso.onmicrosoft.com 

Para usuários sincronizados, a consistência significa que o **userPrincipalName** é definido como um sufixo de domínio verificado e corresponde ao valor **userPrincipalName** local (ShadowUserPrincipalName). Quando um **userPrincipalName** inconsistente for processado, **ProxyCalc** será revertido para o mesmo valor que o **ShadowUserPrincipalName** ou, caso o sufixo de domínio tenha sido removido do locatário, o converterá para o sufixo de domínio padrão *. onmicrosoft.com. 

  

#### <a name="what-does-proxy-address-consistency-mean"></a>O que significa consistência de endereço de proxy? 

Para usuários somente de nuvem, a consistência significa que os endereços de proxy correspondem a um sufixo de domínio verificado. Quando um endereço de proxy inconsistente for processado, o **ProxyCalc** o converterá para o sufixo de domínio padrão *. onmicrosoft.com, por exemplo: SMTP:username@Contoso.onmicrosoft.com 

Para usuários sincronizados, a consistência significa que os endereços de proxy correspondem aos valores de endereço (s) de proxy local (ou seja, ShadowProxyAddresses). Espera-se que **proxyAddresses** estejam em sincronia com **ShadowProxyAddresses**. Se o usuário sincronizado tiver uma licença do Exchange atribuída, os endereços proxy deverão corresponder aos valores de endereço (s) do proxy local e também devem corresponder a um sufixo de domínio verificado. Neste cenário, **ProxyCalc** limpará o endereço de proxy inconsistente com um sufixo de domínio não verificado e será removido do objeto no Azure AD. Se esse domínio não verificado for verificado mais tarde, o **ProxyCalc** recalculará e adicionará o endereço de proxy de **ShadowProxyAddresses** de volta ao objeto no Azure AD.  

> [!NOTE]
> Para objetos sincronizados, para evitar que a lógica **ProxyCalc** Calcule resultados inesperados, é melhor definir endereços proxy para um domínio verificado do Azure AD no objeto local.  

  
Uma das tarefas de administração que poderiam disparar **ProxyCalc** é sempre que há uma alteração de domínio verificada. Essa tarefa ocorre toda vez que um domínio verificado é adicionado/removido de um locatário do Azure AD, que dispara internamente **ProxyCalc**.  

Por exemplo, se você adicionar um domínio verificado Fabrikam.com ao seu locatário do Contoso.onmicrosoft.com, essa ação irá disparar uma operação ProxyCalc em todos os objetos no locatário. Esse evento será capturado nos logs de auditoria do Azure AD como eventos de **atualização de usuário** precedidos por um evento **Adicionar domínio verificado** . Por outro lado, se Fabrikam.com tiver sido removido do locatário do Contoso.onmicrosoft.com, todos os eventos de **atualização do usuário** serão precedidos por um evento de remoção de **domínio verificado** .   

#### <a name="additional-notes"></a>Observações adicionais:

ProxyCalc não causa alterações em determinados objetos que: 

- Não tem uma licença ativa do Exchange 
- ter **MSExchRemoteRecipientType** definido como nulo 
- Não são considerados um recurso compartilhado. O recurso compartilhado é **quando CloudMSExchRecipientDisplayType** contém um dos seguintes valores: **MailboxUser (compartilhado)**, **PublicFolder**, **ConferenceRoomMailbox**, **EquipmentMailbox**, **ArbitrationMailbox**, **salalist**, **TeamMailboxUser**, **caixa de correio de grupo**, caixa de **correio de agendamento**, **ACLableMailboxUser**, **ACLableTeamMailboxUser** 
  
 Para criar mais correlação entre esses dois eventos diferentes, a Microsoft está trabalhando para atualizar as informações de **ator** nos logs de auditoria para identificar essas alterações conforme disparado por uma alteração de domínio verificado. Essa ação ajudará a verificar quando o evento de alteração de domínio verificado ocorreu e começou a atualizar os objetos em massa em seu locatário. 

Além disso, na maioria dos casos, não há nenhuma alteração aos usuários, já que seus endereços de **userPrincipalName** e **proxy** são consistentes, então estamos trabalhando para exibir em logs de auditoria somente as atualizações que causaram uma alteração real no objeto. Essa ação impedirá ruído nos logs de auditoria e ajudará os administradores a correlacionar as alterações de usuário restantes ao evento de alteração de domínio verificado, conforme explicado acima. 

## <a name="next-steps"></a>Próximas etapas

[Atributos sombra do serviço de sincronização do Azure AD Connect](../hybrid/how-to-connect-syncservice-shadow-attributes.md)
