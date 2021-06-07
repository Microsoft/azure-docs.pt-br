---
title: Agrupar servidores para avaliação com migrações para Azure | Microsoft Docs
description: Descreve como agrupar servidores antes de executar uma avaliação com o serviço migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 0570ed73b86223025b250e269d7e2f358473f004
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780847"
---
# <a name="create-a-group-for-assessment"></a>Criar um grupo para avaliação

Este artigo descreve como criar grupos de servidores para avaliação com as migrações para Azure: descoberta e avaliação.

As [migrações para Azure](migrate-services-overview.md) ajudam a migrar para o Azure. As Migrações para Azure oferecem um hub centralizado para acompanhar a descoberta, a avaliação e a migração de infraestrutura, aplicativos e dados locais para o Azure. O hub fornece ferramentas do Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros. 

## <a name="grouping-servers"></a>Agrupando servidores

Você coleta servidores em grupos para avaliar se eles são adequados para a migração para o Azure e para obter estimativas de custo e dimensionamento do Azure para eles. Há duas maneiras de criar grupos:

- Se você souber os servidores que precisam ser migrados juntos, poderá criar manualmente o grupo nas migrações para Azure.
- Se não tiver certeza sobre os servidores que precisam ser agrupados, você poderá usar a funcionalidade de visualização de dependência no migrações para Azure para criar grupos. 

> [!NOTE]
> A funcionalidade de visualização de dependências não está disponível no Azure Governamental.

## <a name="create-a-group-manually"></a>Criar um grupo manualmente

Você pode criar um grupo ao mesmo tempo em que [cria uma avaliação](how-to-create-assessment.md).

Se você quiser criar um grupo manualmente fora da criação de uma avaliação, faça o seguinte:

1. No projeto de migrações para Azure > **visão geral**, clique em **avaliar e migrar servidores**. Em **migrações para Azure: descoberta e avaliação**, clique em **grupos**
    - Se você ainda não tiver adicionado a ferramenta migrações do Azure: descoberta e avaliação, clique para adicioná-la. [Saiba mais](how-to-assess.md).
    - Se você ainda não criou um projeto de migrações para Azure, [saiba mais](./create-manage-projects.md).

    ![Selecionar grupos](./media/how-to-create-a-group/select-groups.png)

2. Clique no ícone de **grupo** .
3. Em **Criar grupo**, especifique um nome de grupo e, em **nome do dispositivo**, selecione o dispositivo migrações para Azure que você está usando para a descoberta de servidor.
4. Na lista servidor, selecione os servidores que você deseja adicionar ao grupo > **criar**.

    ![Criar grupo](./media/how-to-create-a-group/create-group.png)

Agora você pode usar esse grupo ao [criar uma avaliação de VM do Azure](how-to-create-assessment.md) ou [uma avaliação da AVS (solução VMware) do](how-to-create-azure-vmware-solution-assessment.md) Azure ou [uma avaliação do SQL do Azure](how-to-create-azure-sql-assessment.md).

## <a name="refine-a-group-with-dependency-mapping"></a>Refinar um grupo com mapeamento de dependência

O mapeamento de dependência ajuda você a Visualizar dependências entre servidores. Normalmente, você usa o mapeamento de dependência quando deseja avaliar grupos de servidores com níveis mais altos de confiança.
- Ele ajuda a verificar dependências de servidor, antes de executar uma avaliação. 
- Ele também ajuda a planejar efetivamente a migração para o Azure, garantindo que nada seja deixado para trás e, portanto, evitando interrupções surpresa durante a migração.
- Você pode descobrir sistemas interdependentes que precisam ser migrados juntos e identificar se um sistema em execução ainda está atendendo aos usuários ou se é um candidato para encerramento em vez de migração.

Se você já [configurou o mapeamento de dependência](how-to-create-group-machine-dependencies.md)e deseja refinar um grupo existente, faça o seguinte:

1. Na guia **servidores** , no bloco **migrações para Azure: descoberta e avaliação** , clique em **grupos**.
2. Clique no grupo que você deseja refinar.
    - Se você ainda não configurou o mapeamento de dependência, a coluna **dependências** mostrará um status de **instalação exige** . Para cada servidor para o qual você deseja visualizar dependências, clique em **requer instalação**. Instale alguns agentes em cada servidor, antes de poder mapear as dependências do servidor. [Saiba mais](how-to-create-group-machine-dependencies.md).

        ![Adicionar mapeamento de dependência](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Se você já configurou o mapeamento de dependência, na página grupo, clique em **Exibir dependências** para abrir o mapa de dependências de grupo.

3. Depois de clicar em **Exibir dependências**, o mapa de dependências de grupo mostra o seguinte:

    - Conexões TCP de entrada (clientes) e de saída (servidores) para e de todos os servidores no grupo que têm os agentes de dependência instalados.
    - Os servidores dependentes que não têm os agentes de dependência instalados são agrupados por números de porta.
    - Os servidores dependentes com agentes de dependência instalados são mostrados como caixas separadas.
    - Processos em execução dentro do servidor. Expanda cada caixa de servidor para exibir os processos.
    - Propriedades do servidor (incluindo FQDN, sistema operacional, endereço MAC). Clique em cada caixa de servidor para exibir os detalhes.

4. Para exibir as dependências em um intervalo de tempo de sua escolha, modifique o intervalo de tempo (uma hora por padrão) especificando datas de início e término ou a duração.

    > [!NOTE]
    > O intervalo de tempo pode ser de até uma hora. Se você precisar de um intervalo mais longo, use [Azure monitor para consultar dados dependentes](how-to-create-group-machine-dependencies.md) por um período mais longo.

5. Depois de identificar as dependências que você deseja adicionar ou remover do grupo, você pode modificar o grupo. Use CTRL + clique para adicionar ou remover servidores do grupo.

    - Você só pode adicionar servidores que foram descobertos.
    - Adicionar e remover servidores invalida avaliações passadas para um grupo.
    - Opcionalmente, você pode criar uma nova avaliação ao modificar o grupo.


## <a name="next-steps"></a>Próximas etapas

Saiba como configurar e usar o [mapeamento de dependência](how-to-create-group-machine-dependencies.md) para criar grupos de alta confiança.