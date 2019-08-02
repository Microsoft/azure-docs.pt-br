---
title: Agrupar máquinas para avaliação com as Migrações para Azure | Microsoft Docs
description: Descreve como agrupar máquinas antes de executar uma avaliação com o serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 13c640d25265b2663520ef7ab203b0b0a33829e2
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68301690"
---
# <a name="create-a-group-for-assessment"></a>Criar um grupo para avaliação

Este artigo descreve como criar grupos de computadores para avaliação com as migrações para Azure: Avaliação do servidor.

As migrações para [Azure](migrate-services-overview.md) ajudam a migrar para o Azure. As migrações para Azure fornecem um hub centralizado para acompanhar a descoberta, avaliação e migração de infraestrutura, aplicativos e dados locais para o Azure. O Hub fornece ferramentas do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros. 

## <a name="grouping-machines"></a>Agrupando máquinas

Você reúne computadores em grupos para avaliar se eles são adequados para a migração para o Azure e para obter estimativas de custo e dimensionamento do Azure para eles. Há duas maneiras de criar grupos:

- Se você souber quais computadores precisam ser migrados juntos, você pode criar manualmente o grupo em migrações para Azure.
- Se você não tiver certeza sobre os computadores que precisam ser agrupados, você poderá usar a funcionalidade de visualização de dependência no migrações para Azure para criar grupos. 

> [!NOTE]
> A funcionalidade de visualização de dependências não está disponível no Azure Governamental.

## <a name="create-a-group-manually"></a>Criar um grupo manualmente

Você pode criar um grupo ao mesmo tempo em que [cria uma avaliação](how-to-create-assessment.md).

Se você quiser criar um grupo manualmente fora da criação de uma avaliação, faça o seguinte:

1. No projeto de migrações para Azure > **visão geral**, clique em **avaliar e migrar servidores**. Nas **migrações para Azure: Avaliação**do servidor, clique em **grupos**
    - Se você ainda não adicionou a migração do Azure: Ferramenta de avaliação do servidor, clique para adicioná-lo. [Saiba mais](how-to-assess.md).
    - Se você ainda não criou um projeto de migrações para Azure, [saiba mais](how-to-add-tool-first-time.md).

    ![Selecionar grupos](./media/how-to-create-a-group/select-groups.png)

2. Clique no ícone de **grupo** .
3. Em **Criar grupo**, especifique um nome de grupo e, em **nome do dispositivo**, selecione o dispositivo migrações para Azure que você está usando para a descoberta de máquina.
1. Na lista computador, selecione os computadores que você deseja adicionar ao grupo > **criar**.

    ![Criar grupo](./media/how-to-create-a-group/create-group.png)

Agora você pode usar esse grupo ao [criar uma avaliação](how-to-create-assessment.md).

## <a name="refine-a-group-with-dependency-mapping"></a>Refinar um grupo com mapeamento de dependência

O mapeamento de dependência ajuda você a Visualizar dependências entre computadores. Normalmente, você usa o mapeamento de dependência quando deseja avaliar grupos de computadores com níveis mais altos de confiança.
- Ele ajuda a verificar dependências entre máquinas, antes de executar uma avaliação. 
- Ele também ajuda a planejar efetivamente a migração para o Azure, garantindo que nada seja deixado para trás e, portanto, evitando interrupções surpresa durante a migração.
- Você pode descobrir sistemas interdependentes que precisam ser migrados juntos e identificar se um sistema em execução ainda está atendendo aos usuários ou se é um candidato para encerramento em vez de migração.

Se você já configurou o [mapeamento de dependência](how-to-create-group-machine-dependencies.md)e deseja refinar um grupo existente, faça o seguinte:

1. Na guia **servidores** , em **migrações para Azure: Bloco avaliação** do servidor, clique em **grupos**.
2. Clique no grupo que você deseja refinar.
    - Se você ainda não configurou o mapeamento de dependência  , a coluna dependências mostrará um status de **instalação exige** . Para cada VM para a qual você deseja visualizar dependências, clique em **requer instalação**. Instale alguns agentes em cada VM, antes de mapear as dependências da máquina. [Saiba mais](how-to-create-group-machine-dependencies.md).

        ![Adicionar mapeamento de dependência](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Se você já configurou o mapeamento de dependência, na página grupo, clique em **Exibir dependências** para abrir o mapa de dependências de grupo.

3. Depois de clicar em **Exibir dependências**, o mapa de dependências de grupo mostra o seguinte:

    - Conexões TCP de entrada (clientes) e de saída (servidores) para e de todos os computadores no grupo que têm os agentes de dependência instalados.
    - Os computadores dependentes que não têm os agentes de dependência instalados são agrupados por números de porta.
    - Os computadores dependentes com agentes de dependência instalados são mostrados como caixas separadas.
    - Processos em execução dentro da máquina. Expanda cada caixa da máquina para exibir os processos.
    - Propriedades da máquina (incluindo FQDN, sistema operacional, endereço MAC). Clique em cada caixa da máquina para exibir os detalhes.

4. Para exibir as dependências em um intervalo de tempo de sua escolha, modifique o intervalo de tempo (uma hora por padrão) especificando datas de início e término ou a duração.

    > [!NOTE]
    > O intervalo de tempo pode ser de até uma hora. Se você precisar de um intervalo mais longo, use [Azure monitor para consultar dados dependentes](how-to-create-group-machine-dependencies.md) por um período mais longo.

5. Depois de identificar as dependências que você deseja adicionar ou remover do grupo, você pode modificar o grupo. Use CTRL + clique para adicionar ou remover computadores do grupo.

    - Você só pode adicionar máquinas que foram descobertas.
    - Adicionar e remover computadores invalida avaliações passadas para um grupo.
    - Opcionalmente, você pode criar uma nova avaliação ao modificar o grupo.


## <a name="next-steps"></a>Próximas etapas

Saiba como configurar e usar o [mapeamento de dependência](how-to-create-group-machine-dependencies.md) para criar grupos de alta confiança.

