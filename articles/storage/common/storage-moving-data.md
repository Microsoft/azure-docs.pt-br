---
title: Mover grandes quantidades dados do/para armazenamento em nuvem no Azure | Microsoft Docs
description: "Uma visão geral dos diferentes métodos para mover dados para dentro e para fora do Armazenamento do Azure."
services: storage
documentationcenter: 
author: JarrettRenshaw
manager: msmets
editor: tysonn
ms.assetid: 5e3947a9-d99b-4108-9d57-3eb67c03e7ba
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: jarrettr
ms.openlocfilehash: ba390a5973ad33405f1d4217d60d7989f04db3b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="moving-data-to-and-from-azure-storage"></a>Movendo dados para dentro e para fora do Armazenamento do Azure
Se você quiser mover dados locais para o Armazenamento do Azure (ou vice-versa), há várias maneiras de fazer isso. A melhor abordagem para você dependerá do seu cenário. Este artigo fornece uma visão geral rápida de diferentes cenários e ofertas apropriadas para cada um.

## <a name="building-applications"></a>Criando aplicativos
Se você estiver criando um aplicativo, o desenvolvimento na API REST ou em uma das muitas de nossas bibliotecas de cliente é uma excelente maneira de mover dados para dentro e para fora do Armazenamento do Azure.

O Armazenamento do Azure fornece ricas bibliotecas de cliente para .NET, iOS, Java, Android, UWP (Plataforma Universal do Windows), Xamarin, C++, Node.JS, PHP, Ruby e Python. As bibliotecas de cliente oferecem recursos avançados, como lógica de recuperação, registro em log e carregamentos paralelos. Você também pode desenvolver diretamente na API REST, que pode ser chamada por qualquer linguagem que faça solicitações HTTP/HTTPS.

Confira [Introdução ao Armazenamento de Blobs do Azure](../blobs/storage-dotnet-how-to-use-blobs.md) para saber mais.

Além disso, nós também oferecemos a [Biblioteca de movimentação de dados do Armazenamento do Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) que é uma biblioteca projetada para a cópia de alto desempenho dos dados do Azure e para ele. Consulte nossa [documentação](https://github.com/Azure/azure-storage-net-data-movement) da Biblioteca de movimentação de dados para saber mais. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Exibindo/interagindo rapidamente com seus dados
Se você quiser uma maneira fácil de exibir os dados do Armazenamento do Azure e, ao mesmo, a capacidade de carregá-los e baixá-los, considere usar um Gerenciador de Armazenamento do Azure.

Confira nossa lista de [Gerenciadores de Armazenamento do Azure](../storage-explorers.md) para saber mais.

## <a name="system-administration"></a>Administração do Sistema
Se você se sentir mais à vontade com um utilitário de linha de comando (por exemplo, Administradores de Sistema), veja algumas das opções a serem consideradas:

### <a name="azcopy"></a>AzCopy
O AzCopy é um utilitário de linha de comando do Windows desenvolvido para cópia de dados de alto desempenho para dentro e para fora do Armazenamento do Azure. Você também pode copiar dados em uma conta de armazenamento ou entre diferentes contas de armazenamento.

Confira [Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md) para saber mais.

### <a name="azure-powershell"></a>Azure PowerShell
O Azure PowerShell é um módulo que fornece cmdlets para gerenciar serviços no Azure. Trata-se de uma linguagem de scripts e shell de linha de comando baseada em tarefas projetada especialmente para administração do sistema.

Confira [Usando o Azure PowerShell com o Armazenamento do Azure](storage-powershell-guide-full.md) para saber mais.

### <a name="azure-cli"></a>CLI do Azure
A CLI do Azure fornece um conjunto de comandos entre plataformas de software livre para trabalhar com os serviços do Azure. A CLI do Azure está disponível no Windows, OSX e Linux.

Confira [Usando a CLI do Azure com o Armazenamento do Azure](../storage-azure-cli.md) para saber mais.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Mover grandes quantidades de dados com uma rede lenta
Um dos maiores desafios associados à movimentação de grandes quantidades de dados é o tempo de transferência. Se você deseja introduzir dados no Armazenamento do Azure ou extrair dados dele sem se preocupar com os custos de rede ou em escrever código, a Importação/Exportação do Azure é a solução adequada.

Confira [Importação/exportação do Azure](../storage-import-export-service.md) para saber mais.

## <a name="backing-up-your-data"></a>Fazendo backup dos dados
Se você simplesmente precisa fazer backup dos dados no Armazenamento do Azure, o Backup do Azure é a melhor opção. Trata-se de uma solução potente para fazer backup de dados locais e de VMs do Azure.

Confira [Backup do Azure](../../backup/backup-introduction-to-azure-backup.md) para saber mais.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Acessando dados locais e na nuvem
Se você precisar de uma solução para acessar seus dados locais e na nuvem, considere usar a solução de armazenamento de nuvem híbrida do Azure, StorSimple. Essa solução consiste em um dispositivo StorSimple físico que, de maneira inteligente, armazena dados usados com frequência em SSDs, dados usados ocasionalmente em HDDs e dados inativos/de backup/de arquivamento no Armazenamento do Azure.

Confira [StorSimple](../../storsimple/storsimple-overview.md) para saber mais.

## <a name="recovering-your-data"></a>Recuperando dados
Quando houver cargas de trabalho e aplicativos locais, você precisará de uma solução que permita aos negócios continuar em execução no caso de um desastre. O Azure Site Recovery cuida da replicação, do failover e da recuperação de máquinas virtuais e servidores físicos. Os dados replicados são armazenados no Armazenamento do Azure, permitindo que você elimine a necessidade de um datacenter local secundário.

Confira [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) para saber mais.
### <a name="moving-data-faq"></a>Perguntas frequentes sobre a movimentação de dados:
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>Posso migrar VHDs de uma região para outra sem copiar?
A única maneira de copiar VHDs entre regiões é copiando os dados entre as contas de armazenamento em cada região. Você pode usar o AZCopy para isso. Confira Transferir dados com o Utilitário de Linha de Comando AzCopy para saber mais. Para grandes quantidades de dados, também é possível Importar/Exportar do Azure. Confira [Importação/exportação do Azure](https://docs.microsoft.com/en-us/azure/storage/storage-import-export-service) para saber mais.
