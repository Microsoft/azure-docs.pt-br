---
title: Aprimoramentos de gerenciamento de usuário (versão prévia) – Azure Active Directory | Microsoft Docs
description: Descreve como Azure Active Directory permite a pesquisa de usuário, filtragem e mais informações sobre seus usuários.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5038bde01a6b183a25a47f3b4e206c1ce80e6b6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98127831"
---
# <a name="user-management-enhancements-preview-in-azure-active-directory"></a>Aprimoramentos de gerenciamento de usuário (versão prévia) no Azure Active Directory

Este artigo descreve como usar a visualização de aprimoramentos de gerenciamento de usuário no portal do Azure Active Directory (AD do Azure). As páginas **todos os usuários** e **usuários excluídos** foram atualizadas para fornecer mais informações e facilitar a localização dos usuários. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

As alterações na visualização incluem:

- Mais propriedades de usuário visíveis, incluindo ID de objeto, status de sincronização de diretório, tipo de criação e emissor de identidade
- A pesquisa agora permite a pesquisa de subcadeia de caracteres e a pesquisa combinada de nomes, emails e IDs de objeto
- Filtragem aprimorada por tipo de usuário (membro, convidado, nenhum), status de sincronização de diretório, tipo de criação, nome da empresa e nome de domínio
- Novos recursos de classificação em propriedades como nome e nome principal do usuário
- Uma nova contagem total de usuários que é atualizada com pesquisas ou filtros

> [!NOTE]
> Esta versão prévia não está disponível no momento para locatários Azure AD B2C.

## <a name="find-the-preview"></a>Localizar a versão prévia

A visualização é ativada por padrão, para que você possa usá-la imediatamente. Você pode conferir os recursos e aprimoramentos mais recentes selecionando **recursos de visualização** na página **todos os usuários** . Todas as páginas que foram atualizadas como parte dessa visualização exibirão uma marca de visualização. Se você estiver tendo problemas, poderá voltar para a experiência herdada:

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) e selecione **usuários**.
1. Na página **usuários – todos os usuários** , selecione a faixa na parte superior da página.
1. No painel **recursos de visualização** , desative o **Gerenciamento de usuário avançado** .

   ![Como e onde ativar e desativar o gerenciamento de usuário aprimorado](./media/users-search-enhanced/enable-preview.png)

Agradecemos seus comentários para que possamos melhorar nossa experiência.

## <a name="more-user-properties"></a>Mais propriedades de usuário

Fizemos algumas alterações nas colunas disponíveis nas páginas **todos os usuários** e **usuários excluídos** . Além das colunas existentes que fornecemos para gerenciar sua lista de usuários, adicionamos mais algumas colunas.

### <a name="all-users-page"></a>Página todos os usuários

A seguir estão as propriedades do usuário exibidas na página **todos os usuários** :

- Nome: o nome de exibição do usuário.
- Nome principal do usuário: o UPN (nome principal do usuário) do usuário.
- Tipo de usuário: membro, convidado, nenhum.
- Hora de criação: a data e a hora em que o usuário foi criado.
- Cargo: o cargo do usuário.
- Departamento: o departamento no qual o usuário trabalha.
- Diretório sincronizado: indica se o usuário está sincronizado de um diretório local.
- Emissor de identidade: os emissores da identidade usada para entrar em uma conta de usuário.
- ID do objeto: a ID de objeto do usuário.
- Tipo de criação: indica como a conta de usuário foi criada.
- Nome da empresa: o nome da empresa ao qual o usuário está associado.
- Estado do convite: o status do convite para um usuário convidado.
- Email: o email do usuário.
- Última entrada: a data em que o usuário se conectou pela última vez. Esta propriedade é visível somente para usuários com permissão para ler logs de auditoria (Reporting_ApplicationAuditLogs_Read)

![novas propriedades de usuário exibidas em todas as páginas usuários e usuários excluídos](./media/users-search-enhanced/user-properties.png)

### <a name="deleted-users-page"></a>Página usuários excluídos

A página **usuários excluídos** inclui todas as colunas que estão disponíveis na página **todos os usuários** e algumas colunas adicionais, isto é:

- Data de exclusão: a data em que o usuário foi excluído pela primeira vez da organização (o usuário é restaurável).
- Data de exclusão permanente: a data após a qual o processo de exclusão permanente do usuário da organização é iniciado automaticamente.
- Nome principal do usuário original: o UPN original do usuário antes de sua ID de objeto ter sido adicionada como um prefixo ao seu UPN excluído.

> [!NOTE]
> As datas de exclusão são exibidas em UTC (tempo Universal Coordenado).

Algumas colunas são exibidas por padrão. Para adicionar outras colunas, selecione as **colunas** na página, selecione os nomes de coluna que você deseja adicionar e selecione **OK** para salvar suas preferências.

### <a name="identity-issuers"></a>Emissores de identidade

Selecione uma entrada na coluna **emissor de identidade** para qualquer usuário exibir detalhes adicionais sobre o emissor, incluindo o tipo de entrada e a ID atribuída do emissor. As entradas na coluna **emissor da identidade** podem ter valores múltiplos. Se houver vários emissores da identidade do usuário, você verá a palavra vários na coluna emissor de **identidade** em **todas as páginas usuários** e **usuários excluídos** e o painel detalhes listará todos os emissores.

> [!NOTE]
> A coluna de **origem** é substituída por várias colunas, incluindo o **tipo de criação**, o **diretório sincronizado** e o **emissor de identidade** para filtragem mais granular.

## <a name="user-list-search"></a>Pesquisa de lista de usuários

Quando você insere uma cadeia de caracteres de pesquisa, a pesquisa agora usa "começa com" e a pesquisa de subcadeia de caracteres para corresponder nomes, emails ou IDs de objeto em uma única pesquisa. Você pode inserir qualquer um desses atributos na caixa de pesquisa e a pesquisa procurará automaticamente todas essas propriedades para retornar quaisquer resultados correspondentes. A pesquisa de subcadeia de caracteres é executada somente em palavras inteiras. Você pode executar a mesma pesquisa nas páginas **todos os usuários** e **usuários excluídos** .

## <a name="user-list-filtering"></a>Filtragem da lista de usuários

Os recursos de filtragem foram aprimorados para fornecer mais opções de filtragem para as páginas **todos os usuários** e **usuários excluídos** . Agora você pode filtrar por várias propriedades simultaneamente e pode filtrar por mais propriedades.

### <a name="filtering-all-users-list"></a>Filtrando a lista de todos os usuários

A seguir estão as propriedades filtráveis na página **todos os usuários** :

- Tipo de usuário: membro, convidado, nenhum
- Status sincronizado do diretório: Sim, não
- Tipo de criação: convite, email verificado, conta local
- Hora da criação: últimas 7, 14, 30, 90, 360 ou >360 dias atrás
- Título do trabalho: Insira um cargo
- Departamento: Insira um nome de departamento
- Grupo: Pesquisar um grupo
- Estado do convite – aceitação pendente, aceito
- Nome de domínio: Insira um nome de domínio
- Nome da empresa: Insira um nome de empresa
- Unidade administrativa: Selecione esta opção para restringir o escopo dos usuários exibidos a uma única unidade administrativa. Para obter mais informações, consulte [visualização de gerenciamento de unidades administrativas](../roles/administrative-units.md).

### <a name="filtering-deleted-users-list"></a>Filtrando a lista de usuários excluídos

A página **usuários excluídos** tem filtros adicionais que não estão na página **todos os usuários** . A seguir estão as propriedades filtráveis na página **usuários excluídos** :

- Tipo de usuário: membro, convidado, nenhum
- Status sincronizado do diretório: Sim, não
- Tipo de criação: convite, email verificado, conta local
- Hora da criação: últimas 7, 14, 30, 90, 360 ou > 360 dias atrás
- Título do trabalho: Insira um cargo
- Departamento: Insira um nome de departamento
- Estado do convite: aceitação pendente, aceita
- Data de exclusão: últimos 7, 14 ou 30 dias
- Nome de domínio: Insira um nome de domínio
- Nome da empresa: Insira um nome de empresa
- Data de exclusão permanente: últimos 7, 14 ou 30 dias

## <a name="user-list-sorting"></a>Classificação da lista de usuários

Agora você pode classificar por nome e nome principal de usuário nas páginas **todos os usuários** e **usuários excluídos** . Você também pode classificar por data de exclusão na lista de **usuários excluídos** .

## <a name="user-list-counts"></a>Contagens de lista de usuários

Você pode exibir o número total de usuários nas páginas **todos os usuários** e **usuários excluídos** . À medida que você pesquisa ou filtra as listas, a contagem é atualizada para refletir o número total de usuários encontrados.

![Ilustração de contagens de lista de usuários na página todos os usuários](./media/users-search-enhanced/user-list-sorting.png)

## <a name="frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ)

Pergunta | Resposta
-------- | ------
Por que o usuário excluído ainda será exibido quando a data de exclusão permanente tiver passado? | A data de exclusão permanente é exibida no fuso horário UTC, portanto, isso pode não corresponder ao seu fuso horário atual. Além disso, essa data é a primeira data após a qual o usuário será excluído permanentemente da organização, portanto, ele ainda pode estar sendo processado. Os usuários excluídos permanentemente serão removidos automaticamente da lista.
O que acontece com os recursos em massa para usuários e convidados? | As operações em massa ainda estão disponíveis para usuários e convidados, incluindo a criação em massa, o convite em massa, a exclusão em massa e o download de usuários. Acabamos de mesclá-los em um menu chamado **operações em massa**. Você pode encontrar as opções de **operações em massa** na parte superior da página **todos os usuários** .
O que aconteceu com a coluna de origem? | A coluna de **origem** foi substituída por outras colunas que fornecem informações semelhantes, permitindo que você filtre esses valores de forma independente. Os exemplos incluem o **tipo de criação**, o **diretório foi sincronizado** e o **emissor de identidade**.
O que aconteceu com a coluna nome de usuário? | A coluna **nome de usuário** ainda está lá, mas foi renomeada como **nome principal do usuário**. Isso reflete melhor as informações contidas nessa coluna. Você também observará que o nome principal do usuário completo agora é exibido para convidados B2B. Isso corresponde ao que você obteria no MS Graph.  

## <a name="next-steps"></a>Próximas etapas

Operações de usuário

- [Adicionar ou alterar informações de perfil](../fundamentals/active-directory-users-profile-azure-portal.md)
- [Adicionar ou excluir usuários](../fundamentals/add-users-azure-active-directory.md)

Operações em massa

- [Baixar a lista de usuários](users-bulk-download.md)
- [Adição de Usuários em Massa](users-bulk-add.md)
- [Exclusão em massa de usuários](users-bulk-delete.md)
- [Restauração de usuários em massa](users-bulk-restore.md)
