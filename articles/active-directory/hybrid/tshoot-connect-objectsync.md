---
title: 'Azure AD Connect: solucionando de problemas durante a sincronização | Microsoft Docs'
description: Este tópico fornece etapas para solucionar problemas com a sincronização de objetos usando a tarefa de solução de problemas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73d4239dd34f2a64aa7b3edbf88bad4348e01291
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85356195"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Solução de problemas de sincronização de objetos com a sincronização do Azure AD Connect
Este artigo descreve as etapas para solucionar problemas com a sincronização de objetos usando a tarefa de solução de problemas. Para ver como a solução de problemas funciona no Azure Active Directory (Azure AD) Connect, assista a [este breve vídeo](https://aka.ms/AADCTSVideo).

## <a name="troubleshooting-task"></a>Tarefa de solução de problemas
Para a implantação do Azure AD Connect com a versão 1.1.749.0 ou posterior, use a tarefa de solução de problemas no assistente para solucionar problemas de sincronização de objetos. Para versões anteriores, solucione o problema manualmente conforme descrito [aqui](tshoot-connect-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Executar a tarefa de solução de problemas no assistente
Para executar a tarefa de solução de problemas no assistente, execute as etapas a seguir:

1.  Abra uma nova sessão do Windows PowerShell no servidor do Azure AD Connect com a opção Executar como administrador.
2.  Execute `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.
3.  Iniciar o assistente do Azure AD Connect.
4.  Navegue até a página Tarefas Adicionais, selecione Solucionar problemas e clique em Avançar.
5.  Na página de solução de problemas, clique em Iniciar para iniciar o menu de solução de problemas no PowerShell.
6.  No menu principal, selecione Solucionar Problemas de Sincronização de Senha.
![Solucionar problemas de sincronização de objeto](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Parâmetros de entrada para a solução de problemas
Os parâmetros de entrada a seguir são necessários para a tarefa de solução de problemas:
1.  **Nome distinto do objeto** – este é o nome distinto do objeto que precisa de solução de problemas
2.  **Nome do Conector AD** – este é o nome da floresta do AD em que reside o objeto acima.
3.  Credenciais do administrador global de locatário do Azure AD credenciais de ![ administrador global](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Entender os resultados da tarefa de solução de problemas
A tarefa de solução de problemas executa as seguintes verificações:

1.  Detectar incompatibilidade de UPN se o objeto estiver sincronizado com o Azure Active Directory
2.  Verificar se o objeto é filtrado devido a filtragem do domínio
3.  Verificar se o objeto é filtrado devido a filtragem do OU
4.  Verifique se a sincronização de objeto está bloqueada devido a uma caixa de correio vinculada
5. Verifique se o objeto é grupo de distribuição dinâmica o que não tem suporte para ser sincronizado

O restante desta seção descreve resultados específicos que são retornados pela tarefa. Em cada caso, a tarefa fornece uma análise seguida pelas ações recomendadas para resolver o problema.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Detectar incompatibilidade de UPN se o objeto estiver sincronizado com o Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>O sufixo UPN não foi verificado com locatário do Azure Active Directory
Quando o sufixo de UserPrincipalName (UPN)/ID de logon alternativo não é verificado com o locatário do Azure Active Directory, os sufixos de UPN são substituídos pelo nome de domínio padrão "onmicrosoft.com".

![O Azure AD substitui o UPN](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>O recurso de DirSync do Locatário do Azure Active Directory 'SynchronizeUpnForManagedUsers' está desabilitado
Quando o recurso de DirSync do Locatário do Azure Active Directory 'SynchronizeUpnForManagedUsers' está desabilitado, o Azure Active Directory não permite atualizações de sincronização para UserPrincipalName/ID de logon alternativo para contas de usuário licenciado com autenticação gerenciada.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>O objeto é filtrado devido a filtragem do domínio
### <a name="domain-is-not-configured-to-sync"></a>O domínio não está configurado para sincronização
O objeto está fora do escopo porque o domínio não está sendo configurado. No exemplo abaixo, o objeto está fora do escopo de sincronização, pois o domínio ao qual ele pertence é filtrado da sincronização.

![O domínio não está configurado para sincronização](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>O domínio é configurado para sincronização, mas tem perfis de execução/etapas de execução ausentes
O objeto está fora do escopo porque o domínio tem perfis de execução/etapas de execução ausentes. No exemplo abaixo, o objeto está fora do escopo de sincronização, pois o domínio ao qual ele pertence tem etapas de execução ausentes para o perfil de execução Importação Completa.
![perfis de execução ausentes](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>O objeto é filtrado devido a filtragem do OU
O objeto está fora do escopo de sincronização devido a configuração de filtragem de OU. No exemplo abaixo, o objeto pertence à OU=NoSync,DC=bvtadwbackdc,DC=com.  Esse OU não está incluído no escopo de sincronização.</br>

![OU](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Problema de caixa de correio vinculada
Uma caixa de correio vinculada deve para ser associada a uma conta mestre externa localizada em outra floresta de conta confiável. Se não houver nenhuma conta mestre externa, então o Azure AD Connect não sincronizará a conta de usuário corresponde à caixa de correio vinculada na floresta da Troca para o locatário do Azure AD.</br>
![Caixa de correio vinculada](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Problema de Grupo de Distribuição Dinâmica
Devido às várias diferenças entre o Active Directory local e o Azure Active Directory, o Azure AD Connect não sincronizar grupos de distribuição dinâmica para o locatário do Azure AD.

![Grupo Dinâmico de Distribuição](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>Relatório HTML
Além de analisar o objeto, a tarefa de solução de problemas também gera um relatório HTML que tem tudo o que se sabe sobre o objeto. Este relatório HTML pode ser compartilhado com a equipe de suporte para fazer mais solução de problemas, se necessário.

![Relatório HTML](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
