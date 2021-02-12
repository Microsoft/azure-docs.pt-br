---
title: Controle de versão de BLOB
titleSuffix: Azure Storage
description: O controle de versão do armazenamento de BLOBs mantém automaticamente versões anteriores de um objeto e as identifica com carimbos de data/hora. Você pode restaurar versões anteriores de um blob para recuperar seus dados se eles forem modificados ou excluídos erroneamente.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 267fd57b2fd359a73d5c1e01568aba14594e9290
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980150"
---
# <a name="blob-versioning"></a>Controle de versão de BLOB

Você pode habilitar o controle de versão do armazenamento de BLOBs para manter automaticamente as versões anteriores de um objeto.  Quando o controle de versão de blob estiver habilitado, você poderá restaurar uma versão anterior de um blob para recuperar seus dados se eles forem modificados ou excluídos erroneamente.

O controle de versão de blob está habilitado na conta de armazenamento e se aplica a todos os BLOBs na conta de armazenamento. Depois de habilitar o controle de versão de BLOB para uma conta de armazenamento, o armazenamento do Azure mantém automaticamente as versões para cada blob na conta de armazenamento.

A Microsoft recomenda usar o controle de versão de BLOB para manter versões anteriores de um blob para proteção de dados superior. Quando possível, use o controle de versão de BLOB em vez de instantâneos de BLOB para manter as versões anteriores. Os instantâneos de blob fornecem funcionalidade semelhante, pois mantêm versões anteriores de um blob, mas os instantâneos devem ser mantidos manualmente pelo seu aplicativo.

Para saber como habilitar o controle de versão de BLOB, consulte [habilitar e gerenciar o controle de versão de blob](versioning-enable.md).

> [!IMPORTANT]
> O controle de versão de BLOB não pode ajudá-lo a se recuperar da exclusão acidental de uma conta de armazenamento ou contêiner. Para evitar a exclusão acidental da conta de armazenamento, configure um bloqueio **CannotDelete** no recurso de conta de armazenamento. Para obter mais informações sobre como bloquear recursos do Azure, consulte [Bloquear recursos para evitar alterações inesperadas](../../azure-resource-manager/management/lock-resources.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-blob-versioning-works"></a>Como funciona o controle de versão de BLOB

Uma versão captura o estado de um blob em um determinado ponto no tempo. Quando o controle de versão do blob está habilitado para uma conta de armazenamento, o armazenamento do Azure cria automaticamente uma nova versão de um blob cada vez que o blob é modificado ou excluído.

Quando você cria um blob com o controle de versão habilitado, o novo BLOB é a versão atual do blob (ou o blob de base). Se você Modificar posteriormente esse BLOB, o armazenamento do Azure criará uma versão que captura o estado do blob antes de ser modificado. O blob modificado torna-se a nova versão atual. Uma nova versão é criada cada vez que você modifica o blob.

O diagrama a seguir mostra como as versões são criadas em operações de gravação e exclusão e como uma versão anterior pode ser promovida para a versão atual:

:::image type="content" source="media/versioning-overview/blob-versioning-diagram.png" alt-text="Diagrama mostrando como funciona o controle de versão de BLOB":::

Ter um grande número de versões por blob pode aumentar a latência para operações de listagem de BLOBs. A Microsoft recomenda a manutenção de menos de 1000 versões por blob. Você pode usar o gerenciamento do ciclo de vida para excluir automaticamente versões antigas. Para obter mais informações sobre o gerenciamento do ciclo de vida, consulte [otimizar custos automatizando as camadas de acesso do armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md).

Quando você exclui um blob com o controle de versão habilitado, o armazenamento do Azure cria uma versão que captura o estado do blob antes de ele ser excluído. A versão atual do blob é excluída, mas as versões do blob persistem, para que possam ser recriadas se necessário. 

As versões de blob são imutáveis. Você não pode modificar o conteúdo ou os metadados de uma versão de blob existente.

O controle de versão de blob está disponível para contas de armazenamento de BLOBs v2, BLOB de blocos e de uso geral. As contas de armazenamento com um namespace hierárquico habilitado para uso com Azure Data Lake Storage Gen2 não têm suporte no momento.

A versão 2019-10-10 e superior da API REST do armazenamento do Azure dá suporte ao controle de versão de BLOB.

### <a name="version-id"></a>ID da versão

Cada versão de blob é identificada por uma ID de versão. O valor da ID de versão é o carimbo de data/hora em que o blob foi gravado ou atualizado. A ID da versão é atribuída no momento em que a versão é criada.

Você pode executar operações de leitura ou exclusão em uma versão específica de um blob fornecendo sua ID de versão. Se você omitir a ID da versão, a operação agirá em relação à versão atual (o blob de base).

Quando você chama uma operação de gravação para criar ou modificar um blob, o armazenamento do Azure retorna o cabeçalho *x-MS-Version-ID* na resposta. Esse cabeçalho contém a ID da versão atual do blob que foi criado pela operação de gravação.

A ID da versão permanece a mesma durante o tempo de vida da versão.

### <a name="versioning-on-write-operations"></a>Controle de versão em operações de gravação

Quando o controle de versão do blob é ativado, cada operação de gravação em um blob cria uma nova versão. As operações de gravação incluem [colocar blob](/rest/api/storageservices/put-blob), [colocar lista de blocos](/rest/api/storageservices/put-block-list), [copiar blob](/rest/api/storageservices/copy-blob)e [definir metadados de blob](/rest/api/storageservices/set-blob-metadata).

Se a operação de gravação criar um novo BLOB, o blob resultante será a versão atual do blob. Se a operação de gravação modificar um blob existente, os novos dados serão capturados no blob atualizado, que é a versão atual, e o armazenamento do Azure criará uma versão que salva o estado anterior do blob.

Para simplificar, os diagramas mostrados neste artigo exibem a ID da versão como um valor inteiro simples. Na realidade, a ID da versão é um carimbo de data/hora. A versão atual é mostrada em azul e as versões anteriores são mostradas em cinza.

O diagrama a seguir mostra como as operações de gravação afetam as versões de BLOB. Quando um blob é criado, esse blob é a versão atual. Quando o mesmo blob é modificado, uma nova versão é criada para salvar o estado anterior do blob e o blob atualizado se torna a versão atual.

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="Diagrama mostrando como as operações de gravação afetam os BLOBs com versão.":::

> [!NOTE]
> Um blob que foi criado antes do controle de versão habilitado para a conta de armazenamento não tem uma ID de versão. Quando esse blob é modificado, o blob modificado torna-se a versão atual e uma versão é criada para salvar o estado do blob antes da atualização. A versão recebe uma ID de versão que é sua hora de criação.

### <a name="versioning-on-delete-operations"></a>Controle de versão em operações de exclusão

Quando você exclui um blob, a versão atual do blob torna-se uma versão anterior e o blob de base é excluído. Todas as versões anteriores do blob são preservadas quando o blob é excluído.

Chamar a operação [excluir blob](/rest/api/storageservices/delete-blob) sem uma ID de versão exclui o blob de base. Para excluir uma versão específica, forneça a ID dessa versão na operação de exclusão.

O diagrama a seguir mostra o efeito de uma operação de exclusão em um blob com versão:

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="Diagrama mostrando a exclusão de blob com versão.":::

Gravar novos dados no blob cria uma nova versão do blob. As versões existentes não são afetadas, conforme mostrado no diagrama a seguir.

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="Diagrama mostrando a recriação de blob com versão após a exclusão.":::

### <a name="blob-types"></a>Tipos de blobs

Quando o controle de versão de blob está habilitado para uma conta de armazenamento, todas as operações de gravação e exclusão em blobs de bloco disparam a criação de uma nova versão, com exceção da operação [Put Block](/rest/api/storageservices/put-block) .

Para BLOBs de páginas e blobs de acréscimo, apenas um subconjunto de operações de gravação e exclusão aciona a criação de uma versão. Essas operações incluem:

- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Block List](/rest/api/storageservices/put-block-list)
- [Excluir blob](/rest/api/storageservices/delete-blob)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Copiar blob](/rest/api/storageservices/copy-blob)

As operações a seguir não disparam a criação de uma nova versão. Para capturar alterações dessas operações, faça um instantâneo manual:

- [Colocar página](/rest/api/storageservices/put-page) (blob de página)
- [Anexar bloco](/rest/api/storageservices/append-block) (blob de acréscimo)

Todas as versões de um blob devem ser do mesmo tipo de BLOB. Se um blob tiver versões anteriores, você não poderá substituir um blob de um tipo por outro tipo, a menos que exclua primeiro o blob e todas as suas versões.

### <a name="access-tiers"></a>Níveis de acesso

Você pode mover qualquer versão de um blob de blocos, incluindo a versão atual, para uma camada de acesso de blob diferente chamando a operação [definir camada de blob](/rest/api/storageservices/set-blob-tier) . Você pode aproveitar os preços de capacidade mais baixa movendo versões mais antigas de um blob para a camada fria ou de arquivo morto. Para obter mais informações, consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](storage-blob-storage-tiers.md).

Para automatizar o processo de mover BLOBs de blocos para a camada apropriada, use o gerenciamento do ciclo de vida do blob. Para obter mais informações sobre o gerenciamento do ciclo de vida, consulte [gerenciar o ciclo de vida do armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md).

## <a name="enable-or-disable-blob-versioning"></a>Habilitar ou desabilitar o controle de versão de BLOB

Para saber como habilitar ou desabilitar o controle de versão de BLOB, consulte [habilitar e gerenciar o controle de versão de blob](versioning-enable.md).

Desabilitar o controle de versão de BLOB não exclui BLOBs, versões ou instantâneos existentes. Quando você desativa o controle de versão de BLOB, todas as versões existentes permanecem acessíveis em sua conta de armazenamento. Nenhuma nova versão é criada subsequentemente.

Se um blob foi criado ou modificado depois que o controle de versão foi desabilitado na conta de armazenamento, a substituição do blob cria uma nova versão. O blob atualizado não é mais a versão atual e não tem uma ID de versão. Todas as atualizações subsequentes para o blob substituirão seus dados sem salvar o estado anterior.

Você pode ler ou excluir versões usando a ID de versão após a desabilitação do controle de versão. Você também pode listar as versões de um blob depois que o controle de versão está desabilitado.

O diagrama a seguir mostra como a modificação de um blob após o controle de versão é desabilitada cria um blob sem controle de versão. Todas as versões existentes associadas ao blob persistem.

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="Diagrama mostrando o blob de base modificado após a desabilitação do controle de versão.":::

## <a name="blob-versioning-and-soft-delete"></a>Controle de versão e exclusão reversível do blob

O controle de versão de BLOB e de exclusão reversível de blob funcionam juntos para fornecer proteção de dados ideal. Ao habilitar a exclusão reversível, você especifica por quanto tempo o armazenamento do Azure deve reter um blob excluído por software. Qualquer versão de blob com exclusão reversível permanece no sistema e pode ser reexcluída dentro do período de retenção de exclusão reversível. Para obter mais informações sobre exclusão reversível de BLOB, consulte [exclusão reversível para BLOBs de armazenamento do Azure](./soft-delete-blob-overview.md).

### <a name="deleting-a-blob-or-version"></a>Excluindo um BLOB ou uma versão

A exclusão reversível oferece proteção adicional para excluir versões de BLOB. Se o controle de versão e a exclusão reversível estiverem habilitados na conta de armazenamento, quando você excluir um blob, o armazenamento do Azure criará uma nova versão para salvar o estado do blob imediatamente antes da exclusão e excluirá a versão atual. A nova versão não é excluída de maneira reversível e não é removida quando o período de retenção de exclusão reversível expira.

Quando você exclui uma versão anterior do blob, a versão é excluída de maneira reversível. A versão excluída reversível é mantida durante todo o período de retenção especificado nas configurações de exclusão reversível da conta de armazenamento e é excluída permanentemente quando o período de retenção de exclusão reversível expira.

Para remover uma versão anterior de um blob, exclua-o explicitamente especificando a ID da versão.

O diagrama a seguir mostra o que acontece quando você exclui um BLOB ou uma versão de BLOB.

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="Diagrama mostrando a exclusão de uma versão com exclusão reversível habilitada.":::

Se o controle de versão e a exclusão reversível estiverem habilitados em uma conta de armazenamento, nenhum instantâneo excluído por software será criado quando uma versão BLOB ou blob for modificada ou excluída.

### <a name="restoring-a-soft-deleted-version"></a>Restaurando uma versão excluída de maneira reversível

Você pode restaurar uma versão de blob com exclusão reversível chamando a operação [restaurar blob](/rest/api/storageservices/undelete-blob) na versão enquanto o período de retenção de exclusão reversível está em vigor. A operação restaurar **blob** restaura todas as versões com exclusão reversível do blob.

A restauração de versões com exclusão reversível com a operação **restaurar blob** não promove nenhuma versão para a versão atual. Para restaurar a versão atual, primeiro restaure todas as versões excluídas por software e, em seguida, use a operação [copiar blob](/rest/api/storageservices/copy-blob) para copiar uma versão anterior para restaurar o blob.

O diagrama a seguir mostra como restaurar versões de blob com exclusão reversível com a operação **restaurar blob** e como restaurar a versão atual do blob com a operação **copiar blob** .

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="Diagrama mostrando como restaurar versões com exclusão reversível.":::

Depois que o período de retenção de exclusão reversível tiver decorrido, todas as versões de blob excluídas por software serão excluídas permanentemente.

## <a name="blob-versioning-and-blob-snapshots"></a>Controle de versão do blob e instantâneos de BLOB

Um instantâneo de blob é uma cópia somente leitura de um blob que é obtido em um ponto específico no tempo. Instantâneos de BLOB e versões de blob são semelhantes, mas um instantâneo é criado manualmente por você ou seu aplicativo, enquanto uma versão de blob é criada automaticamente em uma operação de gravação ou exclusão quando o controle de versão de blob está habilitado para sua conta de armazenamento.

> [!IMPORTANT]
> A Microsoft recomenda que, depois de habilitar o controle de versão de BLOB, você também atualize seu aplicativo para parar de tirar instantâneos de blobs de blocos. Se o controle de versão estiver habilitado para sua conta de armazenamento, todas as atualizações e exclusões de blob de blocos serão capturadas e preservadas por versões. A criação de instantâneos não oferece nenhuma proteção adicional para seus dados de blob de blocos se o controle de versão de blob estiver habilitado e pode aumentar os custos e a complexidade do aplicativo.

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>Instantâneo de um blob quando o controle de versão está habilitado

Embora não seja recomendado, você pode tirar um instantâneo de um blob que também tem controle de versão. Se você não puder atualizar seu aplicativo para parar de tirar instantâneos de BLOBs quando habilitar o controle de versão, seu aplicativo poderá dar suporte a instantâneos e versões.

Quando você tira um instantâneo de um blob com versão, uma nova versão é criada ao mesmo tempo em que o instantâneo é criado. Uma nova versão atual também é criada quando um instantâneo é tirado.

O diagrama a seguir mostra o que acontece quando você tira um instantâneo de um blob com versão. No diagrama, as versões de BLOB e os instantâneos com a ID de versão 2 e 3 contêm dados idênticos.

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="Diagrama que mostra instantâneos de um blob com versão.":::

## <a name="authorize-operations-on-blob-versions"></a>Autorizar operações em versões de BLOB

Você pode autorizar o acesso a versões de BLOB usando uma das seguintes abordagens:

- Usando o Azure RBAC (controle de acesso baseado em função) para conceder permissões a uma entidade de segurança do Azure Active Directory (Azure AD). A Microsoft recomenda usar o Azure AD para segurança superior e facilidade de uso. Para obter mais informações sobre como usar o Azure AD com operações de BLOB, consulte [autorizar o acesso a BLOBs e filas usando o Azure Active Directory](../common/storage-auth-aad.md).
- Usando uma SAS (assinatura de acesso compartilhado) para delegar o acesso a versões de BLOB. Especifique a ID da versão para o tipo de recurso assinado `bv` , que representa uma versão de BLOB, para criar um token SAS para operações em uma versão específica. Para obter mais informações sobre assinaturas de acesso compartilhado, confira [Conceder acesso limitado a recursos de Armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../common/storage-sas-overview.md).
- Usando as chaves de acesso da conta para autorizar operações em versões de blob com chave compartilhada. Para obter mais informações, consulte [Autorizar com Chave Compartilhada](/rest/api/storageservices/authorize-with-shared-key).

O controle de versão de blob foi projetado para proteger seus dados contra exclusão acidental ou mal-intencionada. Para aprimorar a proteção, a exclusão de uma versão de blob requer permissões especiais. As seções a seguir descrevem as permissões necessárias para excluir uma versão de BLOB.

### <a name="azure-rbac-action-to-delete-a-blob-version"></a>Ação do RBAC do Azure para excluir uma versão de BLOB

A tabela a seguir mostra quais ações do RBAC do Azure dão suporte à exclusão de um BLOB ou uma versão de BLOB.

| Description | Operação do serviço blob | Ação de dados RBAC do Azure necessária | Suporte de função interna do Azure |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| Excluindo a versão atual do blob | Delete Blob | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete** | Colaborador de dados de blob de armazenamento |
| Excluindo uma versão | Delete Blob | **Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/deleteBlobVersion/ação** | Proprietário de Dados do Blob de Armazenamento |

### <a name="shared-access-signature-sas-parameters"></a>Parâmetros de assinatura de acesso compartilhado (SAS)

O recurso assinado para uma versão de blob é `bv` . Para obter mais informações, consulte [criar uma SAS de serviço](/rest/api/storageservices/create-service-sas) ou [criar uma SAS de delegação de usuário](/rest/api/storageservices/create-user-delegation-sas).

A tabela a seguir mostra a permissão necessária em uma SAS para excluir uma versão de BLOB.

| **Permissão** | **Símbolo de URI** | **Operações permitidas** |
|----------------|----------------|------------------------|
| Excluir         | x              | Excluir uma versão de BLOB. |

## <a name="pricing-and-billing"></a>Preços e cobrança

Habilitar o controle de versão de blob pode resultar em encargos de armazenamento de dados adicionais para sua conta. Ao projetar o aplicativo, é importante estar ciente de como essas cobranças podem aumentar para que você possa minimizar custos.

As versões de BLOB, como instantâneos de BLOB, são cobradas com a mesma taxa que os dados ativos. Como as versões são cobradas depende se você definiu explicitamente a camada para o blob de base ou para qualquer uma de suas versões (ou instantâneos). Para saber mais sobre as camadas de blobs, confira [Armazenamento de Blobs do Azure: camadas de acesso quente, frio e de arquivamento](storage-blob-storage-tiers.md).

Se você não alterou uma camada de BLOB ou de versão, será cobrado por blocos exclusivos de dados entre esse BLOB, suas versões e quaisquer instantâneos que ele possa ter. Para obter mais informações, consulte [cobrança quando a camada de BLOB não foi definida explicitamente](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Se você alterou uma camada de BLOB ou de versão, será cobrado pelo objeto inteiro, independentemente de o blob e a versão serem eventualmente na mesma camada novamente. Para obter mais informações, consulte [cobrança quando a camada de blob foi definida explicitamente](#billing-when-the-blob-tier-has-been-explicitly-set).

> [!NOTE]
> A habilitação do controle de versão de dados frequentemente substituídos pode resultar em encargos de capacidade de armazenamento maiores e maior latência durante as operações de listagem. Para atenuar essas preocupações, armazene dados frequentemente substituídos em uma conta de armazenamento separada com o controle de versão desabilitado.

Para obter mais informações sobre detalhes de cobrança para instantâneos de BLOB, consulte [instantâneos de blob](snapshots-overview.md).

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Cobrança quando a camada de BLOB não foi definida explicitamente

Se você não definiu explicitamente a camada de BLOB para um blob de base ou qualquer uma de suas versões, você será cobrado por blocos ou páginas exclusivos no BLOB, suas versões e quaisquer instantâneos que ele possa ter. Os dados compartilhados em um blob e suas versões são cobrados apenas uma vez. Quando um blob é atualizado, os dados em um blob de base divergem dos dados armazenados em suas versões e os dados exclusivos são cobrados por bloco ou página.

Quando você substituir um bloco em um blob de blocos, esse bloco será cobrado subsequentemente como um único bloco. Isso é verdadeiro mesmo se o bloco tiver a mesma ID de bloco e os mesmos dados que ela tem na versão anterior. Depois que o bloco for confirmado novamente, ele divergirá de sua contraparte na versão anterior e você será cobrado por seus dados. O mesmo se aplica para uma página em um blob de páginas é atualizada com os dados idênticos.

O armazenamento de BLOBs não tem meios para determinar se dois blocos contêm dados idênticos. Cada bloco que é carregado e confirmado é tratado como exclusivo, mesmo se tiver os mesmos dados e a mesma ID de bloco. Como as cobranças se acumulam para blocos exclusivos, é importante ter em mente que a atualização de um blob quando o controle de versão está habilitado resultará em blocos exclusivos adicionais e cobranças adicionais.

Quando o controle de versão de blob está habilitado, chame operações de atualização em blobs de blocos para que eles atualizem o número mínimo possível de blocos. As operações de gravação que permitem um controle refinado sobre os blocos são [colocar bloco](/rest/api/storageservices/put-block) e [colocar lista de blocos](/rest/api/storageservices/put-block-list). A operação [Put Blob](/rest/api/storageservices/put-blob) , por outro lado, substitui todo o conteúdo de um blob e, portanto, pode levar a encargos adicionais.

Os cenários a seguir demonstram como as cobranças se acumulam para um blob de blocos e suas versões quando a camada de BLOB não foi definida explicitamente.

#### <a name="scenario-1"></a>Cenário 1

No cenário 1, o blob tem uma versão anterior. O BLOB não foi atualizado desde que a versão foi criada, portanto, os encargos são incorridos apenas para os blocos exclusivos 1, 2 e 3.

![Diagrama 1 mostrando a cobrança de blocos exclusivos no blob de base e na versão anterior.](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>Cenário 2

No cenário 2, um bloco (bloco 3 no diagrama) no blob foi atualizado. Embora o bloco atualizado contenha os mesmos dados e a mesma ID, ele não é o mesmo que o bloco 3 na versão anterior. Como resultado, a conta é cobrada por quatro blocos.

![Diagrama 2 mostrando a cobrança para blocos exclusivos no blob de base e na versão anterior.](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>Cenário 3

No cenário 3, o blob foi atualizado, mas a versão não. O bloco 3 foi substituído pelo bloco 4 no blob de base, mas a versão anterior ainda reflete o bloco 3. Como resultado, a conta é cobrada por quatro blocos.

![Diagrama 3 mostrando a cobrança de blocos exclusivos no blob de base e na versão anterior.](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>Cenário 4

No cenário 4, o blob de base foi totalmente atualizado e não contém nenhum dos seus blocos originais. Como resultado, a conta é cobrada por todos os oito blocos exclusivos &mdash; quatro no blob de base e quatro na versão anterior. Esse cenário pode ocorrer se você estiver gravando em um blob com a operação [Put Blob](/rest/api/storageservices/put-blob) , pois ele substitui todo o conteúdo do blob de base.

![Diagrama 4 mostrando a cobrança de blocos exclusivos no blob de base e na versão anterior.](./media/versioning-overview/versions-billing-scenario-4.png)

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Cobrança quando a camada de blob foi definida explicitamente

Se você definiu explicitamente a camada de BLOB para um BLOB ou versão (ou instantâneo), será cobrado pelo tamanho completo do conteúdo do objeto na nova camada, independentemente de ele compartilhar blocos com um objeto na camada original. Você também é cobrado pelo tamanho completo do conteúdo da versão mais antiga na camada original. Quaisquer outras versões anteriores ou instantâneos que permanecem na camada original são cobrados por blocos exclusivos que podem compartilhar, conforme descrito em [cobrança quando a camada de BLOB não foi definida explicitamente](#billing-when-the-blob-tier-has-not-been-explicitly-set).

#### <a name="moving-a-blob-to-a-new-tier"></a>Movendo um blob para uma nova camada

A tabela a seguir descreve o comportamento de cobrança para um BLOB ou uma versão quando ele é movido para uma nova camada.

| Quando a camada de blob está definida explicitamente em... | Em seguida, você será cobrado por... |
|-|-|
| Um blob de base com uma versão anterior | O blob de base na nova camada e a versão mais antiga na camada original, além de quaisquer blocos exclusivos em outras versões. <sup>1</sup> |
| Um blob de base com uma versão anterior e um instantâneo | O blob de base na nova camada, a versão mais antiga na camada original e o instantâneo mais antigo na camada original, além de quaisquer blocos exclusivos em outras versões ou instantâneos<sup>1</sup>. |
| Uma versão anterior | A versão na nova camada e o blob de base na camada original, além de quaisquer blocos exclusivos em outras versões. <sup>1</sup> |

<sup>1</sup> Se houver outras versões ou instantâneos anteriores que não foram movidos da camada original, essas versões ou instantâneos serão cobrados com base no número de blocos exclusivos que eles contêm, conforme descrito em [cobrança quando a camada de BLOB não tiver sido definida explicitamente](#billing-when-the-blob-tier-has-not-been-explicitly-set).

O diagrama a seguir ilustra como os objetos são cobrados quando um blob com versão é movido para uma camada diferente.

:::image type="content" source="media/versioning-overview/versioning-billing-tiers.png" alt-text="Diagrama mostrando como os objetos são cobrados quando um blob com versão é explicitamente em camadas.":::

A definição explícita da camada para um blob, versão ou instantâneo não pode ser desfeita. Se você mover um blob para uma nova camada e, em seguida, movê-lo de volta para sua camada original, você será cobrado pelo tamanho completo do conteúdo do objeto, mesmo se ele compartilhar blocos com outros objetos na camada original.

As operações que definem explicitamente a camada de um blob, versão ou instantâneo incluem:

- [Definir camada do blob](/rest/api/storageservices/set-blob-tier)
- [Colocar blob](/rest/api/storageservices/put-blob) com a camada especificada
- [Colocar lista de blocos](/rest/api/storageservices/put-block-list) com a camada especificada
- [Copiar blob](/rest/api/storageservices/copy-blob) com camada especificada

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Excluindo um blob quando a exclusão reversível está habilitada

Quando a exclusão reversível de blob estiver habilitada, se você excluir ou substituir um blob de base que teve sua camada definida explicitamente, todas as versões anteriores do blob excluído reversível serão cobradas com tamanho de conteúdo completo. Para obter mais informações sobre como o controle de versão do blob e a exclusão reversível funcionam juntos, consulte [controle de versão e exclusão reversível do blob](#blob-versioning-and-soft-delete).

A tabela a seguir descreve o comportamento de cobrança para um blob que é excluído de maneira reversível, dependendo se o controle de versão está habilitado ou desabilitado. Quando o controle de versão está habilitado, uma versão é criada quando um blob é excluído de maneira reversível. Quando o controle de versão está desabilitado, a exclusão reversível de um blob cria um instantâneo de exclusão reversível.

| Quando você substitui um blob de base pela sua camada definida explicitamente... | Em seguida, você será cobrado por... |
|-|-|
| Se a exclusão reversível de BLOB e o controle de versão estiverem habilitados | Todas as versões existentes com tamanho de conteúdo completo, independentemente da camada. |
| Se a exclusão reversível de blob estiver habilitada, mas o controle de versão estiver desabilitado | Todos os instantâneos de exclusão reversível existentes com tamanho de conteúdo completo, independentemente da camada. |

## <a name="see-also"></a>Consulte também

- [Habilitar e gerenciar o controle de versão de blob](versioning-enable.md)
- [Criando um instantâneo de um blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Exclusão reversível para BLOBs de armazenamento do Azure](./soft-delete-blob-overview.md)