---
title: Migrar dados do data Lake e do data warehouse para o Azure
description: Use Azure Data Factory para migrar dados de seu data Lake e data warehouse para o Azure.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 4e7421cb8ea4b0c14e4f4b59a688cdb1afe7d462
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367700"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Use Azure Data Factory para migrar dados de seu data Lake ou data warehouse para o Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Se você quiser migrar seu EDW (data Lake ou Enterprise data warehouse) para Microsoft Azure, considere o uso de Azure Data Factory. Azure Data Factory é bem adequada para os seguintes cenários:

- Migração de carga de trabalho de Big data do Amazon S3 (serviço de armazenamento simples) ou de um Sistema de Arquivos Distribuído do Hadoop local (HDFS) para o Azure
- Migração de EDW do Oracle Exadata, Netezza, Teradata ou Amazon redshift para o Azure

Azure Data Factory pode mover petabytes (PB) de dados para migração do data Lake e dezenas de terabytes (TB) de dados para data warehouse migração.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Por que Azure Data Factory pode ser usado para a migração de dados

- Azure Data Factory pode facilmente escalar verticalmente a quantidade de capacidade de processamento para mover dados de uma maneira sem servidor com alto desempenho, resiliência e escalabilidade. E você paga apenas pelo que usar. Também observe o seguinte: 
  - Azure Data Factory não tem limitações no volume de dados ou no número de arquivos.
  - Azure Data Factory pode usar totalmente a largura de banda de rede e de armazenamento para obter o maior volume de taxa de transferência de movimentação de dados em seu ambiente.
  - O Azure Data Factory usa um método pago conforme o uso, para que você pague apenas pelo tempo que realmente usa para executar a migração de dados para o Azure.  
- Azure Data Factory pode executar uma carga histórica única e cargas incrementais agendadas.
- O Azure Data Factory usa o IR (tempo de execução de integração do Azure) para mover dados entre os pontos de extremidade do data Lake e do warehouse acessíveis publicamente. Ele também pode usar o IR auto-hospedado para mover dados para os pontos de extremidade do data Lake e do warehouse dentro da VNet (rede virtual) do Azure ou por trás de um firewall.
- Azure Data Factory tem segurança de nível empresarial: você pode usar o Windows Installer (MSI) ou a identidade de serviço para integração segura de serviço a serviço ou usar Azure Key Vault para o gerenciamento de credenciais.
- O Azure Data Factory fornece uma experiência de criação sem código e um painel de monitoramento interno e rico.  

## <a name="online-vs-offline-data-migration"></a>Online versus migração de dados offline

Azure Data Factory é uma ferramenta de migração de dados online padrão para transferir dados por uma rede (Internet, ER ou VPN). Ao passo que com a migração de dados offline, os usuários enviam fisicamente os dispositivos de transferência de dados de sua organização para um data center do Azure.  

Há três considerações importantes quando você escolhe entre uma abordagem de migração online e offline:  

- Tamanho dos dados a serem migrados
- Largura de banda da rede
- Janela de migração

Por exemplo, suponha que você planeje usar Azure Data Factory para concluir a migração de dados dentro de duas semanas (a *janela de migração*). Observe a linha de corte rosa/azul na tabela a seguir. A célula rosa mais baixa para qualquer coluna especificada mostra o emparelhamento de tamanho de dados/largura de banda de rede cuja janela de migração está mais próxima que, porém, menos de duas semanas. (Qualquer emparelhamento de tamanho/largura de banda em uma célula azul tem uma janela de migração online de mais de duas semanas.) 

![Online vs. offline ](media/data-migration-guidance-overview/online-offline.png) esta tabela ajuda a determinar se você pode atender à sua janela de migração pretendida por meio da migração online (Azure data Factory) com base no tamanho dos seus dados e na largura de banda de rede disponível. Se a janela de migração online tiver mais de duas semanas, você desejará usar a migração offline.

> [!NOTE]
> Usando a migração online, você pode obter o carregamento de dados históricos e os feeds incrementais de ponta a ponta por meio de uma única ferramenta.  Por meio dessa abordagem, os dados podem ser mantidos sincronizados entre o repositório existente e o novo repositório durante toda a janela de migração. Isso significa que você pode recriar sua lógica de ETL na nova loja com dados atualizados.


## <a name="next-steps"></a>Próximas etapas

- [Migrar dados do AWS S3 para o Azure](data-migration-guidance-s3-azure-storage.md)
- [Migrar dados do cluster Hadoop local para o Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Migrar dados do servidor Netezza local para o Azure](data-migration-guidance-netezza-azure-sqldw.md)
