---
title: "Solucionar problemas de um objeto que não está sincronizando com o Azure AD | Microsoft Docs"
description: "Solucione problemas de um objeto que não está sincronizando com o Azure AD."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 491a920ceeaac62dd37b1def3f02234056aebfb0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Solucionar problemas de um objeto que não está sincronizando com o Azure AD

Se um objeto não está sincronizando com o Azure AD como esperado, isso pode ser devido a vários motivos. Se você recebeu uma mensagem de erro do Azure AD ou está vendo o erro no Azure AD Connect Health, leia [Solucionar problemas de erros de exportação](active-directory-aadconnect-troubleshoot-sync-errors.md). Mas se estiver solucionando um problema em que o objeto não está no Azure AD, este tópico será ideal para você. Ele descreve como encontrar erros na sincronização do Azure AD Connect do componente local.

Para encontrar os erros, você observará alguns lugares diferentes na seguinte ordem:

1. Os [logs de operação](#operations), para encontrar erros identificados pelo mecanismo de sincronização durante a importação e sincronização.
2. O [espaço conector](#connector-space-object-properties), para encontrar objetos ausentes e erros de sincronização.
3. O [metaverso](#metaverse-object-properties), para encontrar problemas relacionados a dados.

Inicie o [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) antes de começar estas etapas.

## <a name="operations"></a>Operações
A guia de operações no Synchronization Service Manager é o local em que você deve iniciar a solução de problemas. A guia Operações mostra os resultados das operações mais recentes.  
![Synchronization Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/operations.png)  

A metade superior mostra todas as execuções em ordem crônica. Por padrão, as operações de log mantêm informações sobre os últimos sete dias, mas essa configuração pode ser alterada com o [agendador](active-directory-aadconnectsync-feature-scheduler.md). Você deseja procurar qualquer execução que não mostre um status bem-sucedido. É possível alterar a classificação clicando nos cabeçalhos.

A coluna **Status** traz as informações mais importantes e mostra o problema mais grave de uma execução. Aqui está um resumo rápido dos status mais comuns em ordem de prioridade para investigação (em que * indica várias cadeias de caracteres de erro possíveis).

| Status | Comentário |
| --- | --- |
| stopped-* |Não foi possível concluir a execução. Por exemplo, se o sistema remoto está inoperante e não pode ser contatado. |
| stopped-error-limit |Há mais de 5.000 erros. A execução foi interrompida automaticamente devido ao grande número de erros. |
| completed-\*-errors |A execução foi concluída, mas há erros (menos de 5.000) que devem ser investigados. |
| completed-\*-warnings |A execução foi concluída, mas alguns dados não estão no estado esperado. Se houver erros, geralmente, essa mensagem indicará apenas um sintoma. Até que tenha resolvido os erros, você não deverá investigar os avisos. |
| sucesso |Nenhum problema. |

Quando você seleciona uma linha, a parte inferior é atualizada para mostrar os detalhes dessa execução. À extrema esquerda da parte inferior, talvez você veja uma lista indicando **Etapa nº**. Essa lista só será exibida se você tiver vários domínios na floresta, em que cada domínio é representado por uma etapa. O nome de domínio pode ser encontrado sob o título **Partição**. Em **Estatísticas de Sincronização**, é possível encontrar mais informações sobre o número de alterações que foram processadas. É possível clicar nos links para obter uma lista dos objetos alterados. Se você tiver objetos com erros, eles aparecerão em **erros de sincronização**.

### <a name="troubleshoot-errors-in-operations-tab"></a>Solucionar problemas de erros na guia Operações
![Synchronization Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorsync.png)  
Quando houver erros, o objeto com erro e o próprio erro serão exibidos como links que fornecerão mais informações.

Comece clicando na cadeia de caracteres de erro (**sync-rule-error-function-triggered** na imagem). Primeiro, você vê uma visão geral do objeto. Para ver o erro real, clique no botão **Rastreamento de Pilha**. Esse rastreamento fornece informações de nível de depuração sobre o erro.

Clique com o botão direito do mouse na caixa **informações da pilha de chamadas**, escolha **selecionar tudo** e **copiar**. Em seguida, é possível copiar a pilha e examinar o erro em seu editor favorito, como o Bloco de Notas.

* Se o erro for proveniente de **SyncRulesEngine**, as informações da pilha de chamadas primeiro terão uma lista de todos os atributos no objeto. Role para baixo até ver o cabeçalho **InnerException =>**.  
  ![Synchronization Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorinnerexception.png)  
  A linha posterior mostra o erro. Na figura acima, o erro é proveniente de uma Regra de Sincronização personalizada criada pela Fabrikam.

Se o erro em si não fornecer informações suficientes, será o momento de examinar os próprios dados. É possível clicar no link com o identificador de objeto e continuar solucionando problemas do [objeto importado do espaço conector](#cs-import).

## <a name="connector-space-object-properties"></a>Propriedades do objeto do espaço conector
Se você não encontrar nenhum erro na guia [operações](#operations), a próxima etapa será seguir o objeto do espaço conector do Active Directory, para o metaverso e para o Azure AD. Nesse caminho, você deverá encontrar onde está o problema.

### <a name="search-for-an-object-in-the-cs"></a>Pesquisar um objeto no CS

No **Synchronization Service Manager**, clique em **Conectores**, selecione o Active Directory Connector e **Pesquisar Espaço Conector**.

Em **Escopo**, selecione **RDN** (quando desejar pesquisar o atributo CN) ou **DN ou âncora** (quando desejar pesquisar o atributo distinguishedName). Insira um valor e clique em **Pesquisar**.  
![Pesquisa do Espaço Conector](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearch.png)  

Se você não encontrar o objeto que está procurando, talvez ele tenha sido filtrado com a [filtragem baseada em domínio](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) ou a [filtragem baseada em UO](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering). Leia o tópico [Configurar a filtragem](active-directory-aadconnectsync-configure-filtering.md) para verificar se a filtragem está configurada como esperado.

Outra pesquisa útil é selecionar o Conector do Azure AD, em **Escopo**, selecionar **Importação Pendente** e marcar a caixa de seleção **Adicionar**. Essa pesquisa fornece todos os objetos sincronizados no Azure AD que não podem ser associados a um objeto local.  
![Órfão de pesquisa do Espaço Conector](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearchorphan.png)  
Esses objetos foram criados por outro mecanismo de sincronização ou por um mecanismo de sincronização com outra configuração de filtragem. Essa exibição é uma lista de objetos **órfãos** que não são mais gerenciados. Examine essa lista e considere a remoção desses objetos usando os cmdlets do [PowerShell do Azure AD](http://aka.ms/aadposh).

### <a name="cs-import"></a>Importação do CS
Quando você abre um objeto CS, há várias guias na parte superior. A guia **Importação** exibe os dados preparados após uma importação.  
![Objeto do CS](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/csobject.png)    
O **Valor Antigo** mostra o que está atualmente armazenado no Connect e o **Novo Valor**, o que foi recebido do sistema de origem e ainda não foi aplicado. Se houver um erro no objeto, as alterações não serão processadas.

**Erro**  
![Objeto do CS](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssyncerror.png)  
A guia **Erro de Sincronização** só estará visível se houver um problema com o objeto. Para obter mais informações, consulte [Solucionar problemas de erros de sincronização](#troubleshoot-errors-in-operations-tab).

### <a name="cs-lineage"></a>Linhagem do CS
A guia de linhagem mostra como o objeto do espaço conector está relacionado ao objeto de metaverso. Você pode ver quando o Conector realizou a última importação de uma alteração do sistema conectado e quais regras foram aplicadas para popular dados no metaverso.  
![Linhagem do CS](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineage.png)  
Na coluna **Ação**, você pode ver que há uma regra de sincronização de **Entrada** com a ação **Provisionar**. Isso indica que, desde que esse objeto do espaço do conector esteja presente, o objeto do metaverso permanece. Se, em vez disso, a lista de regras de sincronização mostrar uma regra de sincronização com direção de **Saída** e **Provisionar**, isso indicará que o objeto será excluído quando o objeto de metaverso for excluído.  
![Synchronization Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineageout.png)  
Você também pode ver na coluna **PasswordSync** que o espaço conector de entrada pode contribuir com alterações para a senha, uma vez que uma regra de sincronização tem o valor **True**. Essa senha é então enviada ao Azure AD por meio da regra de saída.

Na guia de linhagem, é possível acessar o metaverso clicando em [Propriedades de Objeto do Metaverso](#mv-attributes).

Na parte inferior de todas as guias, há dois botões: **Visualização** e **Log**.

### <a name="preview"></a>Visualização
A página de visualização é usada para sincronizar um único objeto. Isso será útil se você estiver solucionando problemas de algumas regras de sincronização personalizadas e desejar ver o efeito de uma alteração em um único objeto. É possível selecionar entre **Sincronização completa** e **Sincronização delta**. Também é possível selecionar entre **Gerar Visualização**, que mantém apenas a alteração na memória e **Confirmar Visualização**, que atualiza o metaverso e prepara todas as alterações para os espaços conectores de destino.  
![Synchronization Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/preview.png)  
É possível inspecionar o objeto e qual regra é aplicada a um fluxo de atributos específico.  
![Synchronization Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/previewresult.png)

### <a name="log"></a>Registro
A página de Registro é usada para verificar o status de sincronização da senha e o histórico. Para obter mais informações, consulte [Solucionar problemas de sincronização de senha](active-directory-aadconnectsync-troubleshoot-password-synchronization.md).

## <a name="metaverse-object-properties"></a>Propriedades do objeto do metaverso
Em geral, é melhor começar a pesquisa no [espaço conector](#connector-space) de origem do Active Directory. Mas também é possível começar a pesquisa no metaverso.

### <a name="search-for-an-object-in-the-mv"></a>Pesquisar um objeto no MV
No **Synchronization Service Manager**, clique em **Pesquisa de Metaverso**. Crie uma consulta que você sabe que encontrará o usuário. É possível pesquisar atributos comuns, como accountName (sAMAccountName) e userPrincipalName. Para obter mais informações, consulte [Pesquisa de metaverso](active-directory-aadconnectsync-service-manager-ui-mvsearch.md).
![Synchronization Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvsearch.png)  

Na janela **Resultados da Pesquisa**, clique no objeto.

Se você não encontrou o objeto, isso indica que ele ainda não chegou ao metaverso. Continue pesquisando o objeto no [espaço conector](#connector-space-object-properties) do Active Directory. Pode haver um erro de sincronização que está impedindo a chegada do objeto ao metaverso ou pode haver um filtro aplicado.

### <a name="mv-attributes"></a>Atributos do MV
Na guia Atributos, é possível ver os valores e qual Conector os forneceu.  
![Synchronization Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvobject.png)  

Se um objeto não está sincronizando, observe os seguintes atributos no metaverso:
- O atributo **cloudFiltered** está presente e está definido como **true**? Nesse caso, ele foi filtrado de acordo com as etapas descritas em [filtragem baseada em atributo](active-directory-aadconnectsync-configure-filtering.md#attribute-based-filtering).
- O atributo **sourceAnchor** está presente? Caso contrário, você tem uma topologia de floresta de conta-recurso? Se um objeto for identificado como uma caixa de correio vinculada (o atributo **msExchRecipientTypeDetails** tem o valor 2), sourceAnchor será uma contribuição da floresta com uma conta do Active Directory habilitada. Verifique se a conta mestra foi importada e sincronizada corretamente. A conta mestra deve estar listada nos [conectores](#mv-connectors) do objeto.

### <a name="mv-connectors"></a>Conectores do MV
A guia Conectores mostra todos os espaços conectores que contêm uma representação do objeto.  
![Synchronization Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvconnectors.png)  
É necessário ter um conector para:

- Cada floresta do Active Directory em que o usuário está representado. Essa representação pode incluir os objetos foreignSecurityPrincipals e Contact.
- Um conector no Azure AD.

Se você não tiver o conector do Azure AD, leia [Atributos do MV](#MV-attributes) para verificar os critérios de provisionamento no Azure AD.

Essa guia também permite navegar até o [objeto do espaço conector](#connector-space-object-properties). Selecione uma linha e clique em **Propriedades**.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).
