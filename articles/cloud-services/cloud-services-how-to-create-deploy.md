---
title: "Como criar e implantar um serviço de nuvem | Microsoft Docs"
description: "Saiba como criar e implantar um serviço de nuvem usando o método de Criação Rápida no Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 0ea78ccc-5e7d-40f8-bdb6-478c0eb0e265
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 2a2172a78bfd3ac923edbc9de366b035629dd27b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Como criar e implantar um serviço de nuvem
> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-how-to-create-deploy-portal.md)
> * [portal clássico do Azure](cloud-services-how-to-create-deploy.md)
> 
> 

O portal clássico do Azure oferece duas maneiras de criar e de implantar um serviço de nuvem: **Criação Rápida** e **Criação Personalizada**.

Este tópico explica como usar o método Criação Rápida para criar um novo serviço de nuvem e usar **Carregar** para carregar e implantar um pacote de serviço de nuvem no Azure. Ao usar esse método, o portal clássico do Azure disponibiliza links convenientes para o cumprimento de todos os requisitos quando você precisar. Se você estiver pronto para implantar o serviço de nuvem ao criá-lo, é possível usar ambos ao mesmo tempo usando a **Criação Personalizada**.

> [!NOTE]
> Se você planeja publicar o serviço de nuvem do VSTS (Visual Studio Team Services), use a **Criação Rápida** e configure a publicação do VSTS no **Início Rápido** ou no painel.
> 
> 

## <a name="concepts"></a>Conceitos
Três componentes são necessários para implantar um aplicativo como um serviço de nuvem no Azure:

* **Definição de serviço**  
  O arquivo de definição de serviço de nuvem (.csdef) define o modelo de serviço, inclusive o número de funções.
* **Configuração de serviço**  
  O arquivo de configuração de serviço de nuvem (.cscfg) fornece as configurações para o serviço de nuvem e as funções individuais, inclusive o número de instâncias de função.
* **Pacote de serviço**  
  O pacote de serviço (.cspkg) contém o código do aplicativo, as configurações e o arquivo de definição de serviço.

Você pode saber mais sobre eles e sobre como criar um pacote [aqui](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Preparação do aplicativo
Antes de poder implantar um serviço de nuvem, você deve criar o pacote do serviço de nuvem (arquivo .cspkg) do seu código do aplicativo e um arquivo de configuração do serviço de nuvem (.cscfg). O SDK do Azure fornece as ferramentas para preparar os arquivos exigidos para a implantação. Você pode instalar o SDK da página de [downloads de Azure](https://azure.microsoft.com/downloads/) no idioma em que você preferir desenvolver seu código do aplicativo.

Três recursos de serviço de nuvem precisam de configurações especiais antes que você exporte um pacote de serviço:

* Se você desejar implantar um serviço de nuvem que usa protocolo SSL para a criptografia de dados, [configure seu aplicativo](cloud-services-configure-ssl-certificate.md#step-2-modify-the-service-definition-and-configuration-files) para SSL.
* Se você desejar configurar conexões de área de trabalho remota para as instâncias de função, [configure as funções](cloud-services-role-enable-remote-desktop.md) para Área de Trabalho Remota.
* Se você desejar configurar o monitoramento detalhado para o seu serviço de nuvem, habilite o Diagnóstico do Azure para o serviço de nuvem. *Monitoramento mínimo* (o nível de monitoramento padrão) usa contadores de desempenho coletados dos sistemas operacionais do host para as instâncias de função (máquinas virtuais). O "monitoramento detalhado"* coleta métricas adicionais baseadas nos dados de desempenho nas instâncias de função para habilitar uma análise mais próxima dos problemas que ocorrem durante o processamento do aplicativo. Para saber como habilitar o Diagnóstico do Azure, consulte [Habilitação do Diagnóstico no Azure](cloud-services-dotnet-diagnostics.md)(a página pode estar em inglês).

Para criar um serviço de nuvem com implantações de funções Web ou de Trabalho, você deve [criar o pacote de serviço](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Antes de começar
* Se você não instalou o SDK do Azure, clique em **Instalar SDK do Azure** para abrir a [página de Downloads do Azure](https://azure.microsoft.com/downloads/)e baixar o SDK para o idioma em que você preferir desenvolver seu código. (Você terá uma oportunidade de fazer isso posteriormente).
* Se alguma instância de função precisar de certificados, crie-os. Os serviços de nuvem requerem um arquivo .pfx e uma chave privada. Você pode [carregar os certificados para o Azure](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) enquanto cria e implanta o serviço de nuvem.
* Se você planeja implantar o serviço de nuvem em um grupo de afinidade, crie-o. Você pode usar um grupo de afinidade para implantar o serviço de nuvem e outros serviços do Azure no mesmo local em uma região. Você pode criar um grupo de afinidades na área **Redes** do portal clássico do Azure na página **Grupos de Afinidade**.

## <a name="how-to-create-a-cloud-service-using-quick-create"></a>Tutorial: Criar um serviço de nuvem com a Criação Rápida
1. No [Portal Clássico do Azure](http://manage.windowsazure.com/), clique em **Novo**>**Computação**>**Serviço de Nuvem**>**Criação Rápida**.
   
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)
2. Em **URL**, digite um nome do subdomínio para usar na URL pública para acessar o serviço de nuvem em implantações de produção. O formato da URL para implantações de produção é: http://*myURL*.cloudapp.net.
3. Em **Grupo de afinidade/região**, selecione a região geográfica ou o grupo de afinidade no qual você implantará o serviço de nuvem. Selecione um grupo de afinidade se desejar implantar um serviço de nuvem no mesmo local de outros serviços do Azure em uma região.
4. Clique em **Criar Serviço de Nuvem**.
   
    ![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)
   
    Você pode monitorar o status do processo na área de mensagem na parte inferior da janela.
   
    A área **Serviços de Nuvem** é aberta, com o novo serviço de nuvem sendo exibido. Quando o status mudar para Criado, a criação do serviço de nuvem terá sido concluída com êxito.
   
    ![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)

## <a name="how-to-upload-a-certificate-for-a-cloud-service"></a>Tutorial: Carregar um certificado para um serviço de nuvem
1. No [Portal Clássico do Azure](http://manage.windowsazure.com/), clique em **Serviços de Nuvem** e no nome do serviço de nuvem e depois em **Certificados**.
   
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)
2. Clique em **Carregar um certificado** ou **Carregar**.
3. Em **Arquivo**, use **Procurar** para selecionar o certificado (arquivo .pfx).
4. Em **Senha**, insira a chave privada para o certificado.
5. Clique em **OK** (marca de seleção).
   
    ![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)
   
    Você pode observar o progresso do carregamento na área de mensagem, mostrada abaixo. Quando o carregamento for concluído, o certificado será adicionado à tabela. Na área de mensagem, clique em OK para fechar a mensagem.
   
    ![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

## <a name="how-to-deploy-a-cloud-service"></a>Tutorial: Implantar um serviço de nuvem
1. No [Portal Clássico do Azure](http://manage.windowsazure.com/), clique em **Serviços de Nuvem** no nome do serviço de nuvem e depois em **Painel**.
2. Clique em **Carregar uma nova implantação de produção** ou em **Carregar**.
3. Em **Rótulo de implantação**, insira um nome para a nova implantação como, por exemplo, MyCloudServicev4.
4. Em **Pacote**, use **Procurar** para selecionar o arquivo do pacote de serviço (.cspkg) a ser usado.
5. Em **Configuração**, use **Procurar** para selecionar o arquivo de configuração do serviço (.cscfg) a ser usado.
6. Se o serviço de nuvem incluir funções com apenas uma instância, marque a caixa de seleção **Implantar mesmo se uma ou mais funções contiverem uma única instância** para continuar a implantação.
   
    O Azure só pode garantir 99,95 por cento de acesso ao serviço de nuvem durante a manutenção e atualizações do serviço se cada função tiver pelo menos duas instâncias. Se necessário, você pode adicionar instâncias de função adicionais na página **Escala** após implantar o serviço de nuvem. Para obter mais informações, consulte [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/).
7. Clique em **OK** (marca de seleção) para iniciar a implantação do serviço de nuvem.
   
    ![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)
   
    Você pode monitorar o status da implantação na área de mensagem. Clique em OK para ocultar a mensagem.
   
    ![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

## <a name="verify-your-deployment-completed-successfully"></a>Verifique se a implantação foi concluída com êxito
1. Clique em **Painel**.
   
    O status deve mostrar que o serviço está **Executando**.
2. Em **visão rápida**, clique na URL do site para abrir o serviço de nuvem em um navegador.
   
    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


## <a name="next-steps"></a>Próximas etapas
* [Configuração geral do serviço de nuvem](cloud-services-how-to-configure.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name.md).
* [Gerenciar seu serviço de nuvem](cloud-services-how-to-manage.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate.md).

