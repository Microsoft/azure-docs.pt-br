---
title: Modelo de dados de nível de compatibilidade no Azure Analysis Services | Microsoft Docs
description: Noções básicas sobre o nível de compatibilidade do modelo de dados de tabela.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 67a6c99253c549f0b8d3b55809b35b81756843eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032595"
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Nível de compatibilidade para modelos de tabela do Analysis Services

*Nível de compatibilidade* refere-se a comportamentos específicos à versão no mecanismo do Analysis Services. As alterações para o nível de compatibilidade geralmente coincidem com as versões principais do SQL Server. Essas alterações também são implementadas no Azure Analysis Services para manter a paridade entre as duas plataformas. Alterações de nível de compatibilidade também afetam recursos disponíveis em modelos de tabela. Por exemplo, o DirectQuery e os metadados de objeto de tabela têm implementações diferentes dependendo do nível de compatibilidade. O nível de compatibilidade é especificado no projeto do modelo tabular no Visual Studio (SSDT).

O Azure Analysis Services dá suporte a modelos de tabela no nível de compatibilidade 1200 e 1400. O nível de compatibilidade mais recente é 1400. Esse nível coincide com o Analysis Services do SQL Server 2017. Os principais recursos do nível de compatibilidade 1400 incluem:

*  Novos recursos de conectividade de dados e importação com suporte para APIs TOM e scripts de TMSL. 
*  Transformação de dados e recursos de mashup de dados usando expressões Obter dados e M.
*  Medidas que suportam uma propriedade de linhas de detalhes com uma expressão DAX. Essa propriedade permite que as ferramentas de cliente, como o Microsoft Excel, façam buscas detalhadas de dados detalhados do relatório agregado. Por exemplo, quando os usuários visualizam o total de vendas de uma região e mês, eles podem visualizar os detalhes do pedido associado. 
*  Segurança em nível de objeto para nomes de tabela e coluna, além dos dados dentro deles.
*  Suporte aprimorado para hierarquias desbalanceadas.
*  Monitoramento de desempenho e melhorias.

> [!NOTE]
> O Azure Analysis Services dá suporte a arquivos do Power BI Desktop importados no nível de compatibilidade 1465. No entanto, a importação da funcionalidade do Power BI Desktop, que sempre era um recurso de visualização, foi descontinuada e removida do serviço em março de 2019. Os modelos existentes no nível de compatibilidade 1465 permanecem com suportados.  


## <a name="set-compatibility-level"></a>Configuração de nível de compatibilidade

 Ao criar um novo projeto de modelo de tabela no SSDT, você pode especificar o nível de compatibilidade na caixa de diálogo **Designer de modelo de tabela**. 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 Ao selecionar a opção **Não mostrar esta mensagem novamente**, todos os projetos subsequentes usam o nível de compatibilidade que você especificou como o padrão. Você pode alterar o nível de compatibilidade padrão no SSDT em **Ferramentas** > **Opções**.  
  
 Para atualizar um projeto de modelo de tabela existente no SSDT, defina a propriedade do **Nível de compatibilidade** na janela**Propriedades** do modelo. Não se esqueça de que o nível de compatibilidade de atualização é irreversível.
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>Verifique o nível de compatibilidade de um banco de dados de modelo de tabela no SQL Server Management Studio 

 No SSMS, clique o botão direito do mouse no nome do banco de dados > **Propriedades** > **Nível de Compatibilidade**.  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>Verifique o nível de compatibilidade com suporte para um servidor no SSMS  

 No SSMS, clique o botão direito do mouse no nome do servidor > **Propriedades** > **Nível de Compatibilidade com Suporte**.  
  
 Essa propriedade especifica o nível mais alto de compatibilidade de um banco de dados que será executado no servidor (exceto a visualização). O nível de compatibilidade com suporte não pode ser alterado.  

> [!NOTE]
> No SSMS, quando conectado a um servidor do Azure Analysis Services, o **nível de compatibilidade com suporte** propriedade mostrará **1200**. Isso é um problema conhecido e será resolvido no SSMS futuro atualização. Quando resolvido, essa propriedade será mostrada o mais alto nível de compatibilidade com suporte.

## <a name="next-steps"></a>Próximas etapas

  [Gerenciar o Analysis Services](analysis-services-manage.md)  
