---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/27/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: cae108a1d4226e8c0fe39f9cd1cedc1e6a024ffc
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465439"
---
## <a name="sign-in-to-azure"></a>Entrar no Azure 

Entre no Portal do Azure em https://portal.azure.com.

> [!NOTE]
> Se você registrou para usar galerias de imagens compartilhadas durante a visualização, você talvez precise registrar novamente o `Microsoft.Compute` provedor. Abra [Cloud Shell](https://shell.azure.com/bash) e digite: `az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens

Uma galeria de imagens é o principal recurso usado para habilitar o compartilhamento de imagens. Caracteres permitidos para o nome da galeria são letras maiúsculas ou minúsculas, dígitos, pontos e pontos finais. O nome da Galeria não pode conter traços.  Os nomes das galerias devem ser exclusivos dentro de sua assinatura. 

O exemplo a seguir cria uma galeria chamada *myGallery* no grupo de recursos *myGalleryRG*.

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Usar o tipo **Galeria de imagens compartilhadas** na caixa de pesquisa e selecione **Galeria de imagens compartilhadas** nos resultados.
1. No **Galeria de imagens compartilhadas** , clique em **criar**.
1. Selecione a assinatura correta.
1. Na **grupo de recursos**, selecione **criar novo** e digite *myGalleryRG* para o nome.
1. Na **nome**, digite *myGallery* para o nome da Galeria.
1. Deixe o padrão para **região**.
1. Você pode digitar uma descrição breve da galeria, como *minha Galeria de imagens para teste.* e, em seguida, clique em **revisar + criar**.
1. Após a validação é bem-sucedida, selecione **criar**.
1. Quando a implantação for concluída, selecione **ir para o recurso**.
   
## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

Definições de imagem criar um agrupamento lógico de imagens. Eles são usados para gerenciar informações sobre as versões de imagem que são criados dentro deles. Nomes de definição de imagem podem ser compostos por períodos, dígitos, pontos, traços e letras maiusculas ou minúsculas. Para obter mais informações sobre os valores que você pode especificar para uma definição de imagem, consulte [definições de imagem](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Crie a definição da imagem da Galeria dentro de sua galeria. Neste exemplo, a imagem da Galeria é denominada *myImageDefinition*.

1. Na página de sua nova Galeria de imagens, selecione **adicionar uma nova definição de imagem** da parte superior da página. 
1. Para **nome da definição de imagem**, digite *myImageDefinition*.
1. Para **sistema operacional**, selecione a opção correta com base em sua imagem de origem.
1. Para **Publisher**, digite *myPublisher*. 
1. Para **oferecem**, digite *myOffer*.
1. Para **SKU**, digite *mySKU*.
1. Quando terminar, selecione **revisar + criar**.
1. Depois que a definição da imagem passa a validação, selecione **criar**.
1. Quando a implantação for concluída, selecione **ir para o recurso**.


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão de imagem de uma imagem gerenciada. Neste exemplo, a versão da imagem é *1.0.0* e ela é replicado para os datacenters *Centro-Oeste dos EUA* e *Centro-Sul dos EUA*. Ao escolher as regiões de destino para replicação, lembre-se de que você também terá que incluir o *origem* região como um destino para replicação.

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*. *MinorVersion*. *Patch*.

1. Na página de sua definição de imagem, selecione **Adicionar versão** da parte superior da página.
1. Na **região**, selecione a região em que sua imagem gerenciada é armazenada. Versões de imagem precisam ser criado na mesma região que a imagem gerenciada são criados a partir.
1. Para **nome**, digite *1.0.0*. O nome de versão da imagem deve seguir *principais*. *pequenas*. *patch* Formatar usando números inteiros. 
1. Na **imagem de origem**, selecione sua imagem gerenciada de origem na lista suspensa.
1. Na **excluir da versão mais recente**, deixe o valor padrão de *não*.
1. Para **final da vida útil**, selecione uma data no calendário que é de alguns meses no futuro.
1. Na **replicação**, deixe o **contagem de réplica padrão** como 1. Você precisa replicar para a região de origem, portanto, deixe a primeira réplica como o padrão e, em seguida, escolher uma segunda região da réplica seja *Leste dos EUA*.
1. Quando você terminar, selecione **revisar + criar**. Azure validará a configuração.
1. Quando a versão da imagem passa a validação, selecione **criar**.
1. Quando a implantação for concluída, selecione **ir para o recurso**.

Pode levar um tempo para replicar a imagem para todas as regiões de destino.

## <a name="share-the-gallery"></a>Compartilhar a Galeria

É recomendável que você compartilhe o acesso no nível da Galeria de imagem. O seguinte o orienta por meio do compartilhamento da Galeria que você acabou de criar.

1. Abra o [Portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **grupos de recursos**. 
1. Na lista de grupos de recursos, selecione **myGalleryRG**. Folha de seu grupo de recursos será aberto.
1. No menu à esquerda do **myGalleryRG** página, selecione **controle de acesso (IAM)** . 
1. Sob **adicionar uma atribuição de função**, selecione **Add**. O **adicionar uma atribuição de função** painel será aberto. 
1. Sob **função**, selecione **leitor**.
1. Sob **atribuir acesso a**, deixe o padrão de **usuário, grupo ou entidade de serviço do Azure AD**.
1. Sob **selecionar**, digite o endereço de email da pessoa que você gostaria de convidar.
1. Se o usuário estiver fora da sua organização, você verá a mensagem **esse usuário será enviado um email que permite a colaboração com a Microsoft.** Selecione o usuário com o endereço de email e, em seguida, clique em **salvar**.

Se o usuário estiver fora da sua organização, elas receberão um convite por email para participar da organização. O usuário precisa aceitar o convite, em seguida, eles poderão ver a Galeria e todas as definições de imagem e versões em sua lista de recursos.

