---
title: "Início Rápido – Criar o primeiro contêiner de Instâncias de Contêiner do Azure com o Portal do Azure"
description: "Implantar e começar a trabalhar com as Instâncias de Contêiner do Azure"
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 0179107ece1e150246ab40836783d810425be3ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Criar o primeiro contêiner nas Instâncias de Contêiner do Azure

As Instâncias de Contêiner do Azure facilitam a criação e o gerenciamento de contêineres no Azure. Neste início rápido, você cria um contêiner no Azure e o expõe à Internet com um endereço IP público. Esta operação é concluída usando o Portal do Azure. Com apenas alguns cliques, você verá o seguinte no seu navegador:

![Os aplicativos implantados usando Instâncias de Contêiner do Azure são exibidos no navegador][aci-app-browser]

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="create-a-container-instance"></a>Criar uma instância de contêiner

Selecione **Novo** > **Contêineres** > **Instâncias de Contêiner do Azure (versão prévia)**.

![Começar a criar uma nova instância de contêiner no Portal do Azure][aci-portal-01]

Insira os seguintes valores nas caixas de texto **Nome do contêiner**, **Imagem de contêiner** e **Grupo de recursos**. Deixe os outros valores com seus padrões e clique em **OK**.

* Nome do contêiner: `mycontainer`
* Imagem de contêiner: `microsoft/aci-helloworld`
* Grupo de recursos: `myResourceGroup`

![Configurando as definições básicas para uma nova instância de contêiner no Portal do Azure][aci-portal-03]

Você pode criar contêineres Windows e Linux nas Instâncias de Contêiner do Azure. Neste guia de início rápido, deixaremos a configuração padrão do **Linux**, pois especificamos um contêiner com base em Linux (`microsoft/aci-helloworld`) na etapa anterior.

Deixe as outras configurações em **Configuração** com seus padrões e clique em **OK** para validar a configuração.

![Configurando uma nova instância de contêiner no Portal do Azure][aci-portal-04]

Quando a validação é concluída, um resumo das configurações de seu contêiner será exibido. Selecione **OK** para enviar sua solicitação de implantação de contêiner.

![Resumo das configurações para uma nova instância de contêiner no Portal do Azure][aci-portal-05]

Quando a implantação é iniciada, um bloco é colocado no painel do portal indicando o progresso da implantação. Após a conclusão da implantação, o bloco é atualizado para mostrar o novo grupo de contêineres **mycontainer-myc1**.

![Progresso da criação de uma nova instância de contêiner no Portal do Azure][aci-portal-08]

Selecione o grupo de contêineres **mycontainer-myc1** para exibir as propriedades do grupo de contêineres. Anote o **Endereço IP** do grupo de contêineres, bem como o **ESTADO** do seu contêiner.

![Visão geral do grupo de contêineres no Portal do Azure][aci-portal-06]

Depois que o contêiner é movido para o estado **Em Execução**, navegue até o endereço IP que você anotou na etapa anterior para exibir o aplicativo hospedado no seu novo contêiner.

![Os aplicativos implantados usando Instâncias de Contêiner do Azure são exibidos no navegador][aci-app-browser]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-app-browser]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma Instância de Contêiner do Azure com base em uma imagem em um repositório do Hub do Docker público. Se você quiser tentar criar um contêiner sozinho e implantá-lo nas Instâncias de Contêiner do Azure usando o Registro de Contêiner do Azure, prossiga para o tutorial sobre Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Tutoriais sobre Instâncias de Contêiner do Azure](./container-instances-tutorial-prepare-app.md)