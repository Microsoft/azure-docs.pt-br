---
title: Instantâneos de BLOB
titleSuffix: Azure Storage
description: Entenda como os instantâneos de blob funcionam e como eles são cobrados.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/02/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: debce0a1b4c09bb89cdceb1cd29e59e1976c939a
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539138"
---
# <a name="blob-snapshots"></a>Instantâneos de BLOB

Um instantâneo é uma versão somente leitura de um blob capturada em um momento no tempo.

> [!NOTE]
> O controle de versão de blob oferece uma maneira superior de manter versões anteriores de um blob. Para obter mais informações, consulte [controle de versão de blob](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Sobre instantâneos de BLOB

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

O instantâneo de um blob é idêntico ao respectivo blob de base, exceto pelo fato de que o URI do blob tem um valor **DateTime** para indicar a hora em que o instantâneo foi criado. Por exemplo, se um URI de blob de páginas for `http://storagesample.core.blob.windows.net/mydrives/myvhd`, o URI do instantâneo será semelhante a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Todos os instantâneos compartilham o URI do blob de base. A única diferença entre o blob de base e o instantâneo é o acréscimo do valor **DateTime** .

Um blob pode ter qualquer número de instantâneos. Os instantâneos persistem até serem explicitamente excluídos, independentemente ou como parte de uma operação de [exclusão de blob](/rest/api/storageservices/delete-blob) para o blob de base. Você pode enumerar os instantâneos associados ao blob de base para acompanhar seus instantâneos atuais.

Quando você cria um instantâneo de um blob, as propriedades do sistema são copiadas para o instantâneo com os mesmos valores. Os metadados do blob de base também são copiados no instantâneo, a menos que você especifique metadados separados para o instantâneo ao criá-lo. Depois de criar um instantâneo, você pode lê-lo, copiá-lo ou excluí-lo, mas não é possível modificá-lo.

Quaisquer concessões associadas ao blob básico não afetam o instantâneo. Não é possível adquirir uma concessão em um instantâneo.

Um arquivo VHD é usado para armazenar as informações atuais e o status de um disco da VM. Você pode desanexar um disco de dentro da VM ou desligar a VM e, em seguida, tirar um instantâneo de seu arquivo VHD. É possível usar esse arquivo de instantâneo posteriormente para recuperar o arquivo VHD nesse ponto no tempo e recriar a VM.

## <a name="understand-how-snapshots-accrue-charges"></a>Entender como instantâneos acumulam cobranças

### <a name="important-billing-considerations"></a>Considerações importantes sobre cobrança

A lista a seguir inclui os principais pontos a considerar ao criar um instantâneo.

- Sua conta de armazenamento é cobrada por páginas ou blocos exclusivos, estejam eles no blob ou no instantâneo. Sua conta não incorre em cobranças adicionais para instantâneos associados a um blob até que você atualize o blob no qual eles se baseiam. Depois de atualizar o blob de base, ele divergirá dos respectivos instantâneos. Quando isso acontece, você é cobrado por páginas ou blocos exclusivos em cada blob ou instantâneo.
- Quando você substituir um bloco em um blob de blocos, esse bloco será cobrado subsequentemente como um único bloco. Isso é verdadeiro mesmo se o bloco tiver a mesma ID de bloco e os mesmos dados que tem no instantâneo. Depois que o bloco for confirmado novamente, ele divergirá de sua contraparte em qualquer instantâneo e você será cobrado por seus dados. O mesmo se aplica para uma página em um blob de páginas é atualizada com os dados idênticos.
- Atualizar um blob de bloco chamando um método que substitui todo o conteúdo do blob substituirá todos os blocos no BLOB. Se você tiver um instantâneo associado a esse blob, todos os blocos no blob de base e o instantâneo agora divergirão e você será cobrado por todos os blocos em ambos os blobs. Isso é verdadeiro mesmo se os dados no blob de base e o instantâneo permanecerem idênticos.
- O serviço Blob do Azure não tem um meio para determinar se dois blocos contêm dados idênticos. Cada bloco que é carregado e confirmado é tratado como exclusivo, mesmo se tiver os mesmos dados e a mesma ID de bloco. Já que as cobranças se acumulam para blocos exclusivos, é importante considerar que a atualização de um blob que tenha um instantâneo resulta em blocos exclusivos adicionais e cobranças adicionais.

### <a name="minimize-costs-with-snapshot-management"></a>Minimizar os custos com o gerenciamento de instantâneos

É recomendável gerenciar seus instantâneos com cuidado para evitar cobranças extras. Você pode seguir estas práticas recomendadas para ajudar a minimizar os custos incorridos pelo armazenamento de seus instantâneos:

- Exclua e recrie instantâneos associados a um blob sempre que você atualiza o blob, mesmo se você estiver atualizando com dados idênticos, a menos que o design do seu aplicativo requer que você mantenha os instantâneos. Ao excluir e recriar os instantâneos do blob, você pode garantir que o blob e os instantâneos não divirjam.
- Se você estiver mantendo instantâneos para um blob, evite chamar métodos que substituem todo o blob ao atualizar o blob. Em vez disso, atualize o menor número possível de blocos para manter os custos baixos.

### <a name="snapshot-billing-scenarios"></a>Cenários de cobrança de instantâneo

Os cenários a seguir demonstram como cobranças se acumulam para um blob de blocos e seus instantâneos.

## <a name="pricing-and-billing"></a>Preços e cobrança

Criar um instantâneo, que é uma cópia somente leitura de um blob, pode resultar em cobranças de armazenamento de dados adicionais para sua conta. Ao projetar o aplicativo, é importante estar ciente de como essas cobranças podem aumentar para que você possa minimizar custos.

Instantâneos de BLOB, como versões de BLOB, são cobrados com a mesma taxa de dados ativos. O modo como os instantâneos são cobrados depende se você definiu explicitamente a camada para o blob de base ou para qualquer um de seus instantâneos (ou versões). Para saber mais sobre as camadas de blobs, confira [Armazenamento de Blobs do Azure: camadas de acesso quente, frio e de arquivamento](storage-blob-storage-tiers.md).

Se você não tiver alterado uma camada de BLOB ou de instantâneo, será cobrado por blocos exclusivos de dados entre esse BLOB, seus instantâneos e todas as versões que ele possa ter. Para obter mais informações, consulte [cobrança quando a camada de BLOB não foi definida explicitamente](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Se você alterou uma camada de BLOB ou instantâneo, será cobrado pelo objeto inteiro, independentemente de o blob e o instantâneo estarem eventualmente na mesma camada novamente. Para obter mais informações, consulte [cobrança quando a camada de blob foi definida explicitamente](#billing-when-the-blob-tier-has-been-explicitly-set).

Para obter mais informações sobre detalhes de cobrança para versões de BLOB, consulte [controle de versão de blob](versioning-overview.md).

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Cobrança quando a camada de BLOB não foi definida explicitamente

Se você não definiu explicitamente a camada de BLOB para um blob de base ou qualquer um de seus instantâneos, você será cobrado por blocos ou páginas exclusivos no BLOB, seus instantâneos e quaisquer versões que ele possa ter. Os dados que são compartilhados em um blob e seus instantâneos são cobrados apenas uma vez. Quando um blob é atualizado, os dados em um blob de base divergem dos dados armazenados em seus instantâneos e os dados exclusivos são cobrados por bloco ou página.

Quando você substituir um bloco em um blob de blocos, esse bloco será cobrado subsequentemente como um único bloco. Isso é verdadeiro mesmo se o bloco tiver a mesma ID de bloco e os mesmos dados que tem no instantâneo. Depois que o bloco for confirmado novamente, ele divergirá de sua contraparte no instantâneo e você será cobrado por seus dados. O mesmo se aplica para uma página em um blob de páginas é atualizada com os dados idênticos.

O armazenamento de BLOBs não tem meios para determinar se dois blocos contêm dados idênticos. Cada bloco que é carregado e confirmado é tratado como exclusivo, mesmo se tiver os mesmos dados e a mesma ID de bloco. Como as cobranças se acumulam para blocos exclusivos, é importante ter em mente que a atualização de um blob quando esse blob tem instantâneos ou versões resultará em blocos exclusivos adicionais e cobranças adicionais.

Quando um blob tem instantâneos, chame operações de atualização em blobs de blocos para que eles atualizem o número mínimo possível de blocos. As operações de gravação que permitem um controle refinado sobre os blocos são [colocar bloco](/rest/api/storageservices/put-block) e [colocar lista de blocos](/rest/api/storageservices/put-block-list). A operação [Put Blob](/rest/api/storageservices/put-blob) , por outro lado, substitui todo o conteúdo de um blob e, portanto, pode levar a encargos adicionais.

Os cenários a seguir demonstram como as cobranças se acumulam para um blob de blocos e seus instantâneos quando a camada de BLOB não foi definida explicitamente.

#### <a name="scenario-1"></a>Cenário 1

No cenário 1, o blob de base não foi atualizado depois que o instantâneo foi tirado, portanto, as cobranças incorrem apenas para os blocos exclusivos 1, 2 e 3.

![Diagrama 1 mostrando a cobrança de blocos exclusivos em blob de base e instantâneo.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Cenário 2

No cenário 2, o blob de base foi atualizado, mas o instantâneo, não. O bloco 3 foi atualizado e mesmo que ele contenha os mesmos dados e a mesma ID, não é igual ao bloco 3 no instantâneo. Como resultado, a conta é cobrada por quatro blocos.

![Diagrama 2 mostrando a cobrança para blocos exclusivos em blob de base e instantâneo.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Cenário 3

No cenário 3, o blob de base foi atualizado, mas o instantâneo, não. O bloco 3 foi substituído pelo bloco 4 no blob de base, mas o instantâneo ainda reflete o bloco 3. Como resultado, a conta é cobrada por quatro blocos.

![Diagrama 3 mostrando a cobrança de blocos exclusivos em blob de base e instantâneo.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Cenário 4

No cenário 4, o blob de base foi totalmente atualizado e não contém nenhum dos seus blocos originais. Como resultado, a conta é cobrada por todos os oito blocos exclusivos.

![Diagrama 4 mostrando a cobrança para blocos exclusivos em blob de base e instantâneo.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> Evite chamar métodos que substituem todo o blob e, em vez disso, atualize blocos individuais para manter os custos baixos.

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Cobrança quando a camada de blob foi definida explicitamente

Se você definiu explicitamente a camada de BLOB para um BLOB ou instantâneo (ou versão), será cobrado pelo tamanho completo do conteúdo do objeto na nova camada, independentemente de ele compartilhar blocos com um objeto na camada original. Você também é cobrado pelo tamanho completo do conteúdo da versão mais antiga na camada original. Todas as versões ou instantâneos que permanecem na camada original são cobrados por blocos exclusivos que podem compartilhar, conforme descrito em [cobrança quando a camada de BLOB não foi definida explicitamente](#billing-when-the-blob-tier-has-not-been-explicitly-set).

#### <a name="moving-a-blob-to-a-new-tier"></a>Movendo um blob para uma nova camada

A tabela a seguir descreve o comportamento de cobrança para um BLOB ou instantâneo quando ele é movido para uma nova camada.

| Quando a camada de blob está definida explicitamente em... | Em seguida, você será cobrado por... |
|-|-|
| Um blob de base com um instantâneo | O blob de base na nova camada e o instantâneo mais antigo na camada original, além de quaisquer blocos exclusivos em outros instantâneos. <sup>1</sup> |
| Um blob de base com uma versão anterior e um instantâneo | O blob de base na nova camada, a versão mais antiga na camada original e o instantâneo mais antigo na camada original, além de quaisquer blocos exclusivos em outras versões ou instantâneos<sup>1</sup>. |
| Um instantâneo | O instantâneo na nova camada e o blob de base na camada original, além de quaisquer blocos exclusivos em outros instantâneos. <sup>1</sup> |

<sup>1</sup> Se houver outras versões ou instantâneos anteriores que não foram movidos da camada original, essas versões ou instantâneos serão cobrados com base no número de blocos exclusivos que eles contêm, conforme descrito em [cobrança quando a camada de BLOB não tiver sido definida explicitamente](#billing-when-the-blob-tier-has-not-been-explicitly-set).

O diagrama a seguir ilustra como os objetos são cobrados quando um blob com instantâneos é movido para uma camada diferente.

:::image type="content" source="media/snapshots-overview/snapshot-billing-tiers.png" alt-text="Diagrama mostrando como os objetos são cobrados quando um blob com instantâneos é explicitamente em camadas.":::

A definição explícita da camada para um blob, versão ou instantâneo não pode ser desfeita. Se você mover um blob para uma nova camada e, em seguida, movê-lo de volta para sua camada original, você será cobrado pelo tamanho completo do conteúdo do objeto, mesmo se ele compartilhar blocos com outros objetos na camada original.

As operações que definem explicitamente a camada de um blob, versão ou instantâneo incluem:

- [Definir camada do blob](/rest/api/storageservices/set-blob-tier)
- [Colocar blob](/rest/api/storageservices/put-blob) com a camada especificada
- [Colocar lista de blocos](/rest/api/storageservices/put-block-list) com a camada especificada
- [Copiar blob](/rest/api/storageservices/copy-blob) com camada especificada

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Excluindo um blob quando a exclusão reversível está habilitada

Quando a exclusão reversível de blob estiver habilitada, se você excluir ou substituir um blob de base que teve sua camada definida explicitamente, todas as versões anteriores ou instantâneos do blob excluído de forma reversível serão cobrados com tamanho de conteúdo completo. Para obter mais informações sobre como o controle de versão do blob e a exclusão reversível funcionam juntos, consulte [controle de versão e exclusão reversível do blob](versioning-overview.md#blob-versioning-and-soft-delete).

A tabela a seguir descreve o comportamento de cobrança para um blob que é excluído de maneira reversível, dependendo se o controle de versão está habilitado ou desabilitado. Quando o controle de versão está habilitado, uma nova versão é criada quando um blob é excluído de maneira reversível. Quando o controle de versão está desabilitado, a exclusão reversível de um blob cria um instantâneo de exclusão reversível.

| Quando você substitui um blob de base pela sua camada definida explicitamente... | Em seguida, você será cobrado por... |
|-|-|
| Se a exclusão reversível de BLOB e o controle de versão estiverem habilitados | Todas as versões existentes com tamanho de conteúdo completo, independentemente da camada. |
| Se a exclusão reversível de blob estiver habilitada, mas o controle de versão estiver desabilitado | Todos os instantâneos de exclusão reversível existentes com tamanho de conteúdo completo, independentemente da camada. |

## <a name="next-steps"></a>Próximas etapas

- [Controle de versão de BLOB](versioning-overview.md)
- [Criar e gerenciar um instantâneo de blob no .NET](snapshots-manage-dotnet.md)
- [Faça backup dos discos não gerenciados de VM do Azure com instantâneos incrementais](../../virtual-machines/windows/incremental-snapshots.md)
