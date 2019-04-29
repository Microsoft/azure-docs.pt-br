---
title: Gerenciar conjuntos de registros DNS e registros com DNS do Azure
description: O DNS do Azure fornece a capacidade de gerenciar registros e conjuntos de registros de DNS ao hospedar seu domínio.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 10/6/2018
ms.author: victorh
ms.openlocfilehash: 891adfacde6e46b1d8fe8e2f6b5fb39c90ce27a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61296213"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Gerenciar registros e conjuntos de registros DNS usando o portal do Azure

Este artigo mostra como gerenciar registros e conjuntos de registros da zona DNS usando o portal do Azure.

É importante compreender a diferença entre os conjuntos de registros DNS e registros DNS individuais. Um conjunto de registros é uma coleção de registros em uma zona que tem o mesmo nome e o mesmo tipo. Para obter mais informações, veja [Criar registros e conjuntos de registros DNS usando o portal do Azure](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Criar registro e um novo conjunto de registros

Para criar um conjunto de registros no portal do Azure, veja [Create DNS records using the Azure portal](dns-getstarted-create-recordset-portal.md)(Criar registros DNS usando o Portal do Azure).

## <a name="view-a-record-set"></a>Exibir um conjunto de registros

1. No portal do Azure, vá para a folha **Zona DNS** .
2. Procure o conjunto de registros e selecione-o. Isso abrirá as propriedades do conjunto de registros.

    ![Pesquisar um conjunto de registros](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Adicionar um novo registro a um conjunto de registros

Você pode adicionar até 20 registros em qualquer conjunto de registros. Um conjunto de registros não pode conter dois registros idênticos. Conjuntos de registros vazios (sem nenhum registro) podem ser criados, mas não aparecem nos servidores de nome DNS do Azure. Os conjuntos de registros do tipo CNAME podem conter, no máximo, um registro.

1. Na folha **Propriedades do conjunto de registros** da zona DNS, clique no conjunto de registros ao qual você quer adicionar um registro.

    ![Selecionar um conjunto de registros](./media/dns-operations-recordsets-portal/selectset500.png)

2. Especifique as configurações do conjunto de registros preenchendo os campos.

    ![Adicionar um registro](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Clique em **Salvar** na parte superior da folha para salvar as configurações. Em seguida, feche a folha.
4. No canto, você verá que o registro está sendo salvo.

    ![Salvando o conjunto de registros](./media/dns-operations-recordsets-portal/saving150.png)

Assim que o registro for salvo, os valores da folha **Zona DNS** refletirão o novo registro.

## <a name="update-a-record"></a>Atualizar um registro

Ao atualizar um registro em um conjunto de registros existente, os campos disponíveis que poderão ser atualizados dependem do tipo de registro com o qual você está trabalhando.

1. Na folha **Propriedades do conjunto de registros** do conjunto de registros, procure o registro.
2. Modifique o registro. Quando você modifica um registro, é possível alterar suas configurações disponíveis. No exemplo a seguir, o campo **endereço IP** é selecionado e o endereço IP está sendo modificado.

    ![Modificar um registro](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Clique em **Salvar** na parte superior da folha para salvar as configurações. No canto superior direito, você verá a notificação informando que o registro foi salvo.

    ![Conjunto de registros salvo](./media/dns-operations-recordsets-portal/saved150.png)

Assim que o registro for salvo, os valores do conjunto de registros na folha **Zona DNS** refletirão o registro atualizado.

## <a name="remove-a-record-from-a-record-set"></a>Remover um registro de um conjunto de registros

Você pode usar o Portal do Azure para remover registros de um conjunto de registros. Observe que remover o último registro de um conjunto de registros não exclui o conjunto.

1. Na folha **Propriedades do conjunto de registros** do conjunto de registros, procure o registro.
2. Clique no registro que você quer remover. Em seguida, selecione **Remover**.

    ![Remover um registro](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Clique em **Salvar** na parte superior da folha para salvar as configurações.
4. Assim que o registro for removido, os valores do registro na folha **Zona DNS** refletirão a remoção.

## <a name="delete"></a>Excluir um conjunto de registros

1. Na folha **Propriedades do conjunto de registros**do conjunto de registros, clique em **Excluir**.

    ![Excluir um conjunto de registros](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Será exibida uma mensagem perguntando se você deseja excluir o conjunto de registros.
3. Verifique se o nome corresponde ao conjunto de registros que você quer excluir e clique em **Sim**.
4. Na folha **Zona DNS** , você poderá verificar que o conjunto de registros não está mais visível.

## <a name="work-with-ns-and-soa-records"></a>Trabalhar com registros NS e SOA

Os registros NS e SOA que são criados automaticamente são gerenciados de modo diferente de outros tipos de registro.

### <a name="modify-soa-records"></a>Modificar registros SOA

Não é possível adicionar nem remover registros no conjunto de registros SOA criados automaticamente no apex da zona (nome = "\\@\"). No entanto, é possível modificar qualquer um dos parâmetros no registro SOA (exceto o “Host”) e o TTL do conjunto de registros.

### <a name="modify-ns-records-at-the-zone-apex"></a>Modificar registros NS no apex da zona

O registro NS definido no apex da zona é criado automaticamente com cada zona DNS. Ele contém os nomes dos servidores de nome DNS do Azure atribuídos à zona.

Você pode adicionar servidores de nome adicionais a esse conjunto de registros NS para dar suporte à co-hospedagem de domínios com mais de um provedor DNS. Você também pode modificar o TTL e os metadados para esse conjunto de registros. No entanto, você não pode remover nem modificar os servidores de nome DNS do Azure previamente populados.

Observe que isso se aplica somente ao conjunto de registros NS definido no apex da zona. Outros conjuntos de registros NS na sua zona (conforme utilizados para delegar zonas filho) podem ser modificados sem restrição.

### <a name="delete-soa-or-ns-record-sets"></a>Excluir conjuntos de registros SOA ou NS

Não é possível excluir os conjuntos de registros SOA e NS no apex da zona (nome = "\\@\") criados automaticamente quando a zona é criada. Eles são excluídos automaticamente ao excluir a zona.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre o DNS do Azure, confira [Visão geral do DNS do Azure](dns-overview.md).
* Para obter mais informações sobre como automatizar o DNS, confira [Criando zonas DNS e conjuntos de registros usando o SDK do .NET](dns-sdk.md).
* Para saber mais sobre os registros DNS reversos, veja [Visão geral de DNS reverso e suporte no Azure](dns-reverse-dns-overview.md).
* Para obter mais informações sobre os registros de alias do DNS do Azure, consulte [Visão geral dos registros de alias do DNS do Azure](dns-alias.md).
