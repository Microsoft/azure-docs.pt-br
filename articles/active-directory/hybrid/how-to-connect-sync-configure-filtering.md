---
title: 'Configurar a sincronização do Azure AD Connect: configurar a filtragem | Microsoft Docs'
description: Explica como configurar a filtragem no Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 595cf2c1dbc105634d33b426c67e5123b9751e6e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996535"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Sincronização do Azure AD Connect: configurar a filtragem
Com a filtragem, você pode controlar quais objetos do seu diretório local devem aparecer no Azure Active Directory (Azure AD). A configuração padrão obtém todos os objetos em todos os domínios nas florestas configuradas. Em geral, essa é a configuração recomendada. Os usuários que usam cargas de trabalho Microsoft 365, como o Exchange Online e o Skype for Business, se beneficiam de uma lista de endereços global completa para que possam enviar emails e chamar todos. Com a configuração padrão, eles teriam a mesma experiência de uma implementação local do Exchange ou do Lync.

Em alguns casos, é necessário fazer alterações na configuração padrão. Estes são alguns exemplos:

* Você planeja usar a [topologia de vários diretórios do Azure AD](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Então, você precisa aplicar um filtro para controlar os objetos que devem ser sincronizados com um determinado diretório do Azure AD.
* Você executa um piloto para o Azure ou Microsoft 365 e deseja apenas um subconjunto de usuários no Azure AD. Em um piloto pequeno, não é importante ter uma Lista de Endereços Global completa para demonstrar essa funcionalidade.
* Você tem muitas contas de serviço e outras contas não pessoais que não deseja adicionar ao Azure AD.
* Por motivos de conformidade, não exclua contas de usuário locais. Você só pode desabilitá-las. Mas no Azure AD, você deseja ter apenas as contas ativas.

Este artigo mostra como configurar os diferentes métodos de filtragem.

> [!IMPORTANT]
> A Microsoft não oferece suporte à modificação ou à operação da sincronização do Azure AD Connect fora das ações formalmente documentadas. Qualquer uma dessas ações pode resultar em um estado inconsistente ou sem suporte de Azure AD Connect sincronização. Como resultado, a Microsoft não pode fornecer suporte técnico para essas implantações.

## <a name="basics-and-important-notes"></a>Noções básicas e observações importantes
No Azure AD Connect Sync, você pode habilitar a filtragem a qualquer momento. Se você já tiver começado com uma configuração padrão de sincronização de diretório e então configurou a filtragem, os objetos que são filtrados não são mais sincronizados ao AD do Azure. Devido a essa alteração, quaisquer objetos no Azure AD que foram anteriormente sincronizados, mas filtrados depois, serão excluídos do Azure AD.

Antes de começar a fazer alterações na filtragem, [desabilite a tarefa agendada](#disable-the-scheduled-task) para não fazer acidentalmente alterações de exportação que ainda não tenham sido verificadas como corretas.

Como a filtragem pode remover muitos objetos ao mesmo tempo, certifique-se de que os novos filtros estejam corretos antes de iniciar a exportação de todas as alterações para o Azure AD. Depois de concluir as etapas de configuração, recomendamos que você siga as [etapas de verificação](#apply-and-verify-changes) antes de fazer alterações e de exportar para o Azure AD.

Para que você não exclua muitos objetos por acidente, o recurso “[impedir exclusões acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md)” está ativado por padrão. Se você excluir muitos objetos devido à filtragem (500, por padrão), precisará seguir as etapas deste artigo para permitir que as exclusões passem para o Azure AD.

Se você usar uma build anterior à de novembro de 2015([1.0.9125](reference-connect-version-history.md)), alterar a configuração de filtro e usar a sincronização de senha, precisará disparar uma sincronização completa de todas as senhas depois de concluir a configuração. Para obter as etapas para disparar uma sincronização de senhas completa, consulte [Disparar uma sincronização completa de todas as senhas](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Se você estiver usando a versão 1.0.9125 ou uma versão posterior, a ação da **sincronização completa** normal, também avalia se as senhas precisam ser sincronizadas e esta etapa adicional não será mais necessária.

Se objetos de **usuário** tiverem sido excluídos acidentalmente do Azure AD devido a um erro de filtragem, você pode recriar os objetos de usuário no Azure AD, removendo as configurações de filtragem. Em seguida, você pode sincronizar os diretórios novamente. Essa ação restaura os usuários da lixeira no Azure AD. Contudo, não é possível cancelar a exclusão de outros tipos de objeto. Por exemplo, se você excluir acidentalmente um grupo de segurança usado para criar a ACL de um recurso, o grupo e suas ACLs não poderão ser recuperados.

O Azure AD Connect só exclui os objetos que já considerou como parte do escopo. Se houver objetos no Azure AD criados por outro mecanismo de sincronização e eles não estiverem no escopo, adicionar filtros não os removerá. Por exemplo, se você começar com um servidor DirSync, que criou uma cópia completa do diretório inteiro no Azure AD, e instalar um novo servidor de sincronização do Azure AD Connect em paralelo com a filtragem habilitada desde o início, o Azure AD Connect não removerá os objetos adicionais criados pelo DirSync.

As configurações de filtragem serão mantidas quando você instalar ou atualizar para uma versão mais recente do Azure AD Connect. É sempre uma prática recomendada verificar se a configuração não foi inadvertidamente alterada após uma atualização para uma versão mais recente, antes de executar o primeiro ciclo de sincronização.

Se você tiver mais de uma floresta, aplique as configurações de filtragem descritas neste tópico a todas as florestas (supondo que você queira a mesma configuração para todas elas).

### <a name="disable-the-scheduled-task"></a>Desabilitar a tarefa agendada
Para desabilitar o agendador interno, dispara um ciclo de sincronização a cada 30 minutos, siga estas etapas:

1. Vá até um prompt do PowerShell.
2. Execute `Set-ADSyncScheduler -SyncCycleEnabled $False` para desabilitar o agendador.
3. Faça as alterações documentadas neste artigo.
4. Execute `Set-ADSyncScheduler -SyncCycleEnabled $True` para habilitar o agendador novamente.

**Se você usa um build do Azure AD Connect anterior ao 1.1.105.0**  
Para desabilitar a tarefa agendada que dispara um ciclo de sincronização a cada três horas, siga estas etapas:

1. Inicie **Agendador de tarefas** no menu **Iniciar** .
2. Diretamente na **Biblioteca do Agendador de Tarefas**, localize a tarefa chamada **Agendador de Sincronização do Azure AD**, clique com o botão direito do mouse e selecione **Desabilitar**.  
   ![Agendador de Tarefas](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Agora você pode fazer alterações de configuração e executar o mecanismo de sincronização manualmente do console do **Synchronization Service Manager** .

Depois de concluir todas as alterações de filtragem, não se esqueça de voltar e **Habilitar** a tarefa novamente.

## <a name="filtering-options"></a>Opções de filtragem
Você pode aplicar os seguintes tipos de configuração de filtragem à Ferramenta de Sincronização de Diretório:

* [**Baseada em grupo**](#group-based-filtering): a filtragem baseada em um único grupo só pode ser configurada na instalação inicial usando o assistente de instalação.
* [**Baseada em domínio**](#domain-based-filtering): essa opção permite que você selecione quais domínios serão sincronizados com o Azure AD. Você também pode adicionar e remover domínios da configuração do mecanismo de sincronização quando fizer alterações na infraestrutura local, depois de instalar da sincronização do Azure AD Connect.
* [**Baseada em unidade organizacional (OU)**](#organizational-unitbased-filtering): essa opção permite que você selecione quais UOs serão sincronizadas com o Azure AD. Essa opção é para todos os tipos de objeto em UOs selecionadas.
* [**Baseada em atributo**](#attribute-based-filtering): essa opção permite que você filtre objetos com base nos valores de atributos nos objetos. Você também pode ter filtros diferentes para tipos de objeto diferentes.

Você pode usar várias opções de filtragem ao mesmo tempo. Por exemplo, você pode usar a filtragem baseada em unidade organizacional para incluir apenas os objetos em uma unidade organizacional. Ao mesmo tempo, você pode usar a filtragem baseada em atributo para filtrar ainda mais os objetos. Quando você usa vários métodos de filtragem, os filtros usam um “E” lógico entre eles.

## <a name="domain-based-filtering"></a>Filtragem baseada em domínio
Esta seção fornece as etapas para configurar o filtro de domínio. Se você adicionar ou remover domínios na floresta depois de ter instalado o Azure AD Connect, também será necessário atualizar a configuração de filtragem.

A melhor maneira de alterar a filtragem baseada em domínio é executando o assistente de instalação para alterar a [filtragem de domínio e UO](how-to-connect-install-custom.md#domain-and-ou-filtering). O assistente de instalação automatiza todas as tarefas documentadas neste tópico.

Você só deve seguir estas etapas se, por algum motivo, não conseguir executar o assistente de instalação.

A configuração de filtragem baseada em domínio consiste nestas etapas:

1. Selecione os domínios que você deseja incluir na sincronização.
2. Para cada domínio adicionado e removido, ajuste os perfis de execução.
3. [Aplicar e verificar as alterações](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Selecionar os domínios a serem sincronizados
Há duas maneiras de selecionar os domínios a serem sincronizados:
    - Usando o serviço de sincronização
    - Usando o assistente de Azure AD Connect.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>Selecione os domínios a serem sincronizados usando o serviço de sincronização
Para definir o filtro de domínio, siga estas etapas:

1. Entre no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja membro do grupo de segurança **ADSyncAdmins** .
2. Inicie o **serviço de sincronização** no menu **Iniciar** .
3. Selecione **Conectores** e, na lista **Conectores**, selecione o conector com o tipo **Active Directory Domain Services**. Em **Ações**, selecione **Propriedades**.  
   ![Propriedades do conector](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Clique em **Configurar Partições de Diretório**.
5. Na lista **Selecionar partições de diretório**, marque ou desmarque as caixas de seleção dos domínios conforme necessário. Verifique se apenas as partições que você deseja sincronizar estão selecionadas.  
   ![Captura de tela que mostra as partições de diretório na janela "Propriedades".](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Se você tiver alterado a infraestrutura local do Active Drectory e tiver adicionado ou removido domínios da floresta, clique no botão **Atualizar** para obter uma lista atualizada. Ao atualizar, você precisará fornecer as credenciais. Forneça todas as credenciais com o acesso de leitura para o Windows Server Active Directory. Ele não precisa ser o usuário previamente preenchido na caixa de diálogo.  
   ![Atualização necessária](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Quando terminar, clique em **OK** para fechar a caixa de diálogo **Propriedades**. Se você tiver removido domínios da floresta, será exibida uma mensagem informando que um domínio foi removido e que a configuração será limpa.
7. Prossiga para ajustar os perfis de execução.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Selecione os domínios a serem sincronizados usando o assistente de Azure AD Connect
Para definir o filtro de domínio, siga estas etapas:

1.  Iniciar o assistente de Azure AD Connect
2.  Clique em **Configurar**.
3.  Selecione **Personalizar opções de sincronização** e clique em **Avançar**.
4.  Insira suas credenciais de AD do Azure
5.  Na tela **diretórios conectados** , clique em **Avançar**.
6.  Na **página filtragem de domínio e UO,** clique em **Atualizar**.  Novos domínios agora serão exibidos e os domínios excluídos desaparecerão.
   ![Partições](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>Atualizar perfis de execução
Se você tiver atualizado o filtro de domínio, também precisará atualizar os perfis de execução.

1. Na lista **Conectores** , verifique se o conector que você alterou na etapa anterior está selecionado. Em **Ações**, selecione **Configurar Perfis de Execução**.  
   ![Perfis de execução do conector 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Localize e identifique os seguintes perfis:
    * Importação completa
    * sincronização total
    * Importação de delta
    * Sincronização de delta
    * Exportação
3. Para cada perfil, ajuste os domínios **adicionados** e **removidos**.
    1. Para cada um dos cinco perfis, execute as seguintes etapas para cada domínio **adicionado**:
        1. Selecione o perfil de execução e clique em **Nova Etapa**.
        2. Na página **Configurar Etapa**, no menu suspenso **Tipo**, selecione o tipo de etapa com o mesmo nome do perfil que você está configurando. Em seguida, clique em **Próximo**.  
        ![Perfis de execução do conector 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. Na página **Configuração do Conector**, no menu suspenso **Partição**, selecione o nome do domínio que você adicionou ao filtro de domínio.  
        ![Perfis de execução do conector 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Para fechar a caixa de diálogo **Configurar Perfil de Execução**, clique em **Finalizar**.
    2. Para cada um dos cinco perfis, execute as seguintes etapas para cada domínio **removido**:
        1. Selecione o perfil de execução.
        2. Se o **Valor** do atributo **Partition** for um GUID, selecione a etapa de execução e clique em **Excluir Etapa**.  
        ![Perfis de execução do conector 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Verifique se a alteração. Cada domínio que você deseja sincronizar deve estar listado como uma etapa em cada perfil de execução.
4. Para fechar o diálogo **Configurar Perfis de Execução**, clique em **OK**.
5.  Para concluir a configuração, você precisa executar uma **importação completa** e uma **sincronização Delta**. Continue lendo a seção [aplicar e verificar as alterações](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtragem baseada em unidade organizacional
A melhor maneira de alterar a filtragem baseada em unidade organizacional é executar o assistente de instalação e alterar a [filtragem de domínio e UO](how-to-connect-install-custom.md#domain-and-ou-filtering). O assistente de instalação automatiza todas as tarefas documentadas neste tópico.

Você só deve seguir estas etapas se, por algum motivo, não conseguir executar o assistente de instalação.

Para configurar a filtragem baseada em unidade organizacional, execute as seguintes etapas:

1. Entre no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja membro do grupo de segurança **ADSyncAdmins** .
2. Inicie o **serviço de sincronização** no menu **Iniciar** .
3. Selecione **Conectores** e, na lista **Conectores**, selecione o conector com o tipo **Active Directory Domain Services**. Em **Ações**, selecione **Propriedades**.  
   ![Propriedades do conector](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Clique em **Configurar Partições de Diretório**, selecione o domínio que você deseja configurar e clique em **Contêineres**.
5. Quando solicitado, forneça todas as credenciais com o acesso de leitura para o Active Directory local. Ele não precisa ser o usuário previamente preenchido na caixa de diálogo.
6. Na caixa de diálogo **Selecionar Contêineres**, desmarque as UOs que você não deseja sincronizar com o diretório de nuvem e clique em **OK**.  
   ![Unidades organizacionais na caixa de diálogo Selecionar Contêineres](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * O contêiner **Computadores** deve ser selecionado para que os computadores com Windows 10 sejam sincronizados com êxito com o Azure AD. Se os computadores ingressados no domínio estiverem localizados em outras UOs, verifique se eles estão selecionados.
   * O contêiner **ForeignSecurityPrincipals** deverá ser selecionado se você tiver várias florestas com relações de confiança. Esse contêiner permite que a associação de grupo de segurança entre florestas seja resolvida.
   * A UO **RegisteredDevices** deverá ser selecionada caso você tenha habilitado o recurso de write-back do dispositivo. Se você usar outro recurso de write-back, como o write-back de grupo, verifique se esses locais estão selecionados.
   * Selecione qualquer outra UO, onde usuários, iNetOrgPersons, grupos, contatos e computadores estão localizados. Na figura, todas essas UOs estão localizadas na UO ManagedObjects.
   * Se você usar a filtragem baseada em grupo, a UO onde o grupo está localizado deverá ser incluída.
   * Observe que você pode configurar se as novas UOs adicionadas após a conclusão da configuração da filtragem deverão ser sincronizadas ou não. Confira a próxima seção para saber mais detalhes.
7. Quando terminar, clique em **OK** para fechar a caixa de diálogo **Propriedades**.
8. Para concluir a configuração, você precisa executar uma **importação completa** e uma **sincronização Delta**. Continue lendo a seção [aplicar e verificar as alterações](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Sincronizar novas UOs
As novas UOs criadas após a configuração da filtragem são sincronizadas por padrão. Esse estado é indicado por uma marca de seleção na caixa. Você também pode cancelar a seleção de alguns subunidades organizacionais. Para isso, clique na caixa até que ela se torne branca com uma marca de seleção azul (seu estado padrão). Em seguida, desmarque todas as subunidades organizacionais que você não deseja sincronizar.

Se todas as subunidades organizacionais forem sincronizadas, a caixa ficará branca com uma marca de seleção azul.  
![UO com todas as caixas selecionadas](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Se algumas subunidades organizacionais forem desmarcadas, a caixa ficará cinza com uma marca de seleção branca.  
![UO com algumas subunidades organizacionais desmarcadas](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

Com essa configuração, uma nova UO que foi criada em ManagedObjects será sincronizada.

O assistente de instalação do Azure AD Connect sempre cria essa configuração.

### <a name="dont-synchronize-new-ous"></a>Não sincronizar novas UOs
Você pode configurar o mecanismo de sincronização para não sincronizar novas UOs após a conclusão da configuração da filtragem. Esse estado é indicado na interface do usuário por uma caixa cinza sem marca de seleção. Para isso, clique na caixa até que ela se torne branca sem marca de seleção. Em seguida, escolha as subunidades organizacionais que você deseja sincronizar.

![UO com raiz desmarcada](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

Com essa configuração, uma nova UO que foi criada em ManagedObjects não será sincronizada.

## <a name="attribute-based-filtering"></a>Filtragem baseada em atributo
Verifique se você está usando a versão de novembro de 2015 ([1.0.9125](reference-connect-version-history.md)) ou uma versão posterior para que estas etapas funcionem.

> [!IMPORTANT]
>A Microsoft recomenda não alterar as regras padrão criadas pelo **Azure AD Connect**. Se você quiser modificar a regra, clone-a e desabilite a regra original. Faça alterações somente na regra clonada. Observe que ao fazer isso (desabilitar a regra original), você perderá as correções de bugs ou os recursos habilitados por meio dessa regra.

A filtragem baseada em atributo é a maneira mais flexível de filtrar objetos. Você pode usar o [provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning.md) para controlar quase todos os aspectos de quando um objeto deve ser sincronizado com o Azure AD.

Você pode aplicar a filtragem de [entrada](#inbound-filtering) do Active Directory para o metaverso e a filtragem de [saída](#outbound-filtering) do metaverso para o Azure AD. É recomendável que você aplique a filtragem de entrada porque ela é mais fácil de manter. Você só deve usar a filtragem de saída se ela for necessária para unir objetos de mais de uma floresta antes da avaliação.

### <a name="inbound-filtering"></a>Filtragem de entrada
A filtragem de entrada usa a configuração padrão, na qual objetos em direção ao Azure AD não devem ter o atributo metaverso cloudFiltered definido como um valor a ser sincronizado. Se o valor desse atributo for definido como **True**, o objeto não será sincronizado. Ele não deve ser definido como **False** por padrão. Para garantir que outras regras tenham a capacidade de contribuir com um valor, esse atributo só deverá ter os valores **True** ou **NULL** (ausente).

Na filtragem de entrada, você usa o **escopo** para determinar quais objetos devem ou não ser sincronizados. Aqui, você faz os ajustes para os requisitos da sua própria organização. O módulo do escopo tem um **grupo** e uma **cláusula** para determinar quando uma regra de sincronização deve estar no escopo. Um grupo contém uma ou muitas cláusulas. Há um “E” lógico entre várias cláusulas e um “OU” lógico entre vários grupos.

Vamos examinar um exemplo:   
![Uma captura de tela mostrando um exemplo de adição de filtros de escopo.](./media/how-to-connect-sync-configure-filtering/scope.png)  
Isso deve ser lido como **(departamento = TI) OU (departamento = Vendas E c = EUA)**.

Nos exemplos e nas etapas abaixo, usaremos o objeto de usuário como um exemplo, mas você poderá usar isso para todos os tipos de objeto.

Nos exemplos a seguir, o valor de precedência começa com 50. Isso pode ser qualquer número não usado, mas deve ser inferior a 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Filtragem negativa, “não sincronizar estes”
No exemplo abaixo, você filtra (e não sincroniza) todos os usuários em que **extensionAttribute15** tem o valor **NoSync**.

1. Entre no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja membro do grupo de segurança **ADSyncAdmins** .
2. Inicie o **Editor de regras de sincronização** no menu **Iniciar** .
3. Verifique se a opção **Entrada** está selecionada e clique em **Adicionar Nova Regra**.
4. Dê à regra um nome descritivo, como "*Entrada do AD – User DoNotSyncFilter*". Selecione a floresta correta, **Usuário** como o **Tipo de objeto do CS** e **Pessoa** como o **Tipo de objeto do MV**. Em **Tipo de Link**, selecione **Junção**. Em **Precedência**, digite um valor que não esteja sendo usado atualmente por outra regra de sincronização (por exemplo, 50). Em seguida, clique em **Avançar**.  
   ![Descrição da entrada 1](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. Em **Filtro de escopo**, clique em **Adicionar Grupo** e em **Adicionar Cláusula**. Em **Atributo**, selecione **ExtensionAttribute15**. Verifique se **Operador** está definido como **EQUAL** e digite o valor **NoSync** na caixa **Valor**. Clique em **Próximo**.  
   ![Escopo da entrada 2](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Deixe as regras de **Junção** vazias e clique em **Avançar**.
7. Clique em **Adicionar Transformação**, selecione **FlowType** como **Constante** e selecione **cloudFiltered** como o **Atributo de Destino**. Na caixa de texto **Origem**, digite **True**. Clique em **Adicionar** para salvar a regra.  
   ![Transformação da entrada 3](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. Para concluir a configuração, você precisa executar uma **sincronização completa**. Continue lendo a seção [aplicar e verificar as alterações](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Filtragem positiva: "sincronizar somente estas"
Expressar a filtragem positiva pode ser mais desafiador, já que você também precisa considerar os objetos que não são óbvios para a sincronização, como as salas de conferência. Você também irá substituir o filtro padrão na regra diretamente **Entrada do AD – Associação de Usuário**. Quando você criar seu filtro personalizado, não inclua os objetos críticos do sistema, objetos de replicação em conflito, caixas de correio especiais e contas de serviço para o Azure AD Connect.

A opção de filtragem positiva requer duas regras de sincronização. Você precisa de uma regra (ou várias) com o escopo correto dos objetos que serão sincronizados. Você também precisa de uma segunda regra de sincronização pega-tudo que filtra todos os objetos ainda não identificados como um objeto a ser sincronizado.

No exemplo a seguir, você só sincroniza os objetos de usuário quando o atributo de departamento tiver o valor **Vendas**.

1. Entre no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja membro do grupo de segurança **ADSyncAdmins** .
2. Inicie o **Editor de regras de sincronização** no menu **Iniciar** .
3. Verifique se a opção **Entrada** está selecionada e clique em **Adicionar Nova Regra**.
4. Dê à regra um nome descritivo, como "*Entrada do AD – Sincronização de Vendas de Usuário*". Selecione a floresta correta, **Usuário** como o **Tipo de objeto do CS** e **Pessoa** como o **Tipo de objeto do MV**. Em **Tipo de Link**, selecione **Junção**. Em **Precedência**, digite um valor que não esteja sendo usado atualmente por outra regra de sincronização (por exemplo, 51). Em seguida, clique em **Avançar**.  
   ![Descrição da entrada 4](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. Em **Filtro de escopo**, clique em **Adicionar Grupo** e em **Adicionar Cláusula**. Em **Atributo**, selecione **Departamento**. Verifique se Operador está definido como **EQUAL** e digite o valor **Vendas** na caixa **Valor**. Clique em **Próximo**.  
   ![Escopo da entrada 5](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Deixe as regras de **Junção** vazias e clique em **Avançar**.
7. Clique em **Adicionar Transformação**, selecione **Constant** como **FlowType** e selecione **cloudFiltered** como o **Atributo de Destino**. Na caixa **Origem** , digite **False**. Clique em **Adicionar** para salvar a regra.  
   ![Transformação da entrada 6](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Este é um caso especial em que você define cloudFiltered explicitamente como **False**.
8. Agora temos de criar a regra de sincronização que captura tudo. Dê à regra um nome descritivo, como "*Entrada do AD – Filtro Captura Tudo Usuário*". Selecione a floresta correta, **Usuário** como o **Tipo de objeto do CS** e **Pessoa** como o **Tipo de objeto do MV**. Em **Tipo de Link**, selecione **Junção**. Em **Precedência**, digite um valor que não esteja sendo usado atualmente por outra Regra de Sincronização (por exemplo, 99). Você selecionou um valor de precedência mais alto (menor precedência) do que para a regra de sincronização anterior. Mas você também deixou algum espaço para poder adicionar mais regras de sincronização de filtragem depois, quando quiser iniciar a sincronização de outros departamentos. Clique em **Próximo**.  
   ![Descrição da entrada 7](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Deixe **Filtro de escopo** vazio e clique em **Avançar**. Um filtro vazio indica que a regra deve ser aplicada a todos os objetos.
10. Deixe as regras de **Junção** vazias e clique em **Avançar**.
11. Clique em **Adicionar Transformação**, selecione **Constant** como **FlowType** e selecione **cloudFiltered** como o **Atributo de Destino**. Na caixa **Origem** , digite **True**. Clique em **Adicionar** para salvar a regra.  
    ![Transformação da entrada 3](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. Para concluir a configuração, você precisa executar uma **sincronização completa**. Continue lendo a seção [aplicar e verificar as alterações](#apply-and-verify-changes).

Se for necessário, você pode criar mais regras do primeiro tipo, para incluir outros objetos para sincronização.

### <a name="outbound-filtering"></a>Filtragem de saída
Em alguns casos, é necessário fazer a filtragem somente depois que os objetos já estiverem no metaverso. Por exemplo, pode ser necessário examinar o atributo de email na floresta de recursos e o atributo userPrincipalName na floresta de contas para determinar se um objeto deve ser sincronizado. Nesses casos, você cria a filtragem na regra de saída.

Neste exemplo, você altera a filtragem para que somente usuários com emails e o userPrincipalName terminados em @contoso.com sejam sincronizados:

1. Entre no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja membro do grupo de segurança **ADSyncAdmins** .
2. Inicie o **Editor de regras de sincronização** no menu **Iniciar** .
3. Em **Tipos de Regra**, clique em **Saída**.
4. Dependendo da versão do Connect que você usa, localize a regra chamada **out to Azure ad – User Join** ou **out to Azure ad-User Join SOAInAD** e clique em **Edit**.
5. No pop-up, responda **Sim** para criar uma cópia da regra.
6. Na página **Descrição**, altere **Precedência** para um valor não usado, por exemplo, 50.
7. Clique em **Filtro de escopo** na barra de navegação à esquerda e clique em **Adicionar cláusula**. Em **Atributo**, selecione **mail**. Em **Operador**, selecione **ENDSWITH**. Em **valor**, digite **\@ contoso.com** e clique em **Adicionar cláusula**. Em **Atributo**, selecione **userPrincipalName**. Em **Operador**, selecione **ENDSWITH**. Em **valor**, digite **\@ contoso.com**.
8. Clique em **Save** (Salvar).
9. Para concluir a configuração, você precisa executar uma **sincronização completa**. Continue lendo a seção [aplicar e verificar as alterações](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Aplicar e verificar as alterações
Depois de alterar as configurações, você precisa aplicá-las aos objetos já presentes no sistema. Os objetos que não estão presentes no mecanismo de sincronização talvez precisem ser processados (e o mecanismo de sincronização talvez precise ler o sistema de origem novamente para verificar seu conteúdo).

Se tiver alterado a configuração usando a filtragem de **domínio** ou de **unidade organizacional**, você precisará fazer uma **Importação completa** seguida pela **Sincronização Delta**.

Se tiver alterado a configuração usando a filtragem de **atributo**, você precisará fazer uma **Sincronização completa**.

Execute as seguintes etapas:

1. Inicie o **serviço de sincronização** no menu **Iniciar** .
2. Selecione **Conectores**. Na lista **Conectores**, selecione o conector cuja configuração você anteriormente. Em **Ações**, selecione **Executar**.  
   ![Execução do conector](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. Em **Perfis de execução**, selecione a operação mencionada na seção anterior. Se você precisar executar duas ações, execute a segunda após a conclusão da primeira. (A coluna **Estado** indica que o conector selecionado está **Ocioso**.)

Após a sincronização, todas as alterações serão preparadas para exportação. Antes de realmente fazer as alterações no Azure AD, convém verificar se todas essas alterações estão corretas.

1. Inicie um prompt de comando e vá para `%ProgramFiles%\Microsoft Azure AD Sync\bin`.
2. Execute `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   O nome do conector pode ser encontrado no Serviço de Sincronização. Ele tem um nome semelhante a "contoso.com – Azure AD" para o Azure AD.
3. Execute `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Agora você tem um arquivo em %temp% chamado export.csv que pode ser examinado no Microsoft Excel. Esse arquivo contém todas as alterações que estão prestes a ser exportadas.
5. Faça as alterações necessárias na configuração ou nos dados e execute essas etapas novamente (importar, sincronizar e verificar) até que você esteja satisfeito com as alterações que serão exportadas.

Quando estiver satisfeito, exporte as alterações para o Azure AD.

1. Selecione **Conectores**. Na lista **Conectores**, selecione o conector do Azure AD. Em **Ações**, selecione **Executar**.
2. Em **Perfis de execução**, selecione **Exportar**.
3. Se suas alterações na configuração excluírem vários objetos, você verá um erro na exportação quando o número for maior do que o limite configurado (por padrão, 500). Se você vir esse erro, será necessário desabilitar temporariamente o recurso “[impedir exclusões acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md)”.

Agora é hora de habilitar o agendador novamente.

1. Inicie **Agendador de tarefas** no menu **Iniciar** .
2. Diretamente na **Biblioteca do Agendador de Tarefas**, localize a tarefa chamada **Agendador de Sincronização do Azure AD**, clique com o botão direito do mouse e selecione **Habilitar**.

## <a name="group-based-filtering"></a>Filtragem baseada em grupo
Você pode configurar a filtragem baseada em grupo na primeira vez que instalar o Azure AD Connect usando a [instalação personalizada](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Ela é destinada uma implantação piloto para sincronizar um pequeno conjunto de objetos. Se você desabilitar a filtragem baseada em grupo, ela não poderá ser habilitada novamente. *Não há suporte* para o uso da filtragem baseada em grupo em uma configuração personalizada. Esse recurso só pode ser configurado com o assistente de instalação. Depois de concluir o piloto, você deve usar uma das outras opções de filtragem descritas neste tópico. Ao usar a filtragem baseada em UO em conjunto com a filtragem baseada em grupos, as UOs em que o grupo e seus membros estão localizados devem ser incluídas.

Durante a sincronização de várias florestas do AD, você pode configurar a filtragem baseada em grupo com a especificação de um grupo diferente para cada conector do AD. Se você deseja sincronizar um usuário em uma floresta do AD e o mesmo usuário tiver um ou mais objetos correspondentes em outras florestas do AD, você deverá garantir que o objeto de usuário e todos os seus objetos correspondentes estejam dentro do escopo filtragem baseada no grupo. Por exemplo:

* Você tem um usuário em uma floresta que possui um objeto FSP (Entidade de Segurança Externa) correspondente em outra floresta. Ambos os objetos deverão estar dentro do escopo de filtragem baseada no grupo. Caso contrário, o usuário não será sincronizado com o Azure Active Directory.

* Você tem um usuário em uma floresta que tenha uma conta de recurso correspondente (por exemplo, caixa de correio vinculada) em outra floresta. Além disso, você configurou o Azure AD Connect para vincular o usuário com a conta do recurso. Ambos os objetos deverão estar dentro do escopo de filtragem baseada no grupo. Caso contrário, o usuário não será sincronizado com o Azure Active Directory.

* Você tem um usuário em uma floresta que possui um contato correspondente em outra floresta. Além disso, você configurou o Azure AD Connect para vincular o usuário com o contato de correio. Ambos os objetos deverão estar dentro do escopo de filtragem baseada no grupo. Caso contrário, o usuário não será sincronizado com o Azure Active Directory.


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre a configuração da [sincronização do Azure AD Connect](how-to-connect-sync-whatis.md).
- Saiba mais sobre a [integração de identidades locais com o Azure AD](whatis-hybrid-identity.md).
