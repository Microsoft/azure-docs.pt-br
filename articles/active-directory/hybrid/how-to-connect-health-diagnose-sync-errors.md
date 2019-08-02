---
title: Azure Active Directory Connect Health - erros de sincronização atribuídos ao diagnóstico duplicado | Microsoft Docs
description: Este documento descreve o processo de diagnóstico de erros de sincronização de atributos duplicados e uma possível correção dos cenários de objetos órfãos diretamente do portal do Azure.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1fd5f9746299d72ed58a3209013822505b19b56
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702547"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnosticar e corrigir erros de sincronização de atributos duplicados

## <a name="overview"></a>Visão geral
Dando um passo adiante para destacar os erros de sincronização, o Azure Connect Health do Azure AD (Active Directory) apresenta a remediação de autoatendimento. Ele soluciona erros de sincronização de atributos duplicados e corrige objetos que são órfãos do AD do Azure.
O recurso de diagnóstico tem esses benefícios:
- Ele fornece um procedimento de diagnóstico que reduz os erros de sincronização de atributos duplicados. E isso dá correções específicas.
- Aplica uma correção para cenários dedicados do Azure AD para resolver o erro em uma única etapa.
- Nenhuma atualização ou a configuração é necessária para habilitar esse recurso.
Para obter mais informações sobre o Azure AD, consulte [ Sincronização de identidades e resiliência de atributos duplicados](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Problemas
### <a name="a-common-scenario"></a>Um cenário comum
Quando ocorrem **erros de sincronização QuarantinedAttributeValueMustBeUnique** e **AttributeValueMustBeUnique**, é comum ver um conflito entre **UserPrincipalName**  ou **Proxy Addresses** no AD do Azure. Você pode resolver os erros de sincronização atualizando o objeto de origem conflitante do lado local. O erro de sincronização será resolvido após a próxima sincronização. Por exemplo, esta imagem indica que dois usuários têm um conflito de **UserPrincipalName**. Ambos são **Joe.J\@contoso.com**. Os objetos conflitantes são colocados em quarentena no Microsoft Azure Active Directory.

![Diagnosticar cenário comum de erro de sincronização](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Cenário de objeto órfão
Ocasionalmente, você pode descobrir que um usuário existente perde a  **âncora de origem**. A exclusão do objeto de origem ocorreu no Active Directory local. Mas a alteração do sinal de exclusão nunca foi sincronizada com o Azure AD. Essa perda ocorre por motivos como problemas no mecanismo de sincronização ou migração do domínio. Quando o mesmo objeto é restaurado ou recriado, logicamente, um usuário existente deve ser o usuário a sincronizar a partir da  **Âncora de origem**. 

Quando um usuário existente é um objeto somente nuvem, você também pode ver o usuário conflitante sincronizado com o Azure AD. O usuário não pode ser correspondido em sincronia com o objeto existente. Não há nenhuma maneira direta de remapear a  **âncora de origem**. Veja mais sobre a [ base de conhecimento existente](https://support.microsoft.com/help/2647098). 

Por exemplo, o objeto existente no Azure AD preserva a licença de Joe. Um objeto recém-sincronizado com uma  **âncora de origem** diferente ocorre em um estado de atributo duplicado no Azure AD. As alterações de Joe no Active Directory no local não serão aplicadas ao usuário original do Joe (objeto existente) no Azure AD.  

![Diagnosticar o cenário de objeto órfão do erro de sincronização](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Etapas de diagnóstico e solução de problemas no Connect Health 
O recurso de diagnóstico suporta objetos do usuário com os seguintes atributos duplicados:

| Nome do atributo | Tipos de erros de sincronização|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique ou AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique ou AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Para acessar esse recurso, é necessária a permissão **Administração Global** ou a permissão  **Colaborador** das configurações do RBAC.
>

Siga as etapas do portal do Azure para restringir os detalhes do erro de sincronização e fornecer soluções mais específicas:

![Sincronizar etapas de diagnóstico de erros](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

No portal do Azure, siga algumas etapas para identificar cenários fixos específicos:  
1.  Verifique a coluna **Diagnosticar status**. O status mostra se há uma maneira possível de corrigir um erro de sincronização diretamente do Active Directory do Azure. Em outras palavras, existe um fluxo de solução de problemas que pode restringir o caso de erro e possivelmente corrigi-lo.

| Status | O que isso significa? |
| ------------------ | -----------------|
| Não iniciado | Você não visitou este processo de diagnóstico. Dependendo do resultado do diagnóstico, há uma maneira potencial de corrigir o erro de sincronização diretamente do portal. |
| Correção Manual Necessária | O erro não se ajusta aos critérios de correções disponíveis no portal. Os tipos de objetos conflitantes não são usuários ou você já passou pelas etapas de diagnóstico e nenhuma resolução de correção estava disponível no portal. No último caso, uma correção do lado local ainda é uma das soluções. [ Leia mais sobre as correções locais ](https://support.microsoft.com/help/2647098). | 
| Sincronização Pendente | Uma correção foi aplicada. O portal está aguardando o próximo ciclo de sincronização para limpar o erro. |

  >[!IMPORTANT]
  > A coluna de status de diagnóstico será redefinida após cada ciclo de sincronização. 
  >

1. Selecione o botão **Diagnosticar** sob os detalhes do erro. Você responderá algumas perguntas e identificará os detalhes do erro de sincronização. As respostas às perguntas ajudam a identificar um caso de objeto órfão.

1. Se um botão **Close** for exibido no final do diagnóstico, não haverá uma solução rápida disponível no portal com base nas suas respostas. Consulte a solução mostrada na última etapa. Correções do local ainda são as soluções. Selecione o botão **Fechar**. O status do erro de sincronização atual muda para **Correção manual necessária**. O status permanece durante o ciclo de sincronização atual.

1. Depois que um caso de objeto órfão é identificado, você pode corrigir os erros de sincronização de atributos duplicados diretamente do portal. Para acionar o processo, selecione o botão **Apply Fix**. O status do erro de sincronização atual é atualizado para **Pending sync**.

1. Após o próximo ciclo de sincronização, o erro deve ser removido da lista.

## <a name="how-to-answer-the-diagnosis-questions"></a>Como responder às perguntas de diagnóstico 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>O usuário existe no seu Active Directory no local?

Esta questão tenta identificar o objeto de origem do usuário existente no Active Directory local.  
1. Verifique se o Azure Active Directory tem um objeto com o **UserPrincipalName** fornecido. Se não, responda **Não**.
2. Em caso afirmativo, verifique se o objeto ainda está no escopo para sincronização.  
   - Pesquise no espaço do conector do Azure AD usando o DN.
   - Se o objeto for encontrado no estado **Pendente Adicionar**, responda **Não**. O Azure AD Connect não pode conectar o objeto ao objeto correto do Azure AD.
   - Se o objeto não for encontrado, responda **Sim**.

Nesses exemplos, a pergunta tenta identificar se **Joe Jackson** ainda existe no Active Directory local.
Para o **cenário comum**, os usuários **Joe Johnson** e  **Joe Jackson** estão presentes no Active Directory local. Os objetos em quarentena são dois usuários diferentes.

![Diagnosticar cenário comum de erro de sincronização](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

Para o **cenário de objeto órfão**, apenas o único usuário **Joe Johnson** está presente no Active Directory local:

![Diagnosticar erro de sincronização objeto órfão * o usuário existe* cenário](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Ambas as contas pertencem ao mesmo usuário?
Essa pergunta verifica se um usuário conflitante de entrada e o objeto de usuário existente no Azure AD verificam se pertencem ao mesmo usuário.  
1. O objeto conflitante é recém-sincronizado no Azure Active Directory. Compare os atributos dos objetos:  
   - Nome de exibição
   - Nome UPN
   - ID de objeto
2. Se o Azure AD não conseguir compará-los, verifique se o Active Directory tem objetos com o  **UserPrincipalNames** fornecido. Responda **Não** se você encontrar os dois.

No exemplo a seguir, os dois objetos pertencem ao mesmo usuário **Joe Johnson**.

![Diagnosticar erro de sincronização objeto órfão *mesmo cenário do usuário*](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>O que acontece depois que a correção é aplicada no cenário de objeto órfão
Com base nas respostas às perguntas anteriores, você verá o botão **Aplicar correção** quando houver uma correção disponível no AD do Azure. Nesse caso, o objeto local está sincronizando com um objeto inesperado do Azure AD. Os dois objetos são mapeados usando a **âncora de origem**. A alteração de **Aplicar Correção** segue estas ou etapas semelhantes:
1. Atualiza a **Âncora de origem** para o objeto correto no Azure AD.
2. Exclui o objeto conflitante no Azure AD, se estiver presente.

![Diagnosticar erro de sincronização após a correção](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> A alteração **Aplicar correção** se aplica apenas a casos de objeto órfãos.
>

Após as etapas anteriores, o usuário pode acessar o recurso original, que é um link para um objeto existente. O valor de **Diagnosticar status** na exibição de lista é atualizado para **Pending Sync**. O erro de sincronização será resolvido após a próxima sincronização. O Connect Health não mostrará mais o erro de sincronização resolvido na exibição de lista.

## <a name="failures-and-error-messages"></a>Mensagens de erro e falhas
**O usuário com atributo conflitante é excluído do Microsoft Azure Active Directory. Certifique-se de que o usuário é excluído forçadamente antes de tentar novamente.**  
O usuário com atributo conflitante no Azure AD deve ser excluído antes de aplicar a correção. Confira [como excluir o usuário permanentemente no Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore) antes de tentar novamente a correção. O usuário também será automaticamente excluído permanentemente após 30 dias no estado excluído flexível. 

**Não há suporte para atualizar âncora de origem para usuário baseado em nuvem no locatário.**  
O usuário baseado em nuvem no Azure AD não deve ter uma âncora de origem. Nesse caso, não há suporte para atualizar âncora de origem. É necessária a correção manual no local. 

## <a name="faq"></a>Perguntas Frequentes
**P.** O que acontece se a execução do **Apply Fix** falhar?  
**A.** Se a execução falhar, é possível que o Azure AD Connect esteja executando um erro de exportação. Atualize a página do portal e tente novamente após a próxima sincronização. O ciclo de sincronização padrão é de 30 minutos. 


**P.** E se o **objeto existente** deve ser o objeto a ser excluído?  
**A.** Se o **objeto existente** deve ser excluído, o processo não envolve uma mudança de **Âncora de Origem**. Normalmente, você pode corrigi-lo no Active Directory local. 


**P.** Qual permissão o usuário precisa para aplicar a correção?  
**A.** **Global Admin** ou **Contributor** das configurações do RBAC, tem permissão para acessar o processo de diagnóstico e solução de problemas.


**P.** Preciso configurar o Azure AD Connect ou atualizar o agente de integridade do Azure AD Connect para esse recurso?  
**A.** Não, o processo de diagnóstico é um recurso completo baseado em nuvem.


**P.** Se o objeto existente for apagado, o processo de diagnóstico tornará o objeto ativo novamente?  
**A.** Não, a correção não atualizará atributos de objetos diferentes de **Âncora de origem**.
