---
title: Rede virtual gerenciada & pontos de extremidade privados gerenciados
description: Saiba mais sobre a rede virtual gerenciada e pontos de extremidade privados gerenciados em Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: 81d82bccd6b6bd97b84df5269dd59ffac4903370
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980347"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory rede virtual gerenciada (versão prévia)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explicará a rede virtual gerenciada e os pontos de extremidade privados gerenciados em Azure Data Factory.


## <a name="managed-virtual-network"></a>Rede virtual gerenciada

Quando você cria uma Azure Integration Runtime (IR) em Azure Data Factory rede virtual gerenciada (VNET), o Integration Runtime será provisionado com a rede virtual gerenciada e aproveitará os pontos de extremidade privados para se conectar com segurança aos armazenamentos de dados com suporte. 

A criação de um Azure IR na rede virtual gerenciada garante que o processo de integração de dados seja isolado e seguro. 

Benefícios do uso da rede virtual gerenciada:

- Com uma rede virtual gerenciada, você pode descarregar a carga de gerenciamento da rede virtual para Azure Data Factory. Você não precisa criar uma sub-rede para Azure Integration Runtime que pode eventualmente usar muitos IPs privados de sua rede virtual e exigiria um planejamento de infraestrutura de rede anterior. 
- Ele não exige um profundo conhecimento de rede do Azure para fazer integrações de dados com segurança. Em vez disso, começar a usar o ETL seguro é muito simplificado para engenheiros de dados. 
- A rede virtual gerenciada juntamente com pontos de extremidade privados gerenciados protege contra vazamento de dados. 

> [!IMPORTANT]
>Atualmente, a VNet gerenciada só tem suporte na mesma região que Azure Data Factory região.
 

![Arquitetura de rede virtual gerenciada por ADF](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Pontos de extremidade privados gerenciados

Pontos de extremidade privados gerenciados são pontos de extremidade privados criados no Azure Data Factory rede virtual gerenciada estabelecendo um link privado para recursos do Azure. Azure Data Factory gerencia esses pontos de extremidade privados em seu nome. 

![Novo ponto de extremidade privado gerenciado](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory dá suporte a links privados. O link privado permite que você acesse os serviços do Azure (PaaS) (como o armazenamento do Azure, Azure Cosmos DB, Azure Synapse Analytics (anteriormente SQL Data Warehouse)).

Quando você usa um link privado, o tráfego entre os armazenamentos de dados e a rede virtual gerenciada atravessa totalmente a rede de backbone da Microsoft. O Link Privado protege contra riscos de exfiltração dos dados. Você estabelece um link privado para um recurso criando um ponto de extremidade privado.

O ponto de extremidade privado usa um endereço IP privado na rede virtual gerenciada para efetivamente colocar o serviço nele. Os pontos de extremidade privados são mapeados para um recurso específico no Azure e não para todo o serviço. Os clientes podem limitar a conectividade a um recurso específico aprovado por sua organização. Saiba mais sobre [links privados e pontos de extremidade privados](../private-link/index.yml).

> [!NOTE]
> É recomendável que você crie Pontos de extremidade privados gerenciados para se conectar a todas as suas fontes de dados do Azure. 
 
> [!WARNING]
> Se um armazenamento de dados PaaS (BLOB, ADLS Gen2, Azure Synapse Analytics) tiver um ponto de extremidade privado já criado e, mesmo que ele permita acesso de todas as redes, o ADF só poderá acessá-lo usando um ponto de extremidade privado gerenciado. Certifique-se de criar um ponto de extremidade privado nesses cenários. 

Uma conexão de ponto de extremidade privado é criada em um estado "pendente" quando você cria um ponto de extremidade privado gerenciado no Azure Data Factory. Um fluxo de trabalho de aprovação é iniciado. O proprietário do recurso de link privado é responsável por aprovar ou rejeitar a conexão.

![Gerenciar o ponto de extremidade privado](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Se o proprietário aprova a conexão, o link privado é estabelecido. Caso contrário, o link privado não será estabelecido. Em ambos os casos, o Ponto de extremidade privado gerenciado será atualizado com o status da conexão.

![Aprovar ponto de extremidade privado gerenciado](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Somente um Ponto de extremidade privado gerenciado em um estado aprovado pode enviar tráfego para um determinado recurso de link privado.

## <a name="interactive-authoring"></a>Criação interativa
Os recursos de criação interativa são usados para funcionalidades como conexão de teste, procurar lista de pastas e lista de tabelas, obter esquema e Visualizar dados. Você pode habilitar a criação interativa ao criar ou editar um Azure Integration Runtime que está na rede virtual gerenciada pelo ADF. O serviço de back-end irá pré-configurar a computação para funcionalidades de criação interativa. Caso contrário, a computação será alocada toda vez que qualquer operação interativa for executada, o que levará mais tempo. O TTL (vida útil) para a criação interativa é de 60 minutos, o que significa que ele será desabilitado automaticamente após 60 minutos da última operação de criação interativa.

![Criação interativa](./media/managed-vnet/interactive-authoring.png)

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos
### <a name="supported-data-sources"></a>Fontes de dados com suporte
As fontes de dados a seguir têm suporte para se conectar por meio do link privado da rede virtual gerenciada do ADF.
- Armazenamento de Blobs do Azure
- Armazenamento de Tabelas do Azure
- Arquivos do Azure
- Azure Data Lake Gen2
- Banco de dados SQL do Azure (sem incluir o Azure SQL Instância Gerenciada)
- Azure Synapse Analytics (antigo SQL Data Warehouse)
- SQL CosmosDB do Azure
- Cofre de Chave do Azure
- Serviço de vínculo privado do Azure
- Azure Search
- Banco de Dados do Azure para MySQL
- Banco de Dados do Azure para PostgreSQL
- Banco de Dados do Azure para MariaDB

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Azure Data Factory rede virtual gerenciada está disponível nas seguintes regiões do Azure:
- Leste dos EUA
- Leste dos EUA 2
- Centro-Oeste dos EUA
- Oeste dos EUA
- Oeste dos EUA 2
- Centro-Sul dos Estados Unidos
- Centro dos EUA
- Norte da Europa
- Europa Ocidental
- Sul do Reino Unido
- Sudeste Asiático
- Leste da Austrália
- Sudeste da Austrália

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Comunicações de saída por meio do ponto de extremidade público da rede virtual gerenciada por ADF
- Somente a porta 443 é aberta para comunicações de saída.
- O armazenamento do Azure e o Azure Data Lake Gen2 não têm suporte para serem conectados por meio do ponto de extremidade público da rede virtual gerenciada por ADF.

### <a name="linked-service-creation-of-azure-key-vault"></a>Criação de serviço vinculado de Azure Key Vault 
- Quando você cria um serviço vinculado para Azure Key Vault, não há nenhuma referência de Azure Integration Runtime. Portanto, você não pode criar um ponto de extremidade privado durante a criação de serviço vinculado do Azure Key Vault. Mas quando você cria um serviço vinculado para armazenamentos de dados que referencia Azure Key Vault serviço vinculado e esse serviço vinculado faz Azure Integration Runtime com a rede virtual gerenciada habilitada, você pode criar um ponto de extremidade privado para o serviço vinculado Azure Key Vault durante a criação. 
- **Testar** a operação de conexão para o serviço vinculado do Azure Key Vault valida apenas o formato da URL, mas não faz nenhuma operação de rede.

## <a name="next-steps"></a>Próximas etapas

- Tutorial: [criar um pipeline de cópia usando rede virtual gerenciada e pontos de extremidade privados](tutorial-copy-data-portal-private.md) 
- Tutorial: [criar um pipeline de fluxo de base de mapeamento usando rede virtual gerenciada e pontos de extremidade privados](tutorial-data-flow-private.md)