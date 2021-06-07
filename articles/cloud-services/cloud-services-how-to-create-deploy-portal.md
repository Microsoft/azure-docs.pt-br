---
title: Como criar e implantar um serviço de nuvem (clássico) | Microsoft Docs
description: Saiba como usar o método criação rápida para criar um serviço de nuvem e usar carregar para carregar e implantar um pacote de serviço de nuvem no Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 879b86714adf50b5a4da4398389405063ac046dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743399"
---
# <a name="how-to-create-and-deploy-an-azure-cloud-service-classic"></a>Como criar e implantar um serviço de nuvem do Azure (clássico)

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

O portal do Azure oferece duas maneiras de criar e implantar um serviço de nuvem: *Criação Rápida* e *Criação Personalizada*.

Este artigo explica como usar o método Criação Rápida para criar um novo serviço de nuvem e usar **Carregar** para carregar e implantar um pacote de serviço de nuvem no Azure. Ao usar esse método, o Portal do Azure disponibiliza links convenientes para o cumprimento de todos os requisitos quando você precisar. Se você estiver pronto para implantar o serviço de nuvem ao criá-lo, é possível usar ambos ao mesmo tempo usando a Criação Personalizada.

> [!NOTE]
> Se você planeja publicar o serviço de nuvem do Azure DevOps, use a Criação Rápida e configure a publicação do Azure DevOps no Início Rápido do Azure ou no painel. Para saber mais, confira [Entrega contínua no Azure usando Azure DevOps][TFSTutorialForCloudService] ou confira a ajuda da página **Início Rápido**.
>
>

## <a name="concepts"></a>Conceitos
Três componentes são necessários para implantar um aplicativo como um serviço de nuvem no Azure:

* **Definição de serviço**  
   O arquivo de definição de serviço de nuvem (.csdef) define o modelo de serviço, inclusive o número de funções.
* **Configuração do serviço**  
   O arquivo de configuração de serviço de nuvem (.cscfg) fornece as configurações para o serviço de nuvem e as funções individuais, inclusive o número de instâncias de função.
* **Pacote de serviço**  
   O pacote de serviço (.cspkg) contém o código do aplicativo, as configurações e o arquivo de definição de serviço.

Você pode saber mais sobre eles e sobre como criar um pacote [aqui](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Preparação do aplicativo
Antes de poder implantar um serviço de nuvem, você deve criar o pacote do serviço de nuvem (arquivo .cspkg) do seu código do aplicativo e um arquivo de configuração do serviço de nuvem (.cscfg). O SDK do Azure fornece as ferramentas para preparar os arquivos exigidos para a implantação. Você pode instalar o SDK da página de [downloads de Azure](https://azure.microsoft.com/downloads/) no idioma em que você preferir desenvolver seu código do aplicativo.

Três recursos de serviço de nuvem precisam de configurações especiais antes que você exporte um pacote de serviço:

* Se você quiser implantar um serviço de nuvem que usa o protocolo TLS, anteriormente conhecido como protocolo SSL (SSL), para criptografia de dados, [configure seu aplicativo](cloud-services-configure-ssl-certificate-portal.md#modify) para TLS.
* Se você desejar configurar conexões de área de trabalho remota para as instâncias de função, [configure as funções](cloud-services-role-enable-remote-desktop-new-portal.md) para Área de Trabalho Remota.
* Se você desejar configurar o monitoramento detalhado para o seu serviço de nuvem, habilite o Diagnóstico do Azure para o serviço de nuvem. *Monitoramento mínimo* (o nível de monitoramento padrão) usa contadores de desempenho coletados dos sistemas operacionais do host para as instâncias de função (máquinas virtuais). O *monitoramento detalhado* reúne métricas adicionais com base nos dados de desempenho nas instâncias de função para permitir uma análise mais próxima dos problemas que ocorrem durante o processamento do aplicativo. Para saber como habilitar Diagnóstico do Azure, consulte [habilitando o diagnóstico no Azure](cloud-services-dotnet-diagnostics.md).

Para criar um serviço de nuvem com implantações de funções Web ou de Trabalho, você deve [criar o pacote de serviço](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Antes de começar
* Se você não instalou o SDK do Azure, clique em **Instalar SDK do Azure** para abrir a [página de Downloads do Azure](https://azure.microsoft.com/downloads/)e baixar o SDK para o idioma em que você preferir desenvolver seu código. (Você terá uma oportunidade de fazer isso posteriormente).
* Se alguma instância de função precisar de certificados, crie-os. Os serviços de nuvem requerem um arquivo .pfx e uma chave privada. Você pode carregar os certificados para o Azure enquanto cria e implanta o serviço de nuvem.

## <a name="create-and-deploy"></a>Criar e implantar
1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Criar um recurso > Computação**, e, em seguida, role para baixo e clique em **Serviço de Nuvem**.

    ![Publicar seu Service1 de nuvem](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. No novo painel **Serviço de Nuvem**, insira um valor para o **Nome DNS**.
4. Crie um novo **grupo de recursos** ou selecione um existente.
5. Selecione um **Local**.
6. Clique em **Empacotar**. Isso abre o painel **Carregar um pacote**. Preencha os campos obrigatórios. Se alguma das funções contiver uma única instância, verifique se **Implantar mesmo se uma ou mais funções contiverem uma única instância** está marcado.
7. Verifique se a opção **Iniciar implantação** está selecionada.
8. Clique em **OK** que fechará o painel **Carregar um pacote**.
9. Se você não tiver nenhum certificado para adicionar, clique em **Criar**.

    ![Publicar sua nuvem Service2](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Carregar um certificado
Se o pacote de implantação tiver sido [configurado para usar certificados](cloud-services-configure-ssl-certificate-portal.md#modify), você poderá carregar o certificado agora.

1. Selecione **certificados** e, no painel **adicionar certificados** , selecione o arquivo. pfx do certificado TLS/SSL e forneça a **senha** para o certificado,
2. Clique em **Anexar certificado** e, em seguida, clique em **OK** no painel **Adicionar certificados**.
3. Clique em **Criar** no painel **Serviço de Nuvem**. Quando a implantação alcançar o status **Pronto** , será possível passar às próximas etapas.

    ![Publicar sua nuvem Service3](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Verifique se a implantação foi concluída com êxito
1. Clique na instância do serviço de nuvem.

    O status deve mostrar que o serviço está **Executando**.
2. Em **Essentials**, clique na **URL do Site** para abrir o serviço de nuvem em um navegador da Web.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: ./cloud-services-choose-me.md

## <a name="next-steps"></a>Próximas etapas
* [Configuração geral do serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerenciar seu serviço de nuvem](cloud-services-how-to-manage-portal.md).
* Configurar [certificados TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).