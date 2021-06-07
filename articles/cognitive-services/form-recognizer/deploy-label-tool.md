---
title: Como implantar a ferramenta de rotulagem de exemplos do Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Aprenda as diferentes maneiras pelas quais você pode implantar a ferramenta de rotulagem de exemplos do Reconhecimento de Formulários para ajudar com o aprendizado supervisionado.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 02/11/2021
ms.author: lajanuar
ms.openlocfilehash: 0f5f0714235ee23624b3a199eac744155d2bbdd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101093394"
---
# <a name="deploy-the-sample-labeling-tool"></a>Implantar a ferramenta de rotulagem de exemplos

A ferramenta de rotulagem de exemplos do Reconhecimento de Formulários é um aplicativo que fornece uma UI (interface do usuário) simples, que pode ser usada para rotular manualmente os formulários (documentos) para fins de aprendizado supervisionado. Neste artigo, forneceremos links e instruções que ensinam como:

* [Executar a ferramenta de rotulagem de exemplos localmente](#run-the-sample-labeling-tool-locally)
* [Implantar a ferramenta de rotulamento de exemplos em uma ACI (Instância de Contêiner do Azure)](#deploy-with-azure-container-instances-aci)
* [Usar e contribuir com a ferramenta de rotulagem de formulários de OCR de software livre](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Executar a ferramenta de rotulagem de exemplos localmente

A maneira mais rápida de começar a rotulagem de dados é executar a ferramenta de rotulagem de exemplos localmente. O início rápido a seguir usa a API REST do Reconhecimento de Formulários e a ferramenta de rotulagem de exemplos para treinar um modelo personalizado usando dados rotulados manualmente. 

* [Início Rápido: rotular formulários, treinar um modelo e analisar um formulário usando a ferramenta de rotulagem de exemplos](./quickstarts/label-tool.md).

## <a name="deploy-with-azure-container-instances-aci"></a>Implantar com as ACIs (Instâncias de Contêiner do Azure)

Antes de começar, é importante observar que há duas maneiras de implantar a ferramenta de rotulagem de exemplos em uma ACI (Instância de Contêiner do Azure). Ambas as opções são usadas para executar a ferramenta de rotulagem de exemplos em uma ACI:

* [Usando o portal do Azure](#azure-portal)
* [Usando a CLI do Azure](#azure-cli)

### <a name="azure-portal"></a>Portal do Azure

Siga estas etapas para criar um recurso usando o portal do Azure: 

1. Entre no [portal do Azure](https://portal.azure.com/signin/index/).
2. Selecione **Criar um recurso**.
3. Em seguida, selecione **aplicativo Web**.

   > [!div class="mx-imgBorder"]
   > ![Selecione aplicativo Web](./media/quickstarts/create-web-app.png)

4. Primeiro, verifique se a guia **Básico** está selecionada. Agora, você precisará fornecer algumas informações:

   > [!div class="mx-imgBorder"]
   > ![Selecione Básico](./media/quickstarts/select-basics.png)
   * Assinatura – Selecione uma assinatura do Azure existente
   * Grupo de recursos – Você pode reutilizar um grupo de recursos existente ou criar um para este projeto. É recomendável criar um grupo de recursos.
   * Nome – Dê um nome ao seu aplicativo Web. 
   * Publicar – Selecionar o **Contêiner do Docker**
   * Sistema Operacional – Selecione **Linux**
   * Região – Escolha uma região que faça sentido para você.
   * Plano do Linux – Selecione um tipo de preço/plano para o seu serviço de aplicativo. 

   > [!div class="mx-imgBorder"]
   > ![Configurar o aplicativo Web](./media/quickstarts/select-docker.png)

5. Em seguida, selecione a guia **Docker**.

   > [!div class="mx-imgBorder"]
   > ![Selecione Docker](./media/quickstarts/select-docker.png)

6. Agora, vamos configurar o contêiner do Docker. Todos os campos são obrigatórios, a menos que indicado de outra forma:
<!-- markdownlint-disable MD025 -->
# <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

* Opções – Selecione **Contêiner Único**
* Origem da imagem – Selecionar **Registro Privado** 
* URL do servidor – Defina como `https://mcr.microsoft.com`
* Nome de usuário (opcional) – Crie um nome de usuário. 
* Senha (opcional) – Crie uma senha segura da qual você não se esquecerá.
* Imagem e marca – Defina como `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview`
* Implantação contínua – Defina como **Ativado** caso deseje receber atualizações automáticas quando a equipe de desenvolvimento fizer alterações na ferramenta de rotulagem de exemplos.
* Comando de inicialização – Defina como `./run.sh eula=accept`

# <a name="v20"></a>[v2.0](#tab/v2-0)  

* Opções – Selecione **Contêiner Único**
* Origem da imagem – Selecionar **Registro Privado** 
* URL do servidor – Defina como `https://mcr.microsoft.com`
* Nome de usuário (opcional) – Crie um nome de usuário. 
* Senha (opcional) – Crie uma senha segura da qual você não se esquecerá.
* Imagem e marca – Defina como `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
* Implantação contínua – Defina como **Ativado** caso deseje receber atualizações automáticas quando a equipe de desenvolvimento fizer alterações na ferramenta de rotulagem de exemplos.
* Comando de inicialização – Defina como `./run.sh eula=accept`

 ---

   > [!div class="mx-imgBorder"]
   > ![Configurar o Docker](./media/quickstarts/configure-docker.png)

7. É isso. Em seguida, selecione **Examinar + Criar**, depois **Criar** para implantar seu aplicativo Web. Ao concluir, você poderá acessar o aplicativo Web na URL fornecida, na **Visão Geral** do seu recurso.

> [!NOTE]
> Ao criar seu aplicativo Web, você também pode configurar a autorização/autenticação. Isso não é necessário para começar.

> [!IMPORTANT]
> Talvez seja necessário habilitar o TLS para o seu aplicativo Web a fim de ver o aplicativo no endereço `https` dele. Siga as instruções em [Habilitar um ponto de extremidade do TLS](../../container-instances/container-instances-container-group-ssl.md) para configurar um contêiner sidecar que habilite o TLS/SSL para o seu aplicativo Web.
<!-- markdownlint-disable MD001 -->
### <a name="azure-cli"></a>CLI do Azure

Como alternativa ao uso do portal do Azure, você pode criar um recurso usando a CLI do Azure. Antes de continuar, você precisará instalar a [CLI do Azure](/cli/azure/install-azure-cli). Você pode ignorar esta etapa se já estiver trabalhando com a CLI do Azure. 

Há algumas coisas que você precisa saber a respeito deste comando:

* `DNS_NAME_LABEL=aci-demo-$RANDOM` gera um nome DNS aleatório. 
* Este exemplo pressupõe que você tenha um grupo de recursos que pode ser usado para criar um recurso. Substitua `<resource_group_name>` por um grupo de recursos válido associado à sua assinatura. 
* Você precisará especificar onde deseja criar o recurso. Substitua `<region name>` pela região desejada para o aplicativo Web.
* Esse comando aceita automaticamente o EULA.

Na CLI do Azure, execute este comando para criar um recurso de aplicativo Web para a ferramenta de rotulagem de exemplos:

<!-- markdownlint-disable MD024 -->
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


---

### <a name="connect-to-azure-ad-for-authorization"></a>Conectar-se ao Azure AD para obter autorização

É recomendável que você conecte seu aplicativo Web ao Azure Active Directory. Isso faz com que somente usuários com credenciais válidas possam entrar e usar seu aplicativo Web. Siga as instruções em [Configurar seu aplicativo do Serviço de Aplicativo](../../app-service/configure-authentication-provider-aad.md) para se conectar ao Azure Active Directory.

## <a name="open-source-on-github"></a>Software livre no GitHub

A Ferramenta de Rotulagem de Formulário OCR também está disponível como um projeto de software livre no GitHub. A ferramenta é um aplicativo Web criado com o React + Redux e escrito em TypeScript. Para saber mais ou contribuir, confira [Ferramenta de Rotulagem de Formulário OCR](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Próximas etapas

Use o início rápido [Treinar com rótulos](./quickstarts/label-tool.md) para aprender a usar a ferramenta a fim de rotular manualmente os dados de treinamento e executar o aprendizado supervisionado.