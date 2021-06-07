---
title: Início Rápido – Implantar um contêiner do Docker na instância de contêiner – Portal
description: Neste início rápido, você usa o portal do Azure para implantar rapidamente um aplicativo Web em contêineres que é executado em uma instância de contêiner do Azure isolada
ms.date: 08/24/2020
ms.topic: quickstart
ms.custom:
- mvc
- devx-track-js
- mode-portal
ms.openlocfilehash: c0189bbd04e454205b34a6415ab6109f95b6f51a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536235"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Início Rápido: Implantar uma instância de contêiner no Azure usando o portal do Azure

Use as Instâncias de Contêiner do Azure para executar contêineres do Docker sem servidor no Azure de maneira simples e rápida. Implante um aplicativo em uma instância de contêiner sob demanda quando você não precisa de uma plataforma de orquestração de contêiner completa como o Serviço de Kubernetes do Azure.

Neste início rápido, você usará o portal do Azure para implantar um contêiner do Docker isolado e disponibilizar o respectivo aplicativo com um FQDN (nome de domínio totalmente qualificado). Após definir algumas configurações e implantar o contêiner, você poderá navegar até o aplicativo em execução:

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Aplicativos implantados por meio das Instâncias de Contêiner do Azure exibidos no navegador":::

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][azure-free-account] antes de começar.

## <a name="create-a-container-instance"></a>Criar uma instância de contêiner

Selecione **Criar um recurso** > **Contêineres** > **Instâncias de Contêiner**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-01.png" alt-text="Começar a criar uma nova instância de contêiner no Portal do Azure":::

Na página **Noções básicas**, insira os seguintes valores nas caixas de texto **Grupo de recursos**, **Nome do contêiner** e **Imagem de contêiner**. Deixe os outros valores com seus padrões e selecione **OK**.

* Grupo de recursos: **Criar novo** > `myresourcegroup`
* Nome do contêiner: `mycontainer`
* Origem da imagem: **Imagens de início rápido**
* Imagem de contêiner: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-03.png" alt-text="Configurando as definições básicas para uma nova instância de contêiner no Portal do Azure":::

Para este início rápido, você usará configurações padrão para implantar a imagem `aci-helloworld` da Microsoft pública. Esta imagem de exemplo do Linux empacota um pequeno aplicativo Web escrito no Node.js que veicula a uma página HTML estática. Você também pode colocar suas próprias imagens de contêiner armazenadas no Registro de Contêiner do Azure, no Docker Hub ou em outros Registros.

Na página **Rede**, especifique um **Rótulo de nome DNS** para seu contêiner. O nome precisa ser exclusivo dentro da região do Azure em que você cria a instância de contêiner. Seu contêiner estará publicamente acessível em `<dns-name-label>.<region>.azurecontainer.io`. Se você receber uma mensagem de erro “Rótulo de nome DNS não disponível”, tente usar um rótulo de nome DNS diferente.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-04.png" alt-text="Configurar as definições de rede para uma nova instância de contêiner no portal do Azure":::

Deixe as outras configurações com seus padrões e selecione **Examinar + criar**.

Quando a validação for concluída, um resumo das configurações de seu contêiner será exibido. Selecione **Criar** para enviar sua solicitação de implantação de contêiner.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-05.png" alt-text="Resumo das configurações para uma nova instância de contêiner no Portal do Azure":::

Quando a implantação é iniciada, aparece uma notificação para indicar que a implantação está em andamento. Outra notificação será exibida quando o grupo de contêiner tiver sido implantado.

Abra a visão geral do grupo de contêiner navegando até **Grupos de Recursos** > **myresourcegroup** > **mycontainer**. Anote o **FQDN** (nome de domínio totalmente qualificado) da instância do contêiner, bem como seu **Status**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-06.png" alt-text="Visão geral do grupo de contêineres no Portal do Azure":::

Uma vez seu **Status** é *Executando*, navegue para o FQDN do contêiner em seu navegador.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Aplicativos implantados por meio das Instâncias de Contêiner do Azure exibidos no navegador":::

Parabéns! Ao configuras apenas algumas configurações, você implantou um aplicativo publicamente acessível em Instâncias de Contêiner do Azure.

## <a name="view-container-logs"></a>Exibir logs do contêiner

Exibir os logs para uma instância de contêiner é útil ao solucionar problemas com o contêiner ou o aplicativo que é executado.

Para exibir os logs do contêiner, em **Configurações**, selecione **Contêineres** e, em seguida, **Logs**. Você verá a solicitação HTTP GET gerada quando você exibiu o aplicativo em seu navegador.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-11.png" alt-text="Logs de contêiner no portal do Azure":::


## <a name="clean-up-resources"></a>Limpar os recursos

Quando tiver terminado com o contêiner, selecione **Visão geral** para a instância de contêiner *mycontainer*, em seguida, selecione **Excluir**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-09.png" alt-text="Excluir a instância de contêiner no portal do Azure]":::

Escolha **Sim** quando a caixa de diálogo de confirmação aparecer.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-10.png" alt-text="Confirmação de exclusão de uma instância de contêiner no portal do Azure]":::

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou uma instância de contêiner do Azure com base em uma imagem da Microsoft pública. Se você quiser criar uma imagem de contêiner e implantá-la usando um Registro de Contêiner do Azure privado, prossiga para o tutorial das Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Tutorial sobre Instâncias de Contêiner do Azure](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/
