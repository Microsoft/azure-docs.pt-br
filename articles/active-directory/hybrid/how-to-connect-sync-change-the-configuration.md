---
title: 'Sincronização do Azure AD Connect: Fazer uma alteração na configuração padrão'
description: Explica como fazer uma alteração da configuração na sincronização do Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2044653673da10de59d5ff125da44ac1f89e22f9
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861843"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Sincronização do Azure AD Connect: Fazer uma alteração na configuração padrão
O objetivo deste artigo é orientá-lo sobre como fazer alterações na configuração padrão na sincronização do Azure Active Directory (Azure AD) Connect. Ele fornece etapas para alguns cenários comuns. Com esse conhecimento, você deve ser capaz de fazer alterações simples em sua própria configuração com base em suas próprias regras de negócios.

> [!WARNING]
> Se você fizer alterações nas regras de sincronização padrão que estão prontas para uso, essas alterações serão substituídas na próxima vez em que o Azure Active Directory Connect for atualizado, ocasionando resultados inesperados e provável sincronização indesejada.
>
> As regras padrão de sincronização prontas para uso têm uma impressão digital. Se você fizer uma alteração a essas regras, a impressão digital não corresponderá mais. Você pode ter problemas no futuro, quando tentar aplicar uma nova versão do Azure AD Connect. Faça alterações somente como é descrito neste artigo.

## <a name="synchronization-rules-editor"></a>Editor de Regras de Sincronização
O Editor de Regras de Sincronização é usado para exibir e alterar a configuração padrão. Você pode encontrá-lo no menu **Iniciar** no grupo **Azure Connect AD**.  
![Menu Iniciar com o Editor de Regras de Sincronização](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

Ao abrir o editor, você verá as regras padrão prontas para uso.

![Editor de Regras de Sincronização](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navegando no editor
Usando os menus suspensos na parte superior do editor, você pode localizar rapidamente uma regra específica. Por exemplo, se você quiser ver as regras onde o atributo proxyAddresses está incluído, pode alterar os menus suspensos para o seguinte:  
![Filtragem de SRE](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Para redefinir a filtragem e carregar uma nova configuração, pressione F5 no teclado.

Na parte superior direita fica o botão **Adicionar nova regra**. Use esse botão para criar sua própria regra personalizada.

Na parte inferior, há botões para atuar em uma regra de sincronização selecionada. **Editar** e **Excluir** fazem o que você espera. **Exportar** produz um script do PowerShell para recriar a regra de sincronização. Com esse procedimento, você pode mover uma regra de sincronização de um servidor para outro.

## <a name="create-your-first-custom-rule"></a>Criar sua primeira regra personalizada
As alterações mais comuns são nos fluxos do atributo. Os dados no diretório de origem podem não ser o mesmo como no Azure AD. No exemplo nesta seção, verifique se o nome fornecido de um usuário está sempre com o *formato apropriado*.

### <a name="disable-the-scheduler"></a>Desabilitar o agendador
O [Agendador](how-to-connect-sync-feature-scheduler.md) é executado a cada 30 minutos por padrão. Verifique se ele não está iniciando enquanto você está fazendo alterações e solucionando os problemas das novas regras. Para desabilitar temporariamente o agendador, inicie o PowerShell e execute `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Desabilitar o agendador](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Criar a regra
1. Clique em **Adicionar nova regra**.
2. Na página **Descrição**, insira o seguinte:  
   ![Filtragem das regras de entrada](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Name**: Dê um nome descritivo à regra.
   * **Descrição**: Forneça algum esclarecimento para que outras pessoas possam entender para que serve a regra.
   * **Sistema Conectado**: Esse é o sistema no qual o objeto pode ser encontrado. Neste caso, selecione o **Active Directory Connector**.
   * **Tipo de Objeto do Sistema Conectado/Metaverso**: Selecione **Usuário** e **Pessoa**, respectivamente.
   * **Tipo de Link**: Altere esse valor para **Junção**.
   * **Precedência**: Forneça um valor que seja exclusivo no sistema. Um valor numérico mais baixo indica uma precedência mais alta.
   * **Tag**: Deixe esse campo vazio. As regras integradas da Microsoft devem ter essa caixa preenchida com um valor.
3. Na página **Filtro de escopo**, insira **givenName ISNOTNULL**.  
   ![Filtrar escopo das regras de entrada](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Esta seção é usada para definir a quais objetos a regra deve ser aplicada. Se deixada em branco, a regra se aplicaria a todos os objetos de usuário. Contudo, isso incluiria as salas de conferência, contas de serviço e outros objetos de usuário que não são de pessoas.
4. Na página **Regras de associação**, deixe o campo vazio.
5. Na página **Transformações**, altere o **FlowType** para **Expression**. Para **Atributo de destino**, selecione **givenName**. E para **Fonte**, digite **PCase([givenName])** .
   ![Transformações das regras de entrada](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   O mecanismo de sincronização diferencia as letras maiúsculas das minúsculas no nome da função e no nome do atributo. Se você digitar algo errado, verá um aviso quando adicionar a regra. Você pode salvar e continuar, mas precisa reabrir e corrigir a regra.
6. Clique em **Adicionar** para salvar a regra.

A nova regra personalizada deve ficar visível com as outras regras de sincronização no sistema.

### <a name="verify-the-change"></a>Verificar a alteração
Com essa nova alteração, você deseja verificar se ela está funcionando conforme o esperado e não está gerando erros. Dependendo do número de objetos que você tem, há duas maneiras de realizar esta etapa:

- Executar uma sincronização completa em todos os objetos.
- Executar uma visualização e uma sincronização completa em um único objeto.

Abra o **Serviço de Sincronização** no menu **Iniciar**. As etapas nesta seção estão todas nessa ferramenta.

**Sincronização completa em todos os objetos**  

   1. Selecione **Conectores** na parte superior. Identifique o conector que você alterou na seção anterior (neste caso, os Active Directory Domain Services) e selecione-o. 
   2. Para **Ações**, selecione **Executar**.
   3. Selecione **Sincronização Completa** e selecione **OK**.
   ![Sincronização completa](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Os objetos estão atualizados no metaverso. Verifique suas alterações examinando o objeto no metaverso.

**Visualização e sincronização completa em um único objeto**  

   1. Selecione **Conectores** na parte superior. Identifique o conector que você alterou na seção anterior (neste caso, os Active Directory Domain Services) e selecione-o.
   2. Selecione **Pesquisar Espaço do Conector**. 
   3. Use o **Escopo** para localizar um objeto que você deseja usar para testar a alteração. Selecione o objeto e clique em **Visualizar**. 
   4. Na nova tela, selecione **Visualização de Confirmação**.  
   ![Commit preview](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   Agora, a alteração está confirmada no metaverso.

**Ver o objeto no metaverso**  

1. Escolha alguns objetos de exemplo para verificar se o valor é o esperado e se a regra foi aplicada. 
2. Selecione **Pesquisar Metaverso** na parte superior. Adicione qualquer filtro necessário para localizar os objetos relevantes. 
3. No resultado da pesquisa, abra um objeto. Veja os valores do atributo e verifique também na coluna **Regras de Sincronização** se a regra aplicada está conforme o esperado.  
![Pesquisa de metaverso](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Habilitar o agendador
Se tudo estiver como o esperado, você poderá habilitar o agendador novamente. No PowerShell, execute `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Outras alterações comuns no fluxo de atributos
A seção anterior descreveu como fazer alterações em um fluxo de atributos. Nesta seção, são fornecidos alguns exemplos adicionais. As etapas para criar a regra de sincronização estão abreviadas, mas você pode encontrar as etapas completas na seção anterior.

### <a name="use-an-attribute-other-than-the-default"></a>Usar outro atributo diferente do padrão
Nesse cenário do Fabrikam, há uma floresta na qual o alfabeto local é usado para o nome dado, sobrenome e nome de exibição. A representação de caracteres latinos desses atributos pode ser encontrada nos atributos de extensão. Para criar uma lista de endereços global no Azure AD e Microsoft 365, a organização deseja usar esses atributos em vez disso.

Com uma configuração padrão, um objeto da floresta local tem esta aparência:  
![Fluxo de atributos 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Para criar uma regra com outros fluxos de atributos, faça o seguinte:

1. Abra o **Editor de Regras de Sincronização** no menu **Iniciar**.
2. Com a **Entrada** ainda selecionada à esquerda, clique no botão **Adicionar nova regra**.
3. Dê à regra um nome e uma descrição. Selecione a instância local do Active Directory e os tipos de objetos relevantes. Em **Tipo de Link**, selecione **Junção**. Para **Precedência**, escolha um número que não seja usado por outra regra. As regras prontas para uso começam com 100, então, o valor 50 pode ser usado neste exemplo.
  ![Fluxo de atributos 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Deixe **Filtro de escopo** vazio. (Ou seja, deve ser aplicada a todos os objetos de usuário na floresta.)
5. Deixe **Regras de associação** vazio. (Ou seja, permita que a regra pronta para uso lide com quaisquer associações.)
6. Em **Transformações**, crie os seguintes fluxos:  
  ![Fluxo de atributos 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Clique em **Adicionar** para salvar a regra.
8. Vá para o **Synchronization Service Manager**. Em **Conectores**, selecione o conector ao qual você adicionou a regra. Selecione **Executar** e depois **Sincronização Completa**. Uma sincronização completa recalcula todos os objetos usando as regras atuais.

Este é o resultado para o mesmo objeto com essa regra personalizada:  
![Fluxo de atributos 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Comprimento de atributos
Atributos de cadeia de caracteres são indexáveis por padrão, e seu comprimento máximo é de 448 caracteres. Se você estiver trabalhando com atributos da cadeia de caracteres que podem conter mais, inclua o seguinte no fluxo de atributos:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Alterando o userPrincipalSuffix
O atributo userPrincipalName no Active Directory não é sempre conhecido pelos usuários e pode não ser adequado como a ID de logon. Com o assistente de instalação da sincronização do Azure AD Connect, você pode escolher um atributo diferente, por exemplo, *email*. Mas, em alguns casos, o atributo deve ser calculado.

Por exemplo, a empresa Contoso tem dois diretórios do AD do Azure, um para a produção e outro para o teste. Eles querem que os usuários em seu locatário de teste usem outro sufixo na ID de entrada:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

Nesta expressão, pegamos tudo à esquerda do primeiro @-sign (Word) e concatenamos com uma cadeia de caracteres fixa.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Converter um atributo de vários valores em um único valor
Alguns atributos no Active Directory são compostos de vários valores no esquema, mesmo que pareçam de valor único nos Usuários e computadores do Active Directory. Um exemplo é o atributo de descrição:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

Nesta expressão, caso o atributo tenha um valor, pegue o primeiro item (*Item*) no atributo, remova espaços à direita e à esquerda (*Trim*, cortar) e, em seguida, mantenha os primeiros 448 caracteres (*Left*, à esquerda) na cadeia de caracteres.

### <a name="do-not-flow-an-attribute"></a>Não faça um atributo fluir
Para obter informações sobre o cenário para esta seção, confira [Controlar o processo de fluxo de atributos](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Há duas maneiras de não fazer um atributo fluir. A primeira é usando o assistente de instalação para [remover os atributos selecionados](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Essa opção funciona se você nunca tiver sincronizado o atributo antes. No entanto, se você começou a sincronizar esse atributo e removê-lo mais tarde com esse recurso, o mecanismo de sincronização deixará de gerenciar o atributo e os valores existentes serão deixados no Azure AD.

Se você quiser remover o valor de um atributo e assegurar que ele não fluirá no futuro, será necessário criar uma regra personalizada.

Nesse cenário do Fabrikam, percebemos que alguns dos atributos que sincronizamos para a nuvem não deveriam estar lá. Queremos removê-los do AD do Azure.  
![Atributos de extensão ruins](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Crie uma nova regra de sincronização de entrada e preencha a descrição.
  ![Descrições](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Criar fluxos de atributos com **Expressão** para **FlowType** e com **AuthoritativeNull** para **Fonte**. O literal **AuthoritativeNull** indica que o valor deve ser vazio no metaverso mesmo se uma regra de sincronização de precedência inferior tentar preencher o valor.
  ![Transformação dos atributos de extensão](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Salve a regra de sincronização. Inicie o **Serviço de Sincronização**, localize o conector, selecione **Executar** e depois **Sincronização Completa**. Esta etapa recalculará todos os fluxos de atributo.
4. Verifique se as alterações pretendidas estão prestes a ser exportadas pesquisando o Espaço Conector.
  ![Exclusão em etapas](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Criar regras com o PowerShell
Usando o editor de regra de sincronização funciona bem quando você tem apenas algumas alterações a serem feitas. Se você precisar fazer muitas alterações, o PowerShell pode ser uma opção melhor. Alguns recursos avançados só estão disponíveis com o PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Obter o script do PowerShell para uma regra pronta para uso
Para ver o PowerShell script que criou uma regra de caixa de saída, selecione a regra no editor de regras de sincronização e clique em **Exportar**. Esta ação lhe dá o script do PowerShell que criou a regra.

### <a name="advanced-precedence"></a>Precedência avançada
As regras de sincronização prontas para uso começam com um valor de precedência 100. Se você tiver várias florestas e precisar fazer muitas alterações personalizadas, então as 99 regras de sincronização poderão não ser suficientes.

Você pode instruir o mecanismo de sincronização que deseja regras adicionais inseridas antes das regras prontas para uso. Para obter esse comportamento, siga estas etapas:

1. Marque a primeira regra de sincronização pronta para uso (**In from AD-User Join**) no editor de regras de sincronização e selecione **Exportar**. Copie o valor do Identificador SR.  
![PowerShell antes da alteração](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Crie nova regra de sincronização. Você pode usar o editor de regras de sincronização para criá-lo. Exporte a regra para um script do PowerShell.
3. Na propriedade **PrecedenceBefore**, insira o valor do Identificador da regra pronta para uso. Definir o **precedência** para **0**. Verifique se o atributo do Identificador é exclusivo e se você não está reutilizando um GUID de outra regra. Também verifique se a propriedade **ImmutableTag** não está definida. Essa propriedade deve ser definida apenas para uma regra pronta para uso.
4. Salve o script do PowerShell e executá-lo. O resultado é que a regra personalizada recebe o valor de precedência 100 e todas as outras regras prontas para uso são incrementadas.  
![PowerShell após a alteração](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

Você pode ter várias regras de sincronização personalizadas usando o mesmo valor **PrecedenceBefore** quando necessário.

## <a name="enable-synchronization-of-usertype"></a>Habilitar a sincronização de UserType
O Azure AD Connect oferece suporte para sincronização do atributo **UserType** para objetos **Usuário** nas versões 1.1.524.0 e posteriores. Mais especificamente, as seguintes alterações foram introduzidas:

- O esquema do tipo de objeto **Usuário** no Azure AD Connector é estendido para incluir o atributo UserType, que é do tipo cadeia de caracteres e é de valor único.
- O esquema do tipo de objeto **Pessoa** no metaverso é estendido para incluir o atributo UserType, que é do tipo cadeia de caracteres e é de valor único.

Por padrão, o atributo UserType não está habilitado para sincronização porque não há nenhum atributo UserType correspondente no Active Directory local. Você deve habilitar a sincronização manualmente. Antes de fazer isso, você deve anotar o seguinte comportamento imposto pelo Azure AD:

- O Azure AD aceita apenas dois valores para o atributo UserType: **Membro** e **Convidado**.
- Se o atributo UserType não estiver habilitado para sincronização no Azure AD Connect, os usuários do Azure AD, criados por meio da sincronização de diretório, terão o atributo UserType definido como **Membro**.
- Antes da versão 1.5.30.0, o Azure Active Directory não permitia que o atributo UserType em usuários existentes do Azure Active Directory fosse alterado pelo Azure AD Connect. Em versões mais antigas, ele pode ser definido apenas durante a criação dos usuários do Azure AD e [alterado por meio do PowerShell](/powershell/module/azuread/set-azureaduser).

Antes de habilitar a sincronização do atributo UserType, você deve decidir como o atributo será derivado do Active Directory local. A seguir, estão as abordagens mais comuns:

- Designar um atributo do AD local não utilizado (como extensionAttribute1) para ser usado como o atributo de origem. O atributo designado do AD local deve ser do tipo **cadeia de caracteres**, ser de valor único e conter o valor **Membro** ou **Convidado**. 

    Se você escolher essa abordagem, você deverá garantir que o atributo designado seja preenchido com o valor correto para todos os objetos de usuário existentes no Active Directory local que estejam sincronizados com o Azure AD antes de habilitar a sincronização do atributo UserType.

- Como alternativa, você pode derivar o valor do atributo UserType de outras propriedades. Por exemplo, você deseja sincronizar todos os usuários como **Convidados**, caso os respectivos atributos UserPrincipalName do AD local terminem com a parte do domínio <em>@partners.fabrikam123.org</em>. 

    Conforme mencionado anteriormente, versões mais antigas do Azure AD Connect não permitem que o atributo UserType em usuários existentes do Azure Active Directory seja alterado pelo Azure AD Connect. Portanto, você deve garantir que a lógica que você escolheu seja consistente com a maneira como o atributo UserType já está configurado para todos os usuários do Azure AD existentes em seu locatário.

As etapas para habilitar a sincronização do atributo UserType podem ser resumidas como:

1.  Desabilitar o agendador de sincronização e verificar se não há nenhuma sincronização em andamento.
2.  Adicionar o atributo de origem ao esquema do AD Connector local.
3.  Adicionar UserType ao esquema do Azure AD Connector.
4.  Criar uma regra de sincronização de entrada para fazer o valor de atributo fluir do Active Directory local.
5.  Criar uma regra de sincronização de saída para fazer o valor de atributo fluir para o Azure AD.
6.  Executar um ciclo completo de sincronização.
7.  Habilitar o agendador de sincronização.

>[!NOTE]
> O restante desta seção aborda essas etapas. Elas são descritas no contexto de uma implantação do Azure AD com topologia de floresta única e sem regras de sincronização personalizadas. Se você tiver uma topologia de várias florestas, regras de sincronização personalizadas configuradas ou um servidor de preparo, você precisará ajustar as etapas de acordo.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Etapa 1: Desabilitar o agendador de sincronização e verificar se não há nenhuma sincronização em andamento
Para evitar a exportação de alterações indesejadas ao Azure AD, garanta que não haja nenhuma sincronização enquanto você estiver atualizando as regras de sincronização. Para desabilitar o agendador de sincronização interno:

 1. Inicie uma sessão do PowerShell no servidor do Azure AD Connect.
 2. Desabilite a sincronização agendada executando o cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Inicie o Synchronization Service Manager acessando **Iniciar** > **Serviço de Sincronização**.
 4. Acesse a guia **Operações** e confirme se não há nenhuma operação cujo status seja *em andamento*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Etapa 2: Adicione o atributo de origem ao esquema do AD Connector local
Nem todos os atributos do Azure AD são importados para o Espaço do AD Connector local. Para adicionar o atributo de origem à lista de atributos importados:

 1. Acesse a guia **Conectores** no Synchronization Service Manager.
 2. Clique com o botão direito do mouse no AD Connector local e selecione **Propriedades**.
 3. Na caixa de diálogo pop-up, vá para a guia **Selecionar Atributos**.
 4. Verifique se o atributo de origem está selecionado na lista de atributos.
 5. Clique em **OK** para salvar.
![Adicionar atributo de origem ao esquema do Conector do AD local](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-attribute-to-the-azure-ad-connector-schema"></a>Etapa 3: Adicionar o atributo UserType ao esquema do Azure AD Connector
Por padrão, o atributo UserType não será importado para o espaço do Azure AD Connect. Para adicionar o atributo UserType à lista de atributos importados:

 1. Acesse a guia **Conectores** no Synchronization Service Manager.
 2. Clique com o botão direito do mouse em **Conector do Azure AD** e selecione **Propriedades**.
 3. Na caixa de diálogo pop-up, vá para a guia **Selecionar Atributos**.
 4. Certifique-se de que o atributo UserType esteja selecionado na lista de atributos.
 5. Clique em **OK** para salvar.

![Adicionar atributo de origem ao esquema do Azure AD Connector](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Etapa 4: Criar uma regra de sincronização de entrada para fazer o valor de atributo fluir do Active Directory local
A regra de sincronização de entrada permite que o valor do atributo flua do atributo de origem do Active Directory local para o metaverso:

1. Abra o Editor de Regras de Sincronização acessando **Iniciar** > **Editor de Regras de Sincronização**.
2. Defina o filtro de pesquisa **Direção** como **Entrada**.
3. Clique no botão **Adicionar nova regra** para criar uma nova regra de entrada.
4. Na guia **Descrição**, forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | --- | --- | --- |
    | Nome | *Fornecer um nome* | Por exemplo, *Entrada do AD – UserType Usuário* |
    | Descrição | *Fornecer uma descrição* |  |
    | Sistema Conectado | *Selecionar o AD connector local* |  |
    | Tipo de Objeto do Sistema Conectado | **Usuário** |  |
    | Tipo de Objeto de Metaverso | **Person** |  |
    | Tipo de link | **Join** |  |
    | Precedência | *Escolha um número entre 1 e 99* | 1 a 99 são reservados para regras de sincronização personalizadas. Não selecione um valor que seja usado por outra regra de sincronização. |

5. Acesse a guia **Filtro de escopo** e adicione um **único grupo de filtro de escopo** com a seguinte cláusula:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Usuário\_ |

    O filtro de escopo determina a quais objetos AD locais essa regra de sincronização de entrada é aplicada. Neste exemplo, usamos o mesmo filtro de escopo usado como regra de sincronização pronta para uso *Entrada do AD – Usuário Comum*, que impede a aplicação da regra de sincronização a objetos Usuário criados por meio do recurso de write-back de Usuário do Azure AD. Talvez seja necessário ajustar o filtro de escopo de acordo com sua implantação do Azure AD Connect.

6. Acesse a guia **Transformação** e implemente a regra de transformação desejada. Por exemplo, se você designou um atributo local AD não utilizado (como extensionAttribute1) como o atributo de origem para o UserType; você pode implementar um fluxo de atributos direto:

    | Tipo de fluxo | Atributo de destino | Fonte | Aplicar uma vez | Tipo de mesclagem |
    | --- | --- | --- | --- | --- |
    | Direto | UserType | extensionAttribute1 | Desmarcado | Atualizar |

    Em outro exemplo, você deseja derivar o valor do atributo UserType de outras propriedades. Por exemplo, você deseja sincronizar todos os usuários como Convidados, caso seu respectivo atributo UserPrincipalName local do AD termine com a parte do domínio <em>@partners.fabrikam123.org</em>. Você pode implementar uma expressão como esta:

    | Tipo de fluxo | Atributo de destino | Fonte | Aplicar uma vez | Tipo de mesclagem |
    | --- | --- | --- | --- | --- |
    | Expression | UserType | IIF(IsPresent([userPrincipalName]),IIF(CBool(InStr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0),"Member","Guest"),Error("UserPrincipalName não está presente para determinar UserType")) | Desmarcado | Atualizar |

7. Clique em **Adicionar** para criar a regra de entrada.

![Criar regra de sincronização de entrada](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Etapa 5: Criar uma regra de sincronização de saída para fazer o valor de atributo fluir para o Azure AD
A regra de sincronização de saída permite que o valor do atributo flua do metaverso para o atributo UserType no Microsoft Azure AD:

1. Acesse o Editor de Regras de Sincronização.
2. Defina o filtro de pesquisa **Direção** como **Saída**.
3. Clique no botão **Adicionar nova regra**.
4. Na guia **Descrição**, forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | ----- | ------ | --- |
    | Nome | *Fornecer um nome* | Por exemplo, *Saída para AAD – UserType Usuário* |
    | Descrição | *Fornecer uma descrição* ||
    | Sistema Conectado | *Selecione o AAD connector* ||
    | Tipo de Objeto do Sistema Conectado | **Usuário** ||
    | Tipo de Objeto de Metaverso | **Person** ||
    | Tipo de link | **Join** ||
    | Precedência | *Escolha um número entre 1 e 99* | 1 a 99 são reservados para regras de sincronização personalizadas. Não selecione um valor que seja usado por outra regra de sincronização. |

5. Acesse a guia **Filtro de escopo** e adicione um **único grupo de filtro de escopo** com as duas cláusulas a seguir:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Usuário |
    | cloudMastered | NOTEQUAL | True |

    O filtro de escopo determina a quais objetos do Azure AD essa regra de sincronização de saída é aplicada. Neste exemplo, usamos o mesmo filtro de escopo da regra de sincronização pronta para uso de *Saída para AD – identidade do usuário*. Impede que a regra de sincronização seja aplicada aos objetos de Usuário que não estão sincronizados do Active Directory local. Talvez seja necessário ajustar o filtro de escopo de acordo com sua implantação do Azure AD Connect.

6. Acesse a guia **Transformação** e implemente a seguinte regra de transformação:

    | Tipo de fluxo | Atributo de destino | Fonte | Aplicar uma vez | Tipo de mesclagem |
    | --- | --- | --- | --- | --- |
    | Direto | UserType | UserType | Desmarcado | Atualizar |

7. Clique em **Adicionar** para criar a regra de saída.

![Criar regra de sincronização de saída](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Etapa 6: Executar um ciclo completo de sincronização
Em geral, um ciclo completo de sincronização é necessário, já que adicionamos novos atributos ao esquema do Active Directory e do Azure AD Connector e introduzimos regras de sincronização personalizadas. Você deseja verificar as alterações antes de exportá-las para o Azure AD. 

Você pode usar as etapas a seguir para verificar as alterações enquanto executa manualmente as etapas que formam um ciclo completo de sincronização.

1. Execute uma **Importação completa** no **AD Connector local**:

   1. Acesse a guia **Conectores** no Synchronization Service Manager.
   2. Clique com o botão direito do mouse no **AD Connector local** e selecione **Executar**.
   3. Na caixa de diálogo pop-up, selecione **Importação Completa** e clique em **OK**.
   4. Aguarde até operação terminar.

      > [!NOTE]
      > Você poderá ignorar uma Importação completa no AD Connector local se o atributo de origem já estiver incluído na lista de atributos importados. Em outras palavras, você não precisou fazer nenhuma alteração durante a [Etapa 2: Adicionar o atributo de origem ao esquema local do AD Connector](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Execute uma **Importação completa** no **Azure AD Connector**:

   1. Clique com o botão direito do mouse em **Azure AD Connector** e selecione **Executar**.
   2. Na caixa de diálogo pop-up, selecione **Importação Completa** e clique em **OK**.
   3. Aguarde até operação terminar.

3. Verifique se a regra de sincronização muda em um objeto de Usuário existente:

    O atributo de origem do Active Directory local e de UserType do Azure AD foi importado nos respectivos Espaços do Conector. Antes de continuar com uma sincronização completa, faça uma **Visualização** em um objeto Usuário existente no Espaço do AD Connector local. O objeto escolhido deve ter o atributo de origem preenchido.
    
    Uma **Visualização** bem-sucedida com o UserType preenchido no metaverso é um bom indicador de que você configurou as regras de sincronização corretamente. Para obter informações sobre como fazer uma **Visualização**, consulte a seção [Verificar a alteração](#verify-the-change).

4. Execute uma **Sincronização Completa** no **AD Connector local**:

   1. Clique com o botão direito do mouse no **AD Connector local** e selecione **Executar**.
   2. Na caixa de diálogo pop-up, selecione **Sincronização Completa** e clique em **OK**.
   3. Aguarde até operação terminar.

5. Verifique **Exportações Pendentes** para o Azure AD:

   1. Clique com o botão direito do mouse no **Conector do Azure AD** e selecione **Pesquisar Espaço Conector**.
   2. Na caixa de diálogo pop-up **Pesquisar Espaço Conector**:

      - Defina o **Escopo** como **Exportação Pendente**.
      - Selecione todas as três caixas de seleção: **Adicionar**, **Modificar** e **Excluir**.
      - Clique no botão **Pesquisar** para obter a lista de objetos com alterações a serem exportados. Para examinar as alterações para um determinado objeto, clique duas vezes nele.
      - Verifique se as alterações são esperadas.

6. Execute **Exportar** no **Azure AD Connector**:

   1. Clique com o botão direito do mouse em **Azure AD Connector** e selecione **Executar**.
   2. Na caixa de diálogo pop-up **Executar Conector**, selecione a etapa **Exportar** e clique em **OK**.
   3. Aguarde até a exportação para o Azure AD ser encerrada.

> [!NOTE]
> Essas etapas não incluem as etapas de sincronização completa e de exportação no Azure AD Connector. Essas etapas não são obrigatórias, já que os valores de atributo estão fluindo do Active Directory local para o Azure AD apenas.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Etapa 7: Habilitar o agendador de sincronização novamente
Reabilite o agendador de sincronização interno:

1. Inicie uma sessão do PowerShell.
2. Reabilite a sincronização agendada executando o cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`.


## <a name="next-steps"></a>Próximas etapas
* Leia mais sobre o modelo de configuração em [Noções básicas do provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Leia mais sobre a linguagem de expressão em [Noções básicas sobre expressões de provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Tópicos de visão geral**

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](whatis-hybrid-identity.md)
