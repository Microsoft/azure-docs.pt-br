---
title: Perguntas frequentes sobre o Catálogo de Dados do Azure
description: Perguntas frequentes sobre o Catálogo de Dados do Azure, incluindo recursos para descoberta de fontes de dados, anotação e gerenciamento.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 7c5241b9df23bb0334a39f2c684fd1bdff40b4c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61004034"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Perguntas frequentes sobre o Catálogo de Dados do Azure
Este artigo apresenta respostas para perguntas frequentes relacionadas ao serviço de Catálogo de Dados do Azure.

## <a name="what-is-azure-data-catalog"></a>O que é o Catálogo de Dados do Azure?
O Catálogo de Dados é um serviço totalmente gerenciado hospedado no Microsoft Azure que atua como um sistema de registro e descoberta para fontes de dados da empresa. Com o Catálogo de Dados, qualquer usuário, de analistas a cientistas de dados e desenvolvedores, pode registrar, descobrir, entender e consumir fontes de dados.

## <a name="what-customer-challenges-does-it-solve"></a>Quais desafios do cliente ele resolve?
O Catálogo de Dados enfrenta o desafio de descoberta de fonte de dados e os "dados escuros" para que os usuários possam descobrir e entender as fontes de dados da empresa.

## <a name="what-are-its-target-audiences"></a>Quais são seus públicos-alvo?
O Catálogo de Dados é desenvolvido para usuários técnicos e não técnicos, incluindo:

* Os desenvolvedores de dados e os profissionais de BI e análise: Pessoas que são responsáveis por produzir conteúdo de dados e análises para outras pessoas consumirem.
* Administradores de dados: Pessoas que têm conhecimento sobre os dados, o que significa e como ele se destina a ser usado.
* Consumidores de dados: As pessoas que precisam ser capazes de descobrir com facilidade, entender e conectar-se aos dados que precisam para realizar seu trabalho, usando a ferramenta de sua preferência.
* Central IT: Pessoas que precisam tornar centenas de fontes de dados podem ser descobertos por usuários de negócios e que precisam supervisionar a maneira como os dados estão sendo usados e por quem.

## <a name="what-is-its-availability-by-region"></a>Qual é a disponibilidade por região?
No momento, os serviços de Catálogo de Dados estão disponíveis nos seguintes datacenters:

* Oeste dos EUA
* Leste dos EUA
* Europa Ocidental
* Norte da Europa
* Leste da Austrália
* Sudeste Asiático

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Quais são os limites ao número de ativos de dados?
A Edição Gratuita do Catálogo de Dados está limitada a 5 mil ativos de dados registrados.

A Edição Standard do Catálogo de Dados do Azure dá suporte a até 100 mil ativos de dados registrados.

Qualquer objeto registrado no Catálogo de Dados, tal como tabelas, visualizações, arquivos e relatórios, conta como um ativo de dados.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Quais são os tipos de ativos e fonte de dados com suporte?
Para obter uma lista de fontes de dados com suporte no momento, consulte [DSR do Catálogo de Dados](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Como fazer para solicitar suporte para outra fonte de dados?
Para enviar solicitações de recurso e outros comentários, vá aos [Fóruns do Catálogo de Dados do Azure](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="how-do-i-get-started-with-data-catalog"></a>Como fazer para usar o Catálogo de Dados?
É a melhor maneira de começar é acessando a [Introdução ao Catálogo de Dados](data-catalog-get-started.md). Este artigo é uma visão geral de ponta a ponta dos recursos no serviço.

## <a name="how-do-i-register-my-data"></a>Como fazer para registrar meus dados?
Para registrar seus dados no Catálogo de Dados:
1. No portal do Catálogo de Dados do Azure, na área **Publicar**, inicie a ferramenta de registro do Catálogo de Dados do Azure. 
2. Na ferramenta de registro de fonte de dados do Catálogo de Dados, entre com as mesmas credenciais usadas para acessar o portal do Catálogo de Dados.
3. Selecione a fonte de dados e os ativos específicos que você deseja registrar.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Quais propriedades ele extrai para ativos de dados que são registrados?
As propriedades específicas diferem de uma fonte de dados para outra, porém, em geral, o serviço de publicação do Catálogo de Dados extrai as seguintes informações:

* Nome do ativo
* Tipo de Ativo
* Descrição do ativo
* Nomes do atributo/coluna
* Tipos de dados do atributo/coluna
* Descrição do atributo/coluna

> [!IMPORTANT]
> O registro de ativos de dados com o Catálogo de Dados não move nem copia seus dados para a nuvem. Registrar os ativos de uma fonte de dados copia os metadados dos ativos para o Azure, mas os dados permanecem no local da fonte de dados existente. A exceção a essa regra é se você optar pelo upload dos registros de versão prévia ou de um perfil de dados ao registrar os ativos. Quando você inclui uma versão prévia, até 20 registros são copiados de cada ativo e armazenados como um instantâneo no Catálogo de Dados. Quando você inclui um perfil de dados, informações agregadas são calculadas e incluídas nos metadados armazenados no catálogo. As informações agregadas podem incluir o tamanho de tabelas, o percentual de valores nulos por coluna ou os valores mínimos, máximos e médios para as colunas. 
>
>

> [!NOTE]
> Para fontes de dados como SQL Server Analysis Services que têm uma propriedade **Description** de primeira classe, a ferramenta de registro de fonte de dados do Catálogo de Dados extrai esse valor da propriedade. Para bancos de dados relacionais do SQL Server, que não têm uma propriedade **Description** de primeira classe, a ferramente de registro de fonte de dados do Catálogo de Dados extrai o valor da propriedade estendida **ms_description** para objetos e colunas. Para saber mais, confira [Como usar propriedades estendidas em objetos de banco de dados](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Quanto tempo deverá levar para ativos recém-registrados aparecerem no catálogo?
Depois de registrar os ativos no Catálogo de Dados, poderá haver um período de espera de 5 a 10 segundos para eles aparecerem no portal do Catálogo de Dados.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Como fazer para anotar e enriquecer os metadados para meus ativos de dados registrados?
A maneira mais simples de fornecer metadados para ativos registrados é selecionar o ativo no portal do Catálogo de Dados e inserir os valores no painel de propriedades ou no painel de esquema do objeto selecionado.

Você também pode fornecer alguns metadados, como marcas e especialistas, durante o processo de registro. Os valores fornecidos no serviço de publicação do Catálogo de Dados aplicam-se a todos os ativos que estiverem sendo registrados no momento. Para exibir os objetos recém-registrados no portal para anotação adicional, selecione o botão **Exibir Portal** na tela final da ferramenta de registro de fonte de dados do Catálogo de Dados.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Como fazer para excluir meus objetos de dados registrados?
Você pode excluir um objeto do Catálogo de Dados selecionando-o no portal e clicando no botão **Excluir**. Remover o objeto remove seus metadados de Catálogo de Dados, mas não afeta a fonte de dados subjacente.

## <a name="what-is-an-expert"></a>O que é um especialista?
Especialista é uma pessoa que tem uma perspectiva bem informada sobre um objeto de dados. Um objeto pode ter muitos especialistas. Um especialista não precisa ser o "proprietário" de um objeto, ele simplesmente é alguém que sabe como os dados podem e devem ser usados.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Como fazer para compartilhar informações com a equipe do Catálogo de Dados se eu encontrar problemas?
Para relatar problemas, compartilhar informações e fazer perguntas, acesse o [fórum do Catálogo de Dados do Azure](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>O catálogo funciona com outra fonte de dados em eu que tenha interesse?
Estamos trabalhando ativamente para adicionar mais fontes de dados ao Catálogo de Dados. Se você quiser ver uma fonte de dados específica com suporte, sugira-a (ou manifeste seu apoio, se ela já tiver sido sugerida) acessando o [Fóruns de Comentários do Catálogo de Dados do Azure](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Como o Catálogo de Dados do Azure está relacionado ao Catálogo de Dados no Power BI para Office 365?
Pense no Catálogo de Dados do Azure como uma evolução do Catálogo de Dados no Power BI. A partir do primeiro semestre de 2017, o Catálogo de Dados do Azure será usado para habilitar o compartilhamento e a descoberta de consultas no Excel 2016 e no Power Query para Excel. Recursos do Catálogo de Dados do Excel estão disponíveis para usuários com licenças do Power BI Pro.

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>De quais permissões eu preciso para registrar ativos no Catálogo de Dados?
Para executar a ferramenta de registro do Catálogo de Dados, você precisa de permissões na fonte de dados que lhe permitam ler os metadados da fonte. Para incluir também uma versão prévia, você deve ter permissões para ler os dados dos objetos que estão sendo registrados.

Catálogo de dados também permite que os administradores do catálogo restrinjam quais usuários e grupos podem adicionar metadados para o catálogo. Para obter informação adicional, veja [Como proteger o acesso ao catálogo de dados e ativos de dados](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>O Catálogo de Dados será disponibilizado para implantação local também?
O Catálogo de Dados é um serviço de nuvem que pode funcionar com fontes de dados locais e na nuvem para oferecer uma solução de descoberta de fonte de dados híbrida. No momento, não há planos para uma versão do serviço do Catálogo de Dados que seja executada localmente.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Posso extrair mais metadados ou metadados mais avançados das fontes de dados que eu registro?
Estamos trabalhando ativamente para expandir os recursos do Catálogo de Dados. Se você quiser ter metadados adicionais extraídos da fonte de dados durante o registro, sugira-os (ou voto neles, se já tiverem sido sugeridos) no [Fóruns de Comentário do Azure do Catálogo de Dados](https://feedback.azure.com/forums/906052-data-catalog). 

Se você quiser incluir metadados de coluna ou o esquema, visões prévias ou perfis de dados para fontes de dados em que esses metadados não é extraído pela ferramenta de registro da fonte de dados, você pode usar a API de catálogo de dados para adicionar esses metadados. Para obter informações adicionais, consulte a [API REST do Catálogo de Dados do Azure](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Como fazer para restringir a visibilidade dos ativos de dados registrados para que apenas determinadas pessoas possam descobri-los?
Selecione os ativos de dados no Catálogo de Dados e, em seguida, clique no botão **Apropriar-se**. Os proprietários dos ativos de dados no Catálogo de Dados podem alterar as configurações de visibilidade para permitir que todos os usuários descubram os ativos de sua propriedade ou restringir a visibilidade a usuários específicos. Para obter informações adicionais, consulte a [Gerenciar ativos de dados do Catálogo de Dados do Azure](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Como fazer para atualizar o registro para um ativo de dados para que as alterações na fonte de dados reflitam-se no catálogo?
Para atualizar os metadados para ativos de dados que já estão registrados no catálogo, basta registrar novamente a fonte de dados que contém os ativos. Todas as alterações à fonte de dados, como colunas adicionadas ou removidas de tabelas ou exibições, serão atualizadas no catálogo, mas quaisquer anotações fornecidas pelos usuários serão mantidas.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Minha dúvida não é respondida aqui. Como posso obter respostas?
Acesse o [fórum Catálogo de Dados do Azure](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). As perguntas feitas serão posteriormente incluídas aqui.
