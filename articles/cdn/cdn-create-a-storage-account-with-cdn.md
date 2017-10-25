---
title: Integrar uma conta de armazenamento do Azure com a CDN do Azure | Microsoft Docs
description: "Saiba como usar a CDN (Rede de Distribuição de Conteúdo) do Azure para fornecer um conteúdo com alta largura de banda armazenando em cache os blobs a partir do Armazenamento do Azure."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 511076935d06ed0908341044e37069e74530be49
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>Integrar uma conta de armazenamento do Azure com a CDN do Azure
CDN pode ser habilitada em cache o conteúdo do armazenamento do Azure. Ela oferece aos desenvolvedores uma solução global para entrega de conteúdo de largura de banda armazenando em cache blobs e conteúdo estático de instâncias de computação em nós físicos nos Estados Unidos, Europa, Ásia, Austrália e América do Sul.

## <a name="step-1-create-a-storage-account"></a>Etapa 1: Criar uma conta de armazenamento
Use o procedimento a seguir para criar uma nova conta de armazenamento para uma assinatura do Azure. A conta de armazenamento dá acesso aos serviços de armazenamento do Azure. A conta de armazenamento representa o mais alto nível do namespace para acessar cada um dos componentes do serviço de armazenamento do Azure: serviços Blob, serviços Fila e serviços Tabela. Para obter mais informações, veja [Introdução ao Armazenamento do Microsoft Azure](../storage/common/storage-introduction.md).

Para criar uma conta de armazenamento, você deve ser o administrador de serviços ou um coadministrador da assinatura associada.

> [!NOTE]
> Há vários métodos que você pode usar para criar uma conta de armazenamento, incluindo o Portal do Azure e o Powershell.  Para este tutorial, usaremos o Portal do Azure.  
> 
> 

**Para criar uma conta de armazenamento para uma assinatura do Azure**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No canto superior esquerdo, selecione **Novo**. No Diálogo **Novo**, selecione **Dados + Armazenamento** e clique em **Conta de armazenamento**.
    
    A folha **Criar conta de armazenamento** aparece.   

    ![Criar conta de armazenamento][create-new-storage-account]  

3. No campo **Nome** , digite um nome do subdomínio. Essa entrada pode conter de 3 a 24 letras minúsculas e números.
   
    Esse valor torna-se o nome de host no URI que é usado para lidar com os recursos de Blob, Fila ou Tabela da assinatura em questão. Para atender a um recurso de contêiner no serviço Blob, você usaria um URI no seguinte formato, onde *&lt;StorageAccountLabel&gt;* refere-se ao valor digitado em **Inserir uma URL**:
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*
   
    **Importante:** o rótulo da URL forma o subdomínio do URI da conta de armazenamento e deve ser exclusivo entre todos os serviços hospedados no Azure.
   
    Esse valor também é usado como o nome dessa conta de armazenamento no portal ou ao acessar essa conta programaticamente.
4. Mantenha os padrões para **Modelo de implantação**, **Tipo de conta**, **Desempenho** e **Replicação**. 
5. Selecione a **assinatura** que será usada com a conta de armazenamento.
6. Selecione ou crie um **Grupo de Recursos**.  Para saber mais sobre Grupos de Recursos, confira [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Selecione um local para sua conta de armazenamento.
8. Clique em **Criar**. O processo de criação da conta de armazenamento pode levar vários minutos para ser concluído.

## <a name="step-2-enable-cdn-for-the-storage-account"></a>Etapa 2: Habilitar a CDN para a conta de armazenamento

Com a integração mais recente, agora você pode habilitar a CDN para sua conta de armazenamento sem sair de sua extensão do portal de armazenamento. 

1. Selecione a conta de armazenamento, pesquise por "CDN" ou role para baixo no menu de navegação à esquerda e clique em "CDN do Azure".
    
    A folha **CDN do Azure** é exibida.

    ![navegação habilitada para cdn][cdn-enable-navigation]
    
2. Crie um novo ponto de extremidade inserindo as informações necessárias
    - **Perfil CDN**: você pode criar um novo perfil ou usar um existente.
    - **Tipo de preço**: você precisa selecionar um tipo de preço apenas se criar um novo perfil CDN.
    - **Nome do ponto de extremidade da CDN**: insira um nome de ponto de extremidade de sua escolha.

    > [!TIP]
    > O ponto de extremidade da CDN criado usa o nome do host de sua conta de armazenamento como origem por padrão.

    ![cdn new endpoint creation][cdn-new-endpoint-creation]

3. Após a criação, o novo ponto de extremidade aparecerá na lista de pontos de extremidade acima.

    ![novo ponto de extremidade do armazenamento da cdn][cdn-storage-new-endpoint]

> [!NOTE]
> Você também pode ir para a extensão da CDN do Azure para habilitar o CDN.[Tutorial](#Tutorial-cdn-create-profile).
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]  

## <a name="step-3-enable-additional-cdn-features"></a>Etapa 3: Habilitar recursos adicionais da CDN

Na folha de "CDN do Azure" da conta de armazenamento, clique no ponto de extremidade da CDN na lista para abrir a folha de configuração da CDN. Você pode habilitar recursos adicionais da CDN para o fornecimento, como compactação, cadeia de caracteres de consulta e filtragem de área geográfica. Você também pode adicionar o mapeamento de domínio personalizado ao seu ponto de extremidade da CDN e habilitar HTTPS do domínio personalizado.
    
![configuração da cdn do armazenamento da CDN][cdn-storage-cdn-configuration]

## <a name="step-4-access-cdn-content"></a>Etapa 4: acessar conteúdo da CDN
Para acessar o conteúdo armazenado em cache na CDN, utilize a URL da CDN fornecida no portal. O endereço de um blob armazenado em cache será semelhante ao seguinte:

http://<*EndpointName*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> Depois que você habilitar o acesso à CDN para uma conta de armazenamento, todos os objetos disponíveis publicamente estarão qualificados para armazenamento em cache de borda da CDN. Se você modificar um objeto que está armazenado em cache na CDN atualmente, o novo conteúdo não estará disponível por meio da CDN até que a CDN atualize seu conteúdo quando o período de vida do conteúdo em cache expirar.
> 
> 

## <a name="step-5-remove-content-from-the-cdn"></a>Etapa 5: remover conteúdo da CDN
Se não desejar mais armazenar em cache um objeto na CDN (Rede de Distribuição de Conteúdo) do Azure, você poderá executar uma das seguintes etapas:

* Você pode tornar o contêiner particular em vez de público. Veja [Gerenciar acesso anônimo de leitura aos contêineres e blobs](../storage/blobs/storage-manage-access-to-resources.md) para obter mais informações.
* Você pode desabilitar ou excluir o ponto de extremidade CDN usando o Portal de Gerenciamento.
* Você pode modificar seu serviço hospedado para não responder a solicitações do objeto.

Um objeto que já está armazenado em cache na CDN permanecerá em cache até que o período de vida útil do objeto expire ou até que o ponto de extremidade seja limpo. Quando o período de vida expira, a CDN verifica se o ponto de extremidade CDN ainda é válido, e se o objeto ainda pode ser acessado anonimamente. Se não for, o objeto não estará mais armazenado em cache.

## <a name="additional-resources"></a>Recursos adicionais
* [Como mapear o conteúdo da CDN para um domínio personalizado](cdn-map-content-to-custom-domain.md)
* [Habilitar o HTTPS para seu domínio personalizado](cdn-custom-ssl.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png
[cdn-enable-navigation]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png
[cdn-storage-new-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png
[cdn-storage-cdn-configuration]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png 
