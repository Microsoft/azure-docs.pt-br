---
title: Habilitação de recursos do workspace do Synapse em um pool de SQL dedicado (antigo SQL DW)
description: Este documento descreve como um cliente pode acessar e usar sua instância autônoma do SQL DW no Workspace.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694607"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Habilitação de recursos do workspace do Synapse em um pool de SQL dedicado existente (antigo SQL DW)

Todos os clientes do SQL Data Warehouse agora podem acessar e usar instâncias do pool de SQL dedicado (anteriormente chamado de SQL DW) por meio do Synapse Studio e do Workspace, sem afetar a automação, as conexões ou as ferramentas. Este artigo explica como os clientes do Azure Synapse Analytics podem expandir sua solução de análise atual com os novos recursos que estão disponíveis agora por meio do workspace e do Studio do Synapse.   

## <a name="experience"></a>Experiência
 
Com a disponibilidade geral do workspace do Synapse, há um novo recurso disponível na seção de visão geral do portal DW para a criação de um workspace do Synapse para instâncias atuais de pool de SQL dedicado (antigo SQL DW). Essa nova funcionalidade permitirá que você conecte o servidor lógico que hospeda suas instâncias atuais de data warehouse a um novo workspace do Synapse. Com a conexão, todos os data warehouses hospedados no servidor podem ser acessados no Workspace e no Studio, além de ser possível usá-los com os serviços de parceiro do Synapse (pool de SQL sem servidor, pool do Apache Spark e ADF). Você pode acessar e usar seus recursos assim que as etapas de provisionamento forem concluídas e a conexão tiver sido estabelecida com o novo workspace criado.  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="Workspace do Synapse conectado":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>Como usar os recursos do workspace e do Studio do Synapse para acessar e usar um pool de SQL dedicado (antigo SQL DW)
 
As informações a seguir aplicam-se ao usar um SQL DW dedicado (antigo SQL DW) com os recursos do workspace do Synapse habilitados: 
- **Funcionalidades do SQL** Todos os recursos do SQL permanecerão com o SQL Server lógico depois que o recurso de workspace do Synapse for habilitado. O acesso ao servidor por meio do provedor de recursos do SQL ainda será possível depois da habilitação do workspace. É possível iniciar todas as funções de gerenciamento por meio do workspace, e a operação ocorrerá no SQL Server lógico que hospeda os pools de SQL. Não haverá falha ou interrupção de automação, ferramentas ou conexões devido à habilitação do workspace.  
- **Migração de recursos** A migração de recursos em um servidor com o recurso de workspace do Synapse habilitado quebra o link entre o servidor e o workspace, e você não poderá mais acessar as instâncias do pool de SQL dedicado (antigo SQL DW) no workspace. Para manter a conexão, recomendamos manter os dois recursos na mesma assinatura e no mesmo grupo de recurso. 
- **Monitoramento** As solicitações SQL enviadas por meio do Synapse Studio em um pool de SQL dedicado habilitado para workspace (antigo SQL DW) podem ser exibidas no hub do monitor. Para todas as outras atividades de monitoramento, você pode acessar o monitoramento do pool de SQL dedicado do portal do Azure (antigo SQL DW). 
- **Segurança** e **controles de acesso** Conforme mencionado acima, todas as funções de gerenciamento do SQL Server e das instâncias dos pools de SQL dedicados (antigos SQL DW) continuarão a residir no SQL Server lógico. As funções incluem o gerenciamento de regras de firewall, a definição do administrador do Azure AD do servidor e todo o controle de acesso dos dados no pool de SQL dedicado (antigo SQL DW). É necessário seguir as etapas abaixo para acessar e usar o pool de SQL dedicado (antigo SQL DW) no workspace do Synapse. As associações de função do workspace não dão aos usuários permissões para os dados em instâncias do pool de SQL dedicado (antigo SQL DW). Siga suas políticas normais de [autenticação do SQL](sql-data-warehouse-authentication.md) para garantir que os usuários possam acessar as instâncias do pool de SQL dedicado (antigo SQL DW) no servidor lógico. Se já houver uma identidade gerenciada atribuída ao servidor host do pool de SQL dedicado (antigo SQL DW), o nome da identidade será igual ao nome da identidade gerenciada do Workspace que é criada automaticamente em suporte aos serviços de parceiro do Workspace (por exemplo, pipelines de ADF).  Duas identidades gerenciadas com o mesmo nome podem existir em um cenário conectado. É possível distinguir as identidades gerenciadas pelas IDs de objeto do Azure AD. A funcionalidade de criação de usuários do SQL usando IDs de objeto estará disponível em breve.

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > O workspace conectado do Synapse Studio exibe os nomes dos pools dedicados com base nas permissões que o usuário tem no Azure. Os objetos nos pools não estarão acessíveis se o usuário não tiver permissões nos pools de SQL. 

- **Segurança de rede** Se o workspace do Synapse em que você habilitou o pool de SQL dedicado (antigo SQL DW) estiver habilitado para proteção de infiltração de dados, crie uma conexão gerenciada de ponto de extremidade privado entre o workspace e o SQL Server lógico. Aprove a solicitação de conexão de ponto de extremidade privado para permitir comunicações entre o servidor e workspace.
- Pools de SQL do **Studio** no **Hub de dados** Um pool de SQL dedicado habilitado para workspace (antigo SQL DW) pode ser identificado por meio da dica de ferramenta no Hub de dados. 
- **Como criar um pool de SQL dedicado (antigo SQL DW)** É possível criar pools de SQL dedicados por meio do workspace e do Studio do Synapse após a habilitação do recurso do workspace. O provisionamento do novo pool será feito no SQL Server lógico. Os novos recursos serão exibidos no portal e no Studio quando o provisionamento for concluído.      

## <a name="next-steps"></a>Próximas etapas
Habilitar os [recursos do workspace do Synapse](workspace-connected-create.md) em um pool de SQL dedicado (antigo SQL DW)
