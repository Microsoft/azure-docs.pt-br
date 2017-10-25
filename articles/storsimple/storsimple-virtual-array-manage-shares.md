---
title: Gerenciar compartilhamentos do StorSimple Virtual Array | Microsoft Docs
description: "Descreve o Gerenciador de Dispositivos StorSimple e explica como usá-lo para gerenciar compartilhamentos em sua Matriz Virtual StorSimple."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: e5c62689de36baa175001f5f4f70d87568876ef0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Use o serviço do Gerenciador de Dispositivos StorSimple para gerenciar compartilhamentos na Matriz Virtual StorSimple

## <a name="overview"></a>Visão geral

Este tutorial explica como usar o serviço Gerenciador de Dispositivos StorSimple para criar e gerenciar compartilhamentos na Matriz Virtual StorSimple.

O serviço Gerenciador de Dispositivos StorSimple é uma extensão do portal do Azure que permite gerenciar a solução StorSimple em uma única interface da Web. Além de gerenciar compartilhamentos e volumes, você pode usar o serviço Gerenciador de Dispositivos StorSimple para exibir e gerenciar dispositivos, exibir alertas, gerenciar políticas de backup e gerenciar o catálogo de backup.

## <a name="share-types"></a>Tipos de compartilhamento

Os compartilhamentos do StorSimple podem ser:

* **Fixados localmente**: os dados nesses compartilhamentos permanecerão sempre na matriz e não transbordam para a nuvem.
* **Em camadas**: os dados nesses compartilhamentos podem transbordar para a nuvem. Quando você cria um compartilhamento em camadas, aproximadamente 10% do espaço é provisionado na camada de local e 90% do espaço é provisionado na nuvem. Por exemplo, se você provisionasse um compartilhamento de 1 TB, 100 GB residiriam no espaço local e 900 GB seriam usados na nuvem quando os dados fossem distribuídos em camadas. Isso, por sua vez, implica que se você ficar sem todo o espaço local no dispositivo, não poderá provisionar um compartilhamento em camadas (porque 10% necessários na camada local não estarão disponíveis).

### <a name="provisioned-capacity"></a>Capacidade provisionada

Consulte a tabela a seguir para saber a capacidade máxima provisionada para cada tipo de compartilhamento.

| **Identificador de limite** | **Limite** |
| --- | --- |
| Tamanho mínimo de um compartilhamento em camadas |500 GB |
| Tamanho máximo de um compartilhamento em camadas |20 TB |
| Tamanho mínimo de um compartilhamento fixado localmente |50 GB |
| Tamanho máximo de um compartilhamento fixado localmente |2 TB |

## <a name="the-shares-blade"></a>A folha Compartilhamentos

O menu **Compartilhamentos** na sua folha Resumo do serviço StorSimple exibe a lista de compartilhamentos de armazenamento em determinada matriz StorSimple e permite a você gerenciá-los.

![Folha Compartilhamentos](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Um compartilhamento consiste em uma série de atributos:

* **Nome do compartilhamento** – um nome descritivo que deve ser exclusivo e que ajuda a identificar o compartilhamento.
* **Status** – Pode ser online ou offline. Se um compartilhamento estiver offline, os usuários do compartilhamento não poderão acessá-lo.
* **Tipo** – indica se o volume é **Em camadas** (o padrão) ou **Fixado localmente**.
* **Capacidade** – especifica a quantidade de dados usados em comparação com a quantidade total de dados que podem ser armazenados no compartilhamento.
* **Descrição** – uma configuração opcional que ajuda a descrever o compartilhamento.
* **Permissões** -permissões NTFS a para o compartilhamento que podem ser gerenciadas pelo Windows Explorer.
* **Backup** – no caso da Matriz Virtual StorSimple, todos os compartilhamentos são habilitados para backup automaticamente.

![Detalhes de compartilhamentos](./media/storsimple-virtual-array-manage-shares/share-details.png)

Use as instruções neste tutorial para executar as seguintes tarefas:

* Adicionar um compartilhamento
* Modificar um compartilhamento
* Colocar um compartilhamento offline
* Excluir um compartilhamento

## <a name="add-a-share"></a>Adicionar um compartilhamento

1. Na folha Resumo do serviço StorSimple, clique em **+ Adicionar compartilhamento** na barra de comandos. Isso abre a folha **Adicionar compartilhamento**.

    ![Adicionar compartilhamento](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. Na folha **Adicionar compartilhamento**, faça o seguinte:
   
    1. No **Nome do compartilhamento** , insira um nome exclusivo para o compartilhamento. O nome deve ser uma cadeia de caracteres contendo entre 3 e 127 caracteres.

    2. Uma **Descrição** opcional para o compartilhamento. A descrição ajudará a identificar os proprietários de compartilhamento.

    3. Na lista suspensa **Tipo**, especifique se deseja criar um compartilhamento **Em camadas** ou **Fixado localmente**. Para cargas de trabalho que exigem garantias locais, latências menores e um melhor desempenho, selecione um compartilhamento **Fixado localmente** . Para todos os outros dados, selecione compartilhamento **Em camadas** .

    4. No campo **Capacidade** , especifique o tamanho do compartilhamento. Um compartilhamento em camadas deve ter entre 500 GB e 20 TB e um compartilhamento fixo local deve ter entre 50 GB e 2 GB.

    5. No campo **Definir permissões padrão completas a**, atribua as permissões para o usuário ou para o grupo que está acessando esse compartilhamento. Especifique o nome do usuário ou grupo de usuários no formato _john@contoso.com_. É recomendável que você use um grupo de usuários (em vez de um único usuário) para conceder privilégios de administrador para acessar esses compartilhamentos. Depois de atribuir as permissões aqui, você pode usar o Gerenciador de Arquivos para modificar essas permissões.
3. Quando você tiver terminado de configurar o compartilhamento, clique em **Criar**. Um compartilhamento será criado com as configurações especificadas e você verá uma notificação. Por padrão, o backup estará habilitado para o compartilhamento.
4. Para confirmar se o compartilhamento foi criado com êxito, vá para a página **Compartilhamentos** . Você deve ver o compartilhamento listado.
   
    ![Criação do compartilhamento bem-sucedida](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Modificar um compartilhamento

Modifique um compartilhamento quando você precisar alterar a descrição dele. Nenhuma outra propriedade de compartilhamento pode ser modificada depois que ele é criado.

#### <a name="to-modify-a-share"></a>Para modificar um compartilhamento

1. Na configuração **Compartilhamentos** na folha de resumo do serviço StorSimple, selecione a matriz virtual no qual reside o compartilhamento que você deseja modificar.
2. **Selecione** o compartilhamento para exibir a descrição atual e modificá-la.
3. Salve suas alterações clicando na barra de comandos **Salvar**. As configurações especificadas serão aplicadas e você verá uma notificação.
   
    ![ Editar compartilhamento](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Colocar um compartilhamento offline

Talvez seja necessário colocar um compartilhamento offline quando você está planejando modificá-lo ou excluí-lo. Quando um compartilhamento está offline, não fica disponível para acesso de leitura e gravação. Você precisará colocar o compartilhamento offline no host e no dispositivo.

#### <a name="to-take-a-share-offline"></a>Para colocar um compartilhamento offline

1. Verifique se o compartilhamento em questão não está em uso antes de colocá-lo offline.
2. Coloque o compartilhamento na matriz offline executando as seguintes etapas:
   
    1. Na configuração **Compartilhamentos** na folha de resumo do serviço StorSimple, selecione a matriz virtual no qual reside o compartilhamento que você deseja colocar offline.

    2. **Selecione** o compartilhamento e clique em **...** (como alternativa, clique com o botão direito do mouse nesta linha) e, no menu de contexto, selecione **Colocar offline**.
     
        ![Compartilhamento offline](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Examine as informações na folha **Colocar offline** e confirme a aceitação da operação. Clique em **Colocar offline** para desativar o compartilhamento. Você verá uma notificação da operação em andamento.

    4. Para confirmar que o compartilhamento foi colocado offline com êxito, vá para a folha **Compartilhamentos**. Você deve ver o status do compartilhamento como offline.

## <a name="delete-a-share"></a>Excluir um compartilhamento

> [!IMPORTANT]
> Você somente pode excluir um compartilhamento se ele está offline.


Conclua as etapas a seguir para excluir um compartilhamento.

#### <a name="to-delete-a-share"></a>Para excluir um compartilhamento

1. Na configuração **Compartilhamentos** na folha de resumo do serviço StorSimple, selecione a matriz virtual no qual reside o compartilhamento que você deseja excluir.
2. **Selecione** o compartilhamento e clique em **...** (como alternativa, clique com o botão direito do mouse nesta linha) e, no menu de contexto, selecione **Excluir**.
   
    ![Excluir compartilhamento](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Verifique o status do compartilhamento que deseja excluir. Se o compartilhamento que você deseja excluir não estiver offline, coloque-o offline primeiro. Siga as etapas em [Colocar um compartilhamento offline](#take-a-share-offline).
4. Quando a confirmação for solicitada na folha **Excluir**, aceite a confirmação e clique em **Excluir**. O compartilhamento será excluído e a folha **Compartilhamentos** mostrará a lista atualizada de compartilhamentos dentro da matriz virtual.

## <a name="next-steps"></a>Próximas etapas
Saiba como [clonar um compartilhamento StorSimple](storsimple-virtual-array-clone.md).

