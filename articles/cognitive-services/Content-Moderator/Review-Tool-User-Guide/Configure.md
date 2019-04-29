---
title: Definir as configurações da ferramenta de revisão - Content Moderator
titlesuffix: Azure Cognitive Services
description: Use a ferramenta de análise para configurar ou recuperar sua equipe, marcas, conectores, fluxos de trabalho e as credenciais para o Content Moderator.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: f88ccbabc925b651abbc06f571a9d4220ed8aeb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61285316"
---
# <a name="configure-the-review-tool"></a>Configurar a ferramenta de análise

O [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) tem vários recursos importantes que você pode acessar por meio de **configurações** menu no painel.

![Revisão do moderador de conteúdo muito menu Configurações](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Gerenciar equipe e subequipes

O **Team** guia permite que você gerencie a sua equipe e subequipes&mdash;grupos de usuários que podem ser notificados quando determinados [análises humanas](../review-api.md#reviews) são iniciados. Você só pode ter uma equipe (que pode ser criado quando você se inscreve com a ferramenta de análise), mas você pode criar várias subequipes. O administrador da equipe pode convidar membros, definir suas permissões e atribuí-las a diferentes subequipes.

![Examine as configurações de equipe de ferramentas](images/settings-2-team.png)

Subequipes são úteis para a criação de equipes de escalonamento ou equipes dedicadas para revisar categorias específicas de conteúdo. Por exemplo, você pode enviar conteúdo adulto para uma equipe separada para uma análise posterior.

Esta seção explica como criar subequipes e atribuir rapidamente análises em tempo real. No entanto, você pode usar [Fluxos de trabalho](workflows.md) para atribuir revisões com base em critérios específicos.

### <a name="create-a-subteam"></a>Criar uma subequipe

Vá para o **Subequipes** seção e clique em **subequipe adicionar**. Insira seu nome subequipe na caixa de diálogo e clique em **salvar**.

![Nome da subequipe](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Convidar colegas de equipe

É possível atribuir alguém para uma subequipe se elas não ainda estiverem um membro da equipe padrão, portanto, você precisa adicionar os revisores para a equipe padrão primeiro. Clique em **convidar** sobre o **equipe** guia.

![Convidar usuários](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Atribuir membros da equipe subteam

Clique o **Adicionar membro** botão atribuir membros de sua equipe padrão para um ou mais subequipes. Você só pode adicionar usuários existentes para uma subequipe. Para adicionar novos usuários que não estão na ferramenta de análise, convidá-los usando o botão "Convidar" na página de Configurações de equipe.

![Atribuir membros da subequipe](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Atribuem revisões a subequipes

Depois que você criou seu subequipes e atribuídas a membros, você poderá começar a atribuir conteúdo [revisa](../review-api.md#reviews) para esses subequipes. Isso é feito a partir de **revisão** guia do site.
Para atribuir o conteúdo para uma subequipe, clique no botão de reticências no canto superior direito, selecione **mover para**e selecione uma subequipe.

![Atribuir a revisão de imagem à subequipe](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Alternar entre subequipes

Se você for um membro de mais de uma subequipe, você pode alternar entre esses subequipes para alterar quais revisões de conteúdo são exibidas para você. No **revisão** , selecione o menu suspenso rotulado **padrão** e selecione **escolha subequipe**. Você pode exibir as revisões de conteúdo para diferentes subequipes, mas apenas aqueles dos quais seu é um membro.

![Alternar entre subequipes](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Marcas

O **marcas** guia permite que você defina as marcas de moderação personalizados, além das marcas de moderação de padrão de dois&mdash;**isadult** (**um**) e **isracy**  (**r**). Quando você cria uma marca personalizada, ele fica disponível em revisões junto com as marcas padrão. Você pode alterar as marcas que aparecem nas revisões alterando suas configurações de visibilidade.

![Exibir marcas, inclusive as caixas de seleção "É visível"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Criar marcas personalizadas

Para criar uma nova marca, você deve inserir um código curto, nome e descrição nos respectivos campos.

- **Breve código**: Insira um código de duas letras para sua marca. Exemplo: **cb**
- **Nome**: Insira um nome de marca de curto e descritivo em letras minúsculas, sem espaços. Exemplo: **isbullying**.
- **Descrição**: (opcional) Insira uma descrição do tipo de conteúdo que os destinos de marca. Exemplo: **As representações gráficas ou instâncias cibernéticos brigas**.

Clique em **Add** para adicionar uma marca e clique em **salvar** quando tiver terminado de criar marcações.

![Ferramenta de revisão de criar a nova caixa de diálogo de marca](images/settings-3-tags.png)

### <a name="delete-tags"></a>Excluir marcas

Você pode excluir marcas personalizadas, selecionando o ícone de Lixeira ao lado de suas entradas na lista de marcas, mas não é possível excluir as marcas padrão.

## <a name="connectors"></a>Conectores

O **conectores** guia permite que você gerencie seus conectores, que são específicas do serviço Plug-ins que podem processar o conteúdo de diferentes maneiras como parte do conteúdo [fluxos de trabalho](../review-api.md#workflows).

O conector padrão quando você cria um fluxo de trabalho é o conector do Content Moderator, que pode marcar o conteúdo conforme **somente para adultos** ou **erótico**, localizar conteúdo ofensivo e assim por diante. No entanto, você pode usar outros conectores listados aqui, desde que você tem credenciais para seus respectivos serviços (para usar o conector de API de detecção facial, por exemplo, você precisará obter um [API de detecção facial](https://docs.microsoft.com/azure/cognitive-services/face/overview) chave de assinatura).

O [ferramenta de revisão](./human-in-the-loop.md) inclui os seguintes conectores:

- API de Detecção de Emoções
- API de Detecção Facial
- Serviço de nuvem PhotoDNA
- API de Análise de Texto

### <a name="add-a-connector"></a>Adicionar um conexão

Para adicionar um conector (e torná-lo disponível para uso no conteúdo [fluxos de trabalho](../review-api.md#workflows)), selecionar as devidas **Connect** botão. Na próxima caixa de diálogo, insira sua chave de assinatura para o serviço. Quando você terminar, o novo conector deve aparecer na parte superior da página.

![Configurações de conectores do Content Moderator](images/settings-4-connectors.png)

## <a name="workflows"></a>Fluxos de trabalho

O **fluxos de trabalho** guia permite que você gerencie seus [fluxos de trabalho](../review-api.md#workflows). Fluxos de trabalho são filtros baseados em nuvem para o conteúdo e trabalhar com conectores para classificar o conteúdo de diferentes maneiras e tomar as medidas apropriadas. Aqui, você pode definir, editar e testar seus fluxos de trabalho. Ver [definir e usar fluxos de trabalho](Workflows.md) para obter orientação sobre como fazer isso.

![Configurações de fluxo de trabalho do Content Moderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Credenciais

O **credenciais** guia fornece acesso rápido a sua chave de assinatura do Content Moderator, você precisará acessar nenhum dos serviços de moderação de uma chamada REST ou o SDK do cliente.

![Credenciais do Content Moderator](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Usar credenciais externas para fluxos de trabalho

O [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) gera uma chave de avaliação gratuita para serviços do Azure Content Moderator quando você se inscrever, mas você também pode configurá-lo para usar uma existente de chave da sua conta do Azure. Isso é recomendado para cenários de larga escala, como as chaves de avaliação gratuitas têm limites de uso estrito ([preços e limites](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Se você tiver criado uma [recursos do Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) no Azure, navegue até ele no portal do Azure e selecione o **chaves** folha. Copie uma das suas chaves.

![Chaves do Content Moderator no portal do Azure](images/credentials-azure-portal-keys.PNG)

No [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com)do **credenciais** guia, vá para o **configurações de fluxo de trabalho** painel, selecione **editar**e cole sua chave de **Ocp-Apim-Subscription-Key** campo. Agora, os fluxos de trabalho que chamam as APIs de moderação usará sua credencial do Azure.

> [!NOTE]
> Os outros dois campos na **configurações de fluxo de trabalho** painel são para as listas personalizadas de termo e a imagem. Consulte a [termos personalizado](../try-terms-list-api.md) ou [imagens personalizadas](../try-image-list-api.md) guias para saber mais sobre eles.

### <a name="use-your-azure-account-with-the-review-apis"></a>Use sua conta do Azure com as APIs de análise

Para usar a chave do Azure com as APIs de análise, você precisa recuperar a ID do recurso. Vá para o recurso do Content Moderator no portal do Azure e selecione o **propriedades** folha. Copie o valor de ID de recurso e cole-o na **IDs do recurso na lista de permissões** campo da ferramenta de revisão **credenciais** guia.

![ID de Recurso do Content Moderator no portal do Azure](images/credentials-azure-portal-resourceid.PNG)

Se você inseriu sua chave de assinatura em ambos os locais, a chave de avaliação que vem com sua conta da ferramenta de revisão não será usada, mas permanecerão disponível.

## <a name="next-steps"></a>Próximos passos

Siga as [início rápido de ferramenta de revisão](../quick-start.md) para começar a usar a ferramenta de análise em cenários de moderação de conteúdo.