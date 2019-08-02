---
title: Colaboração-LUIS
titleSuffix: Azure Cognitive Services
description: Os aplicativos LUIS exigem um único proprietário e colaboradores opcionais, permitindo que várias pessoas criem um único aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: diberry
ms.openlocfilehash: e53ba9f24e40837a823e6012340358113d490f46
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560799"
---
# <a name="collaborating-with-other-authors"></a>Colaborar com outros autores

Os aplicativos LUIS exigem um único proprietário e colaboradores opcionais, permitindo que várias pessoas criem um único aplicativo.

## <a name="luis-account"></a>Conta LUIS
Uma conta LUIS está associada uma única conta do [Microsoft Live](https://login.live.com/). Cada conta LUIS recebe uma [chave de criação](luis-concept-keys.md#authoring-key) gratuita para ser usada para criar todos os aplicativos LUIS aos quais a conta tem acesso. 

Uma conta LUIS pode ter muitos aplicativos LUIS.

Consulte [Usuário do locatário do Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) para saber mais sobre as contas de usuário do Active Directory. 

## <a name="luis-app-owner"></a>Proprietário do aplicativo LUIS

A conta que cria um aplicativo é o proprietário e cada aplicativo tem um único proprietário. O proprietário é listado na página **[configurações](luis-how-to-collaborate.md)** do aplicativo. O proprietário recebe email quando a cota do ponto de extremidade atinge 75% do limite mensal. 

## <a name="authorization-roles"></a>Funções de autorização
O LUIS não dá suporte a funções diferentes para os proprietários e colaboradores com uma exceção. O proprietário é a única conta que pode excluir o aplicativo.

Se você tiver interesse em controlar o acesso ao modelo, considere dividir o modelo em aplicativos LUIS menores com um conjunto mais limitado de colaboradores. Use [Expedir](https://aka.ms/dispatch-tool) para permitir que um aplicativo LUIS pai gerencie a coordenação entre os aplicativos pai e filho.

## <a name="transfer-ownership"></a>Transferir propriedade
O LUIS não oferece transferência de propriedade; contudo, qualquer colaborador pode exportar o aplicativo e criar um aplicativo importando-o. Lembre-se de que o novo aplicativo tem uma ID do aplicativo diferente. O novo aplicativo precisa ser treinado, publicado e o novo ponto de extremidade, usado.

## <a name="luis-app-collaborators"></a>Colaboradores do aplicativo LUIS
Um proprietário de aplicativo pode adicionar colaboradores a um aplicativo. O proprietário precisa adicionar o endereço de email do colaborador nas **[Configurações](luis-how-to-collaborate.md)** do aplicativo. O colaborador tem acesso completo ao aplicativo. Se o colaborador excluir o aplicativo, o aplicativo será removido da conta do colaborador, mas continuará na conta do proprietário. 

Se desejar compartilhar vários aplicativos com os colaboradores, cada aplicativo precisará do email do colaborador adicionado. 

## <a name="managing-multiple-authors"></a>Gerenciando vários autores
No momento, o site do [LUIS](luis-reference-regions.md#luis-website) não oferece criação no nível da transação. É possível permitir que os autores trabalhem em versões independentes de uma versão base. Dois diferentes métodos são descritos nas seções a seguir.

## <a name="manage-multiple-versions-inside-the-same-app"></a>Gerenciar várias versões dentro do mesmo aplicativo
Comece [clonando](luis-how-to-manage-versions.md#clone-a-version), de uma versão base, para cada autor. 

Cada autor faz alterações em sua própria versão do aplicativo. Depois que cada autor estiver satisfeito com o modelo, exporte as novas versões para os arquivos JSON.  

Aplicativos exportados são arquivos formatados para JSON, que podem ser comparados quanto a alterações. Combine os arquivos para criar um único arquivo JSON da nova versão. Altere a propriedade **versionId** no JSON para significar a nova versão mesclada. Importe essa versão para o aplicativo original. 

Esse método permite que você tenha uma versão ativa, uma versão do estágio e uma versão publicada. É possível comparar os resultados no painel de teste interativo entre as três versões.

## <a name="manage-multiple-versions-as-apps"></a>Gerenciar várias versões como aplicativos
[Exporte](luis-how-to-manage-versions.md#export-version) a versão base. Cada autor importa a versão. A pessoa que importa o aplicativo é o proprietário da versão. Quando ela terminar de modificar o aplicativo, exporte a versão. 

Aplicativos exportados são arquivos formatados para JSON, que podem ser comparados com a exportação base quanto a alterações. Combine os arquivos para criar um único arquivo JSON da nova versão. Altere a propriedade **versionId** no JSON para significar a nova versão mesclada. Importe essa versão para o aplicativo original.

## <a name="collaborator-roles-vs-entity-roles"></a>Funções de colaborador vs. funções de entidade

As [funções de entidade](luis-concept-roles.md) se aplicam ao modelo de dados do aplicativo Luis. As funções de colaborador se aplicam a níveis de acesso de criação. 

## <a name="next-steps"></a>Próximas etapas

Entender os conceitos de [controle de versão](luis-concept-version.md). 

Confira [Configurações do aplicativo](luis-how-to-collaborate.md) para saber como gerenciar colaboradores em seu aplicativo LUIS.

Confira [Add email to access list](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342) (Adicionar email à lista de acesso) com as APIs de criação.
