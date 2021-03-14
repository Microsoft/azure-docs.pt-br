---
title: Habilitando recursos de espaço de trabalho Synapse em um pool SQL dedicado (antigo SQL DW)
description: Este documento descreve como um cliente pode acessar e usar sua instância autônoma existente do SQL DW no espaço de trabalho.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 6d71d9889701ec834747e4bec1dd111157c3206e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694607"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Habilitando recursos de espaço de trabalho Synapse em um pool SQL dedicado existente (antigo SQL DW)

Todos os clientes do SQL data warehouse agora podem acessar e usar uma instância existente do pool SQL dedicado (anteriormente conhecido como SQL DW) por meio do Synapse Studio e do espaço de trabalho, sem afetar a automação, as conexões ou as ferramentas. Este artigo explica como um cliente existente do Azure Synapse Analytics pode se basear e expandir sua solução de análise existente aproveitando os novos recursos ricos em recursos agora disponíveis por meio do espaço de trabalho do Synapse e do Studio.   

## <a name="experience"></a>Experiência
 
Agora que o espaço de trabalho Synapse é GA, uma nova funcionalidade está disponível na seção visão geral do portal do DW que permite que você crie um espaço de trabalho do Synapse para suas instâncias existentes do pool SQL dedicado (antes do SQL DW). Essa nova funcionalidade permitirá que você conecte o servidor lógico que hospeda suas instâncias de data warehouse existentes a um novo espaço de trabalho do Synapse. A conexão garante que todos os data warehouses hospedados nesse servidor sejam acessíveis do espaço de trabalho e do estúdio e possam ser usados em conjunto com os serviços de parceiro Synapse (pool SQL sem servidor, pool de Apache Spark e ADF). Você pode começar a acessar e usar seus recursos assim que as etapas de provisionamento forem concluídas e a conexão tiver sido estabelecida com o espaço de trabalho criado recentemente.  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="Espaço de trabalho Synapse conectado":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>Usando os recursos do Synapse Workspace e do estúdio para acessar e usar um pool SQL dedicado (anteriormente conhecido como SQL DW)
 
As informações a seguir serão aplicadas ao usar um SQL DW dedicado (anteriormente SQL DW) com os recursos do espaço de trabalho do Synapse habilitados: 
- **Funcionalidades do SQL** Todos os recursos do SQL permanecerão com o SQL Server lógico depois que o recurso de espaço de trabalho Synapse tiver sido habilitado. O acesso ao servidor por meio do provedor de recursos do SQL ainda será possível depois que o espaço de trabalho tiver sido habilitado. Todas as funções de gerenciamento podem ser iniciadas por meio do espaço de trabalho e a operação ocorrerá no SQL Server lógico que hospeda seus pools de SQL. Nenhuma automação, ferramenta ou conexão existente será quebrada ou interrompida quando um espaço de trabalho estiver habilitado.  
- **Movimentação de recursos**  Iniciar uma movimentação de recursos em um servidor com o recurso de espaço de trabalho Synapse habilitado fará com que o link entre o servidor e o espaço de trabalho seja interrompido e você não poderá mais acessar as instâncias do pool SQL dedicado existente (anteriormente conhecido como SQL DW) do espaço de trabalho. Para garantir que a conexão seja mantida, é recomendável que ambos os recursos permaneçam dentro da mesma assinatura e grupo de recursos. 
- **Monitoramento** do As solicitações do SQL enviadas por meio do Synapse Studio em um pool de SQL dedicado habilitado para espaço de trabalho (anteriormente SQL DW) podem ser exibidas no Hub do monitor. Para todas as outras atividades de monitoramento, você pode acessar o monitoramento do pool de SQL dedicado do portal do Azure (antigo SQL DW). 
- Controles de **segurança** e **acesso** conforme mencionado acima, todas as funções de gerenciamento para o SQL Server e as instâncias de pools dedicados do SQL (anteriormente denominados SQL DW) continuarão a residir no SQL Server lógico. Essas funções incluem o gerenciamento de regras de firewall, a definição do administrador do Azure AD do servidor e todo o controle de acesso para os dados em seu pool SQL dedicado (anteriormente conhecido como SQL DW). As etapas a seguir devem ser seguidas para garantir que seu pool SQL dedicado (anteriormente conhecido como SQL DW) esteja acessível e possa ser usado por meio do espaço de trabalho Synapse. As associações de função de espaço de trabalho não dão aos usuários permissões para os dados em instâncias do pool SQL dedicado (anteriormente conhecido como SQL DW). Siga as políticas normais de [autenticação do SQL](sql-data-warehouse-authentication.md) para garantir que os usuários possam acessar as instâncias do pool do SQL dedicado (anteriormente conhecido como SQL DW) no servidor lógico. Se o servidor host do pool de SQL dedicado (anteriormente conhecido como SQL DW) tiver uma identidade gerenciada já atribuída a ele, o nome dessa identidade gerenciada será o mesmo da identidade gerenciada do espaço de trabalho que é criada automaticamente para dar suporte aos serviços de parceiros do espaço de trabalho (por exemplo, pipelines do ADF).  Duas identidades gerenciadas com o mesmo nome podem existir em um cenário conectado. As identidades gerenciadas podem ser diferenciadas por suas IDs de objeto do Azure AD, a funcionalidade para criar usuários do SQL usando IDs de objeto estará disponível em breve.

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > O espaço de trabalho conectado Synapse Studio exibirá os nomes de pools dedicados com base nas permissões que o usuário tem no Azure. Os objetos sob os pools não estarão acessíveis se o usuário não tiver permissões nos pools do SQL. 

- **Segurança de rede** Se o espaço de trabalho Synapse que você habilitou em seu pool SQL dedicado existente (anteriormente conhecido como SQL DW) estiver habilitado para proteção de pós-infiltração de dados. Crie uma conexão de ponto de extremidade privada gerenciada do espaço de trabalho para o SQL Server lógico. Aprove a solicitação de conexão de ponto de extremidade privada para permitir comunicações entre o servidor e o espaço de trabalho.
- **Estúdio** Pools SQL no **Hub de dados** um espaço de trabalho habilitado para pool de SQL dedicado (anteriormente conhecido como SQL DW) pode ser identificado por meio da dica de ferramenta no Hub de dados. 
- **Criando um novo pool de SQL dedicado (antigo SQL DW)** Novos pools de SQL dedicados podem ser criados por meio do espaço de trabalho Synapse e do estúdio depois que o recurso de espaço de trabalho tiver sido habilitado e o provisionamento de um novo pool ocorrerá no SQL Server lógico. Os novos recursos serão exibidos no portal e no estúdio quando o provisionamento for concluído.      

## <a name="next-steps"></a>Próximas etapas
Habilitar [recursos de espaço de trabalho Synapse](workspace-connected-create.md) em seu pool SQL dedicado existente (anteriormente SQL DW)
