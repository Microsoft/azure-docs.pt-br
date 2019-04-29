---
title: Terminologia do Catálogo de Dados do Azure
description: Este artigo fornece uma introdução aos conceitos e termos usados na documentação do Catálogo de Dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: a6f2cf1dcee6a85376c8d767e57c504b6b246e5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580452"
---
# <a name="azure-data-catalog-terminology"></a>Terminologia do Catálogo de Dados do Azure

Este artigo fornece uma introdução aos conceitos e termos usados na documentação do Catálogo de Dados do Azure.

## <a name="catalog"></a>Catálogo

O Catálogo de Dados do Azure é um repositório de metadados baseado em nuvem no qual dados e fontes de dados pode ser registrados. O catálogo serve como um local de armazenamento central para metadados estruturais extraídos de fontes de dados e metadados descritivos adicionados pelos usuários.

## <a name="data-source"></a>Fonte de dados

Uma fonte de dados é um sistema ou um contêiner que gerencia ativos de dados. Exemplos incluem bancos de dados do SQL Server, bancos de dados Oracle, bancos de dados do SQL Server Analysis Services (tabulares ou multidimensionais) e servidores do SQL Server Reporting Services.

## <a name="data-asset"></a>Ativos de dados

Ativos de dados são objetos contidos em fontes de dados que podem ser registrados com o catálogo. Exemplos incluem tabelas e exibições do SQL Server, tabelas e exibições do Oracle, medidas, dimensões e KPIs do SQL Server Analysis Services e relatórios do SQL Server Reporting Services.

## <a name="data-asset-location"></a>Local do ativo de dados

O catálogo armazena o local de uma fonte de dados ou ativo de dados, que pode ser usado para se conectar à fonte usando um aplicativo cliente. Os detalhes e o formato do local variam com base no tipo de fonte de dados. Por exemplo, uma tabela do SQL Server pode ser identificada por seu nome de quatro partes – nome do servidor, nome do banco de dados, nome do esquema, nome do objeto – enquanto um relatório do SQL Server Reporting Services pode ser identificado por sua URL.

## <a name="structural-metadata"></a>Metadados estruturais

Metadados estruturais são metadados extraídos de uma fonte de dados que descrevem a estrutura de um ativo de dados. Isso inclui o local de ativos, o nome do objeto e tipo e as características adicionais específicas de tipo. Por exemplo, os metadados estruturais de tabelas e exibições incluem os nomes e tipos de dados das colunas do objeto.

## <a name="descriptive-metadata"></a>Metadados descritivos

Metadados descritivos são metadados que descrevem a finalidade ou a intenção de um ativo de dados. Normalmente os metadados descritivos são adicionados por usuários de catálogo usando o portal do Catálogo de Dados do Azure, mas também podem ser extraídos da fonte de dados durante o registro. Por exemplo, a ferramenta de registro do Catálogo de Dados do Azure extrairá as descrições da propriedade Descrição no SQL Server Analysis Services e SQL Server Reporting Services, e da propriedade [estendida ms_description](https://technet.microsoft.com/library/ms190243.aspx) nos bancos de dados do SQL Server, caso essas propriedades estejam preenchidas com valores.

## <a name="request-access"></a>Solicitar acesso

Os metadados descritivos de um ativo de dados podem incluir informações sobre como solicitar acesso ao ativo de dados ou à fonte de dados. Essas informações são apresentadas com a localização do ativo de dados e podem incluir uma ou mais das seguintes opções:

* O endereço de email do usuário ou da equipe responsável por conceder acesso à fonte de dados.
* A URL do processo documentado que os usuários precisam seguir para obter acesso à fonte de dados.
* A URL de uma ferramenta de gerenciamento de identidade e de acesso (como o Microsoft Identity Manager) que pode ser usada para acessar a fonte de dados.
* Uma entrada de texto livre que descreve como os usuários podem obter acesso à fonte de dados.

## <a name="preview"></a>Visualização

Uma visualização no Catálogo de Dados do Azure é um instantâneo de até 20 registros que podem ser extraídos da fonte de dados durante o registro e armazenados no catálogo com os metadados de ativos de dados. A visualização pode ajudar usuários que descobrem um ativo de dados a compreender melhor sua função e finalidade. Em outras palavras, a visualização de dados de exemplo pode ser mais valiosa do que ver apenas os nomes de coluna e tipos de dados.
Visualizações só têm suporte para tabelas e exibições e devem ser explicitamente selecionadas pelo usuário durante o registro.

## <a name="data-profile"></a>Perfil de dados

Um perfil de dados no Catálogo de Dados do Azure é um instantâneo de nível de tabela e de coluna sobre um ativo de dados registrado que pode ser extraído da fonte de dados durante o registro e armazenado no catálogo com os metadados de ativos de dados. O perfil de dados pode ajudar os usuários que descobrem um ativo de dados a compreender melhor sua função e finalidade. De maneira semelhante ao que ocorre com as visualizações, os perfis de dados devem ser explicitamente selecionados pelo usuário durante o registro.

> [!NOTE]
> Extrair um perfil de dados pode ser uma operação cara para grandes tabelas e exibições e pode aumentar significativamente o tempo necessário para registrar uma fonte de dados.


## <a name="user-perspective"></a>Perspectiva do usuário

No Catálogo de Dados do Azure, qualquer usuário pode fornecer metadados descritivos para um ativo de dados registrado. Cada usuário tem uma perspectiva distinta dos dados e seu uso. Por exemplo, o administrador responsável por um servidor pode fornecer detalhes de seu SLA (Contrato de Nível de Serviço) ou janelas de backup; um administrador de dados pode fornecer links de documentação para os negócios de negócios aos quais os dados dão suporte; e um analista pode fornecer uma descrição em termos mais relevantes para outros analistas e que podem ser mais valiosos para usuários que precisam descobrir e entender os dados.

Cada uma dessas perspectivas é inerentemente valiosa e com o catálogo de dados do Azure, cada usuário pode fornecer as informações que são significativas para eles, enquanto todos os usuários podem usar essas informações para entender os dados e sua finalidade.

## <a name="expert"></a>Especialista

Especialista é um usuário que foi identificado como tendo uma perspectiva de "especialista" informada para um ativo de dados. Qualquer usuário pode adicionar a si mesmo ou outro usuário como um especialista para um ativo. Ser listado como especialista não concede privilégios adicionais no Catálogo de Dados do Azure; permite que os usuários localizem facilmente essas perspectivas que mais provavelmente serão úteis durante a análise de metadados descritivos de um ativo.

## <a name="owner"></a>Proprietário

Um proprietário é um usuário que tem privilégios adicionais para gerenciar um ativo de dados no Catálogo de Dados do Azure. Os usuários podem se apropriar de ativos de dados registrados e os proprietários podem adicionar outros usuários como co-proprietários. Para obter mais informações, consulte [como gerenciar ativos de dados](data-catalog-how-to-manage.md)  

> [!NOTE]
> Propriedade e gerenciamento estão disponíveis somente na Standard Edition do Catálogo de Dados do Azure.

## <a name="registration"></a>Registro

Registro é o ato de extrair metadados de ativos de dados de uma fonte de dados e copiá-los no serviço de Catálogo de Dados do Azure. Ativos de dados que foram registrados podem ser anotados e descobertos.

## <a name="next-steps"></a>Próximas etapas

[Início Rápido: Criar um Catálogo de Dados do Azure](data-catalog-get-started.md) 
