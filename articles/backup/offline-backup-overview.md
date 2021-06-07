---
title: Visão geral do backup offline
description: Saiba mais sobre os componentes do backup offline. Eles incluem backup offline baseado em Azure Data Box e backup offline com base no serviço de Importação/Exportação do Microsoft Azure.
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: references_regions
ms.openlocfilehash: 7c65cf6b36af3057fb06c6a6584fa458b1030c72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98704128"
---
# <a name="overview-of-offline-backup"></a>Visão geral do backup offline

Este artigo fornece uma visão geral do backup offline.

Os primeiros backups completos do Azure transferem grandes quantidades de dados online e, portanto, exigem mais largura de banda em comparação com os backups subsequentes, que transferem apenas as alterações incrementais. Escritórios remotos ou datacenters em determinadas geografias nem sempre têm largura de banda de rede suficiente. Por esse motivo, esses backups iniciais levam vários dias. Durante esse tempo, os backups usam continuamente a mesma rede que foi provisionada para aplicativos em execução no datacenter local.

O Backup do Azure dá suporte ao backup offline, que transfere os dados de backup iniciais offline, sem o uso da largura de banda da rede. Ele fornece um mecanismo para copiar dados de backup em dispositivos físicos de armazenamento. Os dispositivos são então enviados para um datacenter do Azure próximo e carregados em um cofre dos Serviços de Recuperação. Esse processo garante a transferência robusta de dados de backup sem usar nenhuma largura de banda de rede.

## <a name="offline-backup-options"></a>Opções do backup offline

O Backup offline é oferecido em dois modos com base na propriedade dos dispositivos de armazenamento:

- Backup offline baseado no Azure Data Box (versão prévia)
- Backup offline com base no serviço de Importação/Exportação do Microsoft Azure

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Backup offline baseado no Azure Data Box (versão prévia)

Atualmente, esse modo é compatível com o agente de MARS (Serviços de Recuperação do Microsoft Azure), em versão prévia. Essa opção aproveita o [Azure Data Box](https://azure.microsoft.com/services/databox/) para enviar dispositivos de transferência proprietários, seguros e resistentes pela Microsoft com conectores USB para seu datacenter ou escritório remoto. Os dados de backup são gravados diretamente nesses dispositivos. Essa opção economiza o esforço necessário para adquirir seus discos e conectores compatíveis com o Azure ou para provisionar o armazenamento temporário como um local de preparo. A Microsoft também lida com a logística de transferência de ponta a ponta, que pode ser acompanhada pelo portal do Azure.

Uma arquitetura que descreve a movimentação dos dados de backup com essa opção é mostrada aqui.

![Arquitetura de Data Box de Backup do Azure](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Veja um resumo da arquitetura:

1. O Backup do Azure copia diretamente os dados de backup para esses dispositivos pré-configurados.
2. Em seguida, você pode enviar esses dispositivos de volta para um datacenter do Azure.
3. O Azure Data Box copia os dados para uma conta de armazenamento de propriedade do cliente.
4. O Backup do Azure automaticamente copia os dados de backup da conta de armazenamento para o cofre designado de Serviços de Recuperação. Os backups online incrementais são agendados.

Para usar o backup offline com base no Azure Data Box, confira [Backup offline usando o Azure Data Box](offline-backup-azure-data-box.md).

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Backup offline com base no serviço de Importação/Exportação do Microsoft Azure

Essa opção tem suporte do MABS (Servidor de Backup do Azure), do DPM (System Center Data Protection Manager) DPM-A e do Agente do MARS. Ele usa o [serviço de Importação/Exportação do Microsoft Azure](../import-export/storage-import-export-service.md). Você pode transferir os dados de backup iniciais para o Azure usando seus discos e conectores compatíveis com o Azure. Essa abordagem exige que você provisione o armazenamento temporário conhecido como o local de preparo e use utilitários predefinidos para formatar e copiar os dados de backup em discos de propriedade do cliente.

Uma arquitetura que descreve a movimentação dos dados de backup com essa opção é mostrada aqui.

![Arquitetura do serviço de Importação/Exportação do Backup do Azure](./media/offline-backup-overview/azure-backup-import-export.png)

Veja um resumo da arquitetura:

1. Em vez de enviar os dados de backup pela rede, o Backup do Azure grava os dados de backup em um local de preparo.
2. Os dados no local de preparo são gravados em um ou mais discos SATA usando um utilitário personalizado.
3. Como parte do trabalho preparatório, o utilitário cria um trabalho de Importação do Azure. As unidades SATA são enviadas para o datacenter do Azure mais próximo e referenciam o trabalho de importação para conectar as atividades.
4. No datacenter do Azure, os dados nos discos são copiados para uma conta de armazenamento do Azure.
5. O Backup do Azure copia os dados de backup da conta de armazenamento para o cofre de Serviços de Recuperação. Os backups incrementais são agendados.

Para usar o backup offline com base no serviço de Importação/Exportação do Azure com o Agente do MARS, confira [Fluxo de trabalho de backup offline no Backup do Azure](./backup-azure-backup-import-export.md).

Para usar o mesmo junto com o MABS ou o DPM-A, confira [Fluxo de trabalho de backup offline para o DPM e Servidor de Backup do Azure](./backup-azure-backup-server-import-export.md).

## <a name="offline-backup-support-summary"></a>Resumo de suporte de backup offline

A tabela a seguir compara as duas opções disponíveis para que você possa fazer as escolhas apropriadas com base em seu cenário.

| **Consideração**                                            | **Backup offline baseado no Azure Data Box**                     | **Backup offline com base no serviço de Importação/Exportação do Microsoft Azure**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Modelos de implantação do Backup do Azure                              | Agente do MARS (versão prévia)                                              | Agente do MARS, MABS, DPM-A                                           |
| Máximo de dados de backup por servidor (MARS) ou por grupo de proteção (MABS, DPM-A) | [Azure Data Box Disk](../databox/data-box-disk-overview.md) - 7.2 TB <br> [Azure Data Box](../databox/data-box-overview.md) - 80 TB       | 80 TB (até 10 discos de 8 TB cada)                          |
| Segurança (dados, dispositivo e serviço)                           | [Dados](../databox/data-box-security.md#data-box-data-protection) – AES criptografado de 256 bits <br> [Dispositivo](../databox/data-box-security.md#data-box-device-protection) – caixa robusta, interface proprietária e baseada em credenciais para copiar dados <br> [Serviço](../databox/data-box-security.md#data-box-service-protection) – o serviço é protegido pelos recursos de segurança do Azure | Dados – criptografia BitLocker                                 |
| Provisionamento do local temporário de preparo                     | Não obrigatório                                                | Maior que ou igual ao tamanho estimado dos dados de backup        |
| Regiões com suporte                                           | [Regiões do Azure Data Box Disk](../databox/data-box-disk-overview.md#region-availability) <br> [Regiões do Azure Data Box](../databox/data-box-disk-overview.md#region-availability) | [Regiões do serviço de Importação/Exportação do Azure](../import-export/storage-import-export-service.md#region-availability) |
| Envio entre países                                     | Sem suporte  <br>    O endereço de origem e o datacenter do Azure de destino devem estar no mesmo país/região* | Com suporte                                                    |
| Logística de transferência (entrega, transporte, retirada)           | Gerenciado totalmente pela Microsoft                                     | Gerenciado pelo cliente                                            |
| Preços                                                      | [Preço do Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Preço do Azure Data Box Disk](https://azure.microsoft.com/pricing/details/databox/disk/) | [Preços do serviço de Importação/Exportação do Azure](https://azure.microsoft.com/pricing/details/storage-import-export/) |

*Se o seu país/região não tiver um datacenter do Azure, você precisará enviar seus discos para um datacenter do Azure em outro país/região.

## <a name="next-steps"></a>Próximas etapas

- [Backup offline do Backup do Azure usando Azure Data Box](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Fluxo de trabalho de backup offline no Backup do Azure](backup-azure-backup-import-export.md)
- [Fluxo de trabalho do backup offline do DPM e do Servidor de Backup do Azure](backup-azure-backup-server-import-export.md)
