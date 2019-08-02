---
title: Limites de recursos do Banco de Dados SQL do Azure - instância gerenciada | Microsoft Docs
description: Este artigo fornece uma visão geral dos limites de recursos do Banco de Dados SQL do Azure para instâncias gerenciadas.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
manager: craigg
ms.date: 06/26/2019
ms.openlocfilehash: 29ece0677c71a2cb423e541cf2e9f4a06947e44c
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413390"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Visão geral dos limites de recursos de instância gerenciada do banco de dados SQL

Este artigo fornece uma visão geral dos limites de recursos para a instância gerenciada do banco de dados SQL do Azure e fornece informações sobre como solicitar um aumento para esses limites.

> [!NOTE]
> Para diferenças em recursos suportados e instruções T-SQL, consulte [Diferenças de recursos](sql-database-features.md) e [Suporte à instrução T-SQL](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>Limites de recursos no nível da instância

A instância gerenciada tem características e limites de recursos que dependem da infraestrutura e da arquitetura subjacentes. Os limites dependem da geração de hardware e da camada de serviço.

### <a name="hardware-generation-characteristics"></a>Características de geração de hardware

Instância gerenciada do banco de dados SQL do Azure pode ser implantada em duas gerações de hardware: Gen4 e Gen5. As gerações de hardware têm características diferentes, conforme descrito na tabela a seguir:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardware | Processadores Intel E5-2673 v3 (Haswell) de 2,4 GHz, SSD ligado vCore = 1 PP (núcleo físico) | Processadores Intel E5-2673 v4 (Broadwell) 2,3 GHz, SSD NVMe rápido, vCore=1 LP (hyper-thread) |
| Número de vCores | 8, 16, 24 vCores | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| Memória máxima (taxa de memória/núcleo) | 7 GB por vCore<br/>Adicione mais vCores para obter mais memória. | 5.1 GB por vCore<br/>Adicione mais vCores para obter mais memória. |
| Memória máxima OLTP na memória | Limite de instância: 3 GB por vCore<br/>Limites de banco de dados:<br/> -8-núcleo: 8 GB por banco de dados<br/> -16-núcleo: 20 GB por banco de dados<br/> -24-núcleo: 36 GB por banco de dados | Limite de instância: 2,5 GB por vCore<br/>Limites de banco de dados:<br/> -8-núcleo: 13 GB por banco de dados<br/> -16-núcleo: 32 GB por banco de dados |
| Armazenamento reservado de instância máx. |  Uso Geral: 8 TB<br/>Comercialmente Crítico: 1 TB | Uso Geral: 8 TB<br/> Comercialmente Crítico 1 TB, 2 TB ou 4 TB, dependendo do número de núcleos |

> [!IMPORTANT]
> Novos bancos de dados Gen4 não têm mais suporte na região AustraliaEast.

### <a name="service-tier-characteristics"></a>Características de camada de serviço

A instância gerenciada tem duas camadas de serviço: Uso Geral e Comercialmente Crítico. Essas camadas fornecem recursos diferentes, conforme descrito na tabela a seguir:

| **Recurso** | **Uso geral** | **Comercialmente Crítico** |
| --- | --- | --- |
| Número de vCores\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24, 32 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 |
| Memória máxima | Gen4: 56 GB-168 GB (7 GB/vCore)<br/>Gen5: 40,8 GB-408 GB (5.1 GB/vCore)<br/>Adicione mais vCores para obter mais memória. | Gen4: 56 GB-168 GB (7 GB/vCore)<br/>Gen5: 40,8 GB-408 GB (5.1 GB/vCore)<br/>Adicione mais vCores para obter mais memória. |
| Tamanho máximo de armazenamento reservado de instância | -2 TB para 4 vCores (somente Gen5)<br/>-8 TB para outros tamanhos | Gen4: 1 TB <br/> Gen5: <br/>-1 TB para 4, 8, 16 vCores<br/>- 2 TB para 24 vCores<br/>- 4 TB para 32, 40, 64, 80 vCores |
| Tamanho máximo do banco de dados | Determinado pelo tamanho de armazenamento máximo por instância | Determinado pelo tamanho de armazenamento máximo por instância |
| Número máximo de bancos de dados por instância | 100 | 100 |
| Número máximo de arquivos de banco de dados por instância | Até 280 | 32.767 arquivos por banco de dados |
| Dados/Log IOPS (aproximado) | 500 – 7.500 por arquivo<br/>\*[Aumentar o tamanho do arquivo para obter mais IOPS](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11 k-110 K (1375/vCore)<br/>Adicione mais vCores para obter melhor desempenho de e/s. |
| Limite de produtividade de gravação de log | 3 MB/s por vCore<br/>Máximo de 22 MB/s por instância | 4 MB/s por vCore<br/>Máximo de 48 MB/s por instância|
| Taxa de transferência de dados (aproximada) | 100 – 250 MB/s por arquivo<br/>\*[Aumente o tamanho do arquivo para obter melhor desempenho de e/s](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | N/D |
| Latência de e/s de armazenamento (aproximada) | 5-10 ms | 1-2 ms |
| Tamanho máximo de TempDB | 192 – 1.920 GB (24 GB por vCore)<br/>Adicione mais vCores para obter mais espaço de TempDB. | Limitado pelo tamanho máximo de armazenamento da instância. O tamanho do arquivo de log de TempDB está limitado atualmente a 24 GB/vCore. |
| Máx. de sessões | 30000 | 30000 |

> [!NOTE]
> - O tamanho do arquivo de log e de dados nos bancos de dados de sistema e de usuário são incluídos no tamanho de armazenamento de instância que é comparado ao limite de tamanho de armazenamento máximo. Usar a exibição do sistema <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys. master_files</a> para determinar o total o espaço usado pelos bancos de dados. Logs de erros não são persistentes e não são incluídos no tamanho. Backups não são incluídos no tamanho de armazenamento.
> - A taxa de transferência e o IOPS também dependem do tamanho da página que não é explicitamente limitado pela instância gerenciada.

## <a name="supported-regions"></a>Regiões com suporte

As instâncias gerenciadas podem ser criadas somente em [regiões com suporte](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Para criar uma instância gerenciada em uma região que não tem suporte no momento, você pode [Enviar uma solicitação de suporte por meio do portal do Azure](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Tipos de assinatura com suporte

Atualmente, a instância gerenciada dá suporte à implantação somente nos seguintes tipos de assinaturas:

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pague-conforme-o-uso](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Provedor de serviços de nuvem (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Assinaturas com crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limitações de recursos regionais

Os tipos de assinatura suportados podem conter um número limitado de recursos por região. A instância gerenciada tem dois limites padrão por região do Azure, dependendo de um tipo de assinatura:

- **Limite de sub-rede**: O número máximo de sub-redes nas quais as instâncias gerenciadas são implantadas em uma única região.
- **limite de vCore**: O número máximo de vCores que podem ser implantadas em todas as instâncias em uma única região.

> [!Note]
> Esses limites são configurações padrão e não limitações técnicas. Os limites podem ser aumentados sob demanda criando uma solicitação especial [de suporte no portal do Azure](#obtaining-a-larger-quota-for-sql-managed-instance) se você precisar de mais instâncias gerenciadas na região atual. Como alternativa, você pode criar novas instâncias gerenciadas em outra região do Azure sem enviar solicitações de suporte.

A tabela a seguir mostra os limites regionais padrão para assinaturas com suporte:

|Tipo de assinatura| Número máximo de sub-redes de instância gerenciada | Número máximo de unidades vCore * |
| :---| :--- | :--- |
|Pago conforme o uso|3|320|
|CSP |8 (15 em algumas regiões * *)|960 (1440 em algumas regiões * *)|
|Desenvolvimento/Teste pago conforme o uso|3|320|
|Desenvolvimento/Teste Enterprise|3|320|
|EA|8 (15 em algumas regiões * *)|960 (1440 em algumas regiões * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional e Plataformas MSDN|2|32|

\*Ao planejar suas implantações, considere que um vCore de Comercialmente Crítico (BC) (devido à redundância adicionada) consome 4x mais capacidade do que um vCore de Uso Geral (GP). Portanto, para seus cálculos, 1 GP vCore = 1 unidade vCore e 1 BC vCore = 4 unidades vCore. Para simplificar a análise de consumo em relação aos limites padrão, resuma as unidades vCore em todas as sub-redes na região em que as instâncias gerenciadas são implantadas e compare os resultados com os limites de unidade de instância para o tipo de assinatura. O limite **máximo de unidades vCore** aplica-se a cada assinatura em uma região. Não há limite por sub-redes individuais, exceto que a soma de todos os vCores implantados em várias sub-redes deve ser menor ou igual ao **número máximo de unidades vCore**.

\*\*Os limites de maior sub-rede e vCore estão disponíveis nas seguintes regiões: Leste da Austrália, leste dos EUA, leste dos EUA 2, Europa Setentrional, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, Europa Ocidental, oeste dos EUA 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Obtendo uma cota maior para a instância gerenciada do SQL

Se você precisar de mais instâncias gerenciadas em suas regiões atuais, envie uma solicitação de suporte para estender a cota usando o portal do Azure.
Para iniciar o processo de obtenção de uma cota maior:

1. Abra **ajuda + suporte**e clique em **nova solicitação de suporte**.

   ![Ajuda e Suporte](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Na guia Básico da nova solicitação de suporte:
   - Para **tipo de problema**, selecione **Limites de serviço e assinatura (cotas)** .
   - Em **Assinatura**, selecione sua assinatura.
   - Para **tipo de cota**, selecione **instância gerenciada do banco de dados do SQL**.
   - Para **plano de suporte**, selecione o plano de suporte.

     ![Cota de tipo de problema](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Clique em **Avançar**.
4. Na **guia problema** da nova solicitação de suporte:
   - Para **Gravidade**, selecione o nível de gravidade do problema.
   - Para **Detalhes**, forneça informações adicionais sobre o seu problema, incluindo mensagens de erro.
   - Para **Upload de arquivo**, anexe um arquivo com mais informações (até 4 MB).

     ![Detalhes do problema](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Uma solicitação válida deve incluir:
     > - Região na qual o limite de assinatura precisa ser aumentado.
     > - Número necessário de vCores, por camada de serviço em sub-redes existentes após o aumento da cota (se qualquer uma das sub-redes existentes precisar ser expandida.
     > - Número necessário de novas sub-redes e número total de vCores por camada de serviço nas novas sub-redes (se você precisar implantar instâncias gerenciadas em novas sub-redes).

5. Clique em **Avançar**.
6. Na guia Informações de contato da nova solicitação de suporte, insira o método de contato preferencial (email ou telefone) e os detalhes de contato.
7. Clique em **Criar**.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre a instância gerenciada, consulte [o que é uma instância gerenciada?](sql-database-managed-instance.md).
- Para saber mais sobre preços, veja [Preços de instância gerenciada do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Para saber como criar sua primeira instância gerenciada, consulte [o guia de início rápido](sql-database-managed-instance-get-started.md).
