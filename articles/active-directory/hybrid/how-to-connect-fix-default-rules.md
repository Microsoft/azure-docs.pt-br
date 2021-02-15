---
title: Como corrigir regras padrão modificadas-Azure AD Connect | Microsoft Docs
description: Saiba como corrigir as regras padrão modificadas que vêm com Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0fc1bc3158e04c9b1f677af7ef2375ac3ed2ce7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320040"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Corrigir regras padrão modificadas no Azure AD Connect

O Azure Active Directory (Azure AD) Connect usa regras padrão para sincronização.  Infelizmente, essas regras não se aplicam universalmente a todas as organizações. Com base em seus requisitos, talvez seja necessário modificá-los. Este artigo aborda dois exemplos de personalizações mais comuns e explica a maneira correta de alcançar essas personalizações.

>[!NOTE] 
> Não há suporte para a modificação de regras padrão existentes para obter uma personalização necessária. Se você fizer isso, ele impedirá a atualização dessas regras para a versão mais recente em versões futuras. Você não obterá as correções de bug necessárias ou os novos recursos. Este documento explica como obter o mesmo resultado sem modificar as regras padrão existentes. 

## <a name="how-to-identify-modified-default-rules"></a>Como identificar regras padrão modificadas
A partir da versão 1.3.7.0 do Azure AD Connect, é fácil identificar a regra padrão modificada. Vá para **aplicativos na área de trabalho**e selecione **Editor de regras de sincronização**.

![Azure AD Connect, com o editor de regras de sincronização realçado](media/how-to-connect-fix-default-rules/default1.png)

No editor, todas as regras padrão modificadas são mostradas com um ícone de aviso na frente do nome.

![Ícone de aviso:](media/how-to-connect-fix-default-rules/default2.png)

 Uma regra desabilitada com o mesmo nome ao lado dela também é exibida (essa é a regra padrão standard).

![Editor de regras de sincronização, mostrando a regra padrão standard e a regra padrão modificada](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Personalizações comuns
Veja a seguir as personalizações comuns para as regras padrão:

- Alterar o fluxo de atributos
- Alterar filtro de escopo
- Alterar condição de junção

Antes de alterar qualquer regra:

- Desabilite o Agendador de sincronização. O Agendador é executado a cada 30 minutos por padrão. Verifique se ele não está iniciando enquanto você está fazendo alterações e Solucionando problemas de suas novas regras. Para desabilitar temporariamente o Agendador, inicie o PowerShell e execute `Set-ADSyncScheduler -SyncCycleEnabled $false` .
 ![Comandos do PowerShell para desabilitar o Agendador de sincronização](media/how-to-connect-fix-default-rules/default3.png)

- A alteração no filtro de escopo pode resultar na exclusão de objetos no diretório de destino. Tenha cuidado antes de fazer qualquer alteração no escopo dos objetos. Recomendamos que você faça alterações em um servidor de preparo antes de fazer alterações no servidor ativo.
- Execute uma visualização em um único objeto, conforme mencionado na seção [validar regra de sincronização](#validate-sync-rule) , depois de adicionar uma nova regra.
- Execute uma sincronização completa após adicionar uma nova regra ou modificar qualquer regra de sincronização personalizada. Essa sincronização aplica novas regras a todos os objetos.

## <a name="change-attribute-flow"></a>Alterar o fluxo de atributos
Há três cenários diferentes para alterar o fluxo de atributos:
- Adicionando um novo atributo.
- Substituindo o valor de um atributo existente.
- Optando por não sincronizar um atributo existente.

Você pode fazer isso sem alterar regras padrão.

### <a name="add-a-new-attribute"></a>Adicionar um novo atributo
Se você achar que um atributo não está fluindo do diretório de origem para o diretório de destino, use o [Azure ad Connect sincronizar: extensões de diretório](how-to-connect-sync-feature-directory-extensions.md) para corrigir isso.

Se as extensões não funcionarem, tente adicionar duas novas regras de sincronização, descritas nas seções a seguir.


#### <a name="add-an-inbound-sync-rule"></a>Adicionar uma regra de sincronização de entrada
Uma regra de sincronização de entrada significa que a origem do atributo é um espaço do conector e o destino é o metaverso. Por exemplo, para ter um novo fluxo de atributos do Active Directory local para Azure Active Directory, crie uma nova regra de sincronização de entrada. Inicie o **Editor de regras de sincronização**, selecione **entrada** como a direção e selecione **Adicionar nova regra**. 

 ![Captura de tela que mostra o "editor de regras de sincronização" com "entrada" e "Adicionar nova regra" selecionado.](media/how-to-connect-fix-default-rules/default3a.png)

Siga sua própria convenção de nomenclatura para nomear a regra. Aqui, usamos **Custom in do AD-User**. Isso significa que a regra é uma regra personalizada e é uma regra de entrada do espaço do conector de Active Directory para o metaverso.   

 ![Criar regra de sincronização de entrada](media/how-to-connect-fix-default-rules/default3b.png)

Forneça sua própria descrição da regra para que a manutenção futura da regra seja fácil. Por exemplo, a descrição pode ser baseada em qual é o objetivo da regra e por que ela é necessária.

Faça suas seleções para o **sistema conectado**, o **tipo de objeto do sistema conectado**e os campos do **tipo de objeto do metaverso** .

Especifique o valor de precedência de 0 a 99 (quanto menor o número, maior a precedência). Para os campos **marca**, **habilitar Sincronização de senha**e **desabilitado** , use as seleções padrão.

Manter **filtro de escopo** vazio. Isso significa que a regra se aplica a todos os objetos Unidos entre o Active Directory sistema conectado e o metaverso.

Mantenha **as regras de junção** vazias. Isso significa que essa regra usa a condição de junção definida na regra padrão standard. Essa é outra razão para não desabilitar ou excluir a regra padrão standard. Se não houver nenhuma condição de junção, o atributo não fluirá. 

Adicione as transformações apropriadas para seu atributo. Você pode atribuir uma constante para criar um fluxo de valor constante para o atributo de destino. Você pode usar o mapeamento direto entre o atributo de origem ou de destino. Ou, você pode usar uma expressão para o atributo. Aqui estão várias [funções de expressão](./reference-connect-sync-functions-reference.md) que você pode usar.

#### <a name="add-an-outbound-sync-rule"></a>Adicionar uma regra de sincronização de saída
Para vincular o atributo ao diretório de destino, você precisa criar uma regra de saída. Isso significa que a origem é o metaverso e o destino é o sistema conectado. Para criar uma regra de saída, inicie o **Editor de regras de sincronização**, altere a **direção** para **saída**e selecione **Adicionar nova regra**. 

![Editor de Regras de Sincronização](media/how-to-connect-fix-default-rules/default3c.png)

Assim como acontece com a regra de entrada, você pode usar sua própria convenção de nomenclatura para nomear a regra. Selecione o **sistema conectado** como o locatário do Azure AD e selecione o objeto do sistema conectado para o qual você deseja definir o valor do atributo. Defina a precedência de 0 a 99. 

![Criar regra de sincronização de saída](media/how-to-connect-fix-default-rules/default3d.png)

Mantenha o **filtro de escopo** e **as regras de junção** vazias. Preencha a transformação como constante, direta ou expressão. 

Agora você sabe como criar um novo atributo para um fluxo de objeto de usuário de Active Directory para Azure Active Directory. Você pode usar estas etapas para mapear qualquer atributo de qualquer objeto para origem e destino. Para obter mais informações, consulte [criando regras de sincronização personalizadas](how-to-connect-create-custom-sync-rule.md) e [preparar para provisionar usuários](/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Substituir o valor de um atributo existente
Talvez você queira substituir o valor de um atributo que já foi mapeado. Por exemplo, se você sempre quiser definir um valor nulo para um atributo no Azure AD, basta criar apenas uma regra de entrada. Torne o valor constante, `AuthoritativeNull` , fluxo para o atributo de destino. 

>[!NOTE] 
> Use em `AuthoritativeNull` vez de `Null` neste caso. Isso ocorre porque o valor não nulo substitui o valor nulo, mesmo que tenha precedência inferior (um valor de número mais alto na regra). `AuthoritativeNull`, por outro lado, não é substituído por outras regras por um valor não nulo. 

### <a name="dont-sync-existing-attribute"></a>Não sincronizar atributo existente
Se você quiser excluir um atributo da sincronização, use o recurso de filtragem de atributo fornecido no Azure AD Connect. Inicie **Azure ad Connect** no ícone da área de trabalho e, em seguida, selecione **Personalizar opções de sincronização**.

![Azure AD Connect opções de tarefas adicionais](media/how-to-connect-fix-default-rules/default4.png)

 Verifique se a **filtragem de aplicativo e atributo do Azure ad** está selecionada e selecione **Avançar**.

![Azure AD Connect recursos opcionais](media/how-to-connect-fix-default-rules/default5.png)

Desmarque os atributos que você deseja excluir da sincronização.

![Atributos de Azure AD Connect](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Alterar filtro de escopo
Azure AD Sync cuida da maioria dos objetos. Você pode reduzir o escopo de objetos e reduzir o número de objetos a serem exportados, sem alterar as regras padrão de sincronização. 

Use um dos seguintes métodos para reduzir o escopo dos objetos que você está sincronizando:

- atributo cloudFiltered
- Filtragem de unidade organizacional

Se você reduzir o escopo dos usuários que estão sendo sincronizados, a sincronização de hash de senha também para os usuários filtrados. Se os objetos já estiverem sendo sincronizados, depois de reduzir o escopo, os objetos filtrados serão excluídos do diretório de destino. Por esse motivo, certifique-se de que você tem o escopo muito cuidadoso.

>[!IMPORTANT] 
> Não é recomendável aumentar o escopo de objetos configurados por Azure AD Connect. Isso dificulta a equipe de suporte da Microsoft a entender as personalizações. Se você precisar aumentar o escopo de objetos, edite a regra existente, clone-a e desabilite a regra original. 

### <a name="cloudfiltered-attribute"></a>atributo cloudFiltered
Você não pode definir esse atributo em Active Directory. Defina o valor desse atributo adicionando uma nova regra de entrada. Em seguida, você pode usar a **transformação** e a **expressão** para definir esse atributo no metaverso. O exemplo a seguir mostra que você não deseja sincronizar todos os usuários cujo nome de departamento começa com **HRD** (não diferencia maiúsculas de minúsculas):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Primeiro convertemos o departamento da origem (Active Directory) em minúsculas. Em seguida, usando a `Left` função, pegamos apenas os três primeiros caracteres e comparamos com ele `hrd` . Se ele coincidir, o valor será definido como `True` , caso contrário `NULL` . Ao definir o valor como NULL, alguma outra regra com precedência inferior (um valor de número mais alto) pode gravar nele com uma condição diferente. Execute a visualização em um objeto para validar a regra de sincronização, conforme mencionado na seção [validar regra de sincronização](#validate-sync-rule) .

![Criar opções de regra de sincronização de entrada](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtragem de unidade organizacional
Você pode criar uma ou mais UOs (unidades organizacionais) e mover os objetos que não deseja sincronizar com essas UOs. Em seguida, configure a filtragem de UO no Azure AD Connect. Inicie o **Azure ad Connect** no ícone da área de trabalho e selecione as opções a seguir. Você também pode configurar a filtragem de UO no momento da instalação do Azure AD Connect. 

![Azure AD Connect tarefas adicionais](media/how-to-connect-fix-default-rules/default8.png)

Siga o assistente e desmarque as UOs que você não deseja sincronizar.

![Azure AD Connect opções de filtragem de domínio e UO](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Alterar condição de junção
Use as condições de junção padrão configuradas por Azure AD Connect. Alterar as condições de junção padrão torna difícil para o suporte da Microsoft entender as personalizações e dar suporte ao produto.

## <a name="validate-sync-rule"></a>Validar regra de sincronização
Você pode validar a regra de sincronização recém-adicionada usando o recurso de visualização, sem executar o ciclo de sincronização completo. Em Azure AD Connect, selecione **serviço de sincronização**.

![Azure AD Connect, com o serviço de sincronização realçado](media/how-to-connect-fix-default-rules/default10.png)

Selecione **pesquisa de metaverso**. Selecione o objeto de escopo como **pessoa**, selecione **Adicionar cláusula**e mencione seus critérios de pesquisa. Em seguida, selecione **Pesquisar**e clique duas vezes no objeto nos resultados da pesquisa. Verifique se os dados no Azure AD Connect estão atualizados para esse objeto, executando a importação e a sincronização na floresta antes de executar esta etapa.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

Em **Propriedades de objeto de metaverso**, selecione **conectores**, selecione o objeto no conector correspondente (floresta) e selecione **Propriedades...**.

![Propriedades de Objeto do Metaverso](media/how-to-connect-fix-default-rules/default12.png)

Selecionar **visualização...**

![Propriedades de objeto do espaço do conector](media/how-to-connect-fix-default-rules/default13a.png)

Na janela de visualização, selecione **gerar visualização** e **importar fluxo de atributos** no painel esquerdo.

![Captura de tela que mostra a janela de "visualização" com "importar fluxo de atributo" e "gerar visualização" selecionada.](media/how-to-connect-fix-default-rules/default14.png)
 
Aqui, observe que a regra recém-adicionada é executada no objeto e definiu o `cloudFiltered` atributo como true.

![Versão Prévia](media/how-to-connect-fix-default-rules/default15a.png)
 
Para comparar a regra modificada com a regra padrão, exporte as duas regras separadamente, como arquivos de texto. Essas regras são exportadas como um arquivo de script do PowerShell. Você pode compará-los usando qualquer ferramenta de comparação de arquivos (por exemplo, WinDiff) para ver as alterações. 
 
Observe que, na regra modificada, o `msExchMailboxGuid` atributo é alterado para o tipo de **expressão** , em vez de **direto**. Além disso, o valor é alterado para **NULL** e a opção **ExecuteOnce** . Você pode ignorar as diferenças identificadas e de precedência. 

![saída da ferramenta WinDiff](media/how-to-connect-fix-default-rules/default17.png)
 
Para corrigir suas regras para alterá-las de volta para as configurações padrão, exclua a regra modificada e habilite a regra padrão. Certifique-se de que você não perca a personalização que está tentando alcançar. Quando estiver pronto, execute a **sincronização completa**.

## <a name="next-steps"></a>Próximas etapas
- [Pré-requisitos e hardware](how-to-connect-install-prerequisites.md) 
- [Configurações Expressas](how-to-connect-install-express.md)
- [Configurações personalizadas](how-to-connect-install-custom.md)