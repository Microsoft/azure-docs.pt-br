---
title: Migrar para uma chave de criação de recursos do Azure
titleSuffix: Azure Cognitive Services
description: Este artigo descreve como migrar a autenticação de criação de Reconhecimento vocal (LUIS) de uma conta de email para um recurso do Azure.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18, contperf-fy21q2
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 3ff48ff5a3f46d8ec0fbf81b4cd20d20c217344b
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787630"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrar para uma chave de criação de recursos do Azure

> [!IMPORTANT]
>  A partir de 3 de dezembro, os usuários do LUIS existentes devem concluir o processo de migração para continuar a criar aplicativos LUIS.

A autenticação de criação de Reconhecimento vocal (LUIS) foi alterada de uma conta de email para um recurso do Azure. Use este artigo para saber como migrar sua conta, se você ainda não migrou.  


## <a name="what-is-migration"></a>O que é migração?

A migração é o processo de alterar a criação de autenticação de uma conta de email para um recurso do Azure. Sua conta será vinculada a uma assinatura do Azure e a um recurso de criação do Azure depois que você migrar.

A migração deve ser feita no [portal do Luis](https://www.luis.ai). Se você criar as chaves de criação usando a CLI do LUIS, por exemplo, você precisará concluir o processo de migração no portal do LUIS. Você ainda pode ter coautores em seus aplicativos após a migração, mas eles serão adicionados no nível de recurso do Azure em vez de no nível do aplicativo. A migração da sua conta não pode ser revertida.

> [!Note]
> * Se você precisar criar um recurso de tempo de execução de previsão, há [um processo separado](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) para criá-lo.
> * Consulte a seção [notas de migração](#migration-notes) abaixo para obter informações sobre como seus aplicativos e colaboradores serão afetados. 
> * A criação de seu aplicativo LUIS é gratuita, conforme indicado pela camada F0. Saiba [mais sobre os tipos de preço](luis-limits.md#key-limits).

## <a name="migration-prerequisites"></a>Pré-requisitos de migração

* Uma assinatura válida do Azure. Peça ao administrador de locatários para adicioná-lo à assinatura ou [Inscreva-se em um gratuito](https://azure.microsoft.com/free/cognitive-services).
* Um recurso de criação do Azure LUIS do portal do LUIS ou do [portal do Azure](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne). 
    * A criação de um recurso de criação do portal do LUIS faz parte do processo de migração descrito na próxima seção.
* Se você for um colaborador em aplicativos, os aplicativos não serão migrados automaticamente. Você será solicitado a exportar esses aplicativos enquanto passa pelo fluxo de migração. Você também pode usar a [API de exportação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). Você pode importar o aplicativo de volta para o LUIS após a migração. O processo de importação cria um novo aplicativo com uma nova ID do aplicativo, para o qual você é o proprietário.        
* Se você for o proprietário do aplicativo, não precisará exportar seus aplicativos porque eles serão migrados automaticamente. Um modelo de email com uma lista de todos os colaboradores para cada aplicativo é fornecido, para que eles possam ser notificados sobre o processo de migração.

## <a name="migration-steps"></a>Etapas da migração

1. Quando você entrar no [portal do Luis](https://www.luis.ai), uma janela de migração do Azure será aberta com as etapas para migração. Se você descartá-lo, não poderá prosseguir com a criação de seus aplicativos LUIS, e a única ação exibida será continuar com a migração.

    > [!div class="mx-imgBorder"]
    > ![Introdução à janela de migração](./media/migrate-authoring-key/notify-azure-migration.png)

2. Se você tiver colaboradores em qualquer um de seus aplicativos, verá uma lista de nomes de aplicativos pertencentes a você, juntamente com a região de criação e os emails do colaborador em cada aplicativo. É recomendável enviar aos seus colaboradores um email notificando-os sobre a migração clicando no botão **Enviar** símbolo à esquerda do nome do aplicativo.
Um `*` símbolo será exibido ao lado do nome do aplicativo se um colaborador tiver um recurso de previsão atribuído ao seu aplicativo. Após a migração, esses aplicativos ainda terão esses recursos de previsão atribuídos a eles, embora os colaboradores não tenham acesso para criar seus aplicativos. No entanto, essa atribuição será interrompida se o proprietário do recurso de previsão [gerar novamente as chaves](./luis-how-to-azure-subscription.md#regenerate-an-azure-key) do portal do Azure.  

   > [!div class="mx-imgBorder"]
   > ![Notificar colaboradores](./media/migrate-authoring-key/notify-azure-migration-collabs.png)


   Para cada colaborador e aplicativo, o aplicativo de email padrão é aberto com um email levemente formatado. Você pode editar o email antes de enviá-lo. O modelo de email inclui a ID do aplicativo e o nome do aplicativo exatos.

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```
   > [!Note]
   > Depois de migrar sua conta para o Azure, seus aplicativos não estarão mais disponíveis para colaboradores.

3. Se você for um colaborador em qualquer aplicativo, uma lista de nomes de aplicativos compartilhados com você será mostrada junto com a região de criação e os emails de proprietário em cada aplicativo. É recomendável exportar uma cópia dos aplicativos clicando no botão Exportar à esquerda do nome do aplicativo. Você pode importar esses aplicativos de volta após a migração, pois eles não serão migrados automaticamente com você.
Um `*` símbolo será exibido ao lado do nome do aplicativo se você tiver um recurso de previsão atribuído a um aplicativo. Após a migração, seu recurso de previsão ainda será atribuído a esses aplicativos, embora você não tenha mais acesso para criar esses aplicativos. Se você quiser interromper a atribuição entre o recurso de previsão e o aplicativo, será necessário ir para portal do Azure e [regenerar as chaves](./luis-how-to-azure-subscription.md#regenerate-an-azure-key).

   > [!div class="mx-imgBorder"]
   > ![Exporte seus aplicativos.](./media/migrate-authoring-key/migration-export-apps.png)


4. Na janela para migrar regiões, você será solicitado a migrar seus aplicativos para um recurso do Azure na mesma região em que foram criados. LUIS tem três regiões de criação [e portais](./luis-reference-regions.md#luis-authoring-regions). A janela mostrará as regiões em que seus aplicativos de propriedade foram criados. As regiões de migração exibidas podem ser diferentes dependendo do portal regional que você usa e dos aplicativos que você criou. 

   > [!div class="mx-imgBorder"]
   > ![Migração de várias regiões.](./media/migrate-authoring-key/migration-regional-flow.png)

5. Para cada região, escolha criar um novo recurso de criação de LUIS ou para migrar para um existente usando os botões.

   > [!div class="mx-imgBorder"]
   > ![escolha criar ou recurso de criação existente](./media/migrate-authoring-key/migration-multiregional-resource.png)

   Forneça as seguintes informações:

   * **Nome do locatário**: o locatário ao qual sua assinatura do Azure está associada. Por padrão, isso é definido para o locatário que você está usando no momento. Você pode alternar locatários fechando essa janela e selecionando o avatar no canto superior direito da tela, contendo suas iniciais. Clique em **migrar para o Azure** para abrir novamente a janela.
   * **Nome da assinatura do Azure**: a assinatura que será associada ao recurso. Se você tiver mais de uma assinatura que pertence ao seu locatário, selecione aquela que você deseja na lista suspensa.
   * **Nome do recurso de criação**: um nome personalizado que você escolher. Ele é usado como parte da URL para suas consultas de ponto de extremidade de criação e previsão. Se você estiver criando um novo recurso de criação, observe que o nome do recurso pode incluir apenas caracteres alfanuméricos, `-` e não pode iniciar ou terminar com `-` . Se qualquer outro símbolo estiver incluído no nome, a criação e a migração de recursos falharão.
   * **Nome do grupo de recursos do Azure**: um nome de grupo de recursos personalizado que você escolhe na lista suspensa. Os grupos de recursos permitem agrupar os recursos do Azure para acesso e gerenciamento. Se você não tiver um grupo de recursos em sua assinatura, não será permitido criar um no portal do LUIS. Acesse [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) para criar um, em seguida, vá para Luis para continuar o processo de entrada.

6. Depois de migrar com êxito em todas as regiões, clique em concluir. Agora você terá acesso aos seus aplicativos. Você pode continuar criando e mantendo todos os seus aplicativos em todas as regiões dentro do Portal.

## <a name="migration-notes"></a>Notas de migração

* Antes da migração, os coautores são conhecidos como _colaboradores_ no nível do aplicativo Luis. Após a migração, a função de _colaborador_ do Azure é usada para a mesma funcionalidade no nível de recurso do Azure.
* Se tiver entrado em mais de um [Portal Regional do Luis](./luis-reference-regions.md#luis-authoring-regions), você será solicitado a migrar em várias regiões de uma só vez.
* Os aplicativos serão migrados automaticamente com você se você for o proprietário do aplicativo. Os aplicativos não serão migrados com você se você for um colaborador no aplicativo. No entanto, os colaboradores serão solicitados a exportar os aplicativos de que precisam.
* Os proprietários de aplicativos não podem escolher um subconjunto de aplicativos para migrar e não há nenhuma maneira para um proprietário saber se os colaboradores migraram.
* A migração não move automaticamente ou adiciona colaboradores ao recurso de criação do Azure. O proprietário do aplicativo é aquele que precisa concluir esta etapa após a migração. Esta etapa requer [permissões para o recurso de criação do Azure](./luis-how-to-collaborate.md).
* Depois que os colaboradores forem atribuídos ao recurso do Azure, eles precisarão migrar antes que possam acessar os aplicativos. Caso contrário, eles não terão acesso para criar os aplicativos.


## <a name="using-apps-after-migration"></a>Usando aplicativos após a migração

Após o processo de migração, todos os seus aplicativos LUIS para os quais você é o proprietário agora serão atribuídos a um único recurso de criação de LUIS.
A lista **meus aplicativos** mostra os aplicativos migrados para o novo recurso de criação. Antes de acessar seus aplicativos, selecione **escolher um recurso de criação diferente** para selecionar a assinatura e o recurso de criação para exibir os aplicativos que podem ser criados.

> [!div class="mx-imgBorder"]
> ![selecionar a assinatura e o recurso de criação](./media/migrate-authoring-key/select-sub-and-resource.png)


Se você planeja editar seus aplicativos programaticamente, precisará dos valores de chave de criação. Esses valores são exibidos clicando em **gerenciar** na parte superior da tela no portal do Luis e, em seguida, selecionando **recursos do Azure**. Eles também estão disponíveis na portal do Azure na página de pontos de **extremidade e de ponto de extremidades** do recurso. Você também pode criar mais recursos de criação e atribuí-los da mesma página.

## <a name="adding-contributors-to-authoring-resources"></a>Adicionando colaboradores aos recursos de criação

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Saiba [como adicionar colaboradores](luis-how-to-collaborate.md) em seu recurso de criação. Os colaboradores terão acesso a todos os aplicativos sob esse recurso.

Você pode adicionar colaboradores ao recurso de criação do portal do Azure, na página de controle de **acesso (iam)** para esse recurso. Para obter mais informações, consulte [Adicionar colaboradores ao seu aplicativo](luis-how-to-collaborate.md).

> [!Note]
> Se o proprietário do aplicativo LUIS migrou e adicionou o colaborador como um colaborador no recurso do Azure, o colaborador ainda não terá acesso ao aplicativo, a menos que eles também migrem.

## <a name="troubleshooting-the-migration-process"></a>Solucionando problemas do processo de migração

Se você não conseguir encontrar sua assinatura do Azure na lista suspensa:
* Verifique se você tem uma assinatura válida do Azure que está autorizada a criar recursos de serviços cognitivas. Vá para a [portal do Azure](https://ms.portal.azure.com) e verifique o status da assinatura. Se você não tiver uma, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/cognitive-services/).
* Verifique se você está no locatário adequado associado à sua assinatura válida. Você pode alternar os locatários selecionando o avatar no canto superior direito da tela, contendo suas iniciais.

  > [!div class="mx-imgBorder"]
  > ![Página para alternar diretórios](./media/migrate-authoring-key/switch-directories.png)

Se você tiver um recurso de criação existente, mas não conseguir encontrá-lo quando selecionar a opção **usar recurso de criação existente** :
* O recurso foi provavelmente criado em uma região diferente daquela que você está tentando migrar.
* Em vez disso, crie um novo recurso no portal do LUIS.

Se você selecionar a opção **criar novo recurso de criação** e a migração falhar com a mensagem de erro "falha ao recuperar as informações do usuário do Azure, tente novamente mais tarde":
* Sua assinatura pode ter 10 ou mais recursos de criação por região, por assinatura. Se esse for o caso, você não poderá criar um novo recurso de criação.
* Migre selecionando a opção **usar recurso de criação existente** e selecionando um dos recursos existentes em sua assinatura.

## <a name="create-new-support-request"></a>Criar nova solicitação de suporte

Se você tiver problemas com a migração que não são abordados na seção de solução de problemas, [crie um tópico de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e forneça as informações abaixo com os seguintes campos:

   * **Tipo de problema**: técnico
   * **Assinatura**: escolha uma assinatura na lista suspensa
   * **Serviço**: Pesquise e selecione "serviços cognitivas"
   * **Recurso**: escolha um recurso de Luis se houver um existente. Caso contrário, selecione pergunta geral.

## <a name="next-steps"></a>Próximas etapas

* Examinar [conceitos sobre criação e chaves de tempo de execução](luis-how-to-azure-subscription.md)
* Examine como [atribuir chaves](luis-how-to-azure-subscription.md) e [Adicionar colaboradores](luis-how-to-collaborate.md)