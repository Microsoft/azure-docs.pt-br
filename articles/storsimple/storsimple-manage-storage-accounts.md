---
title: Gerenciar sua conta de armazenamento do StorSimple | Microsoft Docs
description: "Explica como você pode usar a página Configurar do StorSimple Manager para adicionar, editar, excluir ou alternar entre as chaves de segurança para uma conta de armazenamento."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 93207c40-e0eb-489e-8724-59fb94907081
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/29/2016
ms.author: v-sharos
ms.openlocfilehash: 68b767c9c93f2daff476a21029b9813f347590b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-your-storage-account"></a>Usar o serviço StorSimple Manager para gerenciar sua conta de armazenamento
## <a name="overview"></a>Visão geral
A página **Configurar** página apresenta todos os parâmetros de serviços globais que podem ser criados no serviço StorSimple Manager. Esses parâmetros podem ser aplicados a todos os dispositivos conectados ao serviço e incluem:

* Contas de armazenamento 
* Modelos de largura de banda 
* Registros de controle de acesso 

Este tutorial explica como você pode usar a página **Configurar** para adicionar, editar ou excluir contas de armazenamento ou alternar entre as chaves de segurança para uma conta de armazenamento.

 ![Configurar página](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)  

As contas de armazenamento contém as credenciais que o dispositivo usa para acessar sua conta de armazenamento com seu provedor de serviços de nuvem. Para contas de armazenamento do Microsoft Azure, essas credenciais podem ser o nome da conta e a chave de acesso primário, por exemplo. 

Na página **Configurar** , todas as contas de armazenamento que são criadas para a assinatura de cobrança são exibidas em formato de tabela contendo as seguintes informações:

* **Nome** – O nome exclusivo atribuído à conta quando a mesma foi criada.
* **SSL habilitado** – Se o SSL está ativado e a comunicação do dispositivo para a nuvem é feita pelo canal seguro.
* **Usado pelo** – O número de volumes usando a conta de armazenamento.

As tarefas mais comuns relacionadas a contas de armazenamento que podem ser executadas na página **Configurar** são:

* Adicionar uma conta de armazenamento 
* Editar uma conta de armazenamento 
* Excluir uma conta de armazenamento 
* Rotação de chave de contas de armazenamento 

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento
Há três tipos de contas de armazenamento que podem ser usadas com o dispositivo StorSimple.

* **Contas de armazenamento geradas automaticamente** – Como o nome sugere, esse tipo de conta de armazenamento é gerada automaticamente quando o serviço é criado pela primeira vez. Para saber mais sobre como essa conta de armazenamento é criada, consulte a [Etapa 1: criar um novo serviço](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service) em [Implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough.md). 
* **Contas de armazenamento na assinatura do serviço** – Essas são as contas de armazenamento do Azure que estão associadas com a mesma assinatura que o serviço. Para saber mais sobre como essas contas de armazenamento são criadas, consulte [Sobre Contas de Armazenamento do Azure](../storage/common/storage-create-storage-account.md). 
* **Contas de armazenamento fora do serviço de assinatura** – Essas são as contas de armazenamento do Azure que não estão associadas ao seu serviço e que provavelmente existiam antes da criação do serviço.

## <a name="add-a-storage-account"></a>Adicionar uma conta de armazenamento
Você pode adicionar uma conta de armazenamento, fornecendo um nome amigável exclusivo e credenciais de acesso vinculadas à conta de armazenamento (com o provedor de serviços de nuvem especificado). Você também tem a opção de habilitar o modo secure sockets layer (SSL) para criar um canal seguro para comunicação de rede entre o dispositivo e a nuvem.

Você pode criar várias contas para um provedor de serviços de nuvem específico. Lembre-se, no entanto, que após a criação de uma conta de armazenamento, você não pode alterar o provedor de serviços de nuvem.

Enquanto a conta de armazenamento está sendo salvo, o serviço tenta se comunicar com o seu provedor de serviços de nuvem. As credenciais e o material de acesso que você forneceu serão autenticados neste momento. Uma conta de armazenamento será criada somente se a autenticação for bem-sucedida. Se a autenticação falhar, será exibida uma mensagem de erro apropriada.

Contas de armazenamento do Gerenciador de Recursos criadas no portal do Azure também são compatíveis com o StorSimple. As contas de armazenamento do Gerenciador de Recursos não aparecerão na lista suspensa para seleção quando tentar criar um contêiner de volume. Apenas as contas de armazenamento criadas no Portal Clássico do Azure serão exibidas. As contas de armazenamento do Gerenciador de Recursos precisarão ser adicionadas usando o procedimento para adicionar uma conta de armazenamento descrita abaixo.

> [!NOTE]
> O procedimento para adicionar uma conta de armazenamento varia de acordo com a versão do software StorSimple que você está usando. Certifique-se de seguir o procedimento correto para a sua versão do StorSimple.
> 
> 

[!INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[!INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Editar uma conta de armazenamento
Você pode editar uma conta de armazenamento usada por um contêiner de volume. Se você editar uma conta de armazenamento que está sendo usada, o único campo disponível para modificar é a chave de acesso da conta de armazenamento. Você pode fornecer a nova chave de acesso de armazenamento e salvar as configurações atualizadas.

#### <a name="to-edit-a-storage-account"></a>Para editar uma conta de armazenamento
1. Na página inicial do serviço, selecione o seu serviço, clique duas vezes no nome do serviço e, em seguida, clique em **Configurar**.
2. Clique em **Adicionar/Editar Contas de Armazenamento**.
3. Na caixa de diálogo **Adicionar/Editar conta de armazenamento** :
   
   1. Na lista suspensa de **Contas de Armazenamento**, escolha uma conta existente que você deseja modificar. Isso também pode incluir contas de armazenamento que foram geradas automaticamente quando o serviço foi criado.
   2. Se necessário, você poderá modificar a seleção em **Habilitar modo SSL** .
   3. Você pode escolher alternar as chaves de acesso da conta de armazenamento. Consulte [Rotação de chaves das contas de armazenamento](#key-rotation-of-storage-accounts) para obter mais informações sobre como executar a rotação de chaves.
   4. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png) para salvar as configurações. As configurações serão atualizadas na página **Configurar** . Clique em **Salvar** para salvar as novas configurações atualizadas.
      
      ![Editar uma conta de armazenamento](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)

## <a name="delete-a-storage-account"></a>Excluir uma conta de armazenamento
> [!IMPORTANT]
> Você pode excluir uma conta de armazenamento somente se ela não for usada por um contêiner de volume. Se uma conta de armazenamento está sendo usada por um contêiner de volume, exclua primeiro o contêiner de volume e, em seguida, exclua a conta de armazenamento associada.
> 
> 

#### <a name="to-delete-a-storage-account"></a>Para excluir uma conta de armazenamento
1. Na página inicial do serviço StorSimple Manager, selecione o seu serviço, clique duas vezes no nome do serviço e, em seguida, clique em **Configurar**.
2. Na lista de contas de armazenamento em formato de tabela, passe o mouse sobre a conta que você deseja excluir.
3. Um ícone de exclusão (**x**) será exibido na coluna mais à direita para essa conta de armazenamento. Clique no ícone **x** para excluir as credenciais.
4. Quando solicitado a confirmar, clique em **Sim** para continuar com a exclusão. A listagem de tabela será atualizada para refletir as alterações.

## <a name="key-rotation-of-storage-accounts"></a>Rotação de chave de contas de armazenamento
Por motivos de segurança, a rotação de chaves é normalmente um requisito em datacenters. 

> [!NOTE]
> As seguintes informações de rotação de chaves e o procedimento de rotação aplicam-se apenas a contas de Armazenamento do Microsoft Azure. Se você estiver usando outro provedor de serviços de nuvem, poderá gerenciar chaves de conta de armazenamento por meio do painel de controle do provedor.
> 
> 

Cada assinatura do Microsoft Azure pode ter uma ou mais contas de armazenamento associadas. O acesso a essas contas é controlado pelas chaves de assinatura e acesso para cada conta de armazenamento. 

Quando você cria uma conta de armazenamento, o Microsoft Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticação quando a conta de armazenamento é acessada. Ter duas chaves de acesso de armazenamento permite que você regenere chaves sem interrupção do serviço de armazenamento ou do acesso a esse serviço. A chave que está sendo usada é a chave *primária* e a chave de backup é conhecida como a chave *secundária*. Uma dessas duas chaves deve ser fornecida quando o dispositivo Microsoft Azure StorSimple acessa o provedor de serviços de armazenamento de nuvem.

## <a name="what-is-key-rotation"></a>O que é a rotação de chaves?
Normalmente, os aplicativos usam apenas uma das chaves para acessar seus dados. Após um determinado período de tempo, você pode fazer com que seus aplicativos passem a usar a segunda chave. Após os seus aplicativos terem trocado para a chave secundária, você pode desativar a primeira chave e, em seguida, gerar uma nova chave. Usar as duas chaves dessa maneira permite que seus aplicativos acessem os dados sem incorrer em nenhum tempo de inatividade.

As chaves da conta de armazenamento são sempre armazenadas no serviço de forma criptografada. No entanto, elas podem ser redefinidas por meio do serviço StorSimple Manager. O serviço pode obter a chave primária e secundária para todas as contas de armazenamento na mesma assinatura, incluindo contas criadas no serviço de armazenamento, bem como as contas de armazenamento padrões geradas quando o serviço StorSimple Manager foi inicialmente criado. O serviço StorSimple Manager sempre receberá essas chaves do portal clássico do Azure, em seguida, as armazenará de forma criptografada.

## <a name="rotation-workflow"></a>Fluxo de trabalho de rotação
Um administrador do Microsoft Azure pode gerar novamente ou alterar a chave primária ou secundária, acessando diretamente a conta de armazenamento (por meio do serviço Microsoft Azure Storage). O serviço StorSimple Manager não verá essa alteração automaticamente.

Para informar o serviço StorSimple Manager da alteração, será necessário acessar o serviço StorSimple Manager, acessar a conta de armazenamento e, em seguida, sincronizar a chave primária ou secundária (dependendo de qual chave foi alterada). Em seguida, o serviço obtém a chave mais recente, criptografa as chaves e envia a chave criptografada para o dispositivo.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service-azure-only"></a>Para sincronizar chaves para contas de armazenamento na mesma assinatura que o serviço (somente Azure)
1. Na página **Serviços** clique na guia **Configurar**.
2. Clique em **Adicionar/Editar Contas de Armazenamento**.
3. Na caixa de diálogo, faça o seguinte:
   
   1. Selecione a conta de armazenamento com a chave que você deseja sincronizar. As chaves são criptografadas quando são exibidas.
   2. No serviço StorSimple Manager, você precisa atualizar a chave que foi alterada anteriormente no serviço Microsoft Azure Storage. Se a chave de acesso primária tiver sido alterada (gerada novamente), clique em **sincronizar chave primária**. Se a chave secundária tiver sido alterada, clique em **sincronizar chave secundária**.
      
      ![sincronizar chaves](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Para sincronizar as chaves para contas de armazenamento fora da assinatura do serviço
1. Na página **Serviços** clique na guia **Configurar**.
2. Clique em **Adicionar/Editar Contas de Armazenamento**.
3. Na caixa de diálogo, faça o seguinte:
   
   1. Selecione a conta de armazenamento com a chave que você deseja atualizar.
   2. Você precisará atualizar a chave de acesso de armazenamento no serviço StorSimple Manager. Nesse caso, você pode ver a chave de acesso de armazenamento. Insira a nova chave na caixa **Chave de Acesso da Conta de Armazenamento**. 
   3. Salve suas alterações. Sua chave de acesso da conta de armazenamento deve estar atualizada.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [segurança do StorSimple](storsimple-security.md).
* Saiba mais sobre o [uso do serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

