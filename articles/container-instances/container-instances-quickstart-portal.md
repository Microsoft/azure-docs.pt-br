---
title: Quickstart - Crie seu primeiro container no Azure Container Instances com o portal do Azure
description: Publique e comece com Instâncias de Contêiner do Azure
services: container-instances
documentationcenter: ''
author: mmacy
manager: timlt
editor: ''
tags:
keywords: ''

ms.assetid:
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: marsma
ms.custom: mvc
---

# Crie seu primeiro contêiner no Azure Container Instances

Azure Container Instances do Azure facilita a criação e gerenciamento de contêineres no Azure. Neste início rápido, você irá criar um contêiner no Azure e o deixará exposto à internet com um endereço de IP público. Esta operação será completada com o uso do portal do Azure. Com apenas alguns cliques, você verá em seu navegador:

![App deployed using Azure Container Instances viewed in browser][aci-app-browser]

## Autentique-se no Azure

Autentique-se no portal do Azure via http://portal.azure.com.

## Crie uma instância de contêiner

Selecione **New** > **Contêineres** > **Azure Container Instances (preview)**.

![Begin creating a new container instance in the Azure portal][aci-portal-01]

Entre os seguintes valores nos text boxes **Nome do Contêiner**, **Nome da Imagem** **Grupo de Recursos**. Deixe os outros valores com o valor padrão, então clique **OK**.

* Nome do Contêiner: `mycontainer`
* Nome da Imagem: `microsoft/aci-helloworld`
* Grupo de Recursos: `myResourceGroup`

![Configuring basic settings for a new container instance in the Azure portal][aci-portal-03]

Você pode criar tanto contêineres em Linux quanto em Windows usando Azure Container Instances. Neste início rápido, iremos deixar com a configuração padrão **Linux** já que especificamos um contêiner baseado em Linux (`microsoft/aci-helloworld`) no passo anterior.

Deixe as outras configurações em **Configurações** com o valor padrão, e então clique em **OK** para validar as configurações.

![Configuring a new container instance in the Azure portal][aci-portal-04]

Quando a validação terminar, você verá um resumo das configurações do seu contêiner. Selecione **OK** para enviar sua requisição de publicação do contêiner.

![Settings summary for a new container instance in the Azure portal][aci-portal-05]

Quando a publicação iniciar, uma janela irá aparecer no painel do seu portal, indicando o progresso da publicação. Uma vez terminada, a janela é atualizada para mostrar seu novo **mycontainer-myc1** grupo de recursos.

![Creation progress of a new container instance in the Azure portal][aci-portal-08]

Selecione o grupo de contêineres **mycontainer-myc1** para exibir as suas propriedades. Tome nota do **Endereço IP** do grupo de contêiner, assim como o **ESTADO** do seu contêiner.

![Container group overview in the Azure portal][aci-portal-06]

Uma vez que o contêiner vai para o estado **Executando**, navegue para o endereço IP que você anotou no passo anterior para exibir a aplicação hospedada no seu contêiner.

![App deployed using Azure Container Instances viewed in browser][aci-app-browser]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-app-browser]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png

## Next steps

Neste início rápido, você criou um Azure Container Instance à partir de uma imagem em um repositório público do Docker Hub. Se você pretende criar um contêiner e implementá-lo no Azure Conteiner Instance usando o Azure Container Registre, continue com o tutorial Azure Container Instances.

> [!div class="nextstepaction"]
> [Azure Container Instances tutorials](./container-instances-tutorial-prepare-app.md)
