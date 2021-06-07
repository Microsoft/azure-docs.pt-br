---
title: Gerenciar grupos de aplicativos para o portal da Área de Trabalho Virtual do Windows – Azure
description: Como gerenciar grupos de aplicativos da Área de Trabalho Virtual do Windows com o portal do Azure.
author: Heidilohr
ms.topic: tutorial
ms.date: 10/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 65ca13cba07230a7bf606a398e28761aced14857
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446002"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>Tutorial: Gerenciar grupos de aplicativos com o portal do Azure

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica) sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

O grupo de aplicativos padrão criado para um novo pool de hosts da Área de Trabalho Virtual do Windows também publica a área de trabalho completa. Além disso, você pode criar um ou mais grupos de aplicativos do RemoteApp para o pool de hosts. Siga este tutorial para criar um grupo de aplicativos do RemoteApp e publicar aplicativos individuais do menu Iniciar.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um grupo do RemoteApp.
> * Permitir acesso a programas do RemoteApps.

## <a name="create-a-remoteapp-group"></a>Criar um grupo do RemoteApp

Se você já tiver criado um pool de host e VMs de host de sessão usando o portal do Azure ou o PowerShell, poderá adicionar grupos de aplicativos do portal do Azure com o seguinte processo:

1.  Entre no [portal do Azure](https://portal.azure.com/).
   
    >[!NOTE]
    > Se estiver entrando no portal US Gov, acesse [https://portal.azure.us/](https://portal.azure.us/).

2.  Pesquise e selecione **Área de Trabalho Virtual do Windows**.

3. Você pode adicionar um grupo de aplicativos diretamente ou adicioná-lo por meio de um pool de host existente. Escolha uma opção abaixo:

    - Selecione **Grupos de aplicativos** no menu do lado esquerdo da página e escolha **+ Adicionar**.

    - Selecione **Pools de host** no menu no lado esquerdo da tela, escolha o nome do pool de host, **Grupos de aplicativos** no menu à esquerda e **+ Adicionar**. Nesse caso, o pool de host já estará selecionado na guia Informações Básicas.

4. Na guia **Informações Básicas**, selecione a **Assinatura** e o **Grupo de recursos** para os quais deseja criar o grupo de aplicativos. Você também pode optar por criar um grupo de recursos em vez de selecionar um existente.

5. Selecione o **Pool de host** que será associado ao grupo de aplicativos no menu suspenso.

    >[!NOTE]
    >Você deve selecionar o pool de host associado ao grupo de aplicativos. Os grupos de aplicativos têm aplicativos ou áreas de trabalho que são atendidos de um host de sessão e os hosts de sessão fazem parte dos pools de hosts. O grupo de aplicativos precisa ser associado a um pool de host durante a criação.

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela da guia Noções Básicas no portal do Azure.](media/basics-tab.png)

6. Escolha **RemoteApp** em **Tipo de grupo de aplicativos** e insira um nome para o RemoteApp.

      > [!div class="mx-imgBorder"]
      > ![Uma captura de tela dos campos de tipo de grupo de aplicativos. "RemoteApp" está realçado.](media/remoteapp-button.png)

7.  Selecione **Avançar: Atribuições >** .

8.  Para atribuir usuários individuais ou grupos de usuários ao grupo de aplicativos, escolha **+Adicionar usuários ou grupos de usuários do Azure AD**.

9.  Selecione os usuários aos quais deseja permitir acesso aos aplicativos. Você pode selecionar um ou vários usuários e grupos de usuários.

     > [!div class="mx-imgBorder"]
     > ![Uma captura de tela do menu de seleção de usuário.](media/select-users.png)

10.  Selecione **Selecionar**.

11.  Selecione **Avançar: Aplicativos >** e **+Adicionar aplicativos**.

12.  Para adicionar um aplicativo do menu iniciar:

      - Em **Origem do aplicativo**, escolha **menu Iniciar** no menu suspenso. Em seguida, em **Aplicativo**, escolha o aplicativo no menu suspenso.

     > [!div class="mx-imgBorder"]
     > ![Uma captura de tela da tela Adicionar Aplicativo com o menu Iniciar selecionado.](media/add-app-start.png)

      - Em **Nome de exibição**, insira o nome do aplicativo que será mostrado ao usuário em seu cliente.

      - Deixe as outras opções no estado em que se encontram e selecione **Salvar**.

13.  Para adicionar um aplicativo de um caminho de arquivo específico:

      - Em **Origem do aplicativo**, selecione **Caminho do arquivo** no menu suspenso.

      - Em **Caminho do aplicativo**, insira o caminho para o aplicativo no host da sessão registrado no pool de host associado.

      - Insira os detalhes do aplicativo nos campos **Nome do aplicativo**, **Nome de exibição**, **Caminho do ícone** e **Índice do ícone**.

      - Clique em **Salvar**.

     > [!div class="mx-imgBorder"]
     > ![Uma captura de tela da página Adicionar Aplicativo com o caminho de arquivo selecionado.](media/add-app-file.png)

14.  Repita esse processo para cada aplicativo que você deseja adicionar ao grupo de aplicativos.

15.  Em seguida, selecione **Avançar: Workspace >** .

16.  Caso deseje registrar o grupo de aplicativos em um workspace, selecione **Sim** em **Registrar grupo de aplicativos**. Se você preferir registrar o grupo de aplicativos depois, selecione **Não**.

17.  Se você selecionar **Sim**, poderá selecionar um workspace existente para registrar seu grupo de aplicativos.

       >[!NOTE]
       >Você só pode registrar o grupo de aplicativos em workspaces criados na mesma localização que o pool de host. Além disso, Se você tiver registrado anteriormente outro grupo de aplicativos do mesmo pool de host que o novo grupo de aplicativos em um workspace, ele será selecionado e você não poderá editá-lo. Todos os grupos de aplicativos de um pool de host devem ser registrados no mesmo workspace.

     > [!div class="mx-imgBorder"]
     > ![Uma captura de tela da página Registrar grupo de aplicativos para um workspace existente. O pool de host é pré-selecionado.](media/register-existing.png)

18.  Opcionalmente, se quiser criar marcas para facilitar a organização do seu workspace, escolha **Avançar: Marcas >** e insira os nomes das marcas.

19.  Quando terminar, selecione **Review + create**.

20.  Aguarde um pouco para que o processo de validação seja concluído. Ao terminar, selecione **Criar** para implantar seu grupo de aplicativos.

O processo de implantação fará o seguinte para você:

- Criar o grupo de aplicativos do RemoteApp.
- Adicionar os aplicativos selecionados ao grupo de aplicativos.
- Publicar o grupo de aplicativos publicado para usuários e grupos de usuários selecionados.
- Registrar o grupo de aplicativos, se você optou por fazer isso.
- Criar um link para um modelo de Azure Resource Manager com base em sua configuração que você pode baixar e salvar para mais tarde.

>[!IMPORTANT]
>Você só pode criar 200 grupos de aplicativos para cada locatário do Azure Active Directory. Adicionamos esse limite devido a limitações de serviço para recuperar feeds para nossos usuários. Esse limite não se aplica a grupos de aplicativos criados na Área de Trabalho Virtual do Windows (clássico).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um grupo de aplicativos, preenchê-lo com os programas RemoteApp e atribuir usuários ao grupo de aplicativos. Para saber como criar um pool de host de validação, consulte o tutorial a seguir. Você pode usar um pool de host de validação para monitorar a atualizações de serviço antes de implantá-las em seu ambiente de produção.

> [!div class="nextstepaction"]
> [Criar um pool de hosts para validar as atualizações de serviço](./create-validation-host-pool.md)
