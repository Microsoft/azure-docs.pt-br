---
title: Perguntas frequentes sobre a sincronização de nuvem do Azure AD Connect
description: Este documento descreve as perguntas frequentes sobre a sincronização de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39d1554fd1b6cac1a90a794cfd93def97e494bfe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98612968"
---
# <a name="azure-active-directory-connect-cloud-sync-faq"></a>Perguntas frequentes sobre sincronização do Azure Active Directory Connect Cloud

Leia sobre as perguntas frequentes sobre o Azure Active Directory (Azure AD) conectar a sincronização de nuvem.

## <a name="general-installation"></a>Instalação geral

**P: com que frequência a sincronização de nuvem é executada?**

O provisionamento de nuvem está agendado para ser executado a cada 2 minutos. A cada 2 minutos, quaisquer alterações de usuário, de grupo e de hash de senha serão provisionadas para o Azure AD.

**P: vendo falhas de sincronização de hash de senha na primeira execução. Por?**

Isso é esperado. As falhas são devido ao objeto de usuário não estar presente no Azure AD. Depois que o usuário for provisionado no Azure AD, os hashes de senha deverão ser provisionados na execução subsequente. Aguarde algumas execuções e confirme se a sincronização de hash de senha já deixou de apresentar os erros.

**P: o que acontece se a instância de Active Directory tem atributos que não são suportados pela sincronização de nuvem (por exemplo, extensões de diretório)?**

O provisionamento em nuvem será executado e provisionará os atributos compatíveis. Os atributos não compatíveis não serão provisionados no Azure AD. Examine as extensões de diretório em Active Directory e certifique-se de que você não precisa desses atributos para fluir para o Azure AD. Se um ou mais atributos forem necessários, considere o uso da sincronização do Azure AD Connect ou mover as informações necessárias para um dos atributos compatíveis (por exemplo, atributos de extensão 1 a 15).

**P: Qual é a diferença entre Azure AD Connect sincronização e sincronização de nuvem?**

Com a sincronização do Azure AD Connect, o provisionamento é executado no servidor de sincronização local. A configuração é armazenada no servidor de sincronização local. Com Azure AD Connect a sincronização de nuvem, a configuração de provisionamento é armazenada na nuvem e é executada na nuvem como parte do serviço de provisionamento do Azure AD. 

**P: posso usar a sincronização de nuvem para sincronizar de várias florestas Active Directory?**

Sim. O provisionamento de nuvem pode ser usado para sincronizar de várias florestas do Active Directory. No ambiente de várias florestas, todas as referências (por exemplo, gerente) precisam estar dentro do domínio.  

**P: como o agente é atualizado?**

Os agentes são atualizados automaticamente pela Microsoft. Para a equipe de TI, isso reduz a carga de ter que testar e validar novas versões de agente. 

**P: posso desabilitar a atualização automática?**

Não há nenhuma maneira compatível de desabilitar a atualização automática.

**P: posso alterar a âncora de origem para a sincronização de nuvem?**

Por padrão, a sincronização de nuvem usa o MS-DS-Consistency-GUID com um fallback para objectGUID como âncora de origem. Não há nenhuma maneira compatível de alterar a âncora de origem.

**P: Eu vejo novas entidades de serviço com os nomes de domínio do AD ao usar a sincronização de nuvem. É esperado?**

Sim, a sincronização de nuvem cria uma entidade de serviço para a configuração de provisionamento com o nome de domínio como o nome da entidade de serviço. Não faça nenhuma alteração na configuração da entidade de serviço.

**P: o que acontece quando um usuário sincronizado é necessário para alterar a senha no próximo logon?**

Se a sincronização de hash de senha estiver habilitada na sincronização de nuvem e o usuário sincronizado for necessário para alterar a senha no próximo logon no AD local, a sincronização de nuvem não provisionará o hash de senha "a ser alterado" para o Azure AD. Depois que o usuário altera a senha, o hash de senha do usuário é provisionado do AD para o Azure AD.

**P: a sincronização de nuvem dá suporte a Write-back de MS-DS-consistencyGUID para qualquer objeto?**

Não, a sincronização de nuvem não dá suporte a Write-back de MS-DS-consistencyGUID para qualquer objeto (incluindo objetos de usuário). 

**P: Estou Provisionando usuários usando a sincronização de nuvem. Excluí a configuração. Por que eu ainda vejo os antigos objetos sincronizados no Azure AD?** 

Quando você exclui a configuração, a sincronização de nuvem não remove automaticamente os objetos sincronizados no Azure AD. Para garantir que você não fique com objetos antigos, altere o escopo da configuração para um grupo vazio ou unidades organizacionais. Depois que o provisionamento é executado e limpa os objetos, desabilite e exclua a configuração. 

**P: o que significa que o Exchange híbrido não tem suporte?**

O recurso de implantação híbrida do Exchange permite a coexistência de caixas de correio do Exchange locais e em Microsoft 365. O Azure AD Connect está sincronizando um conjunto específico de atributos do Azure AD em seu diretório local.  O agente de provisionamento de nuvem atualmente não sincroniza esses atributos de volta para seu diretório local e, portanto, não tem suporte como uma substituição para Azure AD Connect.

**P: posso instalar o agente de provisionamento de nuvem no Windows Server Core?**

Não, não há suporte para a instalação do agente no Server Core.

**P: posso usar um servidor de preparo com o agente de provisionamento de nuvem?**

Não, não há suporte para servidores de preparo.

**P: posso sincronizar contas de usuário convidado?**

Não, não há suporte para a sincronização de contas de usuário convidado.

**P: se eu mover um usuário de uma UO com escopo para sincronização de nuvem para uma UO com escopo para Azure AD Connect, o que acontecerá?**

O usuário será excluído e recriado.  Mover um usuário de uma UO com escopo para sincronização de nuvem será exibido como uma operação de exclusão.  Se o usuário for movido para uma UO gerenciada pelo Azure AD Connect, ele será provisionado novamente para o Azure AD e um novo usuário criado.

**P: se eu renomear ou mover a UO que está no escopo do filtro de sincronização de nuvem, o que acontecerá com o usuário que foi criado no Azure AD?**

Nada.  Os usuários não serão excluídos se a UO for renomeada ou movida.

**P: Azure AD Connect a sincronização de nuvem dá suporte a grupos grandes?**

Sim. Hoje, damos suporte a até 50 mil membros do grupo sincronizados usando a filtragem de escopo de UO. Ao mesmo tempo, quando você usa a filtragem de escopo do grupo, recomendamos que você mantenha o tamanho do seu grupo para menos de 1500 membros. O motivo disso é que, embora você possa sincronizar um grupo grande como parte do filtro de escopo de grupo, ao adicionar membros a esse grupo por lotes maiores que 1500, a sincronização Delta falhará. 

## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem do Azure AD Connect?](what-is-cloud-sync.md)
