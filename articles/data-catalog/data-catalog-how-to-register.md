---
title: Registrar fontes de dados no Catálogo de Dados do Azure
description: Este artigo destaca como registrar fontes de dados no Catálogo de Dados do Azure, incluindo os campos de metadados extraídos durante o registro.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: bab89906-186f-4d35-9ffd-61b1d903905d
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 57b9a040b875c584b126e2062e4938b37875a31b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001286"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registrar fontes de dados no Catálogo de Dados do Azure
## <a name="introduction"></a>Introdução
O Catálogo de Dados do Azure é um serviço de nuvem totalmente gerenciado que atua como um sistema de registro e descoberta para fontes de dados da empresa. Em outras palavras, o Catálogo de Dados ajuda as pessoas a descobrir, entender e usar fontes de dados, além de ajudar as organizações a obterem mais valor dos seus dados existentes. A primeira etapa para tornar uma fonte de dados passível de ser descoberta por meio do Catálogo de Dados é registrá-la.

## <a name="register-data-sources"></a>Registrar fontes de dados
Registro é o processo de extrair metadados da fonte de dados e copiá-los para o serviço de Catálogo de Dados. Os dados permanecem onde atualmente residem e permanecem sob o controle dos administradores e políticas do sistema atual.

Para registrar uma fonte de dados, faça o seguinte:
1. No portal do Catálogo de Dados do Azure, inicie a ferramenta de registro de fonte de dados do Catálogo de Dados. 
2. Entre com sua conta corporativa ou de estudante com as mesmas credenciais do Azure Active Directory utilizadas para entrar no portal.
3. Selecione a fonte de dados que você deseja registrar.

Para obter mais detalhes passo a passo, consulte o tutorial [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md).

Após ter registrado a fonte de dados, o catálogo rastreia o local e indexa seus metadados. Os usuários podem pesquisar, navegar e descobrir a fonte de dados e, em seguida, usar seu local para conectarem-se a ele usando o aplicativo ou a ferramenta de sua escolha.

## <a name="supported-data-sources"></a>Fontes de dados com suporte
Para obter uma lista de fontes de dados com suporte no momento, consulte [DSR do Catálogo de Dados](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Metadados estruturais
Quando você registra uma fonte de dados, a ferramenta de registro extrai informações sobre a estrutura dos objetos selecionados. Essas informações são chamadas de metadados estruturais.

Para todos os objetos, esses metadados estruturais incluem o local do objeto, de modo que os usuários que descobrem os dados podem usar essas informações para conectar-e ao objeto nas ferramentas de cliente de sua preferência. Outros metadados estruturais incluem o tipo e o nome do objeto e o nome de coluna/atributo e tipo de dados.

## <a name="descriptive-metadata"></a>Metadados descritivos
Além dos principais metadados estruturais extraídos da fonte de dados, a ferramenta de registro da fonte de dados também extrai metadados descritivos. Para SQL Server Analysis Services e SQL Server Reporting Services, esses metadados são obtidos das propriedades de Descrição expostas por esses serviços. Para o SQL Server, os valores fornecidos usando a propriedade estendida ms\_description são extraídos. Para o Banco de dados Oracle, a ferramenta de registro da fonte de dados extrai a coluna COMMENTS da exibição ALL\_TAB\_COMMENTS.

Além dos metadados descritivos extraídos da fonte de dados, os usuários também podem inserir metadados descritivos usando a ferramenta de registro de fonte de dados. Os usuários podem adicionar marcas e podem identificar especialistas para os objetos que estão sendo registrados. Todos esses metadados descritivos são copiados para o serviço do Catálogo de Dados juntamente com os metadados estruturais.

## <a name="include-previews"></a>Incluir versões prévias
Por padrão, somente metadados são extraídos das fontes de dados e copiados para o serviço do Catálogo de Dados, mas compreender uma fonte de dados geralmente é mais fácil quando se pode exibir uma amostra dos dados que ela contém.

Ao usar a ferramenta de registro de fonte de dados do Catálogo de Dados, você pode incluir uma versão prévia de instantâneo dos dados em cada tabela e exibir o que é registrado. Se você escolher incluir versões prévias durante o registro, a ferramenta de registro incluirá até 20 registros de cada tabela e exibição. Esse instantâneo então é copiado para o catálogo junto com os metadados estruturais e descritivos.

> [!NOTE]
> Tabelas largas com um grande número de colunas podem ter menos de 20 registros incluídos na sua versão prévia.
>
>

## <a name="include-data-profiles"></a>Incluir perfis de dados
Assim como incluir versões prévias pode fornecer um contexto valioso aos usuários que pesquisam fontes de dados no Catálogo de Dados, incluir um perfil de dados pode facilitar a compreensão das fontes de dados descobertas.

Ao usar a ferramenta de registro de fonte de dados do Catálogo de Dados, você pode incluir um perfil de dados para cada tabela e exibir o que é registrado. Se você optar por incluir um perfil de dados durante o registro, a ferramenta de registro incluirá estatísticas agregadas sobre os dados em cada tabela e exibição, incluindo:

* O número de linhas e o tamanho dos dados no objeto.
* A data da atualização mais recente dos dados e o esquema do objeto.
* O número de registros nulos e valores distintos para colunas.
* Os valores mínimo, máximo, médio e de desvio padrão para as colunas.

Essas estatísticas então são copiadas para o catálogo com os metadados estruturais e descritivos.

> [!NOTE]
> As colunas de data e texto não incluem estatísticas de desvio padrão ou média no seu perfil de dados.
>
>

## <a name="update-registrations"></a>Atualizar os registros
Registrar uma fonte de dados torna possível descobri-la no catálogo de Dados ao usar os metadados e a versão prévia opcional extraída durante o registro. Se a fonte de dados precisar ser atualizada no catálogo (por exemplo, se o esquema de um objeto tiver sido alterado, tabelas originalmente excluídas precisarem ser incluídas ou se você quiser atualizar os dados incluídos nas versões prévias), a ferramenta de registro da fonte de dados poderá ser executada novamente.

O novo registro de uma fonte de dados já registrada executa uma operação "upsert" de mesclagem: objetos existentes são atualizados e os novos objetos são criados. Todos os metadados fornecidos por usuários por meio do portal do Catálogo de Dados são mantidos.

## <a name="summary"></a>Resumo
Uma vez que ele copia metadados estruturais e descritivo de uma fonte de dados para o serviço de catálogo, registrar a fonte de dados no Catálogo de Dados torna mais fácil descobrir e entender os dados. Depois de registrar a fonte de dados, você pode anotar, gerenciar e descobrir essa fonte usando o portal do Catálogo de Dados.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o registro de fontes de dados, consulte o tutorial [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md).
