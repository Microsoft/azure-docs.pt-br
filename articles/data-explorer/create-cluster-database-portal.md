---
title: 'Início Rápido: Criar um cluster e um banco de dados do Azure Data Explorer'
description: Neste início rápido, você aprenderá a criar um e o banco de dados e cluster do Azure Data Explorer e ingerir (carregar) dados.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 07/22/2019
ms.openlocfilehash: cfab883f9b9b063bd51b9fdb7306d45371449180
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406103"
---
# <a name="quickstart-create-an-azure-data-explorer-cluster-and-database"></a>Início Rápido: Criar um cluster e um banco de dados do Azure Data Explorer

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. Para usar o Azure Data Explorer, primeiro crie um cluster e um ou mais bancos de dados nesse cluster. Em seguida, ingira (carregue) dados em um banco de dados para poder executar consultas nele. Neste início rápido, você pode criar um cluster e um banco de dados.

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-cluster"></a>Criar um cluster

Crie um cluster do Azure Data Explorer com um conjunto definido de recursos de computação e armazenamento em um grupo de recursos do Azure.

1. Selecione o botão **Criar um recurso** (+) no canto superior esquerdo do portal.

1. Pesquise *Azure Data Explorer*.

   ![Recursos do Search](media/create-cluster-database-portal/search-resources.png)

1. Em **Azure Data Explorer**, na parte inferior da tela, selecione **Criar**.

1. Preencha os detalhes básicos do cluster com as informações a seguir.

   ![Criar formulário de cluster](media/create-cluster-database-portal/create-cluster-form2.png)

    **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscription | Sua assinatura | Selecione a assinatura do Azure que você deseja usar para seu cluster.|
    | Resource group | Seu grupo de recursos | Use um grupo de recursos existente ou crie um novo. |
    | Nome do cluster | Um nome exclusivo do cluster | Escolha um nome exclusivo que identifique seu cluster. O nome de domínio *[region].kusto.windows.net* é acrescentado ao nome do cluster que você fornece. O nome só pode conter letras minúsculas e números. Ele precisa conter de 4 a 22 caracteres.
    | Região | *Oeste dos EUA* ou *Oeste dos EUA 2* | Selecione *Oeste dos EUA* ou *Oeste dos EUA 2* (se estiver usando zonas de disponibilidade) para este início rápido. Para um sistema de produção, selecione a região que melhor atenda às suas necessidades.
    | Zonas de disponibilidade | *1*, *2* e/ou *3* | Para regiões compatíveis com a redundância de zonas, selecione as zonas nas quais as instâncias residirão (opcional). Os nós de cluster são criados, por padrão, no mesmo data center. Use as [Zonas de Disponibilidade do Azure](/azure/availability-zones/az-overview) para colocar as instâncias de cluster em várias zonas de disponibilidade na mesma região. Ao selecionar várias zonas de disponibilidade, você pode eliminar um ponto único de falha e garantir a alta disponibilidade. 
    | Especificações de computação | *D13_v2* | Selecione a especificação de preço mais baixa para este início rápido. Para um sistema de produção, selecione a especificação que melhor atenda às suas necessidades.
    | | |

1. Selecione **Revisar + criar** para examinar os detalhes do cluster e **Criar** para provisionar o cluster. O provisionamento normalmente leva cerca de 10 minutos.

1. Depois que a implantação estiver concluída, selecione **Ir para o recurso**.

    ![Ir para o recurso](media/create-cluster-database-portal/notification-resource.png)

## <a name="create-a-database"></a>Criar um banco de dados

Agora você está pronto para a segunda etapa no processo: criação de banco de dados.

1. Na guia **Visão geral**, selecione **Criar banco de dados**.

    ![Etapa 2: criar um banco de dados](media/create-cluster-database-portal/database-creation.png)

1. Preencha o formulário com as seguintes informações.

    ![Criar formulário de banco de dados](media/create-cluster-database-portal/create-database.png)

    **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome do banco de dados | *TestDatabase* | O nome do banco de dados deve ser exclusivo dentro do cluster.
    | Período de retenção | *3650* | O período de tempo (em dias) durante o qual há a garantia de que os dados serão mantidos disponíveis para consulta. O período é medido a partir do momento em que os dados são incluídos.
    | Período de cache | *31* | O período de tempo (em dias) durante o qual os dados consultados com frequência devem ser mantidos disponíveis no armazenamento SSD ou RAM, em vez de no armazenamento de longo prazo.
    | | | |

1. Selecione **Criar** para criar o banco de dados. A criação geralmente leva menos de um minuto. Quando o processo for concluído, você estará novamente de volta na guia **Visão geral** do cluster.

## <a name="run-basic-commands-in-the-database"></a>Executar comandos básicos no banco de dados

Agora que você tem um cluster e banco de dados, pode executar consultas e comandos. Você não tem nenhum dado no banco de dados por enquanto, mas ainda pode ver como as ferramentas funcionam.

1. Em seu cluster, selecione **Consulta**. Cole o comando `.show databases` na janela de consulta e, em seguida, selecione **Executar**.

    ![Mostrar comando de bancos de dados](media/create-cluster-database-portal/show-databases.png)

    O conjunto de resultados mostra **TestDatabase**, o único banco de dados no cluster.

1. Cole o comando `.show tables` na janela de consulta e selecione **Executar**.

    Esse comando retorna um conjunto de resultados vazio porque você ainda não tem nenhuma tabela. Você adicionará uma tabela no próximo artigo desta série.

## <a name="stop-and-restart-the-cluster"></a>Parar e reiniciar o cluster

Você pode parar e reiniciar um cluster, dependendo das necessidades de negócios.

1. Para interromper o cluster, na parte superior da guia **Visão geral**, selecione **Parar**.

    Quando o cluster é interrompido, os dados não ficam disponíveis para consultas e você não pode ingerir dados novos.

1. Para reiniciar o cluster, na parte superior da guia **Visão geral**, selecione **Iniciar**.

    Quando o cluster é reiniciado, ele leva cerca de dez minutos para ficar disponível (como quando foi provisionado originalmente). Leva tempo adicional para os dados serem carregados no cache do ativo.  

## <a name="clean-up-resources"></a>Limpar recursos

Caso planeje seguir outros guias de início rápido e tutoriais, mantenha os recursos que você criou. Caso contrário, limpe o grupo de recursos para evitar incorrer em custos.

1. No portal do Azure, selecione **Grupos de recursos** na extremidade esquerda e, em seguida, selecione o grupo de recursos que contém o cluster do Data Explorer.  

1. Selecione **Excluir grupo de recursos** para excluir todo o grupo de recursos. Se estiver usando um grupo de recursos existente, você pode optar por excluir apenas o cluster do Data Explorer.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início Rápido: Ingerir dados do Hub de Eventos no Azure Data Explorer](ingest-data-event-hub.md)


