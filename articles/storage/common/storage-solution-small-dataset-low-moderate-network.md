---
title: Opções de transferência de dados do Azure para conjuntos de dados pequenos com largura de banda de rede baixa a moderada | Microsoft Docs
description: Saiba como escolher uma solução do Azure para transferência de dados quando a largura de banda de rede for baixa a moderada em seu ambiente e você estiver planejando transferir conjuntos de dados pequenos.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: f59d1e297ba4d7607d7abd07a78da4784f55d20f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023220"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Transferência de dados para conjuntos de dados pequenos com largura de banda de rede baixa a moderada
 
Este artigo apresenta uma visão geral das soluções de transferência de dados quando você tem largura de banda da rede baixa a moderada em seu ambiente e planeja transferir conjuntos de dados pequenos. O artigo também descreve as opções de transferência de dados recomendadas e a respectiva matriz das principais funcionalidades para esse cenário.

Para entender uma visão geral de todas as opções de transferência de dados disponíveis, vá para [Escolher uma solução de transferência de dados do Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Descrição do cenário

Conjunto de dados pequenos referem-se a tamanhos de dados na ordem de GBs a alguns TBs. Largura de banda de rede baixa a moderada implica 45 Mbps (conexão T3 no data center) a 1 Gbps.

- Se você estiver transferindo apenas alguns dos arquivos e não precisar automatizar a transferência de dados, considere ferramentas com uma interface gráfica.
- Se você estiver familiarizado com administração do sistema, considere a ferramentas de script/programáticas ou de linha de comando.

## <a name="recommended-options"></a>Opções recomendadas

As opções recomendadas nesse cenário são:

- **Ferramentas de interface gráfica**, como o Gerenciador de Armazenamento do Azure e o Armazenamento do Azure no portal do Azure. Elas fornecem uma maneira fácil de exibir os dados e transferir rapidamente alguns arquivos.

    - **Gerenciador de Armazenamento do Azure** – essa ferramenta multiplataforma permite gerenciar o conteúdo de suas contas de Armazenamento do Azure. Ele permite carregar, baixar e gerenciar blogs, arquivos, filas, tabelas e entidades do Azure Cosmos DB. Use-o com o armazenamento de Blobs para gerenciar blobs e pastas, bem como carregar e baixar blobs entre o sistema de arquivos local e o armazenamento de Blobs ou entre contas de armazenamento.
    - **Portal do Azure** – o Armazenamento do Azure no portal do Azure fornece uma interface baseada na Web para explorar arquivos e carregar novos arquivos um de cada vez. Essa é uma boa opção se você não deseja instalar nenhuma ferramenta nem emitir comandos para explorar rapidamente os arquivos ou para simplesmente carregar novos arquivos.

- **Ferramentas de script/programação**, como AzCopy/PowerShell/CLI do Azure e APIs REST do Armazenamento do Azure.

    - **AzCopy** – use essa ferramenta de linha de comando para copiar dados facilmente de e para Blobs do Azure, Arquivos e armazenamento de Tabela com um desempenho ideal. O AzCopy dá suporte à simultaneidade e ao paralelismo e à capacidade de retomar as operações de cópia quando elas forem interrompidas.
    - **Azure PowerShell** – para usuários familiarizados com a administração do sistema, use o módulo de Armazenamento do Azure no Azure PowerShell para transferir dados.
    - **CLI do Azure** – use essa ferramenta multiplataforma para gerenciar serviços do Azure e carregar dados para o Armazenamento do Azure.
    - **SDKs/APIs REST do Armazenamento do Azure** – ao criar um aplicativo, você pode desenvolver o aplicativo com relação a SDKs/APIs REST do Armazenamento do Azure e usar as bibliotecas de cliente do Azure oferecidas em vários idiomas.


## <a name="comparison-of-key-capabilities"></a>Comparação das principais funcionalidades

A tabela a seguir resume as diferenças nas principais funcionalidades.

| Recurso | Gerenciador de Armazenamento do Azure | Portal do Azure | AzCopy<br>Azure PowerShell<br>CLI do Azure | SDKs ou APIs REST do Armazenamento do Azure |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Disponibilidade | Baixar e instalar <br>Ferramenta autônoma | Ferramentas de exploração baseadas na Web no portal do Azure | Ferramenta de linha de comando |Interfaces programáveis em .NET, Java, Python, JavaScript, C++, Go, Ruby e PHP |
| Interface gráfica | Sim | Sim | Não | Não |
| Plataformas com suporte | Windows, Mac, Linux | Baseado na Web |Windows, Mac, Linux |Todas as plataformas |
| Operações de Armazenamento de Blobs permitidas<br>para blobs e pastas | Carregar<br>Baixar<br>Gerenciar | Carregar<br>Baixar<br>Gerenciar |Carregar<br>Baixar<br>Gerenciar | Sim, personalizável |
| Armazenamento do Data Lake Gen1 permitido<br>operações para arquivos e pastas | Carregar<br>Baixar<br>Gerenciar | Não |Carregar<br>Baixar<br>Gerenciar                   | Não |
| Operações de armazenamento de Arquivo permitidas<br>para arquivos e diretórios | Carregar<br>Baixar<br>Gerenciar | Carregar<br>Baixar<br>Gerenciar   |Carregar<br>Baixar<br>Gerenciar | Sim, personalizável |
| Operações de armazenamento de Tabela permitidas<br>para tabelas |Gerenciar | Não |Suporte de tabela no AzCopy v7 |Sim, personalizável|
| Armazenamento de Fila permitido | Gerenciar | Não  |Não | Sim, é personalizável|


## <a name="next-steps"></a>Próximas etapas

- Saiba como [transferir dados com o Gerenciador de Armazenamento do Azure](../../machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer.md).
- [Transferir dados com AzCopy](./storage-use-azcopy-v10.md)