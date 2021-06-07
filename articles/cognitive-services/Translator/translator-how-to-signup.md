---
title: Criar um recurso Tradutor
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como criar um recurso de tradutor de serviços cognitivas do Azure e obter uma chave de assinatura e uma URL de ponto de extremidade.
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: a0d8532d19aff41bc5e7defb3b58462e81018749
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712922"
---
# <a name="create-a-translator-resource"></a>Criar um recurso Tradutor

Neste artigo, você aprenderá a criar um recurso de tradutor no portal do Azure. O [Azure Translator](translator-info-overview.md) é um serviço de tradução automática baseado em nuvem que faz parte da família de APIs REST dos [Serviços cognitivas do Azure](../what-are-cognitive-services.md) . Os recursos do Azure são instâncias de serviços que você cria. Todas as solicitações de API para os serviços do Azure exigem uma URL de **ponto de extremidade** e uma **chave de assinatura** somente leitura para autenticar o acesso.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de uma [**conta ativa do Azure**](https://azure.microsoft.com/free/cognitive-services/).  Se você não tiver uma, poderá [**criar uma assinatura gratuita de 12 meses**](https://azure.microsoft.com/free/).

## <a name="translator-resource-types"></a>Tipos de recurso do Tradutor

O serviço do tradutor pode ser acessado por meio de dois tipos de recursos diferentes:

* Os tipos de recurso **de serviço único** habilitam o acesso a uma única chave de API de serviço e ponto de extremidade.  

* Os tipos de recursos de **vários serviços** permitem o acesso a vários serviços cognitivas usando uma única chave de API e ponto de extremidade. O recurso de serviços cognitivas está disponível atualmente para os seguintes serviços:
  * Linguagem ([Tradutor](../translator/translator-info-overview.md), [reconhecimento vocal (LUIS)](../luis/what-is-luis.md), [análise de texto](../text-analytics/overview.md))  
  * Visão ([Pesquisa Visual computacional](../computer-vision/overview.md)), ([face](../face/overview.md))  
  * Decisão ([Content moderator](../content-moderator/overview.md))  

## <a name="create-your-resource"></a>Criar seu recurso

* Navegue diretamente para a página [**criar Tradutor**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) no portal do Azure para concluir os detalhes do projeto.

* Navegue diretamente para a página [**criar serviços cognitivas**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) no portal do Azure para concluir os detalhes do projeto.

>[!TIP]
>Se preferir, você pode iniciar no portal do Azure home page para iniciar o processo de **criação** da seguinte maneira:
>
> 1. Navegue até o [**portal do Azure**](https://ms.portal.azure.com/#home) Home Page.
> 1. Selecione ➕**criar um recurso**  no menu serviços do Azure.
>1. Na caixa Pesquisar **na pesquisa do Marketplace** , insira e selecione **Tradutor** (recurso de serviço único) ou **Serviços cognitivas** (recurso de vários serviços).  *Consulte* [escolher o tipo de recurso](#create-your-resource)acima.
> 1. Selecione **criar** e você será levado para a página de detalhes do projeto.
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>Concluir o projeto e os detalhes da instância

1. **Assinatura**. Selecione uma das suas assinaturas do Azure disponíveis.

1. **Grupo de Recursos**. O grupo de recursos do Azure que você escolhe serve como um contêiner virtual para o novo recurso. Você pode criar um novo grupo de recursos ou adicionar seu recurso a um grupo de recursos pré-existente que compartilha o mesmo ciclo de vida, permissões e políticas.

1. **Região de recurso**. Escolha **global** , a menos que seu negócio ou aplicativo exija uma região específica. O tradutor é um serviço não regional – não há nenhuma dependência em uma região específica do Azure. *Consulte* [regiões e zonas de disponibilidade no Azure](../../availability-zones/az-overview.md).

1. **Nome**. Insira o nome que você escolheu para o recurso. O nome escolhido deve ser exclusivo no Azure.

> [!NOTE]
> Se você estiver usando um recurso de tradutor que requer um ponto de extremidade de domínio personalizado, o valor que você inserir no campo nome será o parâmetro de nome de domínio personalizado para o ponto de extremidade.

5. **Tipo de preço**. Selecione um [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/translator) que atenda às suas necessidades:

   * Cada assinatura tem uma camada gratuita.
   * A camada gratuita tem os mesmos recursos e funcionalidades que os planos pagos e não expira.
   * É permitida apenas uma assinatura gratuita por conta.</li></ul>

1. Se você tiver criado um recurso de vários serviços, precisará confirmar detalhes de uso adicionais por meio das caixas de seleção.

1. Selecione **Examinar + criar**.

1. Examine os termos de serviço e selecione **criar** para implantar o recurso.

1. Depois que o recurso tiver sido implantado com êxito, selecione **ir para o recurso**.

### <a name="authentication-keys-and-endpoint-url"></a>Chaves de autenticação e URL do ponto de extremidade

Todas as solicitações de API de serviços cognitivas exigem uma URL de ponto de extremidade e uma chave somente leitura para autenticação.

* **Chaves de autenticação**. Sua chave é uma cadeia de caracteres exclusiva que é passada em cada solicitação para o serviço de tradução. Você pode passar sua chave por meio de um parâmetro de cadeia de caracteres de consulta ou especificando-a no cabeçalho de solicitação HTTP.

* **URL do ponto de extremidade**. Use o ponto de extremidade global na sua solicitação de API, a menos que você precise de uma região específica do Azure. *Consulte* [URLs de base](reference/v3-0-reference.md#base-urls). A URL do ponto de extremidade global é `api.cognitive.microsofttranslator.com` .

## <a name="get-your-authentication-keys-and-endpoint"></a>Obter suas chaves de autenticação e ponto de extremidade

1. Depois que o novo recurso for implantado, selecione **ir para o recurso** ou navegue diretamente para a página de recursos.
1. No trilho esquerdo, em *Gerenciamento de recursos*, selecione **chaves e ponto de extremidade**.
1. Copie e cole as chaves de assinatura e a URL do ponto de extremidade em um local conveniente, como *o bloco de notas da Microsoft*.

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="Obter chave e ponto de extremidade.":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>Como excluir um recurso ou grupo de recursos

> [!Warning]
> A exclusão de um grupo de recursos também exclui todos os recursos contidos no grupo.

Para remover um recurso de serviços ou tradutor cognitiva, você pode **excluir o recurso** ou **excluir o grupo de recursos**.

Para excluir o recurso:

1. Navegue até o grupo de recursos na portal do Azure.
1. Selecione os recursos a serem excluídos marcando a caixa de seleção adjacente.
1. Selecione **excluir** no menu superior próximo à borda direita.
1. Digite *Sim* na caixa de diálogo **recursos excluídos** .
1. Selecione **Excluir**.

Para excluir o grupo de recursos:

1. Navegue até o grupo de recursos na portal do Azure.
1. Selecione o **grupo de recursos excluir** na barra de menus superior próxima à borda esquerda.
1. Confirme a solicitação de exclusão inserindo o nome do grupo de recursos e selecionando **excluir**.

## <a name="how-to-get-started-with-translator"></a>Introdução ao Tradutor

Em nosso início rápido, você aprenderá a usar o serviço Tradutor com APIs REST.

> [!div class="nextstepaction"]
> [Introdução ao Tradutor](quickstart-translator.md)

## <a name="more-resources"></a>Mais recursos

* [Exemplos de código do Microsoft Translator](https://github.com/MicrosoftTranslator).  Os exemplos de código do tradutor de vários idiomas estão disponíveis no GitHub.
* [Fórum de suporte do Microsoft Translator](https://www.aka.ms/TranslatorForum)
* [Introdução ao Azure (vídeo de 3 minutos)](https://azure.microsoft.com/get-started/?b=16.24)