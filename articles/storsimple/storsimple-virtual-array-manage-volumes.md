---
title: Gerenciar volumes na StorSimple Virtual Array | Microsoft Docs
description: "Descreve o Gerenciador de Dispositivos StorSimple e explica como usá-lo para gerenciar volumes em sua Matriz Virtual StorSimple."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: a507bf1866952cb79fa6334fed80c88cd207cd0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Usar o Gerenciador de Dispositivos StorSimple para gerenciar volumes na Matriz Virtual StorSimple

## <a name="overview"></a>Visão geral

Este tutorial explica como usar o serviço Gerenciador de Dispositivos StorSimple para criar e gerenciar volumes na Matriz Virtual StorSimple.

O serviço Gerenciador de Dispositivos StorSimple é uma extensão do portal do Azure que permite gerenciar a solução StorSimple em uma única interface da Web. Além de gerenciar compartilhamentos e volumes, você pode usar o serviço Gerenciador de Dispositivos StorSimple para exibir e gerenciar dispositivos, exibir alertas, exibir e gerenciar políticas de backup e o catálogo de backup.

## <a name="volume-types"></a>Tipos de volumes

Os volumes do StorSimple podem ser:

* **Fixados localmente**: os dados nesses volumes permanecerão sempre na matriz e não transbordam para a nuvem.
* **Em camadas**: os dados nesses volumes podem transbordar para a nuvem. Quando você cria um volume em camadas, aproximadamente 10% do espaço é provisionado na camada de local e 90% do espaço é provisionado na nuvem. Por exemplo, se você provisionar um volume de 1 TB, 100 GB residiria no espaço local e 900 GB seria usado na nuvem quando os dados fossem distribuídos em camadas. Isso, por sua vez, implica que se você ficar sem todo o espaço local no dispositivo, não poderá provisionar um volume em camadas (porque 10% necessários na camada local não estarão disponíveis).

### <a name="provisioned-capacity"></a>Capacidade provisionada
Consulte a tabela a seguir para saber a capacidade máxima provisionada para cada tipo de volume.

| **Identificador de limite**                                       | **Limite**     |
|------------------------------------------------------------|---------------|
| Tamanho mínimo de um volume em camadas                            | 500 GB        |
| Tamanho máximo de um volume em camadas                            | 5 TB          |
| Tamanho mínimo de um volume fixado localmente                    | 50 GB         |
| Tamanho máximo de um volume fixado localmente                    | 500 GB        |

## <a name="the-volumes-blade"></a>A página Volumes
O menu **Volumes** na sua folha de resumo do serviço StorSimple exibe a lista de volumes de armazenamento em determinada matriz StorSimple e permite gerenciá-los.

![Folha Volumes](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Um volume consiste em uma série de atributos:

* **Nome** – Um nome descritivo que deve ser exclusivo e que ajuda a identificar o volume.
* **Status** – Pode ser online ou offline. Se um volume estiver offline, não é visível para os iniciadores (servidores) que têm permissão de acesso para usar o volume.
* **Tipo** – Indica se o volume é **Em camadas** (o padrão) ou **Afixados localmente**.
* **Capacidade** – especifica a quantidade de dados usados em comparação com a quantidade total de dados que podem ser armazenados pelo inicializador (servidor).
* **Backup** – no caso da Matriz Virtual StorSimple, todos os volumes são habilitados para backup automaticamente.
* **Hosts conectados** – especifica os iniciadores (servidores) que podem acessar o volume.

![Detalhes dos volumes](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Use as instruções neste tutorial para executar as seguintes tarefas:

* Adicionar um volume
* Modificar um volume
* Colocar um volume offline
* Excluir um volume

## <a name="add-a-volume"></a>Adicionar um volume

1. Na folha Resumo do serviço StorSimple, clique em **+ Adicionar volume** na barra de comandos. Isso abre a folha **Adicionar volume**.
   
    ![Adicionar volume](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. Na folha **Adicionar volume**, faça o seguinte:
   
   * No campo **Nome do volume**, insira um nome exclusivo para o seu volume. O nome deve ser uma cadeia de caracteres contendo entre 3 e 127 caracteres.
   * Na lista suspensa **Tipo**, especifique se deseja criar um volume **Em camadas** ou **Fixado localmente**. Para as cargas de trabalho que exigem garantias locais, latências baixas e um melhor desempenho, selecione **Volume fixado localmente**. Para todos os outros dados, selecione volume **Em camadas**.
   * No campo **Capacidade**, especifique o tamanho do volume. Um volume em camadas deve ter entre 500 GB e 5 TB e um volume fixado localmente deve ter entre 50 GB e 500 GB.
   * * Clique em **Hosts conectados**, selecione um ACR (registro de controle de acesso) correspondente ao iniciador iSCSI ao qual você deseja conectar esse volume e clique em **Selecionar**.
3. Para adicionar um novo host conectado, clique em **Adicionar novo**, insira um nome para o host e seu IQN (nome qualificado) iSCSI e clique em **Adicionar**.
   
    ![Adicionar volume](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Quando você tiver terminado de configurar o volume, clique em **Criar**. Um volume será criado com as configurações especificadas e você verá uma notificação quando ele for criado com êxito. Por padrão, o backup estará habilitado para o volume.
5. Para confirmar se o volume foi criado com êxito, vá para a folha **Volumes** . Você deve ver o volume listado.
   
    ![Criação do volume bem-sucedida](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Modificar um volume

Modifica um volume quando você precisa alterar os hosts que acessam o volume. Os outros atributos de um volume não poderão ser modificados depois que o volume for criado.

#### <a name="to-modify-a-volume"></a>Para modificar um volume

1. Na configuração **Volumes** na folha de resumo do serviço StorSimple, selecione a matriz virtual no qual reside o volume que você deseja modificar.
2. **Selecione** o volume e clique em **Hosts conectados** para exibir o host conectado atualmente e modificá-lo para um servidor diferente.
   
    ![Editar volume](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Salve suas alterações clicando na barra de comandos **Salvar**. As configurações especificadas serão aplicadas e você verá uma notificação.

## <a name="take-a-volume-offline"></a>Colocar um volume offline

Talvez seja necessário colocar um volume offline quando você estiver planejando modificá-lo ou excluí-lo. Quando um volume está offline, não está disponível para acesso de leitura / gravação. Você precisará colocar o volume offline no host e no dispositivo.

#### <a name="to-take-a-volume-offline"></a>Para colocar um volume offline

1. Certifique-se de que o volume em questão não está em uso antes de colocá-lo offline.
2. Coloque o volume offline no host primeiro. Isso elimina qualquer risco de corrupção de dados no volume. Para etapas específicas, consulte as instruções do sistema operacional do host.
3. Depois que o volume no host ficar offline, coloque o volume no dispositivo offline executando as seguintes etapas:
   
   * Na configuração **Volumes** na folha de resumo do serviço StorSimple, selecione a matriz virtual no qual reside o volume que você deseja colocar offline.
   * **Selecione** o volume e clique em **...** (como alternativa, clique com o botão direito do mouse nesta linha) e, no menu de contexto, selecione **Colocar offline**.
     
        ![Volume offline](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Examine as informações na folha **Colocar offline** e confirme a aceitação da operação. Clique em **Colocar offline** para desativar o volume. Você verá uma notificação da operação em andamento.
   * Para confirmar se o volume foi colocado offline com êxito, vá para a folha **Volumes** . Você deve ver o status do volume como offline.
     
       ![Confirmação de volume offline](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Excluir um volume

> [!IMPORTANT]
> Você pode excluir um volume apenas se ele estiver offline.
> 
> 

Conclua as seguintes etapas para excluir um volume.

#### <a name="to-delete-a-volume"></a>Para excluir um volume

1. Na configuração **Volumes** na folha de resumo do serviço StorSimple, selecione a matriz virtual na qual reside o volume que você deseja excluir.
2. **Selecione** o volume e clique em **...** (como alternativa, clique com o botão direito do mouse nesta linha) e, no menu de contexto, selecione **Excluir**.
   
    ![Excluir volume](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Verifique o status do volume que deseja excluir. Se o volume que você deseja excluir não estiver offline, coloque-o offline em primeiro lugar, seguindo as etapas em [Colocar um volume offline](#take-a-volume-offline).
4. Quando a confirmação for solicitada na folha **Excluir**, aceite a confirmação e clique em **Excluir**. O volume será excluído e a folha **Volumes** mostrará a lista atualizada de volumes na matriz virtual.

## <a name="next-steps"></a>Próximas etapas

Saiba como [Clonar um volume StorSimple](storsimple-virtual-array-clone.md).

