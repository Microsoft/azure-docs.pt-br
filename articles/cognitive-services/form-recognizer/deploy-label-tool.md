---
title: Como implantar a ferramenta de rótulo de exemplo do reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Aprenda as diferentes maneiras de implantar a ferramenta de rotulação de amostra do reconhecedor de formulário para ajudar com o aprendizado supervisionado.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 084ca039e7f388a11e15b29c579606c6ed3086db
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790420"
---
# <a name="deploy-the-sample-labeling-tool"></a>Implantar a ferramenta de rotulagem de exemplos

A ferramenta de rotulação de amostra do reconhecedor de formulário é um aplicativo que fornece uma interface do usuário simples (IU), que pode ser usada para rotular manualmente os formulários (documentos) com a finalidade de aprendizado supervisionado. Neste artigo, forneceremos links e instruções que ensinam como:

* [Executar a ferramenta de rotulação de exemplo localmente](#run-the-sample-labeling-tool-locally)
* [Implantar a ferramenta de rotulamento de exemplo em uma instância de contêiner do Azure (ACI)](#deploy-with-azure-container-instances-aci)
* [Usar e contribuir com a ferramenta de rotulagem de formulários de OCR de código aberto](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Executar a ferramenta de rotulação de exemplo localmente

A maneira mais rápida de começar a rotular dados é executar a ferramenta de rótulo de exemplo localmente. O início rápido a seguir usa a API REST do reconhecedor de formulário e a ferramenta de rótulo de exemplo para treinar um modelo personalizado com dados rotulados manualmente. 

* [Início rápido: rotular formulários, treinar um modelo e analisar um formulário usando a ferramenta de rótulo de exemplo](./quickstarts/label-tool.md).

## <a name="deploy-with-azure-container-instances-aci"></a>Implantar com ACI (instâncias de contêiner do Azure)

Antes de começar, é importante observar que há duas maneiras de implantar a ferramenta de rotulamento de exemplo em uma instância de contêiner do Azure (ACI). Ambas as opções são usadas para executar a ferramenta de rotulagem de exemplo com ACI: 

* [Usando o portal do Azure](#azure-portal)
* [Usando a CLI do Azure](#azure-cli)

### <a name="azure-portal"></a>Portal do Azure

Siga estas etapas para criar um novo recurso usando o portal do Azure: 

1. Entre no [portal do Azure](https://portal.azure.com/signin/index/).
2. Selecione **Criar um recurso**. 
3. Em seguida, selecione **aplicativo Web**. 

   > [!div class="mx-imgBorder"]
   > ![Selecionar aplicativo Web](./media/quickstarts/formre-create-web-app.png)
   
4. Primeiro, certifique-se de que a guia **noções básicas** esteja selecionada. Agora, você precisará fornecer algumas informações: 

   > [!div class="mx-imgBorder"]
   > ![Selecionar noções básicas](./media/quickstarts/formre-select-basics.png)
   * Assinatura – selecione uma assinatura do Azure existente
   * Grupo de recursos – você pode reutilizar um grupo de recursos existente ou criar um novo para este projeto. É recomendável criar um novo grupo de recursos.
   * Nome – dê um nome ao seu aplicativo Web. 
   * Publicar – selecionar **contêiner do Docker**
   * Sistema operacional-selecione **Linux**
   * Região – escolha uma região que faça sentido para você.
   * Plano do Linux – selecione um tipo de preço/plano para seu serviço de aplicativo. 

   > [!div class="mx-imgBorder"]
   > ![Configurar seu aplicativo Web](./media/quickstarts/formre-select-docker-linux.png)

5. Em seguida, selecione a guia **Docker** . 

   > [!div class="mx-imgBorder"]
   > ![Selecionar Docker](./media/quickstarts/formre-select-docker.png)

6. Agora, vamos configurar o contêiner do Docker. Todos os campos são necessários, a menos que indicado de outra forma:

    # <a name="v20"></a>[v2.0](#tab/v2-0)  
   * Opções-selecione **um único contêiner**
   * Origem da imagem – selecionar **registro privado** 
   * URL do servidor-defina como `https://mcr.microsoft.com`
   * Nome de usuário (opcional)-criar um nome de usuário. 
   * Senha (opcional) – crie uma senha segura que você vai lembrar.
   * Imagem e marca-defina isso como `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * Implantação contínua – defina como **ativado** se você quiser receber atualizações automáticas quando a equipe de desenvolvimento fizer alterações na ferramenta de rotulagem de exemplo.
   * Comando de inicialização – defina como `./run.sh eula=accept`

    # <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1) 
   * Opções-selecione **um único contêiner**
   * Origem da imagem – selecionar **registro privado** 
   * URL do servidor-defina como `https://mcr.microsoft.com`
   * Nome de usuário (opcional)-criar um nome de usuário. 
   * Senha (opcional) – crie uma senha segura que você vai lembrar.
   * Imagem e marca-defina isso como `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview`
   * Implantação contínua – defina como **ativado** se você quiser receber atualizações automáticas quando a equipe de desenvolvimento fizer alterações na ferramenta de rotulagem de exemplo.
   * Comando de inicialização – defina como `./run.sh eula=accept`
    
    ---

   > [!div class="mx-imgBorder"]
   > ![Configurar o Docker](./media/quickstarts/formre-configure-docker.png)

7. É isso. Em seguida, selecione **revisar + criar** e **criar** para implantar seu aplicativo Web. Ao concluir, você pode acessar seu aplicativo Web na URL fornecida na **visão geral** do seu recurso.

> [!NOTE]
> Ao criar seu aplicativo Web, você também pode configurar a autorização/autenticação. Isso não é necessário para começar. 

> [!IMPORTANT]
> Talvez seja necessário habilitar o TLS para seu aplicativo Web a fim de exibi-lo em seu `https` endereço. Siga as instruções em [habilitar um ponto de extremidade TLS](../../container-instances/container-instances-container-group-ssl.md) para configurar um contêiner sidecar do que permite o TLS/SSL para seu aplicativo Web.

### <a name="azure-cli"></a>CLI do Azure

Como alternativa ao uso do portal do Azure, você pode criar um recurso usando o CLI do Azure. Antes de continuar, você precisará instalar o [CLI do Azure](/cli/azure/install-azure-cli). Você pode ignorar esta etapa se já estiver trabalhando com o CLI do Azure. 

Há algumas coisas que você precisa saber sobre este comando:

* `DNS_NAME_LABEL=aci-demo-$RANDOM` gera um nome DNS aleatório. 
* Este exemplo pressupõe que você tenha um grupo de recursos que você pode usar para criar um recurso. Substituir `<resource_group_name>` por um grupo de recursos válido associado à sua assinatura. 
* Você precisará especificar onde deseja criar o recurso. Substitua `<region name>` pela região desejada para o aplicativo Web. 
* Esse comando aceita automaticamente o EULA.

No CLI do Azure, execute este comando para criar um recurso de aplicativo Web para a ferramenta de rotulagem de exemplo: 


# <a name="v20"></a>[v2.0](#tab/v2-0)   
```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
``` 
# <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)    
```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
```

---

### <a name="connect-to-azure-ad-for-authorization"></a>Conectar-se ao Azure AD para autorização

É recomendável que você conecte seu aplicativo Web ao Azure Active Directory. Isso garante que somente os usuários com credenciais válidas possam entrar e usar seu aplicativo Web. Siga as instruções em [configurar seu aplicativo do serviço de aplicativo](../../app-service/configure-authentication-provider-aad.md) para se conectar ao Azure Active Directory.

## <a name="open-source-on-github"></a>Código-fonte aberto no GitHub

A ferramenta de rotulagem do formulário OCR também está disponível como um projeto de código-fonte aberto no GitHub. A ferramenta é um aplicativo Web criado com o React + Redux e escrito em TypeScript. Para saber mais ou contribuir, confira [Ferramenta de Rotulagem de Formulário OCR](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Próximas etapas

Use o guia de início rápido [treinar com rótulos](./quickstarts/label-tool.md) para saber como usar a ferramenta para rotular manualmente os dados de treinamento e executar o aprendizado supervisionado.