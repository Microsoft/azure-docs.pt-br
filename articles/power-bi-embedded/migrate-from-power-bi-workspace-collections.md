---
title: "Como migrar o conteúdo da Coleção de Espaços de Trabalho do Power BI para o Power BI Embedded | Microsoft Docs"
description: "Saiba como migrar de Coleções de Espaços de Trabalho do Power BI para o Power BI Embedded e aproveite os avanços para inserção em aplicativos."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 069f31c8213bd0d8586f7ca50e543acfdad8a2b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>Como migrar o conteúdo da Coleção de Espaços de Trabalho do Power BI para o Power BI Embedded

Saiba como migrar de Coleções de Espaços de Trabalho do Power BI para o Power BI Embedded. Este artigo fornece orientação para a migração de Coleções de Espaços de Trabalho do Azure Power BI para o Power BI Embedded. Também analisamos o que esperar para as alterações do aplicativo.

O recurso de Coleções de Espaços de Trabalho do Power BI continua disponível por um período limitado após o lançamento de disponibilidade geral do Power BI Premium. Os clientes em um Contrato Enterprise têm acesso a suas coleções de espaços de trabalho existente até a expiração de seus contratos existentes. Os clientes que adquirirem Coleções de Espaços de Trabalho do Power BI por meio de canais diretos ou CSP aproveitam o acesso por um ano a partir da disponibilidade geral do Power BI Premium.

> [!IMPORTANT]
> Enquanto a migração tem uma dependência no serviço do Power BI, não há uma dependência no Power BI para os usuários do seu aplicativo ao usar um **token de inserção**. Eles não precisam se inscrever no Power BI para exibir o conteúdo inserido em seu aplicativo. Você pode usar essa abordagem de inserção inserir o Power BI para seus clientes.

![Fluxo do Power BI Embedded](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>Preparar para a migração

Há algumas coisas que você precisa fazer para se preparar para a migração do serviço de Coleções de Espaços de Trabalho do Power BI para o Power BI Embedded. Você precisa de um locatário disponível, junto com um usuário que tenha uma licença do Power BI Pro.

1. Certifique-se de que você tem acesso a um locatário do Azure AD (Azure Active Directory).

    Qual locatário usar?

    * Usar seu locatário do Power BI corporativo existente?
    * Usar um locatário separado para seu aplicativo?
    * Usar um locatário separado para cada cliente?

    Se você decidir criar um novo locatário para cada cliente ou seu aplicativo, consulte um dos artigos a seguir:

    * [Crie um locatário do Azure Active Directory](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [Como obter um locatário do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant).

2. Crie um usuário nesse novo locatário que atue como sua conta "mestre" do aplicativo. Essa conta precisa se inscrever no Power BI e deve ter uma licença do Power BI Pro atribuída a ela.

## <a name="accounts-within-azure-ad"></a>Contas no Azure AD

As contas a seguir precisam existir no seu locatário.

> [!NOTE]
> Essas contas precisam ter licenças do Power BI Pro para usar espaços de trabalho do aplicativo.

1. Um usuário administrador do locatário.

    É recomendável que o espaço de trabalho do aplicativo inserido tenha o administrador de locatário listado como um membro.

2. Contas para analistas que criam conteúdo.

    Esses usuários devem ser atribuídos aos espaços de trabalho do aplicativo conforme necessário.

3. Uma conta de usuário *mestre* do aplicativo ou conta de serviço.

    O back-end de aplicativos armazena as credenciais dessa conta. Use a conta *mestre* para adquirir um token do Azure AD para o uso com as APIs REST do Power BI. Essa conta é usada para gerar o token de inserção para o aplicativo. A conta *mestre* precisa ser um administrador dos espaços de trabalho do aplicativo criados para a inserção.

    **Essa conta é apenas uma conta de usuário regular da sua organização que é usada para fins de inserção.**

## <a name="app-registration-and-permissions"></a>Permissões e registro do aplicativo

Para realizar chamadas à API REST, registre um aplicativo com o Azure AD. A configuração de inclusão é aplicada no Portal do Microsoft Azure além da página de registro do aplicativo do Power BI. Para obter mais informações, consulte [Registrar um aplicativo do Azure AD para inserir o conteúdo do Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

A recomendação é registrar o aplicativo usando a conta **mestre** do aplicativo.

## <a name="create-app-workspaces-required"></a>Criar espaços de trabalho do aplicativo (obrigatório)

Se seu aplicativo estiver atendendo a vários clientes, você poderá aproveitar os espaços de trabalho do aplicativo para fornecer um isolamento melhor. Os painéis e relatórios seriam isolados entre seus clientes. Você pode usar uma conta do Power BI por espaço de trabalho do aplicativo para isolar ainda mais as experiências de aplicativos entre os clientes, mas você pode usar apenas uma conta para manter a simplicidade.

> [!IMPORTANT]
> Você não pode usar um espaço de trabalho pessoal (um "Meu espaço de trabalho") para aproveitar a inserção para seus clientes.

Você precisa de um usuário que tenha uma licença Pro para criar um espaço de trabalho do aplicativo no Power BI. O usuário do Power BI que cria o espaço de trabalho do aplicativo é um administrador desse espaço de trabalho por padrão. **A conta *mestre* do aplicativo precisa ser um administrador do espaço de trabalho.**

## <a name="content-migration"></a>Migração de conteúdo

A migração do conteúdo de suas coleções de espaços de trabalho para o Power BI Embedded pode ser executada em paralelo à sua solução atual e não requer nenhum tempo de inatividade.

Há **ferramenta de migração** disponível para uso para ajudá-lo a copiar o conteúdo das Coleções de Espaços de Trabalho do Power BI para o Power BI Embedded. Especialmente se você tem vários relatórios. Para obter mais informações, consulte [Ferramenta de migração Power BI Embedded](migrate-tool.md).

A migração de conteúdo depende principalmente de duas APIs.

1. Baixar PBIX – esta API pode baixar arquivos PBIX que foram carregados no Power BI depois de outubro de 2016.
2. Importar PBIX – esta API carrega qualquer PBIX no Power BI.

Para alguns trechos de código relacionados, consulte [Code snippets for migrating content from Power BI Embedded](migrate-code-snippets.md) (Trechos de código para migrar conteúdo para o Power BI Embedded).

### <a name="report-types"></a>Tipos de relatório

Há vários tipos de relatórios, cada um fluxo exigindo um fluxo de migração diferente.

#### <a name="cached-dataset-and-report"></a>Relatório e conjunto de dados armazenado em cache

Os conjuntos de dados armazenados em cache referem-se a arquivos PBIX que tinham dados importados em oposição a uma conexão em tempo real ou uma conexão do DirectQuery.

**Fluxo**

1. Chame a API Baixar PBIX do espaço de trabalho da Coleção de Espaços de Trabalho do Power BI.
2. Salve o PBIX.
3. Chame Importar PBIX para seu espaço de trabalho do Power BI Embedded.

#### <a name="directquery-dataset-and-report"></a>Relatório e conjunto de dados do DirectQuery

**Fluxo**

1. Chame GET https://api.powerbi.com/v1.0/collections/{collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources e salve a cadeia de conexão recebida.
2. Chame a API Baixar PBIX do espaço de trabalho da Coleção de Espaços de Trabalho do Power BI.
3. Salve o PBIX.
4. Chame Importar PBIX para seu espaço de trabalho do Power BI Embedded.
5. Atualize a cadeia de conexão chamando – POST https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.SetAllConnections
6. Obtenha a ID de GW e a ID da fonte de dados chamando – GET https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.GetBoundGatewayDataSources
7. Atualize as credenciais do usuário chamando – PATCH https://api.powerbi.com/v1.0/myorg/gateways/{gateway_id}/datasources/{datasource_id}

#### <a name="old-dataset-and-reports"></a>Relatórios e conjunto de dados antigos

Os relatórios carregados antes de outubro de 2016 não dão suporte ao recurso de Baixar PBIX.

**Fluxo**

1. Obtenha o PBIX do ambiente de desenvolvimento (o controle do código-fonte interno).
2. Chame Importar PBIX para seu espaço de trabalho do Power BI Embedded.

#### <a name="push-dataset-and-report"></a>Enviar por push o conjunto de dados e o relatório

Baixar PBIX não dá suporte a conjuntos de dados da *API Push*. Os dados do conjunto de dados da API Push não podem ser movidos de Coleções de Espaços de Trabalho do Power BI para o Power BI Embedded.

**Fluxo**

1. Chame a API "Criar conjunto de dados" com o conjunto de dados Json para criar o conjunto de dados para seu espaço de trabalho do Power BI Embedded.
2. Recrie o relatório para o conjunto de dados criado*.

É possível usar algumas soluções alternativas para migrar o relatório da API Push de Coleções de Espaços de Trabalho do Power BI para o Power BI Embedded tentando o seguinte:

1. Carregue um PBIX fictício para seu espaço de trabalho da Coleção de Espaços de Trabalho do Power BI.
2. Clone o relatório da API Push e associá-lo ao PBIX fictício da etapa 1.
3. Baixe o relatório da API Push com o PBIX fictício.
4. Carregue o PBIX fictício para seu espaço de trabalho do Power BI Embedded.
5. Crie o conjunto de dados de push no espaço de trabalho do Power BI Embedded.
6. Reassocie o relatório ao conjunto de dados da API Push.

## <a name="create-and-upload-new-reports"></a>Criar e carregar novos relatórios

Além do conteúdo migrado de Coleções de Espaços de Trabalho do Power BI, você pode criar seus relatórios e conjuntos de dados usando o Power BI Desktop e, em seguida, publicar esses relatórios em um espaço de trabalho do aplicativo. O usuário final que publica os relatórios precisa ter uma licença do Power BI Pro para publicar em um espaço de trabalho do aplicativo.

## <a name="rebuild-your-application"></a>Recompilar seu aplicativo

1. Modifique seu aplicativo para usar as APIs REST do Power BI e o local do relatório em powerbi.com.

2. Recompile sua autenticação AuthN/AuthZ usando a conta *mestre* para seu aplicativo. Você pode aproveitar o uso de um [token de inserção](https://msdn.microsoft.com/library/mt784614.aspx) para permitir que esse usuário atue em nome de outros usuários.

3. Insira seus relatórios do Power BI Embedded em seu aplicativo. Para obter mais informações, consulte [Como inserir seus painéis, relatórios e blocos do Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

## <a name="map-your-users-to-a-power-bi-user"></a>Mapear os usuários para um usuário do Power BI

Dentro de seu aplicativo, você mapeia os usuários gerenciados dentro do aplicativo para uma credencial *mestre* do Power BI para os fins do seu aplicativo. As credenciais para esta conta *mestre* do Power BI são armazenadas em seu aplicativo e são usadas para criar tokens de inserção.

## <a name="what-to-do-when-you-are-ready-for-production"></a>O que fazer quando estiver pronto para produção

Quando estiver pronto para passar para a produção, você precisará fazer o seguinte:

- Se você estiver usando um locatário separado para o desenvolvimento, precisará se certificar de que seus espaços de trabalho do aplicativo, junto com os painéis e relatórios, estão disponíveis no seu ambiente de produção. Certifique-se de que você criou o aplicativo no Azure AD para seu locatário de produção e atribua as permissões de aplicativo adequadas conforme indicado na Etapa 1.

- Compre uma capacidade que atenda às suas necessidades. Você pode usar o [white paper sobre planejamento de capacidade de análise inserida](https://aka.ms/pbiewhitepaper) para ajudar a entender o que pode ser necessário. Quando estiver pronto para comprar, você pode adquirir um recurso do Power BI Embedded no Portal do Azure.

- Edite o espaço de trabalho do aplicativo e atribua a ele uma capacidade em Avançado.

    ![Atribuir o espaço de trabalho do aplicativo a uma capacidade em powerbi.com](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- Implante seu aplicativo atualizado para a produção e comece a inserir relatórios do Power BI Embedded.

## <a name="after-migration"></a>Após a migração

É necessária uma limpeza nas Coleções de Espaços de Trabalho do Power BI.

- Remova todos os espaços de trabalho da solução implantada dentro do serviço do Azure das Coleções de Espaços de Trabalho do Power BI.
- Exclua todas as Coleções de Espaços de Trabalho que existem dentro do Azure.

## <a name="next-steps"></a>Próximas etapas

Parabéns. Seu aplicativo agora está migrado para o Power BI Embedded. Para obter informações sobre como inserir os painéis, relatórios e conjuntos de dados do Power BI, consulte [Como inserir seus painéis, relatórios e blocos do Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Mais perguntas? [Experimente perguntar para a Comunidade do Power BI](http://community.powerbi.com/)