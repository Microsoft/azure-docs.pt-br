---
title: Definir configurações da ferramenta de revisão-Content Moderator
titleSuffix: Azure Cognitive Services
description: Use a ferramenta de revisão para configurar ou recuperar sua equipe, marcas, conectores, fluxos de trabalho e credenciais para Content Moderator.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: 70bc2c9f457aba09f3d3220061b2fd31d0923906
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565667"
---
# <a name="configure-the-review-tool"></a>Configurar a ferramenta de análise

A [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) tem vários recursos importantes que você pode acessar por meio do menu **configurações** no painel.

![Menu Content Moderator revisar configurações](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Gerenciar equipe e subequipes

A guia **equipe** permite que você gerencie seus&mdash;grupos de equipe e subequipes de usuários que podem ser notificados quando determinadas [revisões humanas](../review-api.md#reviews) são iniciadas. Você só pode ter uma equipe (que você cria ao se inscrever com a ferramenta de revisão), mas pode criar várias subequipes. O administrador da equipe pode convidar Membros, definir suas permissões e atribuí-los a diferentes subequipes.

![Examinar as configurações da equipe de ferramentas](images/settings-2-team.png)

Subequipes são úteis para a criação de equipes de escalonamento ou equipes dedicadas para revisar categorias específicas de conteúdo. Por exemplo, você pode enviar conteúdo adulto para uma equipe separada para análise adicional.

Esta seção explica como criar subequipes e atribuir rapidamente revisões imediatamente. No entanto, você pode usar [Fluxos de trabalho](workflows.md) para atribuir revisões com base em critérios específicos.

### <a name="create-a-subteam"></a>Criar uma subequipe

Vá para a  seção subequipes e clique em **Adicionar subequipe**. Insira o nome da sua subequipe na caixa de diálogo e clique em **salvar**.

![Nome da subequipe](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Convidar colegas de equipe

Você não poderá atribuir alguém a uma subequipe se eles ainda não forem membros da equipe padrão, portanto, você precisará primeiro adicionar revisores à equipe padrão. Clique em **convidar** na guia **equipe** .

![Convidar usuários](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Atribuir colegas à subequipe

Clique no botão **Adicionar membro** para atribuir membros de sua equipe padrão a uma ou mais subequipes. Você só pode adicionar usuários existentes para uma subequipe. Para adicionar novos usuários que não estão na ferramenta de análise, convidá-los usando o botão "Convidar" na página de Configurações de equipe.

![Atribuir membros da subequipe](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Atribuir resenhas a subequipes

Depois de criar suas subequipes e os membros atribuídos, você pode começar a atribuir [revisões](../review-api.md#reviews) de conteúdo a essas subequipes. Isso é feito na guia **revisão** do site.
Para atribuir conteúdo a uma subequipe, clique nas reticências no canto superior direito, selecione **mover para**e selecione uma subequipe.

![Atribuir a revisão de imagem à subequipe](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Alternar entre subequipes

Se você for membro de mais de uma subequipe, poderá alternar entre essas subequipes para alterar quais revisões de conteúdo são exibidas para você. Na guia  revisar, selecione o menu suspenso rotulado **como padrão** e selecione **escolher subequipe**. Você pode exibir as revisões de conteúdo para diferentes subequipes, mas apenas aquelas das quais você é membro.

![Alternar entre subequipes](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Marcas

A guia **marcas** permite que você defina marcas de moderação personalizadas além das duas marcas&mdash;de moderação padrão isadulto (**a**) e **isracy** ( **r**). Quando você cria uma marca personalizada, ela fica disponível em revisões junto com as marcas padrão. Você pode alterar quais marcas aparecem em revisões alternando suas configurações de visibilidade.

![Exibição de marcas, incluindo as caixas de seleção "is Visible"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Criar marcas personalizadas

Para criar uma nova marca, você deve inserir um código curto, um nome e uma descrição nos respectivos campos.

- **Código curto**: Insira um código de duas letras para sua marca. Exemplo: **CB**
- **Nome**: Insira um nome de marca curto e descritivo em minúsculas, sem espaços. Exemplo: **isbullying**.
- **Descrição**: (opcional) Insira uma descrição do tipo de conteúdo que sua marca visa. Exemplo: **Representações ou instâncias do cyber bullying**.

Clique em **Adicionar** para adicionar uma marca e clique em **salvar** quando terminar de criar marcas.

![Caixa de diálogo examinar criar nova marca da ferramenta](images/settings-3-tags.png)

### <a name="delete-tags"></a>Excluir marcas

Você pode excluir marcas personalizadas selecionando o ícone de Lixeira ao lado de suas entradas na lista de marcas, mas não pode excluir as marcas padrão.

## <a name="connectors"></a>Conectores

A  guia conectores permite que você gerencie seus conectores, que são plug-ins específicos do serviço que podem processar conteúdo de maneiras diferentes como parte dos [fluxos de trabalho](../review-api.md#workflows)de conteúdo.

O conector padrão quando você cria um fluxo de trabalho é o conector de Content Moderator, que pode marcar o conteúdo como **adulto** ou **erótico**, encontrar profanação e assim por diante. No entanto, você pode usar outros conectores, listados aqui, desde que tenha credenciais para seus respectivos serviços (para usar o conector de API de Detecção Facial, por exemplo, será necessário obter uma chave de assinatura [API de detecção facial](https://docs.microsoft.com/azure/cognitive-services/face/overview) ).

A [ferramenta de revisão](./human-in-the-loop.md) inclui os seguintes conectores:

- API de Detecção de Emoções
- API de Detecção Facial
- Serviço de nuvem PhotoDNA
- API de Análise de Texto

### <a name="add-a-connector"></a>Adicionar um conexão

Para adicionar um conector (e torná-lo disponível para uso em [fluxos de trabalho](../review-api.md#workflows)de conteúdo), selecione o botão **conectar** apropriado. Na próxima caixa de diálogo, insira sua chave de assinatura para esse serviço. Quando terminar, o novo conector deverá aparecer na parte superior da página.

![Configurações de conectores do Content Moderator](images/settings-4-connectors.png)

## <a name="workflows"></a>Workflows

A guia **fluxos de trabalho** permite que você gerencie seus fluxos de [trabalho](../review-api.md#workflows). Os fluxos de trabalho são filtros baseados em nuvem para conteúdo e trabalham com conectores para classificar conteúdo de maneiras diferentes e tomar as medidas apropriadas. Aqui, você pode definir, editar e testar seus fluxos de trabalho. Consulte [definir e usar fluxos de trabalho](Workflows.md) para obter orientação sobre como fazer isso.

![Configurações de fluxo de trabalho do Content Moderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Credenciais

A guia **credenciais** fornece acesso rápido à sua chave de assinatura do Content moderator, que será necessário para acessar qualquer um dos serviços de moderação de uma chamada REST ou SDK do cliente.

![Credenciais do Content Moderator](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Usar credenciais externas para fluxos de trabalho

A [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) gera uma chave de avaliação gratuita para os serviços de Content moderator do Azure quando você se inscreve, mas você também pode configurá-lo para usar uma chave existente de sua conta do Azure. Isso é recomendado para cenários em larga escala, pois as chaves de avaliação gratuitas têm limites de uso estritos ([preços e limites](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Se você tiver criado um [recurso de Content moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) no Azure, navegue até ele na portal do Azure e selecione a folha **chaves** . Copie uma das suas chaves.

![Chaves do Content Moderator no portal do Azure](images/credentials-azure-portal-keys.PNG)

Na guia **credenciais** da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com), vá para o painel configurações de **fluxo de trabalho** , selecione **Editar**e cole sua chave no campo **OCP-APIM-Subscription-Key** . Agora, os fluxos de trabalho que chamam as APIs de moderação usarão sua credencial do Azure.

> [!NOTE]
> Os outros dois campos do painel **configurações de fluxo de trabalho** são para listas de termos e imagens personalizadas. Consulte os [termos personalizados](../try-terms-list-api.md) ou guias de [imagens personalizadas](../try-image-list-api.md) para saber mais sobre eles.

### <a name="use-your-azure-account-with-the-review-apis"></a>Usar sua conta do Azure com as APIs de revisão

Para usar sua chave do Azure com as APIs de revisão, você precisa recuperar sua ID de recurso. Vá para o recurso de Content Moderator no portal do Azure e selecione a folha **Propriedades** . Copie o valor da ID do recurso e cole-o no campo **IDs de recursos da lista** de permissões da guia **credenciais** da ferramenta de revisão.

![ID de Recurso do Content Moderator no portal do Azure](images/credentials-azure-portal-resourceid.PNG)

Se você tiver inserido sua chave de assinatura em ambos os locais, a chave de avaliação que acompanha a sua conta da ferramenta de revisão não será usada, mas permanecerá disponível.

## <a name="next-steps"></a>Próximas etapas

Siga o guia de [início rápido da ferramenta de revisão](../quick-start.md) para começar a usar a ferramenta de revisão em cenários de moderação de conteúdo.