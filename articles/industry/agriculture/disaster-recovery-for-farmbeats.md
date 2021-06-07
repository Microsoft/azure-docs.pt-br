---
title: Recuperação de desastre para FarmBeats
description: Este artigo descreve como a recuperação de dados protege contra a perda de dados.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-ummehabiba
ms.openlocfilehash: 9ece624546cab1b8b6fab8c19f4401bd050f6267
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179877"
---
# <a name="disaster-recovery-for-farmbeats"></a>Recuperação de desastre para FarmBeats

A recuperação de dados protege você contra a perda de dados em um evento como o recolhimento da região do Azure. Nesse caso, você pode iniciar o failover e recuperar os dados armazenados em sua implantação do FarmBeats.

A recuperação de dados não é um recurso padrão no Azure FarmBeats. Você pode configurar esse recurso manualmente Configurando os recursos do Azure necessários que são usados pelo FarmBeats para armazenar dados em uma região emparelhada do Azure. Use a abordagem ativa – passiva para habilitar a recuperação.

As seções a seguir fornecem informações sobre como você pode configurar a recuperação de dados no Azure FarmBeats:

- [Habilitar redundância de dados](#enable-data-redundancy)
- [Restaurar serviço do backup online](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Habilitar redundância de dados

O FarmBeats armazena dados em três serviços de primeira parte do Azure, que são o **armazenamento do Azure**, **Cosmos DB** e **Time Series insights**. Use as etapas a seguir para habilitar a redundância de dados para esses serviços para uma região emparelhada do Azure:

1.  **Armazenamento do Azure** -siga esta diretriz para habilitar a redundância de dados para cada conta de armazenamento em sua implantação do FarmBeats.
2.  **Azure Cosmos DB** -siga essa diretriz para habilitar a redundância de dados para Cosmos DB conta sua implantação do FarmBeats.
3.  **Azure Time Series insights (TSI)** -o TSI não oferece redundância de dados no momento. Para recuperar dados de Time Series Insights, vá para o parceiro do seu sensor/clima e envie os dados novamente para a implantação do FarmBeats.

## <a name="restore-service-from-online-backup"></a>Restaurar serviço do backup online

Você pode iniciar o failover e recuperar os dados armazenados para os quais cada um dos armazenamentos de dados mencionados acima para sua implantação do FarmBeats. Depois de recuperar os dados para o armazenamento do Azure e Cosmos DB, crie outra implantação do FarmBeats na região emparelhada do Azure e, em seguida, configure a nova implantação para usar dados de armazenamentos de dados restaurados (ou seja, armazenamento do Azure e Cosmos DB) usando as etapas abaixo:

1. [Configurar o Cosmos DB](#configure-cosmos-db)
2. [Configurar conta de armazenamento](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Configurar o Cosmos DB

Copie a chave de acesso do Cosmos DB restaurado e atualize o novo FarmBeats Datahub Key Vault.


  ![Captura de tela que realça onde obter a cópia da tecla de acesso.](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> Copie a URL do Cosmos DB restaurado e atualize-o na nova configuração do serviço de aplicativo FarmBeats Datahub. Agora você pode excluir Cosmos DB conta na nova implantação do FarmBeats.

  ![Captura de tela que mostra onde copiar a URL do Cosmos DB restaurado.](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>Configurar conta de armazenamento

Copie a chave de acesso da conta de armazenamento restaurada e atualize-a no novo FarmBeats Datahub Key Vault.

![Captura de tela que mostra onde copiar a chave de acesso da conta de armazenamento restaurada.](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> Certifique-se de atualizar o nome da conta de armazenamento no novo arquivo de configuração de VM do lote FarmBeats.

![Recuperação de desastre](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Da mesma forma, se você tiver habilitado a recuperação de dados para sua conta de armazenamento do acelerador, siga a etapa 2 para atualizar a chave de acesso da conta de armazenamento do acelerador e o nome, na nova instância do FarmBeats.
