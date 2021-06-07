---
title: Alterar como uma conta de armazenamento é replicada
titleSuffix: Azure Storage
description: Saiba como alterar a forma como os dados em uma conta de armazenamento existente são replicados.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/19/2021
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 412e5ac661761d5fda1d375c59511c053a6354a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714775"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Alterar como uma conta de armazenamento é replicada

A redundância do Armazenamento do Azure sempre armazena várias cópias dos seus dados para que eles sejam protegidos contra eventos planejados e não planejados, incluindo falhas de hardware transitórias, interrupções de energia ou rede e desastres naturais de grandes proporções. A redundância garante que sua conta de armazenamento atenda ao [SLA (Contrato de Nível de Serviço) do Armazenamento do Azure](https://azure.microsoft.com/support/legal/sla/storage/) mesmo diante de falhas.

O armazenamento do Azure oferece os seguintes tipos de replicação:

- LRS (armazenamento com redundância local)
- ZRS (armazenamento com redundância de zona)
- Armazenamento com redundância geográfica (GRS) ou armazenamento com redundância geográfica com acesso de leitura (RA-GRS)
- Armazenamento com redundância de zona geográfica (GZRS) ou armazenamento com redundância de acesso de leitura (RA-GZRS)

Para obter uma visão geral de cada uma dessas opções, consulte [redundância de armazenamento do Azure](storage-redundancy.md).

## <a name="switch-between-types-of-replication"></a>Alternar entre tipos de replicação

Você pode alternar uma conta de armazenamento de um tipo de replicação para qualquer outro tipo, mas alguns cenários são mais diretos do que outros. Se você quiser adicionar ou remover a replicação geográfica ou o acesso de leitura para a região secundária, poderá usar o portal do Azure, o PowerShell ou o CLI do Azure para atualizar a configuração de replicação. No entanto, se você quiser alterar a forma como os dados são replicados na região primária, movendo de LRS para ZRS ou vice-versa, você deve executar uma migração manual.

A tabela a seguir fornece uma visão geral de como alternar de cada tipo de replicação para outro:

| Independente | ... para LRS | ... para GRS/RA-GRS | ... para ZRS | ... para GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... de LRS</b> | N/D | Use portal do Azure, PowerShell ou CLI para alterar a configuração de replicação<sup>1, 2</sup> | Executar uma migração manual <br /><br /> OU <br /><br /> Solicitar uma migração ao vivo | Executar uma migração manual <br /><br /> OU <br /><br /> Alterne para GRS/RA-GRS primeiro e, em seguida, solicite uma migração ao vivo<sup>1</sup> |
| <b>... de GRS/RA-GRS</b> | Usar portal do Azure, PowerShell ou CLI para alterar a configuração de replicação | N/D | Executar uma migração manual <br /><br /> OU <br /><br /> Alterne para o LRS primeiro e, em seguida, solicite uma migração ao vivo | Executar uma migração manual <br /><br /> OU <br /><br /> Solicitar uma migração ao vivo |
| <b>... de ZRS</b> | Executar uma migração manual | Executar uma migração manual | N/D | Solicitar uma migração ao vivo |
| <b>... de GZRS/RA-GZRS</b> | Executar uma migração manual | Executar uma migração manual | Usar portal do Azure, PowerShell ou CLI para alterar a configuração de replicação | N/D |

<sup>1</sup> gera uma cobrança de egresso única.<br />
<sup>2</sup> a migração de LRS para grs não terá suporte se a conta de armazenamento contiver BLOBs na camada de arquivo morto.<br />
<sup>3</sup> a conversão de ZRS para GZRS/ra-GZRS ou vice-versa não tem suporte nas seguintes regiões: leste dos EUA 2, leste dos EUA, oeste da Europa.

> [!CAUTION]
> Se você realizou um [failover de conta](storage-disaster-recovery-guidance.md) para sua conta do (ra-) grs ou (ra-) GZRS, a conta é localmente REDUNDANTE (LRS) na nova região primária após o failover. Não há suporte para a migração dinâmica para ZRS ou GZRS para uma conta LRS resultante de um failover. Isso é verdadeiro mesmo no caso de chamadas de operações de failback. Por exemplo, se você executar um failover de conta de RA-GZRS para o LRS na região secundária e, em seguida, configurá-lo novamente para RA-GRS e executar outro failover de conta para a região primária original, não será possível contatar o suporte para a migração dinâmica original para RA-GZRS na região primária. Em vez disso, você precisará executar uma migração manual para ZRS ou GZRS.

## <a name="change-the-replication-setting"></a>Alterar a configuração de replicação

Você pode usar o portal do Azure, o PowerShell ou o CLI do Azure para alterar a configuração de replicação de uma conta de armazenamento, desde que não esteja alterando a forma como os dados são replicados na região primária. Se você estiver migrando de LRS na região primária para ZRS na região primária ou vice-versa, você deverá executar uma migração manual ou uma migração dinâmica.

Alterar a forma como sua conta de armazenamento é replicada não resulta em tempo de inatividade para seus aplicativos.

# <a name="portal"></a>[Portal](#tab/portal)

Para alterar a opção de redundância para sua conta de armazenamento no portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento no portal do Azure.
1. Selecione a definição de **configuração** .
1. Atualize a configuração de **replicação** .

![Captura de tela mostrando como alterar a opção de replicação no portal](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para alterar a opção de redundância para sua conta de armazenamento com o PowerShell, chame o comando [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) e especifique o `-SkuName` parâmetro:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para alterar a opção de redundância para sua conta de armazenamento com CLI do Azure, chame o comando [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) e especifique o `--sku` parâmetro:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>Executar uma migração manual para ZRS, GZRS ou RA-GZRS

Se você quiser alterar a forma como os dados em sua conta de armazenamento são replicados na região primária, movendo de LRS para ZRS ou vice-versa, você pode optar por executar uma migração manual. Uma migração manual fornece mais flexibilidade do que uma migração ao vivo. Você controla o tempo de uma migração manual, portanto, use essa opção se precisar que a migração seja concluída em uma determinada data.

Quando você executa uma migração manual de LRS para ZRS na região primária ou vice-versa, a conta de armazenamento de destino pode ser com redundância geográfica e também pode ser configurada para acesso de leitura à região secundária. Por exemplo, você pode migrar uma conta LRS para uma conta GZRS ou RA-GZRS em uma única etapa.

Uma migração manual pode resultar em tempo de inatividade do aplicativo. Se o seu aplicativo exigir alta disponibilidade, a Microsoft também oferece uma opção de migração ao vivo. Uma migração ao vivo é uma migração no local sem tempo de inatividade.

Com uma migração manual, você copia os dados de sua conta de armazenamento existente para uma nova conta de armazenamento que usa ZRS na região primária. Para executar uma migração manual, você pode usar uma das seguintes opções:

- Copie dados usando uma ferramenta existente, como AzCopy, uma das bibliotecas de cliente de armazenamento do Azure ou uma ferramenta de terceiros confiável.
- Se você estiver familiarizado com o Hadoop ou o HDInsight, poderá anexar a conta de armazenamento de origem e a conta de conta de armazenamento de destino ao cluster. Em seguida, paralelize o processo de cópia de dados com uma ferramenta como DistCp.

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>Solicitar uma migração ao vivo para ZRS, GZRS ou RA-GZRS

Se você precisar migrar sua conta de armazenamento de LRS para ZRS na região primária sem tempo de inatividade do aplicativo, você pode solicitar uma migração ao vivo da Microsoft. Para migrar de LRS para GZRS ou RA-GZRS, primeiro alterne para GRS ou RA-GRS e, em seguida, solicite uma migração ao vivo. Da mesma forma, você pode solicitar uma migração ao vivo de GRS ou RA-GRS para GZRS ou RA-GZRS. Para migrar de GRS ou RA-GRS para ZRS, primeiro mude para LRS e, em seguida, solicite uma migração ao vivo.

Durante uma migração ao vivo, você pode acessar dados em sua conta de armazenamento sem perda de durabilidade ou disponibilidade. O SLA do armazenamento do Azure é mantido durante o processo de migração. Não há perda de dados associada a uma migração dinâmica. Pontos de extremidade de serviço, chaves de acesso, assinaturas de acesso compartilhado e outras opções de conta permanecem inalterados após a migração.

O ZRS dá suporte apenas a contas v2 de uso geral, portanto, atualize sua conta de armazenamento antes de enviar uma solicitação para uma migração ao vivo para o ZRS. Para obter mais informações, confira [Atualizar para uma conta de armazenamento de uso geral v2](storage-account-upgrade.md). Uma conta de armazenamento deve conter dados a serem migrados por meio da migração dinâmica.

A migração ao vivo é suportada apenas para contas de armazenamento que usam a replicação de LRS ou GRS. Se sua conta usa o RA-GRS, você precisa primeiro alterar o tipo de replicação da sua conta para LRS ou GRS antes de prosseguir. Essa etapa intermediária remove o terminal secundário somente leitura fornecido pelo RA-GRS antes da migração.

Enquanto a Microsoft lida com seu pedido de migração ao vivo prontamente, não há garantia de quando uma migração ao vivo será concluída. Se você precisar que seus dados sejam migrados para o ZRS até uma determinada data, a Microsoft recomenda que você execute uma migração manual. Geralmente, quanto mais dados você tiver em sua conta, mais tempo levará para migrar esses dados.

Você deve executar uma migração manual se:

- Você deseja migrar seus dados para uma conta de armazenamento ZRS que esteja localizada em uma região diferente da conta de origem.
- Sua conta de armazenamento é um blob de páginas Premium ou uma conta de blob de blocos.
- Você deseja migrar dados de ZRS para LRS, GRS ou RA-GRS.
- Sua conta de armazenamento inclui dados na camada de arquivo morto.

Você pode solicitar a migração ao vivo por meio do [Portal de Suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). No portal, selecione a conta de armazenamento que você deseja converter em ZRS.

1. Selecione **nova solicitação de suporte**.
2. Conclua os **conceitos básicos** com base nas informações da sua conta: 
    - **Tipo de problema**: selecione **técnico**.
    - **Serviço**: selecione **meus serviços** e **Gerenciamento de conta de armazenamento**.
    - **Recurso**: selecione o recurso que você deseja converter em ZRS.
3. Selecione **Avançar**.
4. Especifique os seguintes valores na seção **Problema**:
    - **Severidade**: deixe o valor padrão como-está.
    - **Tipo de problema**: selecione **migração de dados**.
    - **Categoria**: selecione **migrar para ZRS**.
    - **Título**: Tipo de titúlo descritivo, por exemplo, **migração de conta do ZRS**.
    - **Detalhes**: digite detalhes adicionais na caixa de **detalhes** , por exemplo, eu gostaria de migrar para ZRS de [lRS, grs] na \_ \_ região.
5. Selecione **Avançar**.
6. Verifique se as informações de contato estão corretas na **informações de contato** folha.
7. Selecione **Criar**.

Uma pessoa de suporte entrará em contato com você e fornecerá toda a assistência necessária.

> [!NOTE]
> Os compartilhamentos de arquivos Premium (contas de armazenamento de arquivo) só estão disponíveis para LRS e ZRS.
>
> Atualmente, as contas de armazenamento GZRS não dão suporte à camada de arquivo morto. Consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](../blobs/storage-blob-storage-tiers.md) para obter mais detalhes.
>
> Os discos gerenciados estão disponíveis somente para LRS e não podem ser migrados para o ZRS. Você pode armazenar instantâneos e imagens para discos gerenciados de SSD padrão no armazenamento de HDD padrão e [escolher entre as opções LRS e ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Para obter informações sobre a integração com conjuntos de disponibilidade, consulte [introdução aos Azure Managed disks](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets).

## <a name="switch-from-zrs-classic"></a>Alternar do ZRS clássico

> [!IMPORTANT]
> A Microsoft irá substituir e migrar as contas do ZRS clássico em 31 de março de 2021. Mais detalhes serão fornecidos aos clientes do ZRS Classic antes da reprovação.
>
> Depois que o ZRS se tornar disponível em uma determinada região, os clientes não poderão mais criar contas ZRS clássicas do portal do Azure nessa região. O uso do Microsoft PowerShell e do Azure CLI para criar contas do ZRS Classic é uma opção até que o ZRS Classic seja descontinuado. Para obter informações sobre onde o ZRS está disponível, consulte [redundância de armazenamento do Azure](storage-redundancy.md).

O ZRS Clássico replica os dados de forma assíncrona em datacenters em uma ou duas regiões. Os dados replicados podem não estar disponíveis, a menos que a Microsoft inicie o failover para o secundário. Uma conta do ZRS Classic não pode ser convertida para ou de LRS, GRS ou RA-GRS. As contas do ZRS Classic também não suportam métricas ou registros.

As contas do ZRS Clássico estão disponíveis somente para **blobs de blocos** em contas de armazenamento V1 (GPv1) de uso geral. Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](storage-account-overview.md).

Para migrar manualmente os dados da conta do ZRS para ou de uma conta LRS, GRS, RA-GRS ou ZRS clássico, use uma das seguintes ferramentas: AzCopy, Gerenciador de Armazenamento do Azure, PowerShell ou CLI do Azure. Você também pode criar sua própria solução de migração com uma das bibliotecas de cliente do Armazenamento do Microsoft Azure.

Você também pode atualizar sua conta de armazenamento ZRS clássico para ZRS usando o portal do Azure, o PowerShell ou o CLI do Azure em regiões em que o ZRS está disponível.

# <a name="portal"></a>[Portal](#tab/portal)

Para atualizar para o ZRS no portal do Azure, navegue até as definições de **configuração** da conta e escolha **Atualizar**:

![Atualizar do ZRS Clássico para o ZRS no Portal](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para atualizar para o ZRS usando o PowerShell, chame o seguinte comando:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para atualizar para o ZRS usando CLI do Azure, chame o seguinte comando:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Custos associados à alteração de como os dados são replicados

Os custos associados à alteração de como os dados são replicados dependem do seu caminho de conversão. Ordenar as ofertas de redundância de armazenamento do Azure mais dispendiosas, incluindo LRS, ZRS, GRS, RA-GRS, GZRS e RA-GZRS.

Por exemplo, passar *de* LRS para qualquer outro tipo de replicação incorrerá em encargos adicionais, pois você está mudando para um nível de redundância mais sofisticado. Migrar *para* grs ou ra-grs incorrerá em um encargo de largura de banda de saída, pois seus dados (em sua região primária) estão sendo replicados para sua região secundária remota. Essa cobrança é um custo único na configuração inicial. Depois que os dados são copiados, não há nenhuma cobrança adicional de migração. Para obter detalhes sobre encargos de largura de banda, visite a [página Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Se você migrar sua conta de armazenamento de GRS para LRS, não haverá nenhum custo adicional, mas os dados replicados serão excluídos do local secundário.

> [!IMPORTANT]
> Se você migrar sua conta de armazenamento de RA-GRS para GRS ou LRS, essa conta será cobrada como RA-GRS por um adicional 30 dias além da data em que foi convertida.

## <a name="see-also"></a>Confira também

- [Redundância do Armazenamento do Azure](storage-redundancy.md)
- [Verificar a propriedade Horário da Última Sincronização de uma conta de armazenamento](last-sync-time-get.md)
- [Uso da redundância geográfica para criar aplicativos altamente disponíveis](geo-redundant-design.md)
