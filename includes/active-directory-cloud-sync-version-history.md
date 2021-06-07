---
ms.openlocfilehash: 69f0da2f1528ad1f45762a8f754cc2020b4cb880
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98900740"
---
Este artigo lista as versões e os recursos do agente de provisionamento do Azure Active Directory Connect que foram lançados. A equipe do Azure AD atualiza regularmente o agente de provisionamento com novos recursos e funcionalidades. O agente de provisionamento é atualizado automaticamente quando uma nova versão é liberada. 

A Microsoft fornece suporte direto para a versão mais recente do agente e uma versão anterior.

## <a name="113540"></a>1.1.354.0

20 de janeiro de 2021: liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
- Melhoria na experiência de GMSA, incluindo suporte para conta de GMSA criada previamente personalizada
- Suporte a [cmdlets do PowerShell](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md) para a instalação do GMSA
- [Suporte da CLI](../articles/active-directory/cloud-sync/how-to-install-pshell.md) para instalação do agente (instalação silenciosa)
- Diagnóstico adicional para problemas de quarentena de origem do agente
- Correções de bugs que incluem a redução do uso de memória de filtros de escopo de UO, execução de PHS somente para usuários no escopo, tratamento de objetos aninhados na UO ao usar o escopo de UO etc. 


### <a name="fixed-issues"></a>Problemas corrigidos
-    Impedir quarentena quando o grupo de escopo está fora do escopo
-   Quando os filtros de escopo são configurados-o trabalho PHS agora só funciona para usuários no escopo
-   O agente seria paralisado durante a atualização
-   Sincronização inicial para objetos em UOs aninhadas ao usar o escopo de UO
-   Torne o Repair-AADCloudSyncToolsAccount mais robusto
-   Reduzir o uso de memória grande de filtros de escopo de UO
-   A verificação da função de administrador falhará se os membros da função contiverem um grupo de segurança
-   Corrigir o problema de permissão da pasta GMSA que impede a renovação do certificado do agente







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Status de liberação

23 de novembro de 2020: liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Suporte para [gMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts)
* Suporte para grupos com tamanho inferior a 1500 membros durante o ciclo de sincronização incremental ou Delta. Isso é aplicável ao usar o filtro de escopo de grupo
* Suporte para grupos grandes com tamanho de membro de até 15 k
* Melhorias de sincronização inicial
* Log detalhado avançado
* Introdução do [módulo AADCloudSyncTools PowerShell](../articles/active-directory/cloud-sync/reference-powershell.md)
* Limitações corrigidas para permitir que o agente seja instalado em um servidor que não seja o inglês
* Suporte para filtragem de PHS somente para objetos no escopo (originalmente, estamos sincronizando hashes de senha para todos os objetos)
* Correção do problema de vazamento de memória no agente
* Logs de provisionamento aprimorados
* Suporte para configurar o [tempo limite da conexão LDAP](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 
* Suporte para configurar a busca de [referência](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing) 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Status de liberação

4 de dezembro de 2019: liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Inclui suporte para [sincronização de Azure ad Connect Cloud](../articles/active-directory/cloud-sync/what-is-cloud-sync.md) para sincronizar o usuário, contatar e agrupar dados do Active Directory local para o Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Status de liberação

9 de setembro de 2019: liberado para atualização automática

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Capacidade de configurar rastreamento e log adicionais para depuração de problemas do agente de provisionamento
* Capacidade de buscar somente os atributos do AD do Azure que estão configurados no mapeamento para melhorar o desempenho da sincronização

### <a name="fixed-issues"></a>Problemas corrigidos

* Corrigido um bug em que o agente entrou em um estado sem resposta se houvesse problemas com falhas de conexão do Azure AD
* Correção de um bug que causou problemas quando dados binários foram lidos de Azure Active Directory
* Correção de um bug em que o agente falhou ao renovar a confiança com o serviço de identidade híbrida na nuvem

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Status de liberação

23 de janeiro de 2019: liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Remodelado o agente de provisionamento e a arquitetura de conector para melhorar o desempenho, a estabilidade e a confiabilidade 
* Simplifica a configuração do agente de provisionamento usando o assistente de instalação orientado por interface do usuário 
* Suporte adicionado para atualizações automáticas de agente