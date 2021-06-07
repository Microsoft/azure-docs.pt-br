---
title: Azure DevTest Labs o uso em vários laboratórios e assinaturas
description: Saiba como relatar o uso de Azure DevTest Labs em vários laboratórios e assinaturas.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1e4d1f0abb5596c7fd9d22740bf052827c2ca666
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452638"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Relatório de Azure DevTest Labs uso em vários laboratórios e assinaturas

A maioria das grandes organizações deseja controlar o uso de recursos para ser mais eficiente com esses recursos visualizando tendências e exceções no uso. Com base no uso de recursos, os proprietários ou gerentes de laboratório podem personalizar os laboratórios para [melhorar o uso de recursos e os custos](../cost-management-billing/cost-management-billing-overview.md). No Azure DevTest Labs, você pode baixar o uso de recursos por laboratório, permitindo uma análise mais profunda dos padrões de uso. Esses padrões de uso podem ajudar a identificar as alterações para melhorar a eficiência. A maioria das empresas quer o uso de laboratório individual e o uso geral em [vários laboratórios e assinaturas](/azure/architecture/cloud-adoption/decision-guides/subscriptions/). 

Este artigo discute como lidar com informações de uso de recursos em vários laboratórios e assinaturas.

![Uso de relatórios](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Uso de laboratório individual

Esta seção discute como exportar o uso de recursos para um único laboratório.

Antes de poder exportar o uso de recursos do DevTest Labs, você precisa configurar uma conta de armazenamento do Azure para permitir que os diferentes arquivos que contêm os dados de uso sejam armazenados. Há duas maneiras comuns de executar a exportação de dados:

* [API REST do DevTest Labs](/rest/api/dtl/labs/exportresourceusage) 
* O módulo AZ. Resource do PowerShell [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) com a ação de `exportResourceUsage` , a ID de recurso do laboratório e os parâmetros necessários. 

    O artigo [exportar ou excluir dados pessoais](personal-data-delete-export.md) contém um exemplo de script do PowerShell com informações detalhadas sobre os dados exportados. 

    > [!NOTE]
    > O parâmetro date não inclui um carimbo de data/hora para que os dados incluam tudo, da meia-noite, com base no fuso horário em que o laboratório está localizado.

Quando a exportação for concluída, haverá vários arquivos CSV no armazenamento de BLOBs com as diferentes informações de recurso.
  
Atualmente, há dois arquivos CSV:

* *virtualmachines.csv* -contém informações sobre as máquinas virtuais no laboratório
* *disks.csv* -contém informações sobre os diferentes discos no laboratório 

Esses arquivos são armazenados no contêiner de blob *labresourceusage* sob o nome do laboratório, ID exclusiva do laboratório, data de execução e completa ou a data de início que foi baseada na solicitação de exportação. Um exemplo de estrutura de blob seria:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Exportando o uso de todos os laboratórios

Para exportar as informações de uso para vários laboratórios, considere o uso do 

* [Azure Functions](../azure-functions/index.yml), disponível em muitas linguagens, incluindo o PowerShell ou 
* [Runbook de automação do Azure](../automation/index.yml), use o PowerShell, o Python ou um designer gráfico personalizado para gravar o código de exportação.

Usando essas tecnologias, você pode executar exportações de laboratório individuais em todos os laboratórios em uma data e hora específicas. 

Sua função do Azure deve enviar os dados por push para o armazenamento de longo prazo. Ao exportar dados para vários laboratórios, a exportação pode levar algum tempo. Para ajudar no desempenho e reduzir a possibilidade de duplicação de informações, é recomendável executar cada laboratório em paralelo. Para realizar o paralelismo, execute Azure Functions de forma assíncrona. Aproveite também o gatilho de temporizador que Azure Functions oferece.

## <a name="using-a-long-term-storage"></a>Usando um armazenamento de longo prazo

Um armazenamento de longo prazo consolida as informações de exportação de diferentes laboratórios em uma única fonte de dados. Outro benefício de usar o armazenamento de longo prazo é a capacidade de remover os arquivos da conta de armazenamento para reduzir a duplicação e o custo. 

O armazenamento de longo prazo pode ser usado para fazer qualquer manipulação de texto, por exemplo: 

* adicionando nomes amigáveis
* Criando agrupamentos complexos
* agregando os dados.

Algumas soluções de armazenamento comuns são: [SQL Server](https://azure.microsoft.com/services/sql-database/), [Azure data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)e [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Escolher a solução de armazenamento de longo prazo que você escolher dependerá da preferência. Você pode considerar a escolha da ferramenta, dependendo do que ela oferece em termos de disponibilidade de interação ao visualizar os dados.

## <a name="visualizing-data-and-gathering-insights"></a>Visualizando dados e coletando informações

Use uma ferramenta de visualização de dados de sua escolha para se conectar ao armazenamento de longo prazo para exibir os dados de uso e coletar informações para verificar a eficiência do uso. Por exemplo, [Power bi](/power-bi/power-bi-overview) pode ser usado para organizar e exibir os dados de uso. 

Você pode usar [Azure data Factory](https://azure.microsoft.com/services/data-factory/) para criar, vincular e gerenciar seus recursos em uma única interface de local. Se for necessário um controle maior, o recurso individual poderá ser criado dentro de um único grupo de recursos e gerenciado independentemente do serviço de Data Factory.  

## <a name="next-steps"></a>Próximas etapas

Quando o sistema estiver configurado e os dados estiverem mudando para o armazenamento de longo prazo, a próxima etapa será criar as perguntas que os dados precisam responder. Por exemplo: 

-   Qual é o uso do tamanho da VM?

    Os usuários estão selecionando tamanhos de VM de alto desempenho (mais caros)?
-   Quais imagens do Marketplace estão sendo usadas?

    As imagens personalizadas são a base de VM mais comum, caso um repositório de imagens comum seja criado como a [Galeria de imagens compartilhadas](../virtual-machines/shared-image-galleries.md) ou a [fábrica de imagens](image-factory-create.md).
-   Quais imagens personalizadas estão sendo usadas ou não são usadas?