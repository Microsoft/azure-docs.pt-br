---
title: Visão geral do modelo de compra de vCore
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: O modelo de compra de vCore permite que você escale independentemente recursos de computação e de armazenamento, equipare o desempenho local e otimize o preço do Banco de Dados SQL do Azure e da Instância Gerenciada de SQL do Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 01/15/2021
ms.openlocfilehash: 3bd617f052d52339ae35e5a088c6ee85b797fb48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779175"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>Visão geral do modelo de vCore – Banco de Dados SQL do Azure e Instância Gerenciada de SQL do Azure 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O modelo de compra de vCore (núcleo virtual) usado pelo Banco de Dados SQL do Azure e pela Instância Gerenciada de SQL do Azure fornece vários benefícios:

- Limites de computação, memória, E/S e armazenamento mais altos.
- Controle sobre a geração do hardware de maneira a corresponder melhor aos requisitos de computação e de memória da carga de trabalho.
- Preços com descontos para o [AHB (Benefício Híbrido do Azure)](../azure-hybrid-benefit.md) e a [RI (instância reservada)](reserved-capacity-overview.md).
- Maior transparência nos detalhes do hardware que executa a computação, o que facilita o planejamento de migrações de implantações locais.

## <a name="service-tiers"></a>Camadas de serviço

As opções da camada de serviço no modelo de vCore incluem Uso Geral, Comercialmente Crítico e Hiperescala. Geralmente, a camada de serviço define a arquitetura de armazenamento, os limites de espaço e de E/S e as opções de continuidade de negócios relacionadas à disponibilidade e à recuperação de desastre.

|-|**Uso Geral**|**Comercialmente Crítico**|**Hiperescala**|
|---|---|---|---|
|Mais adequado para|A maioria das cargas de trabalho comerciais. Oferece opções de armazenamento e de computação voltadas para o orçamento, equilibradas e escalonáveis. |Oferece aos aplicativos de negócios a maior resiliência a falhas usando várias réplicas isoladas e fornece o desempenho de E/S mais elevado por réplica de banco de dados.|A maioria das cargas de trabalho de negócios com requisitos de escala de leitura e armazenamento altamente escalonáveis.  Oferece maior resiliência a falhas permitindo a configuração de mais de uma réplica de banco de dados isolada. |
|Armazenamento|Usa o armazenamento remoto.<br/>**Computação provisionada do Banco de Dados SQL**:<br/>5 GB – 4 TB<br/>**Computação sem servidor**:<br/>5 GB – 3 TB<br/>**Instância Gerenciada de SQL**: 32 GB – 8 TB |Usa o armazenamento SSD local.<br/>**Computação provisionada do Banco de Dados SQL**:<br/>5 GB – 4 TB<br/>**Instância Gerenciada de SQL**:<br/>32 GB - 4 TB |Aumento automático flexível do armazenamento, conforme necessário. Dá suporte a até 100 TB de armazenamento. Usa o armazenamento SSD local para o cache do pool de buffers local e o armazenamento de dados local. Usa o armazenamento remoto do Azure como armazenamento de dados de longo prazo final. |
|IOPS e taxa de transferência (aproximada)|**Banco de Dados SQL**: confira os limites de recursos para [bancos de dados individuais](resource-limits-vcore-single-databases.md) e [pools elásticos](resource-limits-vcore-elastic-pools.md).<br/>**Instância Gerenciada de SQL**: confira a [Visão geral dos limites de recursos da Instância Gerenciada de SQL do Azure](../managed-instance/resource-limits.md#service-tier-characteristics).|Confira os limites de recursos para [bancos de dados individuais](resource-limits-vcore-single-databases.md) e [pools elásticos](resource-limits-vcore-elastic-pools.md).|A Hiperescala é uma arquitetura de várias camadas com cache em vários níveis. A IOPS e a taxa de transferência efetivas dependerão da carga de trabalho.|
|Disponibilidade|1 réplica, nenhuma réplica em escala de leitura|3 réplicas, 1 [réplica em escala de leitura](read-scale-out.md),<br/>HA (alta disponibilidade) com redundância de zona|1 réplica de leitura-gravação, mais 0 – 4 [réplicas em escala de leitura](read-scale-out.md)|
|Backups|[RA-GRS (armazenamento com redundância geográfica com acesso de leitura)](../../storage/common/geo-redundant-design.md), 1 – 35 dias (7 dias por padrão)|[RA-GRS](../..//storage/common/geo-redundant-design.md), 1 – 35 dias (7 dias por padrão)|Backups baseados em instantâneos no armazenamento remoto do Azure. As restaurações usam esses instantâneos para recuperação rápida. Os backups são instantâneos e não afetam o desempenho de E/S da computação. As restaurações são rápidas e não são operações com tamanho de dados (levando minutos em vez de horas ou dias).|
|Na memória|Sem suporte|Com suporte|Sem suporte|
|||


### <a name="choosing-a-service-tier"></a>Como escolher uma camada de serviço

Para obter informações sobre como selecionar uma camada de serviço para sua carga de trabalho específica, confira os seguintes artigos:

- [Quando escolher a camada de serviço de Uso Geral](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Quando escolher a camada de serviço Comercialmente Crítico](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Quando escolher a camada de serviço Hiperescala](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Camadas de computação

As opções de camada de computação no modelo vCore incluem as camadas de computação provisionada e sem servidor.


### <a name="provisioned-compute"></a>Computação provisionada

A camada de computação provisionada fornece uma quantidade específica de recursos de computação que são provisionados continuamente, de modo independente da atividade da carga de trabalho, e cobra pela quantidade de computação provisionada com um preço fixo por hora.


### <a name="serverless-compute"></a>Computação sem servidor

A [camada de computação sem servidor](serverless-tier-overview.md) dimensiona automaticamente os recursos de computação com base na atividade da carga de trabalho e cobra pela quantidade de computação usada por segundo.



## <a name="hardware-generations"></a>Gerações de hardware

As opções de geração de hardware no modelo de vCore incluem Gen 4/5, série M, série Fsv2 e série DC. A geração do hardware define de modo geral os limites de computação e de memória e outras características que afetam o desempenho da carga de trabalho.

### <a name="gen4gen5"></a>Gen4/Gen5

- O hardware Gen4/Gen5 fornece recursos de computação e de memória equilibrados e é adequado para a maioria das cargas de trabalho de banco de dados que não têm requisitos de memória mais altos, de vCore mais elevado e de vCore único mais rápido, como os que são fornecidos pelas séries Fsv2 e M.

Para regiões em que o Gen4/Gen5 está disponível, confira [disponibilidade do Gen4/Gen5](#gen4gen5-1).

### <a name="fsv2-series"></a>Série Fsv2

- A série Fsv2 é uma opção de hardware que tem computação otimizada e oferece baixa latência de CPU e alta velocidade para a maioria das cargas de trabalho com maior demanda de CPU.
- Dependendo da carga de trabalho, a série Fsv2 pode fornecer mais desempenho de CPU por vCore que o Gen5, e o tamanho de 72 vCores pode fornecer mais desempenho de CPU com custo menor do que os 80 vCores no Gen5. 
- O Fsv2 fornece menos memória e tempdb por vCore do que outros hardwares, de modo que para cargas de trabalho sensíveis a esses limites pode ser indicado considerar o Gen5 ou a série M.  

A série Fsv2 tem suporte somente na camada de Uso Geral. Para regiões em que a série Fsv2 está disponível, confira [disponibilidade da série Fsv2](#fsv2-series-1).

### <a name="m-series"></a>Série M

- A série M é uma opção de hardware com otimização para memória para cargas de trabalho que exigem mais memória e limites de computação mais altos do que o que é fornecido pelo Gen5.
- A série M fornece 29 GB por vCore e até 128 vCores, o que aumenta o limite de memória com relação ao Gen5 em 8 vezes, chegando a quase 4 TB.

A série M tem suporte somente na camada Comercialmente Crítico e não dá suporte à redundância de zona.  Para regiões em que a série M está disponível, confira [disponibilidade da série M](#m-series-1).

#### <a name="azure-offer-types-supported-by-m-series"></a>Tipos de oferta do Azure com suporte da série M

Para acessar a série M, a assinatura deve ser um tipo de oferta paga, incluindo Pagamento Conforme o Uso ou EA (Contrato Enterprise).  Para ver uma lista completa dos tipos de oferta do Azure com suporte da série M, confira [ofertas atuais sem limites de gastos](https://azure.microsoft.com/support/legal/offer-details).

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>Série DC

> [!NOTE]
> A série DC está em **versão prévia pública**.

- O hardware da série DC usa processadores Intel com a tecnologia Intel SGX (Software Guard Extensions).
- A série DC é necessária para [Always Encrypted com enclaves seguros](/sql/relational-databases/security/encryption/always-encrypted-enclaves), que não tem suporte com outras configurações de hardware.
- A série DC foi projetada para cargas de trabalho que processam dados confidenciais e exigem funcionalidades de processamento de consulta confidencial, fornecidas pelo Always Encrypted com enclaves seguro.
- O hardware da série DC fornece recursos de computação e de memória equilibrados.

A série DC tem suporte somente para computação provisionada (não há suporte para computação sem servidor) e não dá suporte à redundância de zona. Para regiões em que a série DC está disponível, confira [disponibilidade da série DC](#dc-series-1).

#### <a name="azure-offer-types-supported-by-dc-series"></a>Tipos de oferta do Azure com suporte da série DC

Para acessar a série DC, a assinatura deve ser um tipo de oferta paga, incluindo Pagamento Conforme o Uso ou EA (Contrato Enterprise).  Para ver uma lista completa dos tipos de oferta do Azure com suporte da série DC, confira [ofertas atuais sem limites de gastos](https://azure.microsoft.com/support/legal/offer-details).

### <a name="compute-and-memory-specifications"></a>Especificações de computação e memória


|Geração de hardware  |Computação  |Memória  |
|:---------|:---------|:---------|
|Gen4     |– Processadores Intel® E5-2673 v3 (Haswell) de 2,4 GHz<br>– Provisionamento de até 24 vCores (1 vCore = 1 núcleo físico)  |– 7 GB por vCore<br>– Provisionamento de até 168 GB|
|Gen5     |**Computação provisionada**<br>– Processadores Intel® E5-2673 v4 (Broadwell) de 2,3 GHz, Intel® SP-8160 (Skylake)\* e Intel® 8272CL (Cascade Lake) de 2,5 GHz\*<br>– Provisionamento de até 80 vCores (1 vCore = 1 hiper thread)<br><br>**Computação sem servidor**<br>– Processadores Intel® E5-2673 v4 (Broadwell) de 2,3 GHz e Intel® SP-8160 (Skylake)*<br>– Dimensionamento automático para até 40 vCores (1 vCore = 1 hiper thread)|**Computação provisionada**<br>– 5,1 GB por vCore<br>– Provisionamento de até 408 GB<br><br>**Computação sem servidor**<br>– Dimensionamento automático para até 24 GB por vCore<br>– Dimensionamento automático para um máximo de 120 GB|
|Série Fsv2     |– Processadores Intel® 8168 (Skylake)<br>– Incluindo velocidade turbo sustentada de 3,4 GHz em todos os núcleos e velocidade máxima de 3,7 GHz em apenas um núcleo.<br>– Provisionamento de até 72 vCores (1 vCore = 1 hiper thread)|– 1,9 GB por vCore<br>– Provisionamento de até 136 GB|
|Série M     |– Processadores Intel® E7-8890 v3 de 2,5 GHz e Intel® 8280M de 2,7 GHz (Cascade Lake)<br>– Provisionamento de até 128 vCores (1 vCore = 1 hiper thread)|– 29 GB por vCore<br>– Provisionamento de até 3,7 TB|
|Série DC     | – Processadores Intel XEON E-2288G<br>– Incluindo o Intel SGX (Software Guard Extensions)<br>– Provisionamento de até 8 vCores (1 vCore = 1 núcleo físico) | 4,5 GB por vCore |

\* Na exibição de gerenciamento dinâmico [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database), a geração de hardware para bancos de dados que usam processadores Intel® SP-8160 (Skylake) aparece como Gen6, enquanto a geração de hardware para bancos de dados que usam o Intel® 8272CL (Cascade Lake) aparece como Gen7. Os limites de recursos para todos os bancos de dados Gen5 são os mesmos, independentemente do tipo de processador (Broadwell, Skylake ou Cascade Lake).

Para obter mais informações sobre limites de recursos, confira [Limites de recursos para bancos de dados individuais (vCore)](resource-limits-vcore-single-databases.md) ou [Limites de recursos para pools elásticos (vCore)](resource-limits-vcore-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Selecionando uma geração de hardware

No portal do Azure, você pode selecionar a geração de hardware para um banco de dados ou pool no Banco de Dados SQL no momento da criação ou pode alterar a geração de hardware de um banco de dados ou pool existente.

**Para selecionar a geração de hardware ao criar um pool ou banco de dados SQL**

Para obter informações detalhadas, confira [Criar um Banco de Dados SQL](single-database-create-quickstart.md).

Na guia **Básico**, selecione o link **Configurar banco de dados** na seção **Computação + armazenamento** e selecione o link **Alterar configuração**:

  ![configurar o banco de dados](./media/service-tiers-vcore/configure-sql-database.png)

Selecione a geração de hardware desejada:

  ![selecionar hardware](./media/service-tiers-vcore/select-hardware.png)


**Para alterar a geração de hardware de um pool ou banco de dados SQL existente**

Para um banco de dados, na página Visão geral, selecione o link **Tipo de preço**:

  ![alterar hardware](./media/service-tiers-vcore/change-hardware.png)

Para um pool, na página Visão geral, selecione **Configurar**.

Siga as etapas para alterar a configuração e selecione a geração de hardware conforme descrito nas etapas anteriores.

**Para selecionar a geração de hardware ao criar uma Instância Gerenciada de SQL**

Para obter informações detalhadas, confira [Criar uma Instância Gerenciada de SQL](../managed-instance/instance-create-quickstart.md).

Na guia **Básico**, selecione o link **Configurar banco de dados** na seção **Computação + armazenamento** e selecione a geração de hardware desejada:

  ![configurar Instância Gerenciada de SQL](./media/service-tiers-vcore/configure-managed-instance.png)
  
**Para alterar a geração de hardware de uma Instância Gerenciada de SQL existente**

# <a name="the-azure-portal"></a>[O portal do Azure](#tab/azure-portal)

Na página da Instância Gerenciada de SQL, selecione o link **Tipo de preço** na seção Configurações

![alterar o hardware da Instância Gerenciada de SQL](./media/service-tiers-vcore/change-managed-instance-hardware.png)

Na página Tipo de preço, você poderá alterar a geração de hardware conforme descrito nas etapas anteriores.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use o seguinte script do PowerShell:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Para obter mais detalhes, verifique o comando [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance).

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Use o seguinte comando da CLI:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Para obter mais detalhes, confira o comando [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update).

---

### <a name="hardware-availability"></a>Disponibilidade de hardware

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> Gen4/Gen5

O hardware Gen4 [está sendo descontinuado](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) e não está mais disponível para novas implantações. Todos os novos bancos de dados devem ser implantados em hardware Gen5.

O Gen5 está disponível em todas as regiões públicas do mundo.

#### <a name="fsv2-series"></a>Série Fsv2

A série Fsv2 está disponível nas seguintes regiões: Austrália Central, Austrália Central 2, Leste da Austrália, Sudeste da Austrália, Sul do Brasil, Canadá Central, Leste da Ásia, Leste dos EUA, França Central, Índia Central, Coreia Central, Sul da Coreia, Norte da Europa, Norte da África do Sul, Sudeste da Ásia, Sul do Reino Unido, Oeste do Reino Unido, Oeste da Europa, Oeste dos EUA 2.


#### <a name="m-series"></a>Série M

A série M está disponível nas seguintes regiões: Leste dos EUA, Norte da Europa, Oeste da Europa, Oeste dos EUA 2.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

#### <a name="dc-series"></a>Série DC

> [!NOTE]
> A série DC está em **versão prévia pública**.

A série DC está disponível nas seguintes regiões: Canadá Central, Leste do Canadá, Leste dos EUA, Norte da Europa, Sul do Reino Unido, Oeste da Europa, Oeste dos EUA.

Se você precisar da série DC em uma região atualmente sem suporte, [envie um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) seguindo as instruções em [Solicitar aumentos de cota para o Banco de Dados SQL do Azure e a Instância Gerenciada de SQL](quota-increase-request.md).

## <a name="next-steps"></a>Próximas etapas

Para começar. confira: 
- [Criando um banco de dados SQL usando o portal do Azure](single-database-create-quickstart.md)
- [Criando uma Instância Gerenciada de SQL usando o portal do Azure](../managed-instance/instance-create-quickstart.md)

Para obter detalhes sobre preços, confira a [página de preços do Banco de Dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

Para obter detalhes sobre os tamanhos de computação e armazenamento específicos disponíveis nas camadas de serviço de uso geral e comercialmente crítico, confira:

- [Limites de recursos baseados em vCore para o Banco de Dados SQL do Azure](resource-limits-vcore-single-databases.md).
- [Limites de recursos baseados em vCore para o Banco de Dados SQL do Azure em pool](resource-limits-vcore-elastic-pools.md).
- [Limites de recursos baseados em vCore para a Instância Gerenciada de SQL do Azure](../managed-instance/resource-limits.md).
