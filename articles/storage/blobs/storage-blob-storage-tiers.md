---
title: Camadas de acesso para armazenamento de BLOBs do Azure – frequente, fria e arquivo morto
description: Leia sobre as camadas de acesso frequente, fria e de arquivo para o armazenamento de BLOBs do Azure. Examine as contas de armazenamento que dão suporte a camadas.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: klaasl
ms.openlocfilehash: 1a1cb8e1676405cbfbb3f4f61c86d8136b688b88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656831"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Camadas de acesso para armazenamento de BLOBs do Azure – frequente, fria e arquivo morto

O armazenamento do Azure oferece diferentes níveis de acesso, permitindo que você armazene dados de objeto de BLOB da maneira mais econômica. As camadas de acesso disponíveis incluem:

- Otimizado para o **armazenamento de dados** que são acessados com frequência.
-  Muito otimizado para armazenar dados que são acessados com pouca frequência e armazenados por pelo menos 30 dias.
- **Arquivado** -otimizado para armazenar dados que raramente são acessados e armazenados por pelo menos 180 dias com requisitos de latência flexíveis, na ordem de horas.

As seguintes considerações se aplicam às diferentes camadas de acesso:

- A camada de acesso pode ser definida em um blob durante ou após o carregamento.
- Apenas as camadas de acesso quente e frio podem ser definidas no nível da conta. A camada de acesso de arquivamento só pode ser definida no nível de BLOB.
- Os dados na camada de acesso frio têm disponibilidade ligeiramente menor, mas ainda têm alta durabilidade, latência de recuperação e características de taxa de transferência semelhantes aos dados ativos. Para dados frios, disponibilidade ligeiramente menor e custos de acesso mais altos são compensações aceitáveis para reduzir os custos gerais de armazenamento em comparação com os dados ativos. Para obter mais informações, veja [SLA para armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).
- Os dados na camada de acesso de arquivamento são armazenados offline. A camada de arquivo oferece os custos de armazenamento mais baixos, mas também os custos de acesso e a latência mais altos.
- As camadas quente e fria oferecem suporte a todas as opções de redundância. A camada de arquivo dá suporte apenas a LRS, GRS e RA-GRS.
- Os limites de armazenamento de dados são definidos no nível da conta e não por camada de acesso. Você pode optar por usar todo o limite em uma camada ou em todas as três camadas.

Os dados armazenados na nuvem aumentam em ritmo exponencial. Para gerenciar os custos de suas necessidades cada vez maiores de armazenamento, é útil organizar seus dados com base em atributos como frequência de acesso e período de retenção planejado para otimizar os custos. Os dados armazenados na nuvem podem ser diferentes com base em como são gerados, processados e acessados durante o tempo de vida deles. Alguns dados são ativamente acessados e modificados durante seu ciclo de vida. Alguns dados são acessados com frequência no início do seu tempo de vida, mas esse acesso cai drasticamente à medida que os dados envelhecem. Alguns dados permanecem ociosos na nuvem e raramente são acessos depois de armazenados, talvez nunca.

Cada um desses cenários de acesso a dados se beneficia de uma camada de acesso diferente que é otimizada para um padrão de acesso específico. Com as camadas de acesso frequente, esporádica e de arquivo morto, o armazenamento de BLOBs do Azure atende a essa necessidade de camadas de acesso diferenciado com modelos de preços separados.

Todas as ferramentas e bibliotecas de cliente a seguir dão suporte ao armazenamento de camadas e de arquivamento em nível de BLOB.

- Portal do Azure
- PowerShell
- Ferramentas de CLI do Azure
- Biblioteca de clientes do .NET
- Biblioteca de clientes Java
- Biblioteca de clientes Python
- Biblioteca de cliente Node.js

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Contas de armazenamento que dão suporte a camadas

As camadas de dados de armazenamento de objetos entre Hot, fria e arquivo morto têm suporte em contas de armazenamento de BLOBs e Uso Geral v2 (GPv2). As contas do Uso Geral V1 (GPv1) não dão suporte a camadas. Você pode converter facilmente suas contas de armazenamento de GPv1 ou BLOB existentes em contas do GPv2 por meio do portal do Azure. O GPv2 fornece novos preços e recursos para BLOBs, arquivos e filas. Alguns recursos e cortes de preços são oferecidos somente em contas do GPv2. Algumas cargas de trabalho podem ser mais caras em GPv2 do que GPv1. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).

As contas de armazenamento de BLOBs e GPv2 expõem o atributo **camada de acesso** no nível da conta. Esse atributo permite que você especifique a camada de acesso padrão para qualquer BLOB que não tenha definido explicitamente no nível do objeto. Para objetos com a camada definida explicitamente, a camada de conta não será aplicada. A camada de arquivo pode ser aplicada somente no nível do objeto. Você pode alternar entre as camadas de acesso a qualquer momento.

Use GPv2 em vez de contas de armazenamento de BLOBs para camadas. O GPv2 dá suporte a todos os recursos aos quais as contas de armazenamento de BLOBs dão suporte, além de muito mais. Os preços entre o armazenamento de BLOBs e o GPv2 são quase idênticos, mas alguns novos recursos e cortes de preços estão disponíveis apenas em contas do GPv2.

A estrutura de preços entre contas de GPv1 e GPv2 é diferente, e os clientes devem avaliar cuidadosamente antes de decidir usar contas de GPv2. Você pode converter facilmente uma conta existente de Armazenamento de Blobs ou de GPv1 em GPv2 por meio de um processo simples de um clique. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).

## <a name="hot-access-tier"></a>Camada de acesso quente

A camada de acesso quente tem custos de armazenamento maiores que as camadas de acesso aos arquivos e frio, mas tem os custos de acesso mais baixos. Os cenários de uso de exemplo para a camada de acesso quente incluem:

- Dados que estão em uso ativo ou que devem ser lidos e gravados com frequência
- Dados que são preparados para processamento e migração eventual para a camada de acesso fria

## <a name="cool-access-tier"></a>Camada de acesso frio

A camada de acesso frio tem custos de armazenamento mais baixos e custos de acesso mais altos em comparação com a de armazenamento quente. Essa camada destina-se aos dados que permanecerão na camada esporádica por pelo menos 30 dias. Os cenários de uso de exemplo para a camada de acesso frio incluem:

- Backup de curto prazo e recuperação de desastres
- Dados mais antigos não usados com frequência, mas que devem estar disponíveis imediatamente quando acessados
- Grandes conjuntos de dados que precisam ser armazenados de forma econômica, enquanto mais dados estão sendo coletados para processamento futuro

## <a name="archive-access-tier"></a>Camada de acesso aos arquivos

A camada de acesso de arquivamento tem o menor custo de armazenamento, mas os maiores custos de recuperação de dados em comparação com as camadas quentes e frias. Os dados devem permanecer na camada de arquivo por pelo menos 180 dias ou estar sujeitos a uma cobrança de exclusão antecipada. Os dados na camada de arquivo podem levar várias horas para serem recuperados, dependendo da prioridade de reidratação especificada. Para objetos pequenos, um reidratar de alta prioridade pode recuperar o objeto do arquivo em menos de uma hora. Consulte [dados de blob reidratar da camada de arquivo](storage-blob-rehydration.md) para saber mais.

Enquanto um blob está no armazenamento de arquivo morto, os dados de blob ficam offline e não podem ser lidos ou modificados. Para ler ou baixar um blob no arquivo morto, você deve primeiro reidratar-lo a uma camada online. Não é possível tirar instantâneos de um blob no armazenamento de arquivos. No entanto, os metadados de blob permanecem online e disponíveis, permitindo que você liste o blob, suas propriedades, metadados e marcas de índice de BLOB. Não é permitido definir ou modificar os metadados de blob no arquivo. No entanto, você pode definir e modificar as marcas de índice de BLOB. Para BLOBs no arquivo morto, as únicas operações válidas são [obter propriedades de blob](/rest/api/storageservices/get-blob-properties), [obter metadados de blob](/rest/api/storageservices/get-blob-metadata), [definir marcas de blob](/rest/api/storageservices/set-blob-tags), [obter marcas de blob](/rest/api/storageservices/get-blob-tags), [Localizar blobs por marcas](/rest/api/storageservices/find-blobs-by-tags), [listar BLOBs](/rest/api/storageservices/list-blobs), [definir camada de blob](/rest/api/storageservices/set-blob-tier), [copiar blob](/rest/api/storageservices/copy-blob)e [excluir blob](/rest/api/storageservices/delete-blob).

Os cenários de uso de exemplo para a camada de acesso de arquivamento incluem:

- Backup de longo prazo, backup secundário e conjuntos de dados de arquivamento 
- Dados originais (brutos) que devem ser preservados, mesmo após serem processados em formato final utilizável
- Dados de conformidade e arquivamento que precisam ser armazenados por muito tempo e nunca são acessados

> [!NOTE]
> A camada de arquivo morto não tem suporte para contas ZRS, GZRS ou RA-GZRS. A migração de LRS para GRS não terá suporte se a conta de armazenamento contiver BLOBs na camada de arquivo morto.

## <a name="account-level-tiering"></a>Camadas em nível de conta

Os BLOBs em todas as três camadas de acesso podem coexistir dentro da mesma conta. Qualquer BLOB que não tenha uma camada explicitamente atribuída infere a camada da configuração de nível de acesso da conta. Se a camada de acesso vier da conta, você verá a propriedade de blob **camada de acesso inferida** definida como "true", e a propriedade de BLOB da **camada de acesso** corresponderá à camada da conta. No portal do Azure, a propriedade _camada de acesso deduzida_ é exibida com a camada de acesso de blob como **ativa (inferida)** ou **fria (inferida)**.

A alteração da camada de acesso à conta se aplica a todos os objetos _inferidos da camada de acesso_ armazenados na conta que não têm um conjunto de camadas explícito. Se você alternar a camada de conta de quente para fria, você será cobrado pelas operações de gravação (por 10.000) para todos os BLOBs sem uma camada de conjunto somente nas contas GPv2. Não há nenhum encargo para essa alteração nas contas de armazenamento de BLOBs. Você será cobrado por operações de leitura (por 10.000) e por recuperação de dados (por GB) se alternar de fria para quente no armazenamento de BLOBs ou em contas de GPv2.

Somente as camadas de acesso quente e fria podem ser definidas como a camada de acesso de conta padrão. A camada arquivo só pode ser definida no nível do objeto. No upload de BLOB, você pode especificar a camada de acesso de sua escolha para ser quente, fria ou arquivo morto, independentemente da camada de conta padrão. Essa funcionalidade permite que você grave dados diretamente na camada de arquivo para obter economia de custos desde o momento em que cria dados no armazenamento de BLOBs.

## <a name="blob-level-tiering"></a>Camada no nível do blob

A camada no nível do blob permite que você carregue dados para a camada de acesso de sua escolha usando as operações [colocar blob](/rest/api/storageservices/put-blob) ou [colocar lista de blocos](/rest/api/storageservices/put-block-list) e alterar a camada dos dados no nível do objeto usando o recurso definir operação de [camada de blob](/rest/api/storageservices/set-blob-tier) ou gerenciamento de [ciclo de vida](#blob-lifecycle-management) . Você pode carregar dados para a camada de acesso necessária e alterar facilmente a camada de acesso de blob entre as camadas frequente, esporádica ou de arquivo morto conforme os padrões de uso mudam, sem precisar mover dados entre contas. Todas as solicitações de alteração de camada acontecem imediatamente e as alterações de camada entre a quente e a fria são instantâneas. Reidratar um blob da camada de arquivo pode levar várias horas.

A hora da última alteração na camada de blob é exposta por meio da propriedade do blob **Acessar Hora de Alteração da Camada**. O **tempo de alteração da camada de acesso** é uma propriedade de nível de BLOB e não é atualizado quando a camada de conta padrão é alterada. As propriedades de conta e de blob são separadas. Seria extremamente caro atualizar o **tempo de alteração da camada de acesso** em cada blob em uma conta de armazenamento sempre que a camada de acesso padrão da conta for alterada.

Ao substituir um blob na camada quente ou fria, o blob recém-criado herda a camada do blob que foi substituído, a menos que a nova camada de acesso de blob seja definida explicitamente na criação. Se um blob estiver na camada de arquivo morto, ele não poderá ser substituído, portanto, carregar o mesmo BLOB não será permitido nesse cenário.

> [!NOTE]
> O armazenamento de arquivos e as camadas no nível do blob só oferecem suporte aos blobs de bloco.

### <a name="blob-lifecycle-management"></a>Gerenciamento de ciclo de vida de blob

O gerenciamento do ciclo de vida do armazenamento de BLOBs oferece uma política avançada baseada em regras que você pode usar para fazer a transição de seus dados para a melhor camada de acesso e para expirar os dados no final do ciclo de vida. Consulte [otimizar os custos automatizando as camadas de acesso do armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md) para saber mais.

> [!NOTE]
> Os dados armazenados em uma conta de armazenamento de blob de blocos (desempenho Premium) atualmente não podem ser colocados em camadas para quente, frio ou arquivo morto usando [Set Blob Tier](/rest/api/storageservices/set-blob-tier) ou usando o gerenciamento de ciclo de vida do armazenamento de BLOBs do Azure.
> Para mover dados, você deve copiar blobs de forma síncrona da conta de armazenamento de blobs de blocos para a camada de acesso quente em uma conta diferente usando o [bloco Put da API de URL](/rest/api/storageservices/put-block-from-url) ou uma versão do AzCopy que dê suporte a essa API.
> A API **Colocar bloco pela URL** copia dados sincronicamente no servidor, o que significa que a chamada é concluída apenas depois que todos os dados são movidos do local do servidor de origem para o local de destino.

### <a name="blob-level-tiering-billing"></a>Cobrança da camada no nível do blob

Quando um blob é carregado ou movido entre as camadas, ele é cobrado com a taxa correspondente imediatamente após o carregamento ou a alteração da camada.

Quando um blob é movido para uma camada mais esporádica (frequente -> esporádico, frequente -> arquivos ou esporádico -> arquivos), a operação é cobrada como uma operação de gravação na camada de destino, em que os encargos pela operação de gravação (por 10.000) e gravação de dados (por GB) da camada de destino são aplicados.

Quando um blob é movido para uma camada mais quente (arquivo morto->frio, >quente ou frio >quente), a operação é cobrada como uma leitura da camada de origem, em que a operação de leitura (por 10.000) e os encargos de recuperação de dados (por GB) da camada de origem se aplicam. Os encargos de [exclusão antecipada](#cool-and-archive-early-deletion) de qualquer blob movido da camada fria ou de arquivo também podem ser aplicados. Os [dados de reidratar da camada de arquivamento](storage-blob-rehydration.md) levam tempo e os preços de arquivamento serão cobrados até que os dados sejam restaurados online e a camada de blob seja alterada para dinâmica ou fria.

A tabela a seguir resume como as alterações na camada são cobradas.

| | **Encargos de gravação (operação + acesso)** | **Ler encargos (operação + acesso)** |
| ---- | ----- | ----- |
| **Definir camada do blob** | frio de > quente<br> arquivo morto de > quente<br> arquivo de > frio | arquivo morto-> frio<br> arquivo morto-> quente<br> frio-> quente

### <a name="cool-and-archive-early-deletion"></a>Exclusão antecipada esporádica e de arquivo

Qualquer BLOB que é movido para a camada fria (somente contas GPv2) está sujeito a um período de exclusão antecipado de 30 dias. Qualquer blob movido para a camada de arquivo está sujeito a um período de exclusão antecipada de 180 dias. A cobrança é proporcional. Por exemplo, se um blob for movido para o arquivo morto e, em seguida, excluído ou movido para a camada quente após 45 dias, você será cobrado como uma taxa de exclusão inicial equivalente a 135 (180 menos 45) dias de armazenamento desse blob no arquivo morto.

Há alguns detalhes ao se mover entre as camadas frias e de arquivo morto:

1. Se um blob for inferido como frio com base na camada de acesso padrão da conta de armazenamento e o blob for movido para o arquivo morto, não haverá nenhum encargo de exclusão antecipado.
1. Se um blob for explicitamente movido para a camada fria e, em seguida, movido para o arquivo morto, a cobrança de exclusão antecipada se aplicará.

Calcule o tempo de exclusão antecipado usando a propriedade blob **modificada pela última vez** , se não houver nenhuma alteração na camada de acesso. Caso contrário, use quando a camada de acesso foi modificada pela última vez para fria ou arquivo morto exibindo a propriedade blob: **acesso-camada-alteração-tempo**. Para obter mais informações sobre as propriedades de blob, confira [Obter Propriedades de Blob](/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Comparando opções de armazenamento de blobs de blocos

A tabela a seguir mostra uma comparação do armazenamento de blobs de blocos de desempenho premium e as camadas de acesso frequente, fria e de arquivo.

|                                           | **Desempenho premium**   | **Camada quente** | **Camada fria**       | **Camada de arquivo**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Disponibilidade**                          | 99,9%                     | 99,9%        | 99%                 | Offline           |
| **Disponibilidade** <br> **(Leituras de RA-GRS)**  | N/D                       | 99,99%       | 99,9%               | Offline           |
| **Encargos de uso**                         | Custos de armazenamento mais altos, acesso menor e custo de transação | Custos de armazenamento maiores, custos de acesso e de transações menores | Custos de armazenamento menores, custos de acesso e de transações maiores | Custos de armazenamento mais baixos, custos de acesso e de transações mais altos |
| **Duração mínima de armazenamento**              | N/D                       | N/D          | 30 dias<sup>1</sup> | 180 dias
| **Latência** <br> **(Tempo até o primeiro byte)** | Milissegundos de dígito único | milissegundos | milissegundos        | horas<sup>2</sup> |

<sup>1</sup> os objetos na camada fria em contas GPv2 têm uma duração de retenção mínima de 30 dias. As contas de armazenamento de BLOBs não têm uma duração de retenção mínima para a camada fria.

<sup>2</sup> armazenamento de arquivos atualmente dá suporte a duas prioridades de reidratação, alta e Standard, oferecendo diferentes latências de recuperação e custos. Para obter mais informações, consulte [dados de blob reidratar da camada de arquivo morto](storage-blob-rehydration.md).

> [!NOTE]
> As contas de armazenamento de BLOBs dão suporte às mesmas metas de desempenho e escalabilidade que as contas de armazenamento de uso geral v2. Para obter mais informações, consulte [escalabilidade e metas de desempenho para o armazenamento de BLOBs](scalability-targets.md).

## <a name="pricing-and-billing"></a>Preços e cobrança

Todas as contas de armazenamento usam um modelo de preços para armazenamento de blobs de blocos com base na camada de cada blob. Tenha em mente as seguintes considerações de cobrança:

- **Custos de armazenamento**: além da quantidade de dados armazenados, o custo de armazenamento de dados varia dependendo da camada de acesso. O custo por gigabyte diminui conforme a camada fica mais esporádica.
- **Custos de acesso a dados**: os encargos de acesso a dados aumentam conforme a camada fica mais esporádica. Para dados na camada de acesso fria e de arquivo morto, você será cobrado por um encargo de acesso a dados por gigabyte para leituras.
- **Custos de transação**: há uma cobrança por transação para todas as camadas que aumentam à medida que a camada fica mais fria.
- **Custos de transferência de dados de replicação geográfica**: esse encargo só se aplica a contas com replicação geográfica configurada, incluindo grs e ra-grs. A transferência de dados de replicação geográfica acarreta um encargo por gigabyte.
- **Custos de transferência de dados de saída**: transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) acarretam a cobrança por uso de largura de banda por gigabyte, de forma consistente com as contas de armazenamento de finalidade geral.
- **Alterar a camada de acesso**: alterar a camada de acesso da conta resultará em encargos de alteração de camada para todos os blobs que não têm um conjunto de camadas explícita. Para obter informações sobre como alterar a camada de acesso de um único BLOB, consulte [cobrança em camadas no nível do blob](#blob-level-tiering-billing).

    Alterar a camada de acesso de um blob quando o controle de versão está habilitado ou se o blob tem instantâneos, pode resultar em encargos adicionais. Para obter informações sobre BLOBs com controle de versão habilitado, consulte [preços e cobrança](versioning-overview.md#pricing-and-billing) na documentação de controle de versão do blob. Para obter informações sobre BLOBs com instantâneos, consulte [preços e cobrança](snapshots-overview.md#pricing-and-billing) na documentação de instantâneos de BLOB.

> [!NOTE]
> Para obter mais informações sobre os preços para BLOBs de blocos, consulte [preço do blob de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/). Para obter mais informações sobre cobranças de transferência de dados de saída, consulte a página [detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/)

## <a name="availability"></a>Disponibilidade

Diferentes camadas de acesso, juntamente com camadas no nível do blob, estão disponíveis em regiões selecionadas. Para obter uma lista completa, consulte [Produtos do Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

## <a name="next-steps"></a>Próximas etapas

Saiba como gerenciar BLOBs e contas em camadas de acesso.

- [Como gerenciar a camada de um blob em uma conta de armazenamento do Azure](manage-access-tier.md)
- [Como gerenciar a camada de acesso de conta padrão de uma conta de armazenamento do Azure](../common/manage-account-default-access-tier.md)
- [Otimize os custos automatizando as camadas de acesso do armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md)
