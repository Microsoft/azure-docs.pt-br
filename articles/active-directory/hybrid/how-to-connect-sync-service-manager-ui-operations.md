---
title: Operações do Synchronization Service Manager do Azure AD Connect | Microsoft Docs
description: Entenda como usar a guia Operações no Synchronization Service Manager para o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64ab5901ad54e37fc05d7da757ce7f31d42fe3b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85357096"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Usando a guia Operações do Synchronization Service Manager

![Synchronization Service Manager](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

A guia Operações mostra os resultados das operações mais recentes. Essa guia é o segredo para entender e solucionar problemas.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Entender as informações visíveis na guia Operações
A metade superior mostra todas as execuções em ordem cronológica. Por padrão, as operações de log mantêm informações sobre os últimos sete dias, mas essa configuração pode ser alterada com o [agendador](how-to-connect-sync-feature-scheduler.md). Você deseja procurar qualquer execução que não mostre um status bem-sucedido. É possível alterar a classificação clicando nos cabeçalhos.

A coluna **Status** traz as informações mais importantes e mostra o problema mais grave de uma execução. Aqui está um resumo rápido dos status mais comuns em ordem de prioridade para investigação (em que * indica várias cadeias de caracteres de erro possíveis).

| Status | Comentário |
| --- | --- |
| parado-\* |Não foi possível concluir a execução. Por exemplo, se o sistema remoto está inoperante e não pode ser contatado. |
| stopped-error-limit |Há mais de 5.000 erros. A execução foi interrompida automaticamente devido ao grande número de erros. |
| completed-\*-errors |A execução foi concluída, mas há erros (menos de 5.000) que devem ser investigados. |
| completed-\*-warnings |A execução foi concluída, mas alguns dados não estão no estado esperado. Se houver erros, geralmente, essa mensagem indicará apenas um sintoma. Até que tenha resolvido os erros, você não deverá investigar os avisos. |
| sucesso |Nenhum problema. |

Quando você seleciona uma linha, a parte inferior é atualizada para mostrar os detalhes dessa execução. À extrema esquerda da parte inferior, talvez você veja uma lista indicando **Etapa nº**. Essa lista só será exibida se você tiver vários domínios na floresta, em que cada domínio é representado por uma etapa. O nome de domínio pode ser encontrado sob o título **Partição**. Em **Estatísticas de Sincronização**, é possível encontrar mais informações sobre o número de alterações que foram processadas. É possível clicar nos links para obter uma lista dos objetos alterados. Se você tiver objetos com erros, eles aparecerão em **erros de sincronização**.

Para obter mais informações, consulte [Solução de problemas de um objeto que não está sincronizando](tshoot-connect-object-not-syncing.md)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](how-to-connect-sync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
