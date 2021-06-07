---
title: Corrigir problemas com associações de grupo dinâmicos – Azure AD | Microsoft Docs
description: Dicas de solução de problemas para associação de grupo dinâmico no Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: troubleshooting
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41bfdf11bad28ab772b68839a5a7bf7776eb4dff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96548096"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Solucionar problemas e resolver questões de grupos

## <a name="troubleshooting-group-creation-issues"></a>Solucionando problemas de criação de grupo

**Desabilitei a criação do grupo de segurança na portal do Azure mas os grupos ainda podem ser criados por meio do PowerShell** O **usuário pode criar grupos de segurança na configuração de portais do Azure** no portal do Azure controla se os usuários não administradores podem ou não criar grupos de segurança no painel de acesso ou no portal do Azure. Ele não controla a criação do grupo de segurança por meio do PowerShell.

Para desabilitar a criação de grupo para usuários não administradores no PowerShell:
1. Verifique se os usuários não administradores têm permissão para criar grupos:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Se retornar `UsersPermissionToCreateGroupsEnabled : True`, os usuários não administradores podem criar grupos. Para desabilitar esse recurso:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Recebi um erro de máximo de grupos permitidos ao tentar criar um grupo dinâmico no PowerShell**<br/>
Se você receber uma mensagem no PowerShell indicando que a _contagem máxima de grupos permitidos das diretivas de grupo dinâmicos foi atingida_, isso significa que você atingiu o limite máximo de grupos dinâmicos em sua organização. O número máximo de grupos dinâmicos por organização é 5.000.

Para criar novos grupos dinâmicos, primeiro você precisará excluir alguns grupos dinâmicos existentes. Não há como aumentar o limite.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Solucionando problemas de associações dinâmicas a grupos

**Configurei uma regra em um grupo, mas nenhuma associação foi atualizada no grupo**<br/>
1. Verifique os valores dos atributos de usuário ou dispositivo na regra. Verifique se há usuários que satisfaçam a regra. Para dispositivos, verifique as propriedades do dispositivo para garantir que todos os atributos sincronizados contenham os valores esperados.<br/>
2. Verifique o status de processamento de associação para confirmar se ele está concluído. Você pode verificar o [status de processamento de associação](groups-create-rule.md#check-processing-status-for-a-rule) e a data da última atualização na página **visão geral** do grupo.

Se tudo estiver correto, aguarde alguns instantes para que o grupo seja populado. Dependendo do tamanho da sua organização do Azure AD, o grupo pode levar até 24 horas para ser populado pela primeira vez ou após uma alteração de regra.

**Configurei uma regra, mas agora os membros da regra existentes foram removidos**<br/>Esse comportamento é esperado. Membros existentes do grupo são removidos quando uma regra é habilitada ou alterada. Os usuários retornados da avaliação da regra são adicionados como membros ao grupo.

**Não vejo as alterações da associação instantaneamente quando adiciono ou altero uma regra, por que não?**<br/>A avaliação de associação dedicada é feita periodicamente em um processo assíncrono em segundo plano. O tempo que o processo leva é determinado pelo número de usuários no diretório e pelo tamanho do grupo criado como resultado da regra. Normalmente, os diretórios com um pequeno número de usuários verão as alterações de associação de grupo em poucos minutos. Os diretórios com um grande número de usuários podem levar até 30 minutos ou mais para serem populados.

**Como posso forçar o processamento do grupo agora?**<br/>
Atualmente, não há como disparar automaticamente o grupo a ser processado sob demanda. No entanto, você pode disparar manualmente o reprocessamento atualizando a regra de associação para adicionar um espaço em branco no final.  

**Eu encontrei uma erro de processamento de regra**<br/>A seguinte tabela relacionará os erros de regra de associação e como corrigi-los.

| Erro do analisador de regra | Erro de uso | Uso corrigido |
| --- | --- | --- |
| Erro: O atributo não tem suportado. |(user.invalidProperty -eq "Valor") |(user.department -eq "value") A propriedade<br/><br/>Verifique se o atributo está na [lista de propriedades com suporte](groups-dynamic-membership.md#supported-properties). |
| Erro: Operador não é tem suportada no atributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true) A propriedade <br/><br/>Não há suporte para o operador usado para o tipo de propriedade (neste exemplo, -contains não pode ser usado no tipo booliano). Use os operadores corretos para o tipo de propriedade. |
| Erro: Erro de compilação de consulta. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match "*@domain.ext") | 1. operador ausente. Use -and ou -or para unir predicados<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. erro na expressão regular usada com-Match<br>(user.userPrincipalName -match "*@domain.ext")<br>ou alternativamente: (user.userPrincipalName -match "@domain.ext") |

## <a name="next-steps"></a>Próximas etapas

Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciando o acesso a recursos com grupos de Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Gerenciamento de aplicativos no Microsoft Azure Active Directory](../manage-apps/what-is-application-management.md)
* [O que é o Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](../hybrid/whatis-hybrid-identity.md)