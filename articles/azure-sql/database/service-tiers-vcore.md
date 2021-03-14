---
title: Visão geral do modelo de compra vCore
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: O modelo de compra vCore permite que você dimensione independentemente os recursos de computação e armazenamento, correspondam ao desempenho local e otimize o preço do banco de dados SQL do Azure e do Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 01/15/2021
ms.openlocfilehash: 499e0aa1ee451969923dbdf5f84be1c844a9aab4
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659317"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>Visão geral do modelo vCore-banco de dados SQL do Azure e Azure SQL Instância Gerenciada 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O modelo de compra vCore (núcleo virtual) usado pelo banco de dados SQL do Azure e pelo Azure SQL Instância Gerenciada fornece vários benefícios:

- Limites de computação, memória, e/s e armazenamento mais altos.
- Controle sobre a geração de hardware para corresponder melhor aos requisitos de computação e de memória da carga de trabalho.
- Descontos de preço para [benefício híbrido do Azure (AHB)](../azure-hybrid-benefit.md) e [instância reservada (ri)](reserved-capacity-overview.md).
- Maior transparência nos detalhes de hardware que capacitam a computação, que facilita o planejamento de migrações de implantações locais.

## <a name="service-tiers"></a>Camadas de serviço

As opções da camada de serviço no modelo vCore incluem Uso Geral, Comercialmente Crítico e hiperescala. A camada de serviço geralmente define a arquitetura de armazenamento, os limites de espaço e de e/s e as opções de continuidade de negócios relacionadas à disponibilidade e à recuperação de desastres.

|-|**Uso Geral**|**Comercialmente Crítico**|**Hiperescala**|
|---|---|---|---|
|Mais adequado para|A maioria das cargas de trabalho comerciais. Oferece opções de armazenamento e de computação voltadas para o orçamento, equilibradas e escalonáveis. |Oferece aos aplicativos de negócios a resiliência mais alta para falhas usando várias réplicas isoladas e fornece o desempenho de e/s mais alto por réplica de banco de dados.|A maioria das cargas de trabalho de negócios com requisitos de armazenamento e escala de leitura altamente escalonáveis.  Oferece maior resiliência a falhas, permitindo a configuração de mais de uma réplica de banco de dados isolada. |
|Armazenamento|Usa o armazenamento remoto.<br/>**Computação provisionada do banco de dados SQL**:<br/>5 GB – 4 TB<br/>**Computação sem servidor**:<br/>5 GB-3 TB<br/>**SQL instância gerenciada**: 32 GB-8 TB |Usa o armazenamento SSD local.<br/>**Computação provisionada do banco de dados SQL**:<br/>5 GB – 4 TB<br/>**Instância gerenciada do SQL**:<br/>32 GB - 4 TB |Crescimento automático flexível do armazenamento, conforme necessário. Dá suporte a até 100 TB de armazenamento. Usa o armazenamento SSD local para o cache do pool de buffers local e o armazenamento de dados local. Usa o armazenamento remoto do Azure como armazenamento de dados de longo prazo final. |
|IOPS e taxa de transferência (aproximado)|**Banco de dados SQL**: consulte limites de recursos para [bancos de dados individuais](resource-limits-vcore-single-databases.md) e [pools elásticos](resource-limits-vcore-elastic-pools.md).<br/>**Sql instância gerenciada**: consulte [visão geral limites de recursos do SQL instância gerenciada do Azure](../managed-instance/resource-limits.md#service-tier-characteristics).|Consulte limites de recursos para [bancos de dados individuais](resource-limits-vcore-single-databases.md) e [pools elásticos](resource-limits-vcore-elastic-pools.md).|O hiperscale é uma arquitetura de várias camadas com cache em vários níveis. O IOPS e a taxa de transferência efetivos dependerão da carga de trabalho.|
|Disponibilidade|1 réplica, sem réplicas de escala de leitura|3 réplicas, 1 [réplica em escala de leitura](read-scale-out.md),<br/>alta disponibilidade com redundância de zona (HA)|1 réplica de leitura/gravação, além [de 0-4 réplicas de escala de leitura](read-scale-out.md)|
|Backups|[Armazenamento com redundância geográfica com acesso de leitura (ra-grs)](../../storage/common/geo-redundant-design.md), 1-35 dias (7 dias por padrão)|[Ra-grs](../..//storage/common/geo-redundant-design.md), 1-35 dias (7 dias por padrão)|Backups baseados em instantâneo no armazenamento remoto do Azure. As restaurações usam esses instantâneos para recuperação rápida. Os backups são instantâneos e não afetam O desempenho de e/s de computação. As restaurações são rápidas e não são uma operação de tamanho de dados (levando minutos em vez de horas ou dias).|
|Na memória|Sem suporte|Com suporte|Sem suporte|
|||


### <a name="choosing-a-service-tier"></a>Como escolher uma camada de serviço

Para obter informações sobre como selecionar uma camada de serviço para sua carga de trabalho específica, consulte os seguintes artigos:

- [Quando escolher a camada de serviço Uso Geral](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Quando escolher a camada de serviço Comercialmente Crítico](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Quando escolher a camada de serviço de hiperescala](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Camadas de computação

As opções de camada de computação no modelo vCore incluem as camadas de computação provisionada e sem servidor.


### <a name="provisioned-compute"></a>Computação provisionada

A camada de computação provisionada fornece uma quantidade específica de recursos de computação que são continuamente provisionados independentemente da atividade de carga de trabalho e cobra pela quantidade de computação provisionada a um preço fixo por hora.


### <a name="serverless-compute"></a>Computação sem servidor

A [camada de computação sem servidor](serverless-tier-overview.md) dimensiona automaticamente os recursos de computação com base na atividade de carga de trabalho e cobra pela quantidade de computação usada por segundo.



## <a name="hardware-generations"></a>Gerações de hardware

As opções de geração de hardware no modelo vCore incluem Gen 4/5, série M, Fsv2 e série DC. A geração do hardware define de modo geral os limites de computação e de memória e outras características que afetam o desempenho da carga de trabalho.

### <a name="gen4gen5"></a>Gen4/Gen5

- O hardware Gen4/Gen5 fornece recursos de computação e memória equilibrados e é adequado para a maioria das cargas de trabalho de banco de dados que não têm mais memória, vCore mais elevado ou requisitos de vCore único mais rápidos, conforme fornecido pela série Fsv2 ou série M.

Para regiões em que o Gen4/Gen5 está disponível, consulte [disponibilidade de Gen4/Gen5](#gen4gen5-1).

### <a name="fsv2-series"></a>Série Fsv2

- A série Fsv2 é uma opção de hardware otimizado para computação que oferece baixa latência de CPU e alta velocidade de clock para a maioria das cargas de trabalho com maior demanda de CPU.
- Dependendo da carga de trabalho, a série Fsv2 pode fornecer mais desempenho de CPU por vCore do que Gen5, e o tamanho vCore de 72 pode fornecer mais desempenho de CPU para menos custo do que 80 vCores em Gen5. 
- O Fsv2 fornece menos memória e tempdb por vCore do que outros Hardwares, de modo que as cargas de trabalho sensíveis a esses limites podem querer considerar a Gen5 ou a série M em vez disso.  

Fsv2-Series somente com suporte na camada de Uso Geral. Para regiões em que a Fsv2-Series está disponível, consulte [disponibilidade da série Fsv2](#fsv2-series-1).

### <a name="m-series"></a>Série M

- A série M é uma opção de hardware com otimização de memória para cargas de trabalho que exigem mais memória e limites de computação mais altos do que o fornecido pelo Gen5.
- A série M fornece 29 GB por vCore e até 128 vCores, o que aumenta o limite de memória em relação ao Gen5 por 8x a quase 4 TB.

A série M só tem suporte na camada de Comercialmente Crítico e não oferece suporte à redundância de zona.  Para regiões em que a série M está disponível, consulte [disponibilidade da série m](#m-series-1).

#### <a name="azure-offer-types-supported-by-m-series"></a>Tipos de oferta do Azure com suporte da série M

Para acessar a série M, a assinatura deve ser um tipo de oferta paga, incluindo pré-pago ou Contrato Enterprise (EA).  Para obter uma lista completa dos tipos de oferta do Azure com suporte da série M, consulte [ofertas atuais sem limites de gastos](https://azure.microsoft.com/support/legal/offer-details).

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>Série DC

> [!NOTE]
> A série DC está atualmente em **Visualização pública**.

- O hardware da série DC usa processadores Intel com a tecnologia Intel SGX (software Guard Extensions).
- A série DC é necessária para [Always Encrypted com o Secure enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves), que não tem suporte com outras configurações de hardware.
- A série DC foi projetada para cargas de trabalho que processam dados confidenciais e exigem recursos de processamento de consulta confidencial, fornecidos por Always Encrypted com enclaves seguro.
- O hardware da série DC fornece recursos de computação e memória equilibrados.

A série DC só tem suporte para a computação provisionada (sem suporte para servidor) e não dá suporte à redundância de zona. Para regiões em que a série CC está disponível, consulte [disponibilidade da série CC](#dc-series-1).

#### <a name="azure-offer-types-supported-by-dc-series"></a>Tipos de oferta do Azure com suporte da série DC

Para acessar a série DC, a assinatura deve ser um tipo de oferta paga, incluindo pré-pago ou Contrato Enterprise (EA).  Para obter uma lista completa dos tipos de oferta do Azure com suporte da série DC, consulte [ofertas atuais sem limites de gastos](https://azure.microsoft.com/support/legal/offer-details).

### <a name="compute-and-memory-specifications"></a>Especificações de memória e computação


|Geração de hardware  |Computação  |Memória  |
|:---------|:---------|:---------|
|Gen4     |-Processadores Intel® E5-2673 v3 (Haswell) 2,4 GHz<br>-Provisionar até 24 vCores (1 vCore = 1 núcleo físico)  |-7 GB por vCore<br>-Provisionar até 168 GB|
|Gen5     |**Computação provisionada**<br>-Processadores Intel® E5-2673 V4 (Broadwell) 2,3-GHz, Intel® SP-8160 (Skylake) \* e Intel® 8272CL (cascadey Lake) 2,5 GHz \*<br>-Provisionar até 80 vCores (1 vCore = 1 Hyper-thread)<br><br>**Computação sem servidor**<br>-Processadores Intel® E5-2673 V4 (Broadwell) 2,3-GHz e Intel® SP-8160 (Skylake) *<br>-Escalar verticalmente até 40 vCores (1 vCore = 1 Hyper-thread)|**Computação provisionada**<br>-5,1 GB por vCore<br>-Provisionar até 408 GB<br><br>**Computação sem servidor**<br>-Escala automática de até 24 GB por vCore<br>-Escala automática de até 120 GB máx.|
|Série Fsv2     |-Processadores Intel® 8168 (Skylake)<br>-Apresentando uma velocidade de clock de Turbo principal de 3,4 GHz e uma velocidade máxima de clock de único núcleo de 3,7 GHz.<br>-Provisionar até 72 vCores (1 vCore = 1 Hyper-thread)|-1,9 GB por vCore<br>-Provisionar até 136 GB|
|Série M     |-Processadores Intel® E7-8890 v3 2,5 GHz e Intel® 8280M 2,7 GHz (cascade)<br>-Provisionar até 128 vCores (1 vCore = 1 Hyper-thread)|-29 GB por vCore<br>-Provisionar até 3,7 TB|
|Série DC     | -Processadores Intel XEON E-2288G<br>-Incluindo a extensão Intel software Guard (Intel SGX))<br>-Provisionar até 8 vCores (1 vCore = 1 núcleo físico) | 4,5 GB por vCore |

\* Na exibição de gerenciamento dinâmico [Sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) , a geração de hardware para bancos de dados usando os processadores Intel® SP-8160 (Skylake) aparece como Gen6, enquanto a geração de hardware para bancos de dados usando o Intel® 8272CL (cascadey Lake) aparece como Gen7. Os limites de recursos para todos os bancos de dados do Gen5 são os mesmos, independentemente do tipo de processador (Broadwell, Skylake ou Cascade Lake).

Para obter mais informações sobre limites de recursos, consulte [limites de recursos para VCORE (bancos de dados individuais)](resource-limits-vcore-single-databases.md)ou [limites de recursos para pools elásticos (VCORE)](resource-limits-vcore-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Selecionando uma geração de hardware

No portal do Azure, você pode selecionar a geração de hardware para um banco de dados ou pool no banco de dados SQL no momento da criação ou pode alterar a geração de hardware de um banco de dados ou pool existente.

**Para selecionar uma geração de hardware ao criar um banco de dados SQL ou pool**

Para obter informações detalhadas, consulte [criar um banco de dados SQL](single-database-create-quickstart.md).

Na guia **noções básicas** , selecione o link **configurar banco de dados** na seção **computação + armazenamento** e, em seguida, selecione o link **Alterar configuração** :

  ![configurar o banco de dados](./media/service-tiers-vcore/configure-sql-database.png)

Selecione a geração de hardware desejada:

  ![selecionar hardware](./media/service-tiers-vcore/select-hardware.png)


**Para alterar a geração de hardware de um pool ou banco de dados SQL existente**

Para um banco de dados, na página Visão geral, selecione o link **tipo de preço** :

  ![alterar hardware](./media/service-tiers-vcore/change-hardware.png)

Para um pool, na página Visão geral, selecione **Configurar**.

Siga as etapas para alterar a configuração e selecione a geração de hardware conforme descrito nas etapas anteriores.

**Para selecionar uma geração de hardware ao criar um SQL Instância Gerenciada**

Para obter informações detalhadas, consulte [criar um instância gerenciada de dados SQL](../managed-instance/instance-create-quickstart.md).

Na guia **noções básicas** , selecione o link **configurar banco de dados** na seção **computação + armazenamento** e selecione a geração de hardware desejada:

  ![configurar o SQL Instância Gerenciada](./media/service-tiers-vcore/configure-managed-instance.png)
  
**Para alterar a geração de hardware de um SQL Instância Gerenciada existente**

# <a name="the-azure-portal"></a>[O portal do Azure](#tab/azure-portal)

Na página SQL Instância Gerenciada, selecione o link do **tipo de preço** posicionado na seção Configurações

![alterar o hardware do SQL Instância Gerenciada](./media/service-tiers-vcore/change-managed-instance-hardware.png)

Na página tipo de preço, você poderá alterar a geração de hardware conforme descrito nas etapas anteriores.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use o seguinte script do PowerShell:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Para obter mais detalhes, verifique o comando [set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) .

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Use o seguinte comando da CLI:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Para obter mais detalhes, marque o comando [AZ SQL Mi Update](/cli/azure/sql/mi#az-sql-mi-update) .

---

### <a name="hardware-availability"></a>Disponibilidade de hardware

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> Gen4/Gen5

O hardware Gen4 está [sendo](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) descontinuado e não está mais disponível para novas implantações. Todos os novos bancos de dados devem ser implantados em hardware Gen5.

O Gen5 está disponível em todas as regiões públicas em todo o mundo.

#### <a name="fsv2-series"></a>Série Fsv2

A série Fsv2 está disponível nas seguintes regiões: Austrália Central, Austrália Central 2, leste da Austrália, sudeste da Austrália, sul do Brasil, central do Canadá, Ásia Oriental, leste dos EUA, França central, Índia central, Coreia central, Coreia do Sul, Europa Setentrional, África do Sul, sudeste da Ásia, Sul do Reino Unido, Oeste do Reino Unido, Europa Ocidental, oeste dos EUA 2.


#### <a name="m-series"></a>Série M

A série M está disponível nas seguintes regiões: leste dos EUA, Europa Setentrional, Europa Ocidental, oeste dos EUA 2.
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
> A série DC está atualmente em **Visualização pública**.

A série DC está disponível nas seguintes regiões: Canadá central, leste do Canadá, leste dos EUA, Europa Setentrional, Sul do Reino Unido, Europa Ocidental, oeste dos EUA.

Se você precisar de uma série DC em uma região atualmente sem suporte, [envie um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) seguindo as instruções em [aumentos de cota de solicitação para o banco de dados SQL do Azure e o SQL instância gerenciada](quota-increase-request.md).

## <a name="next-steps"></a>Próximas etapas

Para começar. confira: 
- [Criando um banco de dados SQL usando o portal do Azure](single-database-create-quickstart.md)
- [Criando um Instância Gerenciada SQL usando o portal do Azure](../managed-instance/instance-create-quickstart.md)

Para obter detalhes de preços, consulte a [página de preços do banco de dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

Para obter detalhes sobre os tamanhos de computação e armazenamento específicos disponíveis nas camadas de serviço de uso geral e crítico para os negócios, consulte:

- [limites de recursos baseados em vCore para o banco de dados SQL do Azure](resource-limits-vcore-single-databases.md).
- [limites de recursos baseados em vCore para o banco de dados SQL do Azure em pool](resource-limits-vcore-elastic-pools.md).
- [limites de recursos baseados em vCore para o Azure SQL instância gerenciada](../managed-instance/resource-limits.md).
