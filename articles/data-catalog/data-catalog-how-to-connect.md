---
title: Como conectar-se a fontes de dados no Catálogo de Dados do Azure
description: Artigo de instruções que destaca como se conectar a fontes de dados descobertas com o Catálogo de Dados do Azure.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 4e6b27a5-cf75-4012-b88c-333c1fe638e8
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: c64340491dba11870364610a6c2ff62e25c1328a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001779"
---
# <a name="how-to-connect-to-data-sources"></a>Como se conectar a fontes de dados
## <a name="introduction"></a>Introdução
**Catálogo de Dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que atua como um sistema de registro e sistema de descoberta em fontes de dados da empresa. Em outras palavras, o **Catálogo de Dados do Azure** ajuda as pessoas a descobrir, entender e usar fontes de dados, ajudando as empresas a obter mais valor de seus dados existentes. Um aspecto fundamental deste cenário está usando os dados – quando um usuário descobrir uma fonte de dados e compreender sua finalidade, a próxima etapa será se conectar à fonte de dados para colocar seus dados em uso.

## <a name="data-source-locations"></a>Locais de origem de dados
Durante o registro da fonte de dados, o **Catálogo de dados do Azure** recebe metadados sobre a fonte de dados. Esses metadados incluem os detalhes do local da fonte de dados. Os detalhes do local variam de fonte de dados para fonte de dados, mas sempre conterão as informações necessárias para a conexão. Por exemplo, o local para uma tabela do SQL Server inclui o nome do servidor, o nome do banco de dados, o nome do esquema e o nome da tabela, enquanto o local para um relatório do SQL Server Reporting Services inclui o nome do servidor e o caminho para o relatório. Outros tipos de fonte de dados terão locais que reflitam a estrutura e os recursos do sistema de origem.

## <a name="integrated-client-tools"></a>Ferramentas de cliente integradas
A maneira mais simples de se conectar a uma fonte de dados é usar o menu "Abrir em..." no portal do **Catálogo de Dados do Azure**. Esse menu exibe uma lista de opções para a conexão ao ativo de dados selecionado.
Quando o modo de exibição de bloco padrão for usado, esse menu estará disponível em cada bloco.

 ![Abrindo uma tabela do SQL Server no Excel desde o bloco de ativos de dados](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Quando o modo de exibição de lista for usado, o menu estará disponível na barra de pesquisa na parte superior da janela do portal.

 ![Abrindo um relatório do SQL Server Reporting Services no Gerenciador de Relatórios desde a barra de pesquisa](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Aplicativos do Cliente com Suporte
Ao usar o menu "Abrir em..." para as fontes de dados no portal do Catálogo de Dados do Azure, o aplicativo cliente correto deve ser instalado no computador do cliente.

| Abrir no aplicativo | Extensão de arquivo / protocolo | Versões do aplicativo com suporte |
| --- | --- | --- |
| Excel |.odc |Excel 2010 ou posterior |
| Excel (Top 1000) |.odc |Excel 2010 ou posterior |
| Power Query |.xlsx |Excel 2016, Excel 2010 ou Excel 2013 com o Power Query para o suplemento do Excel instalado |
| Power BI Desktop |.pbix |Power BI Desktop de julho de 2016 ou posterior |
| Ferramentas de dados do SQL Server |vsweb:// |Visual Studio 2013 Atualização 4 ou posterior com ferramentas do SQL Server instaladas |
| Gerenciador de Relatórios |http:// |Consulte [requisitos do navegador para os Serviços de Relatório do SQL Server](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Seus dados, suas ferramentas
As opções disponíveis no menu dependerão do tipo de ativo de dados selecionado no momento. É claro que nem todas as ferramentas possíveis serão incluídas no menu "Abrir em...", mas ainda é fácil conectar-se à fonte de dados usando uma ferramenta de cliente. Quando um ativo de dados for selecionado no portal do **Catálogo de Dados do Azure**, o local completo será exibido no painel de propriedades.

 ![Informações de conexão para uma tabela do SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Os detalhes de informações de conexão serão diferentes do tipo de fonte de dados para o tipo de fonte de dados, mas as informações incluídas no portal oferecerão a você todo o necessário para a conexão à fonte de dados em qualquer ferramenta de cliente. Os usuários podem copiar os detalhes da conexão para as fontes de dados descobertas por meio do **Catálogo de Dados do Azure**, permitindo que eles trabalhem com os dados na ferramenta escolhida por eles.

## <a name="connecting-and-data-source-permissions"></a>Permissões de conexão e de fonte de dados
Embora o **Catálogo de Dados do Azure** torne as fontes de dados descobríveis, o acesso aos dados propriamente ditos permanecerá sob o controle do proprietário ou do administrador da fonte de dados. A descoberta de uma fonte de dados no **Catálogo de Dados do Azure** não concede a um usuário as permissões para acessar a fonte de dados.

Para facilitar a descoberta de uma fonte de dados para usuários que não tenham permissão para acessar seus dados, os usuários poderão fornecer informações na propriedade Solicitar Acesso ao anotarem uma fonte de dados. As informações fornecidas aqui, incluindo os links para o processo ou o ponto de contato para obtenção de acesso à fonte de dados – são apresentadas junto com as informações de localização da fonte de dados no portal.

 ![Informações de conexão com as instruções de acesso de solicitação fornecidas](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Resumo
O registro de uma fonte de dados no **Catálogo de Dados do Azure** torna os dados descobríveis ao copiar metadados estruturais e descritivos da fonte de dados para o serviço Catálogo. Depois que uma fonte de dados for registrada e descoberta, os usuários poderão se conectar à fonte de dados do menu “Abrir no...” do portal **Catálogo de Dados do Azure** ou usando suas ferramentas de dados à sua escolha.

## <a name="see-also"></a>Consulte também
* [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md) para obter detalhes passo a passo sobre como conectar as fontes de dados.
