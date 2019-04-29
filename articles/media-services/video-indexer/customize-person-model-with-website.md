---
title: Usar o site do Video Indexer para personalizar um modelo de Pessoa – Azure
titlesuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo de Pessoa com o site do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 03/19/2019
ms.author: anzaman
ms.openlocfilehash: 8dd535d97e40fe1dd4358d782db60940af1dd95d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60555480"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Personalizar um modelo de Pessoa com o site do Video Indexer

Video Indexer dá suporte a reconhecimento de celebridades para conteúdo de vídeo. O recurso de reconhecimento de celebridades abrange aproximadamente um milhão de rostos baseados em fontes de dados geralmente solicitadas, como IMDB, Wikipédia e principais influenciadores do LinkedIn. Para obter uma visão detalhada, consulte [personalizar um modelo de pessoa no Video Indexer](customize-person-model-overview.md).

Use o site do Video Indexer para editar os rostos detectados em um vídeo, conforme descrito neste tópico. Você também pode usar a API, conforme descrito em [personalizar um modelo de Person usando as APIs](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Gerenciamento central dos modelos de pessoa em sua conta

1. Para exibir, editar e excluir modelos de pessoa em sua conta, navegue até o site do indexador de vídeo e entrar.
2. Clique no botão de personalização do modelo de conteúdo no canto superior direito da página.

    ![Personalização do modelo de conteúdo](./media/customize-face-model/content-model-customization.png)
3. Selecione a guia pessoas.

    Você verá o modelo padrão pessoa em sua conta. O modelo padrão pessoa mantém qualquer faces tenha editado ou alterado nas insights de seus vídeos para o qual você não especificou um modelo personalizado de pessoa durante a indexação. 

    Se você criou outros modelos de pessoa, eles serão listados nesta página também. 

    ![Personalização do modelo de conteúdo](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Criar um novo modelo de Pessoa

1. Clique o **+ Adicionar modelo** botão.

    ![Adicionar uma nova pessoa](./media/customize-face-model/add-new-person.png)
2. Insira o nome do modelo e clique no botão de seleção ao lado do nome.

    ![Adicionar uma nova pessoa](./media/customize-face-model/add-new-person2.png)

    Você criou um novo modelo de pessoa. Agora você pode adicionar faces para o novo modelo de pessoa.
3. Clique no botão de menu de lista e escolha **+ adicionar pessoa**.

    ![Adicionar uma nova pessoa](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Adicionar uma nova pessoa a um modelo de pessoa

> [!NOTE]
> Indexador de vídeo permite que você adicione várias pessoas com o mesmo nome em um modelo de pessoa. No entanto, é recomendável que você atribua unque nomes para cada pessoa em seu modelo para maior clareza e usabilidade.

1. Para adicionar um novo rosto a um modelo de pessoa, clique no botão de menu lista ao lado do modelo de pessoa que você deseja adicionar a face para.
1. Clique em **+ adicionar pessoa** no menu.

    ![Adicionar uma nova face para a pessoa](./media/customize-face-model/add-new-face.png)
 
    Um pop-up solicitará que você preencha os detalhes da pessoa. Digite o nome da pessoa e clique no botão de seleção.

    ![Adicionar uma nova face para a pessoa](./media/customize-face-model/add-new-face2.png)
 
Em seguida, você pode escolher entre o Explorador de arquivos ou arraste e solte as imagens de face da face. Indexador de vídeo o levará a imagem padrão de todos os tipos de arquivo (por exemplo: JPG, PNG e muito mais).

Indexador de vídeo deve ser capaz de detectar ocorrências dessa pessoa nos vídeos futuras desse índice para você e os vídeos atuais que você já tinha indexado, usando o modelo de pessoa à qual você adicionou essa nova face para. Reconhecimento da pessoa em seus vídeos atuais pode levar algum tempo para entrar em vigor, pois esse é um processo em lote.


## <a name="rename-a-person-model"></a>Renomear um modelo de pessoa

Você pode renomear o modelo de qualquer pessoa em sua conta, incluindo o modelo padrão de pessoa. Mesmo se você renomear seu modelo de pessoa padrão, ele ainda servirá como o modelo padrão de pessoa em sua conta.

1. Clique no botão de menu lista ao lado do modelo de pessoa que você deseja renomear.
2. Clique em **Renomear** no menu.

    ![Renomear uma pessoa](./media/customize-face-model/rename-person.png)
3. Clique no nome do modelo atual e digite o novo nome.

    ![Renomear uma pessoa](./media/customize-face-model/rename-person2.png)
4. Clique no botão de seleção para o seu modelo a ser renomeado.

## <a name="delete-a-person-model"></a>Excluir um modelo de Pessoa

Você pode excluir qualquer modelo de pessoa que criou na sua conta. No entanto, você não pode excluir seu modelo de pessoa padrão.

1. Clique em **excluir** no menu.

    ![Excluir uma pessoa](./media/customize-face-model/delete-person.png)
    
    Um pop-up será exibido e notificá-lo que essa ação excluirá o modelo de pessoa e todas as pessoas e os arquivos que ele contém. Essa ação não pode ser desfeita. 

    ![Excluir uma pessoa](./media/customize-face-model/delete-person2.png)
1. Se você tiver certeza, clique em Excluir novamente.

> [!NOTE]
> Os vídeos existentes que foram indexados usando esse modelo de pessoa (agora excluído) não oferecerá suporte a capacidade de atualizar os nomes das faces que aparecem no vídeo. Você poderá editar os nomes de faces nesses vídeos somente depois que você reindexá-los usando o modelo de outra pessoa. Se você reindexar sem especificar um modelo de pessoa, o modelo padrão será usado. 

## <a name="manage-existing-people-in-a-person-model"></a>Gerencie pessoas existentes em um modelo de pessoa

Para examinar o conteúdo de qualquer um dos seus modelos de pessoa, clique na seta ao lado do nome do modelo de pessoa.
Na lista suspensa mostra todas as pessoas nesse modelo pessoa específico. Se você clicar no botão de menu lista ao lado de cada uma das pessoas, você verá renomear e excluir as opções.  

![Adicionar uma nova face para a pessoa](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Renomear uma pessoa

1. Para renomear uma pessoa em seu modelo de pessoa, clique no botão de menu de lista e escolha **Renomear** no menu de lista.
1. Clique no nome atual da pessoa e digite o novo nome.
1. Clique no botão de seleção e a pessoa será renomeada.

### <a name="delete-a-person"></a>Excluir uma pessoa

1. Para excluir uma pessoa do seu modelo de pessoa, clique no botão de menu de lista e escolha **excluir** no menu de lista.
1. Um pop-up que informa que essa ação excluirá a pessoa e que essa ação não pode ser desfeita.
1. Clique em Excluir novamente e isso removerá a pessoa de modelo de pessoa.

### <a name="manage-a-person"></a>Gerenciar uma pessoa

Se você clicar em **gerenciar**, você verá todas as faces que esse modelo da pessoa que está sendo treinado do. Essas faces vêm de ocorrências daquela pessoa nos vídeos que usam esse modelo de pessoa ou de imagens que você carregou manualmente. 

Você pode adicionar mais faces à pessoa, clicando em Adicionar imagens.

Você pode usar o painel de gerenciar para renomear a pessoa e para excluir a pessoa de modelo de pessoa.

## <a name="use-a-person-model-to-index-a-video"></a>Use um modelo de pessoa para indexar um vídeo

Você pode usar um modelo de pessoa para indexar seu novo vídeo atribuindo o modelo de pessoa durante o carregamento do vídeo.

Para usar seu modelo de pessoa em um novo vídeo, faça o seguinte:

1. Clique o **carregar** botão na parte superior da página.

    ![Carregar](./media/customize-face-model/upload.png)
1. Descarte o arquivo de vídeo no círculo ou procurar o arquivo.
1. Clique na seta de opções avançadas.

    ![Carregar](./media/customize-face-model/upload2.png)
1. Clique na lista suspensa e selecione o modelo da pessoa que você criou.

    ![Carregar](./media/customize-face-model/upload3.png)
1. Clique na opção de carregar na parte inferior da página, e seu novo vídeo será indexado usando seu modelo de pessoa.

Se você não especificar um modelo de pessoa durante o carregamento, o indexador de vídeo indexará o vídeo usando o modelo padrão pessoa em sua conta.

## <a name="use-a-person-model-to-reindex-a-video"></a>Usar um modelo de pessoa para reindexar um vídeo 

Para usar um modelo de pessoa para reindexar um vídeo em sua coleção, vá para seus vídeos com a conta na home page do indexador de vídeo e passe o mouse sobre o nome do vídeo que você deseja reindexar.

Você verá opções para editar, excluir e reindexar seu vídeo. 

1. Clique na opção de reindexar o vídeo.

    ![Reindexar](./media/customize-face-model/reindex.png)

    Agora você pode selecionar o modelo de pessoa reindexar o vídeo.
1. Clique na lista suspensa e selecione o modelo da pessoa que você deseja usar. 

    ![Reindexar](./media/customize-face-model/reindex2.png)

1. Clique o **reindexar** botão e o vídeo serão reindexados usando seu modelo de pessoa.

Todas as novas edições feitas as faces detectadas e reconhecido no vídeo que você acabou de ser reindexados serão salvo no modelo de pessoa que você usou o vídeo de reindexação.

## <a name="managing-people-in-your-videos"></a>Gerenciamento de pessoas em seus vídeos

Você pode gerenciar as faces detectadas e as pessoas que foram reconhecidas nos vídeos que você indexa editando e excluindo faces.

Excluindo uma face, remove uma face específica as análises do vídeo.

Editar uma face, renomeia uma face que é detectada e possivelmente reconhecida no vídeo. Quando você edita uma face em vídeo, esse nome é salvo como uma entrada de person no modelo de pessoa que foi atribuído ao vídeo durante o carregamento e indexação.

Se você não atribuir um modelo de pessoa ao vídeo durante o carregamento, sua edição será salva no modelo de pessoa da sua conta padrão.

### <a name="edit-a-face"></a>Editar um rosto


> [!NOTE]
> Se um modelo de pessoa tiver duas ou mais pessoas diferentes com o mesmo nome, você não poderá marcar esse nome dos vídeos que usam esse modelo de pessoa. Você só poderá fazer alterações às pessoas que compartilham esse nome na guia da página de personalização do modelo de conteúdo no Video Indexer pessoas. Por esse motivo, é recomendável que você atribua nomes exclusivos para cada pessoa em seu modelo de pessoa.

1. Navegue até o site do indexador de vídeo e entre.
1. Pesquise um vídeo que você deseja exibir e editar em sua conta.
1. Para editar uma face em vídeo, vá para a guia Insights e clique no ícone de lápis no canto superior direito da janela.

    ![Editar uma face em vídeo](./media/customize-face-model/edit-face.png)
1. Clique em um dos rostos detectados e altere seus nomes de "Desconhecido nº X" (ou o nome que foi atribuído anteriormente ao rosto). 
1. Depois de digitar o novo nome, clique no ícone de verificação ao lado do novo nome. Isso salva o novo nome e reconhece e nomes de todas as ocorrências dessa face em seus outros vídeos atuais e futuramente vídeos que você carregar. Reconhecimento de face em seus outros vídeos atuais pode levar algum tempo entrem em vigor, pois esse é um processo em lote.

Se você nomear uma face com o nome de uma pessoa existente no modelo de pessoa que o vídeo está usando, isso vai mesclar as imagens de rosto detectado através deste vídeo daquela pessoa com o que já existe no modelo. Se você nomear uma face com um nome completamente novo, isso criará uma nova entrada de Person no modelo de pessoa que o vídeo está usando. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Excluir um rosto

Para excluir um rosto detectado no vídeo, vá para o painel de informações e clique no ícone de lápis no canto superior direito do painel. Clique na opção de exclusão sob o nome da face. Isso removerá o rosto detectado do vídeo. Face da pessoa será detectado ainda outros vídeos em que aparece, mas você pode excluir a face desses vídeos bem depois que eles tenham sido indexados. As pessoas, se tivesse sido chamada, também continuará a existir no modelo de pessoa que foi usado para indexar o vídeo em que você excluiu a face a menos que você exclua especificamente a pessoa do modelo de pessoa.

![Excluir uma face em vídeo](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de Pessoa usando APIs](customize-person-model-with-api.md)
