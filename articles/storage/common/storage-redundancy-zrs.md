---
title: Compilar aplicativos de Armazenamento do Microsoft Azure altamente disponíveis no ZRS (armazenamento com redundância de zona) | Microsoft Docs
description: O ZRS (armazenamento com redundância de zona) oferece uma maneira simples de compilar aplicativos altamente disponíveis. O ZRS protege contra falhas de hardware no datacenter e contra alguns desastres regionais.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: jeking
ms.subservice: common
ms.openlocfilehash: ab3984b29b3bdfac7599c68c14bd6cc5b671cdf4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731374"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Armazenamento com redundância de zona (ZRS): Aplicativos de Armazenamento do Microsoft Azure altamente disponíveis
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Compatível com cobertura e disponibilidade regional
Atualmente, o ZRS oferece suporte ao padrão de tipos de conta uso geral v2. Para obter mais informações sobre os tipos de conta de armazenamento, consulte [Visão geral da conta de armazenamento do Azure](storage-account-overview.md).

O ZRS está disponível para blobs de blocos, blobs de página que não são disco, arquivos, tabelas e filas.

ZRS está geralmente disponível nas seguintes regiões:

- Sudeste da Ásia
- Europa Ocidental
- Norte da Europa
- França Central
- Leste do Japão
- Sul do Reino Unido
- Leste dos EUA
- Leste dos EUA 2
- Oeste dos EUA 2
- Centro dos EUA

A Microsoft continua a habilitar o ZRS em mais regiões do Azure. Verifique a página [Atualizações de Serviço do Azure](https://azure.microsoft.com/updates/) regularmente para obter informações sobre novas regiões.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>O que acontece quando uma zona fica indisponível?
Seus dados ainda podem ser acessados por operações de leitura e de gravação, mesmo em caso de não disponibilidade de uma zona. A Microsoft recomenda que você continue seguindo as práticas recomendadas para tratamento de falhas transitórias. Essas práticas incluem implementar políticas de repetição com retirada exponencial.

Quando uma zona não estiver disponível, o Azure realiza atualizações da rede, como a reposição de DNS. Essas atualizações podem afetar seu aplicativo se você estiver acessando seus dados antes que as atualizações sejam concluídas.

O ZRS não pode proteger seus dados contra um desastre regional em que várias zonas permanentemente são afetadas. Em vez disso, o ZRS oferece resiliência para seus dados se ele ficar indisponível temporariamente. Para proteção contra desastres regionais, a Microsoft recomenda o uso de armazenamento com redundância geográfica (GRS). Para obter mais informações sobre o GRS, consulte [Armazenamento com redundância geográfica (GRS): Replicação inter-regional para Armazenamento do Microsoft Azure](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Convertendo em replicação ZRS
Migrando para ou do LRS, GRS e RA-GRS é simples. Use o portal do Azure ou a API de provedor de recursos de armazenamento para alterar o tipo de redundância da sua conta. Azure, em seguida, replicar os dados adequadamente. 

Migrando dados para ou de ZRS requerem uma estratégia diferente. Migração de ZRS envolve a movimentação física de dados de um carimbo de armazenamento único para vários carimbos dentro de uma região.

Há duas opções principais para a migração para o ZRS: 

- Copie ou mova dados manualmente para uma nova conta do ZRS de uma conta existente.
- Solicite uma migração ao vivo.

A Microsoft recomenda enfaticamente que você execute uma migração manual. Uma migração manual fornece mais flexibilidade do que uma migração ao vivo. Com uma migração manual, você está no controle do tempo.

Para executar uma migração manual, você tem opções:
- Use ferramentas existentes como o AzCopy, uma das bibliotecas cliente do Armazenamento do Azure ou ferramentas de terceiros confiáveis.
- Se você estiver familiarizado com o Hadoop ou o HDInsight, anexe a conta de origem e de destino (ZRS) ao seu cluster. Em seguida, paralelize o processo de cópia de dados com uma ferramenta como DistCp.
- Crie seu próprio ferramental usando uma das bibliotecas cliente do Armazenamento do Azure.

Uma migração manual pode resultar em tempo de inatividade do aplicativo. Se o seu aplicativo exigir alta disponibilidade, a Microsoft também oferece uma opção de migração ao vivo. Uma migração ao vivo é uma migração no local. 

Durante uma migração ao vivo, você pode usar sua conta de armazenamento enquanto seus dados são migrados entre os carimbos de armazenamento de origem e de destino. Durante o processo de migração, você tem o mesmo nível de durabilidade e disponibilidade de SLA que você normalmente.

Tenha em mente as seguintes restrições sobre a migração ao vivo:

- Enquanto a Microsoft lida com seu pedido de migração ao vivo prontamente, não há garantia de quando uma migração ao vivo será concluída. Se você precisar que seus dados sejam migrados para o ZRS até uma determinada data, a Microsoft recomenda que você execute uma migração manual. Geralmente, quanto mais dados você tiver em sua conta, mais tempo levará para migrar esses dados. 
- A migração ao vivo é suportada apenas para contas de armazenamento que usam a replicação de LRS ou GRS. Se sua conta usa o RA-GRS, você precisa primeiro alterar o tipo de replicação da sua conta para LRS ou GRS antes de prosseguir. Essa etapa intermediária remove o terminal secundário somente leitura fornecido pelo RA-GRS antes da migração.
- Sua conta deve conter dados.
- Você só pode migrar dados dentro da mesma região. Se você quiser migrar os dados para uma conta do ZRS localizada em uma região diferente da conta de origem, você deve executar uma migração manual.
- Apenas os tipos de conta de armazenamento padrão suportam a migração ao vivo. As contas de armazenamento premium devem ser migradas manualmente.
- Não há suporte para migração ao vivo de ZRS para LRS, GRS ou RA-GRS. Você precisará mover os dados manualmente para um novo ou uma conta de armazenamento existente.

Você pode solicitar a migração ao vivo por meio do [Portal de Suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). No portal, selecione a conta de armazenamento que você deseja converter em ZRS.
1. Selecione **Nova solicitação de suporte**
2. Preencha o **Básico** com base nas informações da sua conta. Na seção **Serviço**, selecione **Gerenciamento de Contas de Armazenamento** e o recurso que você deseja converter em ZRS. 
3. Selecione **Avançar**. 
4. Especifique os seguintes valores na seção **Problema**: 
    - **Gravidade**: Deixe o valor padrão como-está.
    - **Tipo de problema**: Selecione **Migração de Dados**.
    - **Categoria**: Selecione **Migrar para o ZRS dentro de uma região**.
    - **Título**: Insira um título descritivo, por exemplo, **migração de conta do ZRS**.
    - **Detalhes**: Digite detalhes adicionais nos **detalhes** caixa, por exemplo, eu quero migrar para o ZRS de [LRS, GRS] na região \_\_. 
5. Selecione **Avançar**.
6. Verifique se as informações de contato estão corretas na **informações de contato** folha.
7. Selecione **Criar**.

Uma pessoa de suporte entrará em contato com você e fornecerá toda a assistência necessária.

## <a name="live-migration-to-zrs-faq"></a>Migração ao vivo para perguntas frequentes sobre o ZRS

**Deve planejar para qualquer tempo de inatividade durante a migração?**

Não há nenhum tempo de inatividade causado pela migração. Durante uma migração ao vivo, você pode continuar a sua conta de armazenamento, enquanto os dados são migrados entre carimbos de armazenamento de origem e de destino. Durante o processo de migração, você tem o mesmo nível de durabilidade e disponibilidade de SLA que você normalmente.

**Há perda de dados associada com a migração?**

Não há nenhuma perda de dados associada com a migração. Durante o processo de migração, você tem o mesmo nível de durabilidade e disponibilidade de SLA que você normalmente.

**Depois que a migração for concluída, são todas as atualizações necessárias para os aplicativos?**

Depois que a migração estiver concluída o tipo de replicação da conta (s) será alterado para "armazenamento com redundância de zona (ZRS)". Pontos de extremidade de serviço, acessar chaves de SAS e outras opções de configuração de conta permanecem intactos e inalterados.

**Posso solicitar uma migração dinâmica de minha conta de uso geral v1 (s) para o ZRS?**

ZRS suporta apenas contas de uso geral v2, portanto, antes de enviar uma solicitação para uma migração ao vivo para o ZRS Certifique-se de atualizar sua conta (s) para uso geral v2. Ver [visão geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview) e [atualizar para uma conta de armazenamento de uso geral v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) para obter mais detalhes.

**Posso solicitar uma migração ao vivo das minhas contas de armazenamento com redundância geográfica de acesso de leitura (RA-GRS) para o ZRS?**

Antes de enviar uma solicitação para uma migração ao vivo para o ZRS Verifique se seu aplicativo (s) ou cargas de trabalho não precisam de acesso para o ponto de extremidade de somente leitura secundário e alterar o tipo de replicação de suas contas de armazenamento para o armazenamento com redundância geográfica (GRS). Ver [alterar estratégia de replicação](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) para obter mais detalhes.

**Posso solicitar uma migração ao vivo das minhas contas de armazenamento para o ZRS para outra região?**

Se você quiser migrar os dados para uma conta do ZRS localizada em uma região diferente da região da conta de origem, você deve executar uma migração manual.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZR Clássico: Opção herdada para redundância de blob de blocos
> [!NOTE]
> A Microsoft irá substituir e migrar as contas do ZRS clássico em 31 de março de 2021. Mais detalhes serão fornecidos aos clientes do ZRS Classic antes da reprovação. 
>
> Depois que se torna o ZRS [geralmente disponível](#support-coverage-and-regional-availability) em uma região, os clientes não será possível criar contas do ZRS clássico no Portal nessa região. O uso do Microsoft PowerShell e do Azure CLI para criar contas do ZRS Classic é uma opção até que o ZRS Classic seja descontinuado.

O ZRS Clássico replica os dados de forma assíncrona em datacenters em uma ou duas regiões. Os dados replicados podem não estar disponíveis, a menos que a Microsoft inicie o failover para o secundário. Uma conta do ZRS Classic não pode ser convertida para ou de LRS, GRS ou RA-GRS. As contas do ZRS Classic também não suportam métricas ou registros.

As contas do ZRS Clássico estão disponíveis somente para **blobs de blocos** em contas de armazenamento V1 (GPv1) de uso geral. Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](storage-account-overview.md).

Para migrar manualmente os dados da conta do ZRS para ou de uma conta do LRS, ZRS Classic, GRS ou RA-GRS, use uma das seguintes ferramentas: AzCopy, Azure Storage Explorer, Azure PowerShell ou CLI do Azure. Você também pode criar sua própria solução de migração com uma das bibliotecas de cliente do Armazenamento do Microsoft Azure.

Você também pode atualizar suas contas do ZRS clássico para o ZRS no Portal ou usando Azure PowerShell ou CLI do Azure nas regiões em que o ZRS está disponível.

Para atualizar para o ZRS no Portal de ir para a seção de configuração da conta e escolha atualizar:![Atualizar o ZRS clássico para o ZRS no Portal](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.jpg)

Para atualizar para o ZRS usando o PowerShell chamar o comando a seguir:
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

Para atualizar para o ZRS usando a CLI chamar o comando a seguir:
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>Consulte também
- [Replicação de Armazenamento do Azure](storage-redundancy.md)
- [Armazenamento com redundância local (LRS): Redundância de dados de baixo custo para o Armazenamento do Microsoft Azure](storage-redundancy-lrs.md)
- [Armazenamento com redundância geográfica (GRS): Replicação inter-regional para Armazenamento do Microsoft Azure](storage-redundancy-grs.md)
