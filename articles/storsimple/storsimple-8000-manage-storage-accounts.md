---
title: Gerenciar suas credenciais de conta do Armazenamento do StorSimple para dispositivos Microsoft Azure StorSimple da série 8000 | Microsoft Docs
description: Explica como você pode usar a página Configurar o Gerenciador de Dispositivos do StorSimple para adicionar, editar, excluir ou girar as chaves de segurança para uma conta de armazenamento.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 53aa442b86f5c82ded2f212a64f43852e6b3d2c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632528"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Usar o serviço do Gerenciador de Dispositivos do StorSimple para gerenciar as credenciais da sua conta de armazenamento

## <a name="overview"></a>Visão geral

A seção **Configuração** da folha do serviço do Gerenciador de Dispositivos do StorSimple apresenta os parâmetros de serviço globais que podem ser criados no serviço do Gerenciador de Dispositivos do StorSimple. Esses parâmetros podem ser aplicados a todos os dispositivos conectados ao serviço e incluem:

* Credenciais da conta de armazenamento
* Modelos de largura de banda 
* Registros de controle de acesso 

Este tutorial explica como adicionar, editar ou excluir as credenciais da conta de armazenamento ou girar entre as chaves de segurança para uma conta de armazenamento.

 ![Lista de credenciais de conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

As contas de armazenamento contém as credenciais que o dispositivo StorSimple usa para acessar sua conta de armazenamento com seu provedor de serviços de nuvem. Para contas de armazenamento do Microsoft Azure, essas credenciais podem ser o nome da conta e a chave de acesso primário, por exemplo. 

Na folha **Credenciais da conta de armazenamento**, todas as contas de armazenamento criadas para a assinatura de cobrança são exibidas em formato tabular contendo as seguintes informações:

* **Nome** – O nome exclusivo atribuído à conta quando a mesma foi criada.
* **SSL habilitado** – Se o SSL está ativado e a comunicação do dispositivo para a nuvem é feita pelo canal seguro.
* **Usado pelo** – O número de volumes usando a conta de armazenamento.

As tarefas mais comuns relacionadas a contas de armazenamento que podem ser executadas são:

* Adicionar uma conta de armazenamento 
* Editar uma conta de armazenamento 
* Excluir uma conta de armazenamento 
* Rotação de chave de contas de armazenamento 

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

Há três tipos de contas de armazenamento que podem ser usadas com o dispositivo StorSimple.

* **Contas de armazenamento geradas automaticamente** – Como o nome sugere, esse tipo de conta de armazenamento é gerada automaticamente quando o serviço é criado pela primeira vez. Para saber mais sobre como essa conta de armazenamento é criada, confira [Etapa 1: Criar um novo serviço](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) em [Implantar seu dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md). 
* **Contas de armazenamento na assinatura do serviço** – Essas são as contas de armazenamento do Azure que estão associadas com a mesma assinatura que o serviço. Para saber mais sobre como essas contas de armazenamento são criadas, consulte [Sobre Contas de Armazenamento do Azure](../storage/common/storage-create-storage-account.md). 
* **Contas de armazenamento fora do serviço de assinatura** – Essas são as contas de armazenamento do Azure que não estão associadas ao seu serviço e que provavelmente existiam antes da criação do serviço.

## <a name="add-a-storage-account"></a>Adicionar uma conta de armazenamento

Você pode adicionar uma conta de armazenamento, fornecendo um nome amigável exclusivo e credenciais de acesso vinculadas à conta de armazenamento (com o provedor de serviços de nuvem especificado). Você também tem a opção de habilitar o modo secure sockets layer (SSL) para criar um canal seguro para comunicação de rede entre o dispositivo e a nuvem.

Você pode criar várias contas para um provedor de serviços de nuvem específico. Lembre-se, no entanto, que após a criação de uma conta de armazenamento, você não pode alterar o provedor de serviços de nuvem.

Enquanto a conta de armazenamento está sendo salvo, o serviço tenta se comunicar com o seu provedor de serviços de nuvem. As credenciais e o material de acesso que você forneceu serão autenticados neste momento. Uma conta de armazenamento será criada somente se a autenticação for bem-sucedida. Se a autenticação falhar, será exibida uma mensagem de erro apropriada.

Use os procedimentos a seguir para adicionar as credenciais de conta de armazenamento do Azure:

* Para adicionar uma credencial de conta de armazenamento com a mesma assinatura do Azure como o serviço Gerenciador de Dispositivos
* Para adicionar uma credencial de conta de armazenamento do Azure que esteja fora da assinatura do serviço Gerenciador de Dispositivos

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Para adicionar uma credencial de conta de armazenamento do Azure que esteja fora da assinatura do serviço do Gerenciador de Dispositivos do StorSimple

1. Navegue até seu serviço de Gerenciador de Dispositivos do StorSimple, selecione e clique duas vezes nele. Isso abre a folha **Visão geral**.
2. Selecione **Credenciais da conta de armazenamento** na seção **Configuração**. Isso lista quaisquer credenciais de conta de armazenamento existentes associadas ao serviço Gerenciador de Dispositivos StorSimple.
3. Clique em **Adicionar**.
4. Na folha **Adicionar credencial de uma conta de armazenamento**, faça o seguinte:
   
    1. Para **Assinatura**, selecione **Outras**.
   
    2. Forneça o nome da sua credencial de conta de armazenamento do Azure.
   
    3. Na caixa de texto **Chave de acesso da conta de armazenamento**, forneça a chave de acesso primária para suas credenciais de conta de armazenamento do Azure. Para obter essa chave, vá para o serviço de Armazenamento do Azure, escolha sua credencial de conta de armazenamento e clique em **Gerenciar chaves de conta**. Agora você pode copiar a chave de acesso primária.
   
    4. Para habilitar o SSL, clique no botão **Habilitar** para criar um canal seguro para comunicação de rede entre o serviço Gerenciador de Dispositivos do StorSimple e a nuvem. Clique no botão **Desabilitar** somente se você estiver operando em uma nuvem privada.
   
    5. Clique em **Adicionar**. Você será notificado depois que a credencial de conta de armazenamento tiver sido criada com êxito.

5. A credencial de conta de armazenamento criada recentemente é exibida na folha do serviço Gerenciador de Dispositivos do StorSimple em **Credenciais de conta de armazenamento**.
   


## <a name="edit-a-storage-account"></a>Editar uma conta de armazenamento

Você pode editar uma conta de armazenamento usada por um contêiner de volume. Se você editar uma conta de armazenamento que está sendo usada, o único campo disponível para modificar é a chave de acesso da conta de armazenamento. Você pode fornecer a nova chave de acesso de armazenamento e salvar as configurações atualizadas.

#### <a name="to-edit-a-storage-account"></a>Para editar uma conta de armazenamento

1. Vá até o seu serviço do Gerenciador de Dispositivos StorSimple. Na seção **Configuração**, clique em **Credenciais da conta de armazenamento**.

    ![Credenciais da conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. Na folha **Credenciais da conta de armazenamento**, na lista de credenciais de conta de armazenamento, selecione e clique naquela que você deseja editar. 

3. É possível modificar a seleção em **Habilitar SSL**. Você também pode clicar em **Mais...**  e selecionar **Sincronizar chave de acesso para girar** suas chaves de acesso da conta de armazenamento. Acesse [Rotação de chaves das contas de armazenamento](#key-rotation-of-storage-accounts) para obter mais informações sobre como executar a rotação de chaves. Depois de modificar as configurações, clique em **Salvar**. 

    ![Salvar as credenciais editadas da conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Quando solicitado a confirmar, clique em **Sim**. 

    ![Confirmar modificações](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

As configurações serão atualizadas e salvas para sua conta de armazenamento. 

## <a name="delete-a-storage-account"></a>Excluir uma conta de armazenamento

> [!IMPORTANT]
> Você pode excluir uma conta de armazenamento somente se ela não for usada por um contêiner de volume. Se uma conta de armazenamento está sendo usada por um contêiner de volume, exclua primeiro o contêiner de volume e, em seguida, exclua a conta de armazenamento associada.

#### <a name="to-delete-a-storage-account"></a>Para excluir uma conta de armazenamento

1. Vá até o seu serviço do Gerenciador de Dispositivos StorSimple. Na seção **Configuração**, clique em **Credenciais da conta de armazenamento**.

2. Na lista de contas de armazenamento em formato de tabela, passe o mouse sobre a conta que você deseja excluir. Clique com o botão direito do mouse para invocar o menu de contexto e clique em **Excluir**.

    ![Excluir as credenciais da conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Quando solicitado a confirmar, clique em **Sim** para continuar com a exclusão. A listagem de tabela será atualizada para refletir as alterações.

    ![Confirmar exclusão](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Rotação de chave de contas de armazenamento

Por motivos de segurança, a rotação de chaves é normalmente um requisito em datacenters. Cada assinatura do Microsoft Azure pode ter uma ou mais contas de armazenamento associadas. O acesso a essas contas é controlado pelas chaves de assinatura e acesso para cada conta de armazenamento. 

Quando você cria uma conta de armazenamento, o Microsoft Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticação quando a conta de armazenamento é acessada. Ter duas chaves de acesso de armazenamento permite que você regenere chaves sem interrupção do serviço de armazenamento ou do acesso a esse serviço. A chave que está sendo usada é a chave *primária* e a chave de backup é conhecida como a chave *secundária*. Uma dessas duas chaves deve ser fornecida quando o dispositivo Microsoft Azure StorSimple acessa o provedor de serviços de armazenamento de nuvem.

## <a name="what-is-key-rotation"></a>O que é a rotação de chaves?

Normalmente, os aplicativos usam apenas uma das chaves para acessar seus dados. Após um determinado período de tempo, você pode fazer com que seus aplicativos passem a usar a segunda chave. Após os seus aplicativos terem trocado para a chave secundária, você pode desativar a primeira chave e, em seguida, gerar uma nova chave. Usar as duas chaves dessa maneira permite que seus aplicativos acessem os dados sem incorrer em nenhum tempo de inatividade.

As chaves da conta de armazenamento são sempre armazenadas no serviço de forma criptografada. No entanto, elas podem ser redefinidas usando o serviço do Gerenciador de Dispositivos do StorSimple. O serviço pode obter as chaves primária e secundária para todas as contas de armazenamento na mesma assinatura, incluindo contas criadas no serviço de Armazenamento e contas de armazenamento padrão geradas quando o serviço do Gerenciador de Dispositivos do StorSimple foi criado pela primeira vez. O serviço do Gerenciador de Dispositivos do StorSimple sempre obterá essas chaves do Portal Clássico do Azure e as armazenará de forma criptografada.

## <a name="rotation-workflow"></a>Fluxo de trabalho de rotação

Um administrador do Microsoft Azure pode gerar novamente ou alterar a chave primária ou secundária, acessando diretamente a conta de armazenamento (por meio do serviço Microsoft Azure Storage). O serviço Gerenciador de Dispositivos StorSimple não vê essa alteração automaticamente.

Para informar o serviço do Gerenciador de Dispositivos do StorSimple da alteração, será necessário acessar o serviço do Gerenciador de Dispositivos do StorSimple, acessar a conta de armazenamento e, em seguida, sincronizar a chave primária ou secundária (dependendo de qual delas foi alterada). Em seguida, o serviço obtém a chave mais recente, criptografa as chaves e envia a chave criptografada para o dispositivo.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Para sincronizar as chaves para contas de armazenamento na mesma assinatura que o serviço 
1. Vá até o seu serviço do Gerenciador de Dispositivos StorSimple. Na seção **Configuração**, clique em **Credenciais da conta de armazenamento**.
2. Na listagem tabular das contas de armazenamento, clique naquela que você deseja modificar. 

    ![sincronizar chaves](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Clique em **...Mais** e selecione **Sincronizar chave de acesso para girar**.   

    ![sincronizar chaves](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. No serviço Gerenciador de Dispositivos StorSimple, você precisa atualizar a chave que foi alterada anteriormente no serviço Armazenamento do Microsoft Azure. Se a chave de acesso primária tiver sido alterada (gerada novamente), selecione a chave **primária**. Se a chave secundária tiver sido alterada, selecione a chave **secundária**. Clique em **Sincronizar chave**.
      
      ![sincronizar chaves](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Você será notificado depois que a chave tiver sido sincronizada com êxito.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Para sincronizar as chaves para contas de armazenamento fora da assinatura do serviço
1. Na página **Serviços** clique na guia **Configurar**.
2. Clique em **Adicionar/Editar Contas de Armazenamento**.
3. Na caixa de diálogo, faça o seguinte:
   
   1. Selecione a conta de armazenamento com a chave que você deseja atualizar.
   2. Será necessário atualizar a chave de acesso de armazenamento no serviço do Gerenciador de Dispositivos do StorSimple. Nesse caso, você pode ver a chave de acesso de armazenamento. Insira a nova chave na caixa **Chave de Acesso da Conta de Armazenamento**. 
   3. Salve suas alterações. Sua chave de acesso da conta de armazenamento deve estar atualizada.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [segurança do StorSimple](storsimple-8000-security.md).
* Saiba mais sobre como [usar o serviço Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

