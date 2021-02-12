---
title: Exclusão reversível para blobs
titleSuffix: Azure Storage
description: A exclusão reversível para BLOBs protege seus dados para que você possa recuperar seus dados com mais facilidade quando eles forem modificados ou excluídos erroneamente por um aplicativo ou por outro usuário da conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: d380b9d6a20cbe28a8fc4b64179437cd31fd2937
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979277"
---
# <a name="soft-delete-for-blobs"></a>Exclusão reversível para blobs

A exclusão reversível para BLOBs protege seus dados contra acidentalmente ou excluídos de maneira acidental ou excluída. Quando a exclusão reversível para BLOBs está habilitada para uma conta de armazenamento, BLOBs, versões de BLOB e instantâneos nessa conta de armazenamento podem ser recuperados após serem excluídos, dentro de um período de retenção especificado por você.

Se houver uma possibilidade de que seus dados sejam acidentalmente modificados ou excluídos por um aplicativo ou outro usuário de conta de armazenamento, é recomendável ativar a exclusão reversível. Para obter mais informações sobre como habilitar a exclusão reversível, consulte [habilitar e gerenciar a exclusão reversível para BLOBs](./soft-delete-blob-enable.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-soft-delete-for-blobs"></a>Sobre exclusão reversível para BLOBs

Quando a exclusão reversível de BLOBs está habilitada em uma conta de armazenamento, você pode recuperar objetos depois que eles tiverem sido excluídos, dentro do período de retenção de dados especificado. Essa proteção se estende a qualquer BLOB (BLOBs de blocos, blobs de acréscimo ou BLOBs de páginas) que são apagados como resultado de uma substituição.

O diagrama a seguir mostra como um blob excluído pode ser restaurado quando a exclusão reversível de blob está habilitada:

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="Diagrama mostrando como um blob excluído por software pode ser restaurado":::

Se os dados em um BLOB ou instantâneo existente forem excluídos enquanto a exclusão reversível de blob estiver habilitada, mas o controle de versão do BLOB não estiver habilitado, um instantâneo com exclusão reversível será gerado para salvar o estado dos dados substituídos. Depois que o período de retenção especificado tiver expirado, o objeto será excluído permanentemente.

Se o controle de versão do blob e a exclusão reversível do blob estiverem habilitados na conta de armazenamento, a exclusão de um blob criará uma nova versão em vez de um instantâneo excluído por software. A nova versão não é excluída de maneira reversível e não é removida quando o período de retenção de exclusão reversível expira. As versões com exclusão reversível de um blob podem ser restauradas dentro do período de retenção chamando a operação [restaurar blob](/rest/api/storageservices/undelete-blob) . O blob pode ser restaurado subsequentemente de uma de suas versões chamando a operação de [cópia de blob](/rest/api/storageservices/copy-blob) . Para obter mais informações sobre como usar o controle de versão de BLOB e a exclusão reversível em conjunto, consulte [controle de versão e exclusão reversível](versioning-overview.md#blob-versioning-and-soft-delete)do blob.

Os objetos com exclusão reversível são invisíveis, a menos que explicitamente listados.

A exclusão reversível de blob é compatível com versões anteriores, portanto, você não precisa fazer nenhuma alteração em seus aplicativos para aproveitar as proteções que esse recurso proporciona. No entanto, a [recuperação de dados](#recovery) apresenta uma nova API **Restaurar Blob**.

A exclusão reversível de blob está disponível para contas de armazenamento de BLOB e novas, de uso geral, v1 e existente. Há suporte para os tipos de conta Standard e Premium. A exclusão reversível de blob está disponível para todas as camadas de armazenamento, incluindo quente, frio e arquivo. A exclusão reversível está disponível para discos não gerenciados, que são blobs de páginas nos bastidores, mas não estão disponíveis para discos gerenciados.

### <a name="configuration-settings"></a>Definições de configuração

Quando você cria uma nova conta, a exclusão reversível é desabilitada por padrão. A exclusão reversível também é desabilitada por padrão para contas de armazenamento existentes. Você pode habilitar ou desabilitar a exclusão reversível para uma conta de armazenamento a qualquer momento.

Ao habilitar a exclusão reversível, você deve configurar o período de retenção. O período de retenção indica o tempo de armazenamento e disponibilidade dos dados com exclusão reversível para recuperação. Para objetos que são explicitamente excluídos, o relógio do período de retenção é iniciado quando os dados são excluídos. Para versões com exclusão reversível ou instantâneos gerados pelo recurso de exclusão reversível quando os dados são substituídos, o relógio é iniciado quando a versão ou o instantâneo é gerado. O período de retenção pode estar entre 1 e 365 dias.

Você pode alterar o período de retenção de exclusão reversível a qualquer momento. Um período de retenção atualizado aplica-se apenas aos dados excluídos recentemente. Os dados excluídos anteriormente expiram com base no período de retenção que foi configurado quando esses dados foram excluídos. A tentativa de excluir um objeto com exclusão reversível não afeta sua hora de expiração.

Se você desabilitar a exclusão reversível, poderá continuar a acessar e recuperar dados com exclusão reversível em sua conta de armazenamento que foi salva enquanto o recurso estava habilitado.

### <a name="saving-deleted-data"></a>Salvando dados excluídos

A exclusão reversível preserva seus dados em muitos casos em que os objetos são excluídos ou substituídos.

Quando um blob é substituído usando **Put Blob**, **Put bloquear List** ou **Copy blob**, uma versão ou um instantâneo do estado do blob antes da operação de gravação é gerado automaticamente. Esse objeto é invisível, a menos que os objetos excluídos por software sejam explicitamente listados. Consulte a seção [Recuperação](#recovery) para saber como listar os objetos com exclusão reversível.

![Um diagrama que mostra como os instantâneos de BLOBs são armazenados à medida que são substituídos usando Put Blob, put bloquear lista ou copiar BLOB.](media/soft-delete-blob-overview/storage-blob-soft-delete-overwrite.png)

*Os dados com exclusão reversível estão em cinza, enquanto os dados ativos são azuis. Os dados gravados mais recentemente aparecem abaixo dos dados mais antigos. Quando B0 é substituído por B1, um instantâneo com exclusão reversível de B0 é gerado. Quando B1 é substituído por B2, um instantâneo com exclusão reversível de B1 é gerado.*

> [!NOTE]  
> A exclusão reversível só permite substituir a proteção para operações de cópia quando ela está habilitada para a conta do blob de destino.

> [!NOTE]  
> A exclusão reversível não permite substituir a proteção para blobs na camada de arquivos. Se um blob no arquivo for substituído por um novo blob em qualquer nível, o blob substituído ficará permanentemente expirado.

Quando **Excluir Blob** é chamado em um instantâneo, esse instantâneo é marcado como com exclusão reversível. Um novo instantâneo não é gerado.

![Um diagrama que mostra como instantâneos de BLOBs são excluídos de forma reversível ao usar excluir BLOB.](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Os dados com exclusão reversível estão em cinza, enquanto os dados ativos são azuis. Os dados gravados mais recentemente aparecem abaixo dos dados mais antigos. Quando o **blob de instantâneo** é chamado, B0 torna-se um instantâneo e B1 é o estado ativo do blob. Quando o instantâneo B0 é excluído, ele é marcado como com exclusão reversível.*

Quando **Excluir Blob** é chamado em um blob de base (qualquer blob que não seja ele próprio um instantâneo), esse blob é marcado como com exclusão reversível. Conforme o comportamento anterior, chamar **Excluir Blob** em um blob que tenha instantâneos ativos retornará um erro. Chamar **Excluir Blob** em um blob com instantâneos com exclusão reversível não retornará um erro. Você ainda poderá excluir um blob e todos os seus instantâneos em única operação quando a exclusão reversível estiver ativada. Isso marca o blob de base e os instantâneos como com exclusão reversível.

![Um diagrama que mostra o que acontece quando o blog de exclusão é chamado em um blob de base.](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-include.png)

*Os dados com exclusão reversível estão em cinza, enquanto os dados ativos são azuis. Os dados gravados mais recentemente aparecem abaixo dos dados mais antigos. Aqui, uma chamada **delete blob** é feita para excluir B2 e todos os instantâneos associados. O blob ativo, B2 e todos os instantâneos associados são marcados como com exclusão reversível.*

> [!NOTE]  
> Quando um blob com exclusão reversível é substituído, um instantâneo com exclusão reversível do estado do blob antes da operação de gravação é gerado automaticamente. O novo blob herda a camada do blob substituído.

A exclusão reversível não salva seus dados em casos de exclusão de contêiner ou conta, nem quando os metadados de BLOB e as propriedades de blob são substituídos. Para proteger uma conta de armazenamento da exclusão, você pode configurar um bloqueio usando o Azure Resource Manager. Para obter mais informações, consulte o artigo Azure Resource Manager [Bloquear recursos para evitar alterações inesperadas](../../azure-resource-manager/management/lock-resources.md).

A tabela a seguir detalha o comportamento esperado quando a exclusão reversível é ativada:

| Operação de API REST | Tipo de recurso | Description | Alteração no comportamento |
|--------------------|---------------|-------------|--------------------|
| [Delete (excluir)](/rest/api/storagerp/StorageAccounts/Delete) | Conta | Exclui a conta de armazenamento, incluindo todos os contêineres e blobs que ela contém.                           | Nenhuma alteração. Contêineres e blobs na conta excluída não são recuperáveis. |
| [Delete Container](/rest/api/storageservices/delete-container) | Contêiner | Exclui o contêiner, incluindo todos os blobs que ele contém. | Nenhuma alteração. Os blobs no contêiner excluído não são recuperáveis. |
| [Put Blob](/rest/api/storageservices/put-blob) | Blobs de bloco, acréscimo e página | Cria um novo blob ou substitui um blob existente dentro de um contêiner | Se usado para substituir um blob existente, um instantâneo do estado do blob anterior à chamada é gerado automaticamente. Isso também se aplica a um blob com exclusão reversível anteriormente se e somente se ele for substituído por um blob do mesmo tipo (bloco, acréscimo ou página). Se ele for substituído por um blob de um tipo diferente, todos os dados com exclusão reversível existentes expirarão permanentemente. |
| [Excluir blob](/rest/api/storageservices/delete-blob) | Blobs de bloco, acréscimo e página | Marca um blob ou instantâneo de blob para exclusão. O blob ou o instantâneo é excluído depois durante a coleta de lixo | Se usado para excluir que um instantâneo de blob, esse instantâneo será marcado como com exclusão reversível. Se usado para excluir um blob, esse blob será marcado como com exclusão reversível. |
| [Copiar blob](/rest/api/storageservices/copy-blob) | Blobs de bloco, acréscimo e página | Copia um blob de origem para um blob de destino na mesma conta de armazenamento ou em outra conta de armazenamento. | Se usado para substituir um blob existente, um instantâneo do estado do blob anterior à chamada é gerado automaticamente. Isso também se aplica a um blob com exclusão reversível anteriormente se e somente se ele for substituído por um blob do mesmo tipo (bloco, acréscimo ou página). Se ele for substituído por um blob de um tipo diferente, todos os dados com exclusão reversível existentes expirarão permanentemente. |
| [Put Block](/rest/api/storageservices/put-block) | Blobs de bloco | Cria um novo bloco a ser confirmado como parte de um blob de bloco. | Se for usado para confirmar um bloco em um blob que está ativo, não haverá alteração. Se usado para confirmar um bloco para um blob com exclusão reversível, um novo blob será criado, e um instantâneo, gerado automaticamente para capturar o estado do blob com exclusão reversível. |
| [Put Block List](/rest/api/storageservices/put-block-list) | Blobs de bloco | Confirma um blob especificando o conjunto de IDs de bloco que compõem o blob de bloco. | Se usado para substituir um blob existente, um instantâneo do estado do blob anterior à chamada é gerado automaticamente. Isso também se aplica a um blob com exclusão reversível anteriormente se e somente se ele for um blob de blocos. Se ele for substituído por um blob de um tipo diferente, todos os dados com exclusão reversível existentes expirarão permanentemente. |
| [Colocar Página](/rest/api/storageservices/put-page) | Blobs de páginas | Grava um intervalo de páginas em um blob de páginas. | Nenhuma alteração. Os dados de blob de páginas substituídos ou limpos usando essa operação não são salvos e não são recuperáveis. |
| [Acrescentar Bloco](/rest/api/storageservices/append-block) | Blob de acréscimo | Grava um bloco de dados no final de um blob de acréscimo | Nenhuma alteração. |
| [Set Blob Properties](/rest/api/storageservices/set-blob-properties) | Blobs de bloco, acréscimo e página | Define valores para propriedades do sistema definidas para um blob. | Nenhuma alteração. As propriedades do blob substituído não são recuperáveis. |
| [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) | Blobs de bloco, acréscimo e página | Define metadados definidos pelo usuário para o blob especificado como um ou mais pares de nome-valor. | Nenhuma alteração. Os metadados de blob substituídos não são recuperáveis. |

É importante observar que chamar **Put Page** para substituir ou limpar intervalos de um blob de páginas não gerará automaticamente instantâneos. Os discos de máquina virtual são apoiados por blobs de página e usam a **página Put** para gravar dados.

### <a name="recovery"></a>Recuperação

Chamar a operação de [remover blob](/rest/api/storageservices/undelete-blob) em um blob de base com exclusão reversível restaura e todos os instantâneos de exclusão reversível associados como ativos. Chamar a operação **restaurar blob** em um blob de base ativo restaura todos os instantâneos com exclusão reversível associados como ativos. Quando instantâneos são restaurados como ativos, eles se assemelham a instantâneos gerados pelo usuário; eles não podem substituir o blob de base.

Para restaurar um blob para um instantâneo de exclusão reversível específico, você pode chamar **undelete blob** no blob de base. Em seguida, você pode copiar o instantâneo sobre o blob ativo agora. Você também pode copiar o instantâneo para um novo blob.

![Um diagrama que mostra o que acontece quando o blob de reexclusão é usado.](media/soft-delete-blob-overview/storage-blob-soft-delete-recover.png)

*Os dados com exclusão reversível estão em cinza, enquanto os dados ativos são azuis. Os dados gravados mais recentemente aparecem abaixo dos dados mais antigos. Aqui, o **blob** de restauração é chamado no blob B, restaurando assim o blob de base, B1 e todos os instantâneos associados, aqui apenas B0, como ativo. Na segunda etapa, B0 é copiado sobre o blob de base. Essa operação de cópia gera um instantâneo com exclusão reversível de B1.*

Para exibir blobs com exclusão reversível e instantâneos de blob, você pode optar por incluir dados excluídos em **Listar Blobs**. Você pode optar por exibir somente blobs de base com exclusão reversível ou incluir também instantâneos de blob com exclusão reversível. Para todos os dados com exclusão reversível, você pode exibir a hora quando os dados foram excluídos, bem como o número de dias antes da expiração permanente dos dados.

### <a name="example"></a>Exemplo

Veja a seguir a saída do console de um script .NET que carrega, substitui, faz o instantâneo, exclui e restaura um blob chamado *HelloWorld* quando a exclusão reversível está ativada:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Consulte a seção [Próximas etapas](#next-steps) seção para obter um ponteiro para o aplicativo que gerou essa saída.

## <a name="pricing-and-billing"></a>Preços e cobrança

Todos os dados com exclusão reversível são cobrados com a mesma taxa dos dados ativos. Você não será cobrado pelos dados que são excluídos permanentemente após o período de retenção configurado. Para obter mais detalhes sobre os instantâneos e como eles acumulam cobranças, consulte [noções básicas sobre como os instantâneos acumulam encargos](./snapshots-overview.md).

Você não será cobrado pelas transações relacionadas à geração automática de instantâneos. Você será cobrado por restaurar transações de **blob** na taxa de operações de gravação.

Para obter mais detalhes sobre preços para Armazenamento de Blob do Azure em geral, confira [Página de Preços do Armazenamento de Blob do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Quando você ativa a exclusão reversível inicialmente, a Microsoft recomenda usar um período de retenção curto para entender melhor como o recurso afetará sua fatura.

Habilitar a exclusão reversível para dados frequentemente substituídos pode resultar em encargos de capacidade de armazenamento maiores e maior latência ao listar BLOBs. Você pode reduzir esse custo e latência adicionais armazenando os dados frequentemente substituídos em uma conta de armazenamento separada em que a exclusão reversível está desabilitada.

## <a name="faq"></a>Perguntas frequentes

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Posso usar a API de definição de camada do blob para criar camadas de blobs com instantâneos que foram excluídos com exclusão reversível?

Sim. Os instantâneos com exclusão reversível permanecerão na camada original, mas o blob de base será movido para a nova camada.

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>As contas de armazenamento Premium têm um limite de 100 instantâneos por blob. A contagem de instantâneos com exclusão reversível vai além desse limite?

Não, a contagem de instantâneos com exclusão reversível não vai além desse limite.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Se eu excluir uma conta ou um contêiner inteiro com a exclusão reversível ativada, todos os blobs associados serão salvos?

Não, se você excluir uma conta ou um contêiner inteiro, todos os blobs associados serão excluídos permanentemente. Para obter mais informações sobre como proteger uma conta de armazenamento de ser acidentalmente excluída, consulte [Bloquear recursos para evitar alterações inesperadas](../../azure-resource-manager/management/lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Posso exibir as métricas de capacidade para os dados excluídos?

Os dados com exclusão reversível serão incluídos como parte de sua capacidade de conta de armazenamento total. Para obter mais informações sobre como controlar e monitorar a capacidade de armazenamento, consulte [análise de armazenamento](../common/storage-analytics.md).

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Consigo ler e copiar instantâneos com exclusão reversível de meu blob?  

Sim, mas você deve primeiro chamar Restaurar no blob.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>A exclusão reversível está disponível para discos de máquina virtual?  

A exclusão reversível está disponível para discos não gerenciados Premium e Standard, que são blobs de páginas nos bastidores. A exclusão reversível só ajudará a recuperar dados excluídos por **excluir blob**, **colocar blob**, **colocar lista de blocos** e operações de **copiar blob** . Os dados substituídos por uma chamada para **Colocar Página** não são recuperáveis.

Uma máquina virtual do Azure grava em um disco não gerenciado usando chamadas para **colocar página**, portanto, usar a exclusão reversível para desfazer gravações em um disco não gerenciado de uma VM do Azure não é um cenário com suporte.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Preciso alterar meus aplicativos existentes para usar a exclusão reversível?

É possível aproveitar a exclusão reversível, independentemente da versão de API que você está usando. No entanto, para listar e recuperar blobs com exclusão reversível e instantâneos de BLOB, você precisará usar a versão 2017-07-29 da [API REST do armazenamento do Azure](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) ou superior. A Microsoft recomenda sempre usar a versão mais recente da API de armazenamento do Azure.

## <a name="next-steps"></a>Próximas etapas

- [Habilitar exclusão reversível para blobs](./soft-delete-blob-enable.md)
- [Controle de versão de BLOB](versioning-overview.md)