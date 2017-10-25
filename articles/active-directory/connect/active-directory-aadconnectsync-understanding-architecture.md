---
title: "Sincronização do Azure AD Connect: noções básicas sobre a arquitetura | Microsoft Docs"
description: "Este tópico descreve a arquitetura da sincronização do Azure AD Connect e explica os termos usados."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 51082ad453d53f56f30f814b78578801c00f4827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Sincronização do Azure AD Connect: noções básicas sobre a arquitetura
Este tópico aborda a arquitetura básica para a sincronização do Azure AD Connect. Em muitos aspectos, ela é semelhante à de seus predecessores MIIS 2003, ILM 2007 e FIM 2010. A sincronização do Azure AD Connect é a evolução dessas tecnologias. Se você estiver familiarizado com qualquer uma dessas tecnologias mais antigas, o conteúdo deste tópico também será familiar. Se você ainda não estiver familiarizado com a sincronização, este tópico é para você. No entanto, não é um requisito saber os detalhes deste tópico para conseguir fazer as personalizações na sincronização do Azure AD Connect (chamada de mecanismo de sincronização neste tópico).

## <a name="architecture"></a>Arquitetura
O mecanismo de sincronização cria uma exibição integrada dos objetos armazenados em várias fontes de dados conectadas e gerencia as informações de identidade nessas fontes de dados. Essa exibição integrada é determinada pelas informações de identidade recuperadas de fontes de dados conectadas e por um conjunto de regras que determinam como processar essas informações.

### <a name="connected-data-sources-and-connectors"></a>Fontes de Dados Conectadas e Conectores
O mecanismo de sincronização processa informações de identidade de diferentes repositórios de dados, como o Active Directory ou um banco de dados SQL Server. Cada repositório de dados que organiza seus dados em um formato de banco de dados e que fornece métodos de acesso a dados padrão é um potencial candidato a fonte de dados para o mecanismo de sincronização. Os repositórios de dados sincronizados pelo mecanismo de sincronização são chamados de **fontes de dados conectadas** ou **diretórios conectados**.

O mecanismo de sincronização encapsula a interação com uma fonte de dados conectada em um módulo chamado **Conector**. Cada tipo de fonte de dados conectada tem um Conector específico. O Conector converte uma operação necessária para o formato que a fonte de dados conectada compreende.

Os conectores fazem chamadas de API para trocar informações de identidade (leitura e gravação) com uma fonte de dados conectada. Também é possível adicionar um Conector personalizado usando a estrutura de conectividade extensível. A ilustração a seguir mostra como um Conector se conecta a uma fonte de dados conectada ao mecanismo de sincronização.

![Arco1](./media/active-directory-aadconnectsync-understanding-architecture/arch1.png)

Os dados podem fluir em qualquer direção, mas não podem fluir em ambas as direções simultaneamente. Em outras palavras, um Conector pode ser configurado para permitir que os dados fluam da fonte de dados conectada para o mecanismo de sincronização ou do mecanismo de sincronização para a fonte de dados conectada, mas apenas uma dessas operações pode ocorrer por vez para um atributo e um objeto. A direção pode ser diferente para objetos diferentes e atributos diferentes.

Para configurar um Conector, especifique os tipos de objeto que você deseja sincronizar. A especificação dos tipos de objeto define o escopo dos objetos incluídos no processo de sincronização. A próxima etapa é selecionar os atributos a serem sincronizados, o que é conhecido como uma lista de inclusão de atributos. Essas configurações poderão ser alteradas em qualquer ocasião em resposta a alterações feitas nas regras de negócio. Quando você usa o assistente de instalação do Azure AD Connect, essas configurações são definidas para você.

Para exportar objetos para uma fonte de dados conectada, a lista de inclusão de atributos deverá incluir pelo menos os atributos mínimos necessários para a criação de um tipo de objeto específico em uma fonte de dados conectada. Por exemplo, o atributo **sAMAccountName** deve ser colocado na lista de inclusão de atributos para exportar um objeto de usuário para o Active Directory porque todos os objetos de usuário no Active Directory devem ter um atributo **sAMAccountName** definido. Novamente, o assistente de instalação faz essa configuração para você.

Se a fonte de dados conectada usar componentes estruturais, como partições ou contêineres, para organizar objetos, você poderá limitar as áreas na fonte de dados conectada usadas para uma determinada solução.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Estrutura interna do namespace do mecanismo de sincronização
O namespace do mecanismo de sincronização completo consiste em dois namespaces que armazenam as informações de identidade. Os dois namespaces são:

* O espaço do conector
* O metaverso

O **espaço do conector** é uma área de preparação que contém representações dos objetos designados de uma fonte de dados conectada e os atributos especificados na lista de inclusão de atributo. O mecanismo de sincronização usa o espaço do conector para determinar o que mudou na fonte de dados conectada e para testar as alterações recebidas. O mecanismo de sincronização também usa o espaço do conector para preparar as alterações de saída para exportação para a fonte de dados conectada. O mecanismo de sincronização mantém um espaço do conector diferente da área de preparação para cada Conector.

Usando uma área de preparação, o mecanismo de sincronização permanece independente das fontes de dados conectadas e não é afetado pela disponibilidade e pela acessibilidade delas. Como resultado, você pode processar informações de identidade a qualquer momento usando os dados na área de preparação. O mecanismo de sincronização pode solicitar apenas as alterações feitas na fonte de dados conectada desde a última sessão de comunicação encerrada ou enviar por push somente as alterações feitas nas informações de identidade que a fonte de dados conectada ainda não recebeu, o que reduz o tráfego de rede entre o mecanismo de sincronização e a fonte de dados conectada.

Além disso, o mecanismo de sincronização armazena informações de status sobre todos os objetos que ele prepara no espaço do conector. Quando novos dados são recebidos, o mecanismo de sincronização sempre avalia se os dados já foram sincronizados.

O **metaverso** é uma área de armazenamento que contém as informações de identidade agregadas de várias fontes de dados conectadas, fornecendo uma única exibição global e integrada de todos os objetos combinados. Os objetos de metaverso são criados com base nas informações de identidade, recuperadas de fontes de dados conectadas e de um conjunto de regras que permitem que você personalize o processo de sincronização.

A ilustração a seguir mostra o namespace de espaço do conector e o namespace do metaverso no mecanismo de sincronização.

![Arco2](./media/active-directory-aadconnectsync-understanding-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Objetos de identidade do mecanismo de sincronização
Os objetos no mecanismo de sincronização são representações de qualquer um dos objetos na fonte de dados conectada ou na exibição integrada que o mecanismo de sincronização tem desses objetos. Todos os objetos do mecanismo de sincronização devem ter um identificador global exclusivo (GUID). Os GUIDs fornecem a integridade dos dados e expressam relacionamentos entre objetos.

### <a name="connector-space-objects"></a>Objetos de espaço do conector
Quando o mecanismo de sincronização se comunica com uma fonte de dados conectada, ele lê as informações de identidade na fonte de dados conectada e usa essas informações para criar uma representação do objeto de identidade no espaço do conector. Você não pode criar ou excluir esses objetos individualmente. No entanto, você pode excluir manualmente todos os objetos em um espaço de conector.

Todos os objetos no espaço do conector têm dois atributos:

* Um identificador global exclusivo (GUID)
* Um nome distinto (também conhecido como DN)

Se a fonte de dados conectada atribui um atributo exclusivo ao objeto, objetos no espaço do conector também podem ter um atributo de âncora. O atributo de âncora identifica exclusivamente um objeto na fonte de dados conectada. O mecanismo de sincronização usa a âncora para localizar a representação desse objeto correspondente na fonte de dados conectada. O mecanismo de sincronização supõe que a âncora de um objeto nunca muda durante a vida útil do objeto.

Muitos Conectores usam um identificador exclusivo conhecido para gerar automaticamente uma âncora para cada objeto quando ele é importado. Por exemplo, o Active Directory Connector usa o atributo **objectGUID** para uma âncora. Para as fontes de dados conectadas que não forneçam um identificador exclusivo claramente definido, é possível especificar a geração de uma âncora como parte da configuração do Conector.

Nesse caso, a âncora é criada a partir de um ou mais atributos exclusivos de um tipo de objeto e nenhum deles será alterado, o que identificará exclusivamente o objeto no espaço do conector (por exemplo, um número de funcionário ou uma ID de usuário).

Podem ser objetos de espaço do conector:

* Um objeto de preparação
* Um espaço reservado

### <a name="staging-objects"></a>Objetos de preparação
Um objeto de preparação representa uma instância dos tipos de objeto designado da fonte de dados conectada. Além do GUID e do nome distinto, um objeto de preparação sempre tem um valor que indica o tipo de objeto.

Os objetos de preparação que foram importados sempre terão um valor para o atributo de âncora. Os objetos de preparação que foram recém-provisionados pelo mecanismo de sincronização e que estiverem em processo de criação na fonte de dados conectada não terão um valor para o atributo de âncora.

Os objetos de preparação também carregam os valores atuais dos atributos de negócios e as informações operacionais necessários ao mecanismo de sincronização para a execução do processo de sincronização. As informações operacionais incluem sinalizadores que indicam o tipo de atualizações preparadas no objeto de preparação. Se um objeto de preparação tiver recebido novas informações de identidade da fonte de dados conectada que ainda não foram processadas, o objeto será sinalizado como **importação pendente**. Se um objeto de preparação tiver novas informações de identidade que ainda não foram exportadas para a fonte de dados conectada, ele será marcado como **exportação pendente**.

Um objeto de preparação pode ser um objeto de importação ou de exportação. O mecanismo de sincronização cria um objeto de importação usando informações de objeto recebidas da fonte de dados conectada. Quando o mecanismo de sincronização recebe informações sobre a existência de um novo objeto que corresponde a um dos tipos de objeto selecionados no Conector, ele cria um objeto de importação no espaço do conector como uma representação do objeto na fonte de dados conectada.

A ilustração a seguir mostra um objeto de importação que representa um objeto na fonte de dados conectada.

![Arco3](./media/active-directory-aadconnectsync-understanding-architecture/arch3.png)

O mecanismo de sincronização cria um objeto de exportação usando informações de objeto no metaverso. Os objetos de exportação são exportados para a fonte de dados conectada durante a próxima sessão de comunicação. Da perspectiva do mecanismo de sincronização, os objetos de exportação ainda não existem na fonte de dados conectada. Portanto, o atributo de âncora para um objeto de exportação não está disponível. Depois de receber o objeto do mecanismo de sincronização, a fonte de dados conectada cria um valor exclusivo para o atributo de âncora do objeto.

A ilustração a seguir mostra como um objeto de exportação é criado usando as informações de identidade no metaverso.

![Arco4](./media/active-directory-aadconnectsync-understanding-architecture/arch4.png)

O mecanismo de sincronização confirma a exportação do objeto ao importar novamente o objeto de fonte de dados conectada. Os objetos de exportação se tornam objetos de importação quando o mecanismo de sincronização os recebe durante a próxima importação da fonte de dados conectada.

### <a name="placeholders"></a>Espaços reservados
O mecanismo de sincronização usa um namespace simples para armazenar objetos. No entanto, algumas fontes de dados conectadas, como o Active Directory, usam um namespace hierárquico. Para transformar informações de um namespace hierárquico em um namespace simples, o mecanismo de sincronização usa espaços reservados para preservar a hierarquia.

Cada espaço reservado representa um componente (por exemplo, uma unidade organizacional) de um nome hierárquico do objeto que não foi importado para o mecanismo de sincronização, mas é necessário para construir o nome hierárquico. Eles preenchem lacunas criadas pelas referências da fonte de dados conectada para os objetos que não são objetos de preparação no espaço do conector.

O mecanismo de sincronização também usa os espaços reservados para armazenar os objetos referenciados que ainda não foram importados. Por exemplo, se a sincronização estiver configurada para incluir o atributo gerente para o objeto *Clara Barbosa* e o valor recebido for um objeto que ainda não foi importado, como *CN=Pedro Gonçalves,CN=Usuários,DC=fabrikam,DC=com*, as informações de gerente serão armazenadas como espaços reservados no espaço do conector. Se o objeto gerente for importado mais tarde, o objeto do espaço reservado será  substituído pelo objeto de preparação que representa o gerente.

### <a name="metaverse-objects"></a>Objetos do metaverso
Um objeto do metaverso contém a exibição agregada que o mecanismo de sincronização tem dos objetos de preparação no espaço do conector. O mecanismo de sincronização cria os objetos de metaverso usando as informações dos objetos de importação. Diversos objetos de espaço do conector podem ser vinculados a um único objeto do metaverso, mas um objeto de espaço do conector não pode ser vinculado a mais de um objeto do metaverso.

Os objetos de metaverso não podem ser criados ou excluídos manualmente. O mecanismo de sincronização excluirá automaticamente os objetos de metaverso que não tiverem um link para qualquer objeto de espaço do conector no espaço do conector.

Para mapear objetos dentro de uma fonte de dados conectada para um tipo de objeto correspondente no metaverso, o mecanismo de sincronização fornece um esquema extensível com um conjunto predefinido de tipos de objetos e atributos associados. Você pode criar novos tipos de objeto e de atributos para os objetos de metaverso. Os atributos podem ter valor único ou diversos valores, e os tipos de atributo podem ser cadeias de caracteres, referências, números e valores boolianos.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relacionamentos entre objetos de preparação e objetos de metaverso
Dentro do namespace do mecanismo de sincronização, o fluxo de dados é habilitado pela relação de vínculo entre objetos de preparo e objetos de metaverso. Um objeto de preparação vinculado a um objeto de metaverso é chamado de **objeto unido** (ou **objeto conector**). Um objeto de preparação vinculado a um objeto de metaverso é chamado de **objeto separado** (ou **objeto desconector**). Os termos unido e separado são preferíveis para não confundir com os Conectores responsáveis pela importação e pela exportação de dados de um diretório conectado.

Os espaços reservados nunca são vinculados a um objeto de metaverso

Um objeto unido consiste em um objeto temporário e em seu relacionamento vinculado com um objeto de metaverso único. Os objetos unidos são usados para sincronizar os valores de atributo entre um objeto de espaço do conector e um objeto de metaverso.

Quando um objeto de preparação se torna um objeto unido durante a sincronização, os atributos podem fluir entre o objeto de preparação e o objeto de metaverso. O fluxo de atributos é bidirecional e é configurado usando regras de atributos de importação e de exportação.

Um único objeto de espaço do conector pode ser vinculado a apenas um objeto de metaverso. No entanto, cada objeto de metaverso pode ser vinculado a vários objetos de espaço do conector no mesmo ou em espaços do conector diferentes, como mostrado na ilustração a seguir.

![Arco5](./media/active-directory-aadconnectsync-understanding-architecture/arch5.png)

O relacionamento vinculado entre o objeto de preparação e um objeto de metaverso é persistente e só pode ser removido por regras especificadas por você.

Um objeto separado é um objeto de preparação vinculado que não está vinculado a qualquer objeto de metaverso. Os valores de atributo de um objeto separado não são mais processados no metaverso. Os valores de atributo do objeto correspondente na fonte de dados conectada não são atualizados pelo mecanismo de sincronização.

Ao usar os objetos separados, você pode armazenar informações de identidade no mecanismo de sincronização e processá-las mais tarde. Manter um objeto de preparação como um objeto separado no espaço do conector tem muitas vantagens. Como o sistema já preparou as informações necessárias sobre esse objeto, não será necessário criar uma representação desse objeto novamente durante a próxima importação da fonte de dados conectada. Dessa forma, o mecanismo de sincronização sempre terá um instantâneo completo da fonte de dados conectada, mesmo se não houver uma conexão atual com a fonte de dados conectada. Os objetos separados podem ser convertidos em objetos unidos e vice-versa, dependendo das regras que você especificar.

Um objeto de importação é criado como um objeto separado. Um objeto de exportação deve ser um objeto unido. A lógica do sistema impõe essa regra e exclui todos os objetos de exportação que não sejam objetos unidos.

## <a name="sync-engine-identity-management-process"></a>Processo de gerenciamento de identidades do mecanismo de sincronização
O processo de gerenciamento de identidades controla como as informações de identidade são atualizadas entre as diferentes fontes de dados conectadas. O gerenciamento de identidades ocorre em três processos:

* Importar
* Sincronização
* Exportação

Durante o processo de importação, o mecanismo de sincronização avaliará as informações de identidade de entrada de uma fonte de dados conectada. Quando forem detectadas alterações, ele criará novos objetos de preparação ou atualizará os objetos de preparação existentes no espaço do conector para sincronização.

Durante o processo de sincronização, o mecanismo de sincronização atualiza o metaverso para refletir as alterações que ocorreram no espaço do conector e atualiza o espaço do conector para refletir as alterações que ocorreram no metaverso.

Durante o processo de exportação, o mecanismo de sincronização envia as alterações preparadas nos objetos de preparação e as alterações sinalizadas como exportação pendente.

A ilustração a seguir mostra onde cada um dos processos ocorre à medida que as informações de identidade fluem de uma fonte de dados conectada para outra.

![Arco6](./media/active-directory-aadconnectsync-understanding-architecture/arch6.png)

### <a name="import-process"></a>Processo de importação
Durante o processo de importação, o mecanismo de sincronização avalia as atualizações de informações de identidade. O mecanismo de sincronização compara as informações de identidade recebidas da fonte de dados conectada com as informações de identidade sobre um objeto de preparação e determina se o objeto de preparação precisa de atualizações. Se for necessário atualizar o objeto de preparação com novos dados, o objeto de preparação será sinalizado como importação pendente.

Ao preparar objetos no espaço do conector antes da sincronização, o mecanismo de sincronização só poderá processar as informações de identidade alteradas. Esse processo oferece os seguintes benefícios:

* **Sincronização eficiente**. A quantidade de dados processados durante a sincronização é minimizada.
* **Ressincronização eficiente**. Você pode alterar a forma como o mecanismo de sincronização processa as informações de identidade sem reconectar o mecanismo de sincronização à fonte de dados.
* **Oportunidade de visualizar a sincronização**. Você pode visualizar a sincronização para verificar se suas suposições sobre o processo de gerenciamento de identidades estão corretas.

Para cada objeto especificado no Conector, o mecanismo de sincronização primeiro tenta localizar uma representação do objeto no espaço do conector do Conector. O mecanismo de sincronização examina todos os objetos de preparação no espaço do conector e tenta localizar um objeto de preparação correspondente que tenha um atributo de âncora correspondente. Se nenhum objeto de preparação existente tiver um atributo de âncora correspondente, o mecanismo de sincronização tentará encontrar um objeto de preparação correspondente com o mesmo nome distinto.

Quando o mecanismo de sincronização encontrar um objeto de preparação que corresponda ao nome distinto, mas não por âncora, ocorrerá o seguinte comportamento especial:

* Se o objeto localizado no espaço do conector não tiver âncoras, o mecanismo de sincronização removerá o objeto do espaço do conector e marcará o objeto de metaverso ao qual ele está vinculado como **tentar provisionar novamente na próxima execução de sincronização**. Em seguida, ele criará o novo objeto de importação.
* Se o objeto localizado no espaço do conector tiver uma âncora, o mecanismo de sincronização suporá que esse objeto foi renomeado ou excluído no diretório conectado. Ele atribui um novo nome distinto temporário ao objeto de espaço do conector para que ele possa preparar o objeto de entrada. O objeto antigo se torna **transitório**, aguardando o Conector importar a renomeação ou a exclusão para resolver a situação.

Se o mecanismo de sincronização localizar um objeto de preparação que corresponda ao objeto especificado no Conector, ele determinará que tipo de alterações serão aplicadas. Por exemplo, o mecanismo de sincronização pode renomear ou excluir o objeto da fonte de dados conectada ou pode apenas atualizar os valores de atributo do objeto.

Os objetos de preparação com dados atualizados são marcados como importação pendente. Tipos diferentes de importações pendentes estão disponíveis. Dependendo do resultado do processo de importação, um objeto de preparação no espaço do conector terá um dos seguintes tipos de importação pendente:

* **Nenhum**. Nenhuma alteração em qualquer um dos atributos do objeto de preparação está disponível. O mecanismo de sincronização não sinaliza esse tipo como importação pendente.
* **Adicionar**. O objeto de preparação é um objeto de importação novo no espaço do conector. O mecanismo de sincronização sinaliza esse tipo como importação pendente para processamento adicional no metaverso.
* **Atualizar**. O mecanismo de sincronização encontra um objeto de preparação correspondente no espaço do conector e sinaliza esse tipo como importação pendente para que as atualizações dos atributos possam ser processadas no metaverso. As atualizações incluem a renomeação do objeto.
* **Excluir**. O mecanismo de sincronização encontra um objeto de preparação correspondente no espaço do conector e sinaliza esse tipo como importação pendente para que o objeto unido possa ser excluído.
* **Excluir/Adicionar**. O mecanismo de sincronização encontra um objeto de preparação correspondente no espaço do conector, mas os tipos de objeto não correspondem. Nesse caso, uma modificação excluir-adicionar é preparada. Uma modificação excluir-adicionar indica para o mecanismo de sincronização que deverá ocorrer uma ressincronização completa do objeto porque são aplicados conjuntos de regras diferentes a esse objeto quando o tipo de objeto é alterado.

Ao definir o status de um objeto de preparação como importação pendente, você pode reduzir significativamente a quantidade de dados processados durante a sincronização, já que isso permite que o sistema processe somente os objetos com dados atualizados.

### <a name="synchronization-process"></a>Processo de sincronização
A sincronização consiste em dois processos relacionados:

* A sincronização de entrada, quando o conteúdo do metaverso é atualizado com os dados no espaço do conector.
* A sincronização de saída, quando o conteúdo do espaço do conector é atualizado com os dados no metaverso.

Usando as informações preparadas no espaço do conector, o processo de sincronização de entrada cria no metaverso a exibição integrada dos dados armazenados nas fontes de dados conectadas. Todos os objetos de preparação ou apenas aqueles com informações de importação pendente são agregados, dependendo de como as regras forem configuradas.

As atualizações do processo de sincronização de saída exportam objetos quando os objetos de metaverso são alterados.

A sincronização de entrada cria a exibição integrada no metaverso das informações de identidade recebidas das fontes de dados conectadas. O mecanismo de sincronização pode processar informações de identidade a qualquer momento usando as informações de identidade mais recentes obtidas da fonte de dados conectada.

**Sincronização de entrada**

A sincronização de entrada inclui os seguintes processos:

* **Provisionamento** (também chamado de **Projeção** se for importante distinguir este processo do provisionamento de sincronização de saída). O mecanismo de sincronização cria um novo objeto de metaverso com base em um objeto de preparação e os vincula. O provisionamento é uma operação que ocorre no nível do objeto.
* **Junção**. O mecanismo de sincronização vincula um objeto de preparação a um objeto de metaverso existente. Uma junção é uma operação que ocorre no nível do objeto.
* **Fluxo de atributos de importação**. O mecanismo de sincronização atualiza os valores de atributo, chamados de fluxo de atributos, do objeto no metaverso. O fluxo de atributos de importação é uma operação que ocorre no nível do atributo que exige um vínculo entre um objeto de preparação e um objeto de metaverso.

O provisionamento é o único processo que cria objetos no metaverso. O provisionamento afeta apenas os objetos de importação separados. Durante o provisionamento, o mecanismo de sincronização cria um objeto de metaverso que corresponde ao tipo de objeto do objeto de importação e estabelece um vínculo entre os dois objetos criando, assim, um objeto unido.

O processo de junção também estabelece um vínculo entre objetos de importação e um objeto de metaverso. A diferença entre a junção e o provisionamento é que o processo de junção exige que o objeto de importação seja vinculado a um objeto de metaverso existente, enquanto o processo de provisionamento cria um novo objeto de metaverso.

O mecanismo de sincronização tentará unir a um objeto de importação a um objeto de metaverso usando critérios especificados na configuração da Regra de Sincronização.

Durante os processos de provisionamento e de junção, o mecanismo de sincronização vincula um objeto separado a um objeto de metaverso, unindo-os. Após a conclusão dessas operações no nível do objeto, o mecanismo de sincronização poderá atualizar os valores de atributo do objeto de metaverso associado. Esse processo é chamado de fluxo de atributos de importação.

O fluxo de atributos de importação ocorre em todos os objetos de importação que carregam novos dados e que estejam vinculados a um objeto de metaverso.

**Sincronização de saída**

A sincronização de saída atualiza objetos de exportação quando um objeto de metaverso é alterado mas não é excluído. O objetivo da sincronização de saída é avaliar se as alterações feitas em objetos de metaverso exigem atualizações de objetos de preparação nos espaços do conector. Em alguns casos, as alterações podem exigir que os objetos de preparação em todos os espaços do conector sejam atualizados. Os objetos de preparação alterados são sinalizados como exportação pendente, transformando-os em objetos de exportação. Esses objetos de exportação são enviados por push mais tarde para a fonte de dados conectada durante o processo de exportação.

A sincronização de saída tem três processos:

* **Provisionamento**
* **Desprovisionamento**
* **Fluxo de atributos de exportação**

O provisionamento e o desprovisionamento são ambas operações que ocorrem no nível do objeto. O desprovisionamento depende do provisionamento porque somente o provisionamento pode iniciá-lo. O desprovisionamento é disparado quando o provisionamento remove o vínculo entre um objeto do metaverso e um objeto de exportação.

O provisionamento sempre é disparado quando as alterações são aplicadas a objetos no metaverso. Quando são feitas alterações nos objetos de metaverso, o mecanismo de sincronização pode executar as tarefas a seguir como parte do processo de provisionamento:

* Criar objetos unidos, onde um objeto de metaverso é vinculado a um objeto de exportação recém-criado.
* Renomear um objeto unido.
* Separar os vínculos entre um objeto de metaverso e objetos de preparação, criando um objeto separado.

Se o provisionamento exigir que o mecanismo de sincronização crie um novo objeto de conector, o objeto de preparação ao qual o objeto de metaverso está vinculado sempre será um objeto de exportação porque o objeto ainda não existirá na fonte de dados conectada.

Se o provisionamento exigir que o mecanismo de sincronização separe um objeto unido criando, assim, um objeto separado, o desprovisionamento será disparado. O processo de desprovisionamento exclui o objeto.

Durante o desprovisionamento, a exclusão de um objeto de exportação não excluirá fisicamente o objeto. O objeto é sinalizado como **excluído**, o que significa que a operação de exclusão foi preparada no objeto.

A exportação do fluxo de atributos também ocorre durante o processo de sincronização de saída, semelhante à forma como a importação de fluxo de atributos ocorre durante a sincronização de entrada. O fluxo de atributos de exportação ocorre apenas entre os objetos de metaverso e os objetos de exportação unidos.

### <a name="export-process"></a>Processo de exportação
Durante o processo de exportação, o mecanismo de sincronização examina todos os objetos de exportação que serão sinalizados como pendentes de exportação no espaço do conector e, em seguida, envia atualizações para a fonte de dados conectada.

O mecanismo de sincronização pode determinar o êxito de uma exportação, mas não consegue determinar de forma suficiente que o processo de gerenciamento de identidades foi concluído. Os objetos na fonte de dados conectada sempre podem ser alterados por outros processos. Como o mecanismo de sincronização não tem uma conexão persistente com a fonte de dados conectada, não é suficiente fazer suposições sobre as propriedades de um objeto na fonte de dados conectada com base somente em uma notificação de exportação com êxito.

Por exemplo, um processo na fonte de dados conectada pode alterar os atributos do objeto para seus valores originais (ou seja, a fonte de dados conectada pode substituir os valores imediatamente depois que os dados são enviados por push pelo mecanismo de sincronização e aplicados com êxito na fonte de dados conectada).

O mecanismo de sincronização armazena as informações sobre o status de exportação e de importação sobre cada objeto de preparação. Se os valores dos atributos especificados na lista de inclusão de atributos tiverem sido alterados desde a última exportação, o armazenamento do status de importação e de exportação permitirá que o mecanismo de sincronização reaja adequadamente. O mecanismo de sincronização usa o processo de importação para confirmar os valores de atributo que foram exportados para a fonte de dados conectada. Uma comparação entre as informações importadas e exportadas, como mostrado na ilustração a seguir, permite que o mecanismo de sincronização determine se a exportação foi bem-sucedida ou se precisará ser repetida.

![Arco7](./media/active-directory-aadconnectsync-understanding-architecture/arch7.png)

Por exemplo, se o mecanismo de sincronização exportar o atributo C, com um valor 5, para uma fonte de dados conectada, ele armazenará C=5 em sua memória de status de exportação. Cada exportação adicional nesse objeto resultará em uma nova tentativa de exportar C=5 para a fonte de dados conectada, já que o mecanismo de sincronização supõe que esse valor não foi persistentemente aplicado ao objeto (ou seja, a menos que um valor diferente tenha sido importado recentemente da fonte de dados conectada). A memória de exportação é desmarcada quando C=5 é recebido durante uma operação de importação no objeto.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

