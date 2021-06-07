---
title: Solucionar problemas de um objeto que não está sincronizando com o Azure Active Directory | Microsoft Docs
description: Solucionar problemas de um objeto que não está sincronizando com o Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a0c8a42edad08308095469039c048f8dd8552af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94413455"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Solucionar problemas de um objeto que não está sincronizando com o Azure Active Directory

Um objeto pode não estar sincronizando conforme o esperado com o Microsoft Azure Active Directory (Azure AD) por vários motivos. Se você recebeu uma mensagem de erro do Azure AD ou estiver vendo o erro no Azure AD Connect Health, consulte o artigo [Solucionando erros durante a sincronização](tshoot-connect-sync-errors.md). Mas se está solucionando um problema em que o objeto não está no Azure AD, este é o artigo ideal para você. Ele descreve como encontrar erros na sincronização do Azure AD Connect do componente local.

>[!IMPORTANT]
>Para a implantação do Azure AD Connect com a versão 1.1.749.0 ou posterior, use a [tarefa de solução de problemas](tshoot-connect-objectsync.md) no assistente para solucionar problemas de sincronização de objetos. 

## <a name="synchronization-process"></a>Processo de sincronização

Antes de investigar os problemas de sincronização, é preciso entender o processo de sincronização do Azure AD Connect:

  ![Diagrama do processo de sincronização do Azure AD Connect](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Espaço do conector, uma tabela em um banco de dados
* **MV:** Metaverso, uma tabela em um banco de dados

### <a name="synchronization-steps"></a>**Etapas de Sincronização**
O processo de sincronização envolve as seguintes etapas:

1. **Importar do AD:** Active Directory objetos são trazidos para o Active Directory CS.

2. **Importar do Azure AD:** Os objetos do Azure AD são trazidos para o CS do Azure AD.

3. **Sincronização:** As regras de sincronização de entrada e as regras de sincronização de saída são executadas na ordem do número de precedência, de menor para mais alto. Para exibir as regras de sincronização, vá ao Editor de Regras de Sincronização nos aplicativos da área de trabalho. As regras de sincronização de entrada trazem dados do CS para o MV. As regras de sincronização de saída movem os dados do MV para o CS.

4. **Exportar para o AD:** Após a sincronização, os objetos são exportados do Active Directory CS para Active Directory.

5. **Exportar para o Azure AD:** Após a sincronização, os objetos são exportados do Azure AD CS para o Azure AD.

## <a name="troubleshooting"></a>Solução de problemas

Para encontrar os erros, verifique diferentes lugares, na seguinte ordem:

1. Os [logs de operação](#operations), para encontrar erros identificados pelo mecanismo de sincronização durante a importação e a sincronização.
2. O [espaço conector](#connector-space-object-properties), para encontrar objetos ausentes e erros de sincronização.
3. O [metaverso](#metaverse-object-properties), para encontrar problemas relacionados aos dados.

Inicie o [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) antes de começar estas etapas.

## <a name="operations"></a>Operations
A guia **Operações** no Synchronization Service Manager é o local em que você deve iniciar a solução de problemas. Essa guia mostra os resultados das operações mais recentes. 

![Captura de tela do Synchronization Service Manager mostrando a guia de Operações selecionada](./media/tshoot-connect-object-not-syncing/operations.png)  

A metade superior da guia **Operações** mostra todas as execuções em ordem cronológica. Por padrão, as operações de log mantêm informações sobre os últimos sete dias, mas essa configuração pode ser alterada com o [agendador](how-to-connect-sync-feature-scheduler.md). Procure qualquer execução que não mostre o status de **bem-sucedida**. É possível alterar a classificação clicando nos cabeçalhos.

A coluna **Status** traz as informações mais importantes e mostra o problema mais grave de uma execução. Aqui está um resumo dos status mais comuns em ordem de prioridade para investigação (em que * indica várias cadeias de caracteres de erro possíveis).

| Status | Comentário |
| --- | --- |
| stopped- *  |Não foi possível concluir a execução. Isso pode acontecer, por exemplo, se o sistema remoto está inoperante e não pode ser contatado. |
| stopped-error-limit |Há mais de 5.000 erros. A execução foi interrompida automaticamente devido ao grande número de erros. |
| completed-\*-errors |A execução foi concluída, mas há erros (menos de 5.000) que devem ser investigados. |
| completed-\*-warnings |A execução foi concluída, mas alguns dados não estão no estado esperado. Se houver erros, geralmente, essa mensagem indicará apenas um sintoma. Não investigue os avisos até que tenha resolvido os erros. |
| sucesso |Nenhum problema. |

Quando você seleciona uma linha, a parte inferior da guia **Operações** é atualizada para mostrar os detalhes dessa execução. Na extremidade esquerda desta área, pode haver uma lista intitulada **Etapa Nº**. Essa lista só será exibida se você tiver vários domínios na floresta, e cada domínio for representado por uma etapa. O nome de domínio pode ser encontrado sob o título **Partição**. No cabeçalho **Estatísticas de Sincronização**, é possível encontrar mais informações sobre o número de alterações que foram processadas. Selecione os links para obter uma lista dos objetos alterados. Se houver objetos com erros, estes aparecerão no cabeçalho **Erros de Sincronização**.

### <a name="errors-on-the-operations-tab"></a>Erros na guia Operações
Quando houver erros, o Synchronization Service Manager mostrará o objeto com erro e o próprio erro como links que fornecerão mais informações.

![Captura de tela de erros no Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Comece selecionando a cadeia de caracteres de erro. (Na figura anterior, a cadeia de caracteres de erro é **Sync-regra-Error-function-Triggered**.) Primeiro, você verá uma visão geral do objeto. Para ver o erro real, selecione **Rastreamento de Pilha**. Esse rastreamento fornece informações de nível de depuração sobre o erro.

Clique com o botão direito do mouse na caixa **Informações da Pilha de Chamadas**, clique em **Selecionar tudo** e em **Copiar**. Copie a pilha e examine o erro em seu editor favorito, como o Bloco de Notas.

Se o erro for proveniente de **SyncRulesEngine**, as informações da pilha de chamadas irão primeiro listar todos os atributos no objeto. Role para baixo até ver o cabeçalho **InnerException =>**.  

  ![Captura de tela do Synchronization Service Manager mostrando as informações de erro no cabeçalho InnerException = >](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
A linha depois do cabeçalho mostra o erro. Na figura anterior, o erro é de uma regra de sincronização personalizada que a Fabrikam criou.

Se o erro não fornecer informações suficientes, será o momento de examinar os próprios dados. Selecione o link com o identificador de objeto e continue solucionando os problemas do [objeto importado do espaço conector](#cs-import).

## <a name="connector-space-object-properties"></a>Propriedades do objeto do espaço conector
Se a guia [**Operações**](#operations) não mostrar erros, siga o objeto do espaço conector do Active Directory para o metaverso para o Azure AD. Nesse caminho, você deverá encontrar onde está o problema.

### <a name="searching-for-an-object-in-the-cs"></a>Pesquisar um objeto no CS

No Synchronization Service Manager, clique em **Conectores**, selecione o Active Directory Connector e, depois, **Pesquisar Espaço Conector**.

Na caixa **Escopo**, selecione **RDN** quando desejar pesquisar o atributo CN ou **DN ou âncora** quando desejar pesquisar o atributo **distinguishedName**. Insira um valor e selecione **Pesquisar**. 
 
![Captura de tela de uma pesquisa do espaço conector](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Se você não encontrar o objeto que está procurando, talvez ele tenha sido filtrado com a [filtragem baseada em domínio](how-to-connect-sync-configure-filtering.md#domain-based-filtering) ou a [filtragem baseada em UO](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Para verificar se a filtragem está configurada conforme o esperado, leia [Azure ad Connect sincronização: configurar a filtragem](how-to-connect-sync-configure-filtering.md).

Você pode executar outra pesquisa útil selecionando o Conector do Azure AD. Na caixa **Escopo**, selecione **Importação Pendente** e, em seguida, marque a caixa de seleção **Adicionar**. Essa pesquisa fornece todos os objetos sincronizados no Azure AD que não podem ser associados a um objeto local.  

![Captura de tela de órfãos em uma pesquisa do espaço conector](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Esses objetos foram criados por outro mecanismo de sincronização ou por um mecanismo de sincronização com uma configuração de filtragem diferente. Esses objetos órfãos não são mais gerenciados. Examine essa lista e considere a remoção desses objetos usando os cmdlets do [Azure AD PowerShell](/previous-versions/azure/jj151815(v=azure.100)).

### <a name="cs-import"></a>Importação do CS
Quando você abre um objeto CS, há várias guias na parte superior. A guia **Importação** exibe os dados preparados após uma importação.  

![Captura de tela da janela Propriedades de Objeto do Espaço Conector, com a guia Importação selecionada](./media/tshoot-connect-object-not-syncing/csobject.png)    

A coluna **Valor Antigo** mostra o que está atualmente armazenado no Connect, e a coluna **Novo Valor**, o que foi recebido do sistema de origem e ainda não foi aplicado. Se houver um erro no objeto, as alterações não serão processadas.

A guia **Erro de Sincronização** só estará visível na janela **Propriedades de Objeto do Espaço Conector** se houver um problema com o objeto. Para saber mais, veja como [solucionar problemas de sincronização na guia **Operações**](#errors-on-the-operations-tab).

![Captura de tela da guia Erro de Sincronização na janela Propriedades de Objeto do Espaço Conector](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>Linhagem do CS
A guia **Linhagem** na janela **Propriedades de Objeto do Espaço Conector** mostra como o objeto do espaço conector está relacionado ao objeto do metaverso. Você pode ver quando o conector realizou a última importação de uma alteração do sistema conectado e quais regras foram aplicadas para popular dados no metaverso.  

![Captura de tela mostrando a guia Linhagem na janela Propriedades de Objeto do Espaço Conector](./media/tshoot-connect-object-not-syncing/cslineage.png)  

Na figura anterior, a coluna **Ação** mostra uma regra de sincronização de entrada com a ação **Provisionar**. Isso indica que, desde que esse objeto do espaço do conector esteja presente, o objeto do metaverso permanece. Se, em vez disso, a lista de regras de sincronização mostrar uma regra de sincronização de saída com uma ação **Provisionar**, esse objeto será excluído quando o objeto do metaverso for excluído.  

![Captura de tela mostrando a guia Linhagem na janela Propriedades de Objeto do Espaço Conector](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

Na imagem anterior, é possível ver que na coluna **PasswordSync** o espaço conector de entrada pode contribuir com alterações na senha, uma vez que uma regra de sincronização tem o valor **True**. Essa senha é enviada ao Azure AD por meio da regra de saída.

Na guia **Linhagem**, é possível acessar o metaverso clicando em [**Propriedades de Objeto do Metaverso**](#mv-attributes).

### <a name="preview"></a>Versão Prévia
No canto inferior esquerdo da janela **Propriedades de Objeto do Espaço Conector**, veja o botão **Visualização**. Selecione esse botão para abrir a página **Visualização**, onde será possível sincronizar um único objeto. Essa página será útil se você estiver solucionando problemas de algumas regras de sincronização personalizadas e desejar ver o efeito de uma alteração em um único objeto. Você pode selecionar uma **sincronização completa** ou uma **sincronização Delta**. Você também pode selecionar **gerar visualização**, que mantém apenas a alteração na memória. Ou selecionar **Visualização de Confirmação**, o que atualiza o metaverso e prepara todas as alterações para os espaços conectores de destino.  

![Captura de tela da página Visualização, com o item Iniciar Visualização selecionado](./media/tshoot-connect-object-not-syncing/preview.png)  

Na visualização, é possível inspecionar o objeto e verificar qual regra é aplicada a um fluxo de atributos específico.  

![Captura de tela da página Visualização mostrando o Fluxo de Atributo de Importação](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Log
Ao lado do botão **Visualização**, selecione o botão **Log** para abrir a página **Log**. Veja aqui o status e o histórico de sincronização de senha. Para saber mais, confira o artigo [Solucionar problemas de sincronização de senha com a sincronização do Azure AD Connect](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Propriedades do objeto do metaverso
Em geral, é melhor começar a pesquisa no espaço conector de origem do Active Directory. Mas também é possível começar a pesquisa no metaverso.

### <a name="searching-for-an-object-in-the-mv"></a>Pesquisar um objeto no MV
No Synchronization Service Manager, selecione **Pesquisa de Metaverso**, como na imagem a seguir. Crie uma consulta que você sabe que encontrará o usuário. Pesquise os atributos comuns, como **accountName** (**sAMAccountName**) e **userPrincipalName**. Para saber mais, confira a [Pesquisa de Metaverso no Synchronization Service Manager](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Captura de tela do Synchronization Service Manager mostrando a guia de Pesquisa de Metaverso selecionada](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

Na janela **Resultados da Pesquisa**, clique no objeto.

Se você não encontrou o objeto, ele ainda não chegou ao metaverso. Continue pesquisando o objeto no [espaço conector](#connector-space-object-properties) do Active Directory. Se você encontrar o objeto no espaço do conector do Active Directory, poderá haver um erro de sincronização impedindo o objeto de ir para o metaverso ou pode haver um filtro de escopo de regra de sincronização aplicado.

### <a name="object-not-found-in-the-mv"></a>Objeto não encontrado no MV
Se o objeto estiver no Active Directory CS, porém não estiver presente no MV, um filtro de escopo será aplicado. Para examinar o filtro de escopo vá ao menu de aplicativo da área de trabalho e selecione **Editor de Regras de Sincronização**. Filtre as regras aplicáveis ao objeto ajustando o filtro abaixo.

  ![Captura de tela do Editor de Regras de Sincronização, mostrando uma pesquisa de regras de sincronização de entrada](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Exiba cada regra na lista acima e verifique o **Filtro de escopo**. No filtro de escopo a seguir, se o valor **isCriticalSystemObject** for nulo ou FALSE ou estiver vazio, ele estará no escopo.

  ![Captura de tela de um filtro de escopo em uma pesquisa de regra de sincronização de entrada](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Vá à lista de atributos de [Importação do CS](#cs-import) e verifique qual filtro está impedindo o objeto de ir para o MV. A lista de atributos do **Espaço do Conector** mostrará somente atributos não nulos e não vazios. Por exemplo, se **isCriticalSystemObject** não estiver aparecendo na lista, o valor desse atributo será nulo ou vazio.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Objeto não encontrado no Azure AD CS
Se o objeto não estiver no espaço conector do Azure AD, mas estiver presente no MV, verifique o filtro de escopo das regras de saída do espaço conector correspondente e veja se o objeto foi filtrado porque os [atributos do MV](#mv-attributes) não atendem aos critérios.

Para ver o filtro de escopo de saída, selecione as regras aplicáveis ao objeto ajustando o filtro abaixo. Exiba cada regra e veja o valor do [atributo do MV](#mv-attributes) correspondente.

  ![Captura de tela de uma pesquisa das regras de sincronização de saída no Editor de Regras de Sincronização](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>Atributos do MV
Na guia **Atributos**, é possível ver os valores e qual Conector os forneceu.  

![Captura de tela da janela Propriedades de Objeto do Metaverso, com a guia Atributos selecionada](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Se um objeto não estiver sincronizando, verifique as seguintes perguntas sobre o estado do atributo no metaverso:
- O atributo **cloudFiltered** está presente e definido como **true**? Se estiver, ele foi filtrado de acordo com as etapas descritas em [filtragem baseada em atributo](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- O atributo **sourceAnchor** está presente? Caso contrário, você tem uma topologia de floresta de conta-recurso? Se um objeto for identificado como uma caixa de correio vinculada (o atributo **msExchRecipientTypeDetails** tem o valor **2**), o **sourceAnchor** será uma contribuição da floresta com uma conta do Active Directory habilitada. Verifique se a conta principal foi importada e sincronizada corretamente. A conta principal deve estar listada nos [conectores](#mv-connectors) do objeto.

### <a name="mv-connectors"></a>Conectores do MV
A guia **conectores** mostra todos os espaços conectores que têm uma representação do objeto. 
 
![Captura de tela da janela Propriedades de Objeto do Metaverso, com a guia Conectores selecionada](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

É necessário ter um conector para:

- Cada floresta do Active Directory em que o usuário está representado. Essa representação pode incluir **foreignSecurityPrincipals** e objetos de **contato** .
- Um conector no Azure AD.

Se você não tiver o conector do Azure AD, confira a seção [Atributos do MV](#mv-attributes) para verificar os critérios de provisionamento no Azure AD.

Na guia **Conectores**, também é possível acessar o [objeto do espaço conector](#connector-space-object-properties). Selecione uma linha e clique em **Propriedades**.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre a [ Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md).
- Saiba mais sobre [identidades híbridas](whatis-hybrid-identity.md).