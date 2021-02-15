---
title: Restauração pontual para BLOBs de blocos
titleSuffix: Azure Storage
description: A restauração pontual para BLOBs de blocos fornece proteção contra exclusão acidental ou corrupção, permitindo que você restaure uma conta de armazenamento para seu estado anterior em um determinado momento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1df2f12d6947734314609dc50787a59a2fa88731
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980500"
---
# <a name="point-in-time-restore-for-block-blobs"></a>Restauração pontual para BLOBs de blocos

A restauração pontual fornece proteção contra exclusão acidental ou corrupção, permitindo que você restaure dados de blob de blocos para um estado anterior. A restauração pontual é útil em cenários em que um usuário ou aplicativo exclui acidentalmente dados ou onde um erro de aplicativo corrompe os dados. A restauração pontual também permite cenários de teste que exigem a reversão de um conjunto de dados para um estado conhecido antes da execução de outros testes.

A restauração pontual tem suporte apenas para contas de armazenamento v2 de uso geral. Somente os dados nas camadas de acesso quentes e frias podem ser restaurados com a restauração pontual.

Para saber como habilitar a restauração pontual para uma conta de armazenamento, consulte [executar uma restauração pontual em dados de blob de blocos](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Como funciona a restauração pontual

Para habilitar a restauração pontual, você cria uma política de gerenciamento para a conta de armazenamento e especifica um período de retenção. Durante o período de retenção, você pode restaurar blobs de blocos do estado atual para um estado em um ponto anterior no tempo.

Para iniciar uma restauração pontual, chame a operação [restaurar intervalos de blob](/rest/api/storagerp/storageaccounts/restoreblobranges) e especifique um ponto de restauração em hora UTC. Você pode especificar intervalos lexicográfica de contêineres e nomes de blob a serem restaurados ou omitir o intervalo para restaurar todos os contêineres na conta de armazenamento. Há suporte para até 10 intervalos lexicográfica por operação de restauração.

O armazenamento do Azure analisa todas as alterações feitas nos BLOBs especificados entre o ponto de restauração solicitado, especificado em hora UTC e o momento atual. A operação de restauração é atômica, portanto, ela é bem-sucedida completamente na restauração de todas as alterações ou falha. Se houver BLOBs que não podem ser restaurados, a operação falhará e as operações de leitura e gravação para os contêineres afetados serão retomadas.

O diagrama a seguir mostra como funciona a restauração pontual. Um ou mais contêineres ou intervalos de BLOBs são restaurados para seu estado de *n* dias atrás, em que *n* é menor ou igual ao período de retenção definido para a restauração pontual. O efeito é reverter as operações de gravação e exclusão que ocorreram durante o período de retenção.

:::image type="content" source="media/point-in-time-restore-overview/point-in-time-restore-diagram.png" alt-text="Diagrama mostrando como o ponto no tempo restaura os contêineres para um estado anterior":::

Somente uma operação de restauração pode ser executada em uma conta de armazenamento por vez. Uma operação de restauração não pode ser cancelada quando está em andamento, mas uma segunda operação de restauração pode ser executada para desfazer a primeira operação.

A operação **restaurar intervalos de blob** retorna uma ID de restauração que identifica exclusivamente a operação. Para verificar o status de uma restauração pontual, chame a operação **obter status da restauração** com a ID de restauração retornada da operação **restaurar intervalos de blob** .

> [!IMPORTANT]
> Ao executar uma operação de restauração, o armazenamento do Azure bloqueia operações de dados nos BLOBs nos intervalos que estão sendo restaurados durante a operação. As operações de leitura, gravação e exclusão são bloqueadas no local principal. Por esse motivo, as operações como os contêineres de listagem no portal do Azure podem não ser executadas conforme o esperado enquanto a operação de restauração está em andamento.
>
> As operações de leitura do local secundário podem continuar durante a operação de restauração se a conta de armazenamento for replicada geograficamente.

> [!CAUTION]
> A restauração pontual dá suporte a operações de restauração somente em blobs de blocos. Não é possível restaurar operações em contêineres. Se você excluir um contêiner da conta de armazenamento chamando a operação [excluir contêiner](/rest/api/storageservices/delete-container) , esse contêiner não poderá ser restaurado com uma operação de restauração. Em vez de excluir um contêiner inteiro, exclua BLOBs individuais se você quiser restaurá-los mais tarde.

### <a name="prerequisites-for-point-in-time-restore"></a>Pré-requisitos para restauração pontual

A restauração pontual requer que os seguintes recursos de armazenamento do Azure sejam habilitados antes de habilitar a restauração pontual:

- [Exclusão reversível](./soft-delete-blob-overview.md)
- [Feed de alteração](storage-blob-change-feed.md)
- [Controle de versão de BLOB](versioning-overview.md)

### <a name="retention-period-for-point-in-time-restore"></a>Período de retenção para restauração pontual

Ao habilitar a restauração pontual para uma conta de armazenamento, você especifica um período de retenção. Blobs de blocos na sua conta de armazenamento podem ser restaurados durante o período de retenção.

O período de retenção começa alguns minutos depois que você habilita a restauração pontual. Tenha em mente que você não pode restaurar BLOBs para um estado antes do início do período de retenção. Por exemplo, se você habilitou a restauração pontual em 1º de maio com uma retenção de 30 dias, em 15 de maio, você pode restaurar para um máximo de 15 dias. Em 1º de junho, você pode restaurar dados entre 1 e 30 dias.

O período de retenção para a restauração pontual deve ser pelo menos um dia menor do que o período de retenção especificado para exclusão reversível. Por exemplo, se o período de retenção de exclusão reversível for definido como 7 dias, o período de retenção de restauração pontual poderá ser entre 1 e 6 dias.

> [!IMPORTANT]
> O tempo necessário para restaurar um conjunto de dados é baseado no número de operações de gravação e exclusão feitas durante o período de restauração. Por exemplo, uma conta com 1 milhão objetos com 3.000 objetos adicionados por dia e 1.000 objetos excluídos por dia exigirá aproximadamente duas horas para restaurar para um ponto de 30 dias no passado. Um período de retenção e uma restauração mais de 90 dias no passado não seriam recomendados para uma conta com essa taxa de alteração.

### <a name="permissions-for-point-in-time-restore"></a>Permissões para restauração pontual

Para iniciar uma operação de restauração, um cliente deve ter permissões de gravação para todos os contêineres na conta de armazenamento. Para conceder permissões para autorizar uma operação de restauração com o Azure Active Directory (Azure AD), atribua a função de **colaborador da conta de armazenamento** à entidade de segurança no nível da conta de armazenamento, do grupo de recursos ou da assinatura.

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

A restauração pontual para BLOBs de blocos tem as seguintes limitações e problemas conhecidos:

- Somente os blobs de blocos em uma conta de armazenamento padrão de uso geral v2 podem ser restaurados como parte de uma operação de restauração pontual. Blobs de acréscimo, blobs de página e blobs de blocos Premium não são restaurados. 
- Se você tiver excluído um contêiner durante o período de retenção, esse contêiner não será restaurado com a operação de restauração pontual. Se você tentar restaurar um intervalo de BLOBs que inclui BLOBs em um contêiner excluído, a operação de restauração pontual falhará. Para saber mais sobre como proteger contêineres da exclusão, consulte [exclusão reversível para contêineres (versão prévia)](soft-delete-container-overview.md).
- Se um blob tiver se movido entre as camadas quente e fria no período entre o momento atual e o ponto de restauração, o blob será restaurado para sua camada anterior. Não há suporte para a restauração de blobs de blocos na camada de arquivo. Por exemplo, se um blob na camada de acesso frequente foi movido para a camada de acesso aos arquivos dois dias atrás e uma operação de restauração a um ponto três dias atrás, o blob não será restaurado para a camada de acesso frequente. Para restaurar um blob arquivado, primeiro mova-o para fora da camada de arquivo morto. Para obter mais informações, consulte [dados de blob reidratar da camada de arquivo morto](storage-blob-rehydration.md).
- Um bloco que foi carregado por meio do [bloco Put](/rest/api/storageservices/put-block) ou [Put bloco da URL](/rest/api/storageservices/put-block-from-url), mas não confirmado via [lista de blocos Put](/rest/api/storageservices/put-block-list), não faz parte de um blob e, portanto, não é restaurado como parte de uma operação de restauração.
- Um blob com uma concessão ativa não pode ser restaurado. Se um blob com uma concessão ativa estiver incluído no intervalo de BLOBs a serem restaurados, a operação de restauração falhará atomicamente. Interrompa todas as concessões ativas antes de iniciar a operação de restauração.
- Os instantâneos não são criados ou excluídos como parte de uma operação de restauração. Somente o blob de base é restaurado para seu estado anterior.
- Não há suporte para a restauração Azure Data Lake Storage Gen2 namespaces simples e hierárquicos.

> [!IMPORTANT]
> Se você restaurar blobs de blocos para um ponto anterior a 22 de setembro de 2020, as limitações de visualização para a restauração pontual entrarão em vigor. A Microsoft recomenda que você escolha um ponto de restauração que seja igual ou posterior a 22 de setembro de 2020 para aproveitar o recurso de restauração pontual disponível.

## <a name="pricing-and-billing"></a>Preços e cobrança

A cobrança pela restauração pontual depende da quantidade de dados processados para executar a operação de restauração. A quantidade de dados processados é baseada no número de alterações ocorridas entre o ponto de restauração e o momento atual. Por exemplo, supondo uma taxa relativamente constante de alteração para bloquear dados de BLOB em uma conta de armazenamento, uma operação de restauração que volta no tempo 1 dia custaria 1/10 de uma restauração que volta no tempo de 10 dias.

Para estimar o custo de uma operação de restauração, examine o log do feed de alterações para estimar a quantidade de dados que foram modificados durante o período de restauração. Por exemplo, se o período de retenção do feed de alterações for de 30 dias e o tamanho do feed de alterações for de 10 MB, a restauração para um ponto de 10 dias antes custaria aproximadamente um terço do preço listado para uma conta do LRS nessa região. Restaurar para um ponto de 27 dias antes custaria aproximadamente nove décimos do preço listado.

Para obter mais informações sobre os preços da restauração pontual, consulte preço do [blob de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="next-steps"></a>Próximas etapas

- [Executar uma restauração pontual em dados de blob de blocos](point-in-time-restore-manage.md)
- [Suporte ao feed de alterações no armazenamento de BLOBs do Azure](storage-blob-change-feed.md)
- [Habilitar exclusão reversível para blobs](./soft-delete-blob-enable.md)
- [Habilitar e gerenciar o controle de versão de blob](versioning-enable.md)
