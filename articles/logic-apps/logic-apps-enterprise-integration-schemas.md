---
title: "Esquemas para validação de XML – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Validar documentos XML com esquemas para o Aplicativo Lógico do Azure e o Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 4f58a587c1f10aea1cee89e46fa9ec340e0d21c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="validate-xml-with-schemas-for-azure-logic-apps-and-the-enterprise-integration-pack"></a>Validar XML com esquemas para o Aplicativo Lógico do Azure e o Enterprise Integration Pack

Esquemas confirmam que os documentos XML recebidos são válidos e têm os dados esperados em um formato predefinido. Esquemas também ajudam a validar mensagens trocadas em um cenário B2B.

## <a name="add-a-schema"></a>Adicionar um esquema

1. No portal do Azure, clique em **Mais serviços**.

    ![Portal do Azure, "Mais serviços"](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. Insira **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados.

    ![Filtre a caixa de pesquisa](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. Escolha a **conta de integração** à qual você deseja adicionar o esquema.

    ![Lista de contas de integração](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. Escolha o bloco **Esquemas**.

    ![Conta de integração de exemplo, "Esquemas"](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>Adicione um arquivo de esquema com menos de 2 MB

1. Na folha **Esquemas** (das etapas anteriores), selecione **Adicionar**.

    ![Folha Esquemas, "Adicionar"](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. Digite um nome para o seu esquema. Para carregar o arquivo de esquema, selecione o ícone de pasta ao lado da caixa **Esquema**. Após a conclusão do processo de upload, selecione **OK**.

    ![Captura de tela de "Adicionar esquema" com "Arquivo pequeno" realçado](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>Adicione um arquivo de esquema com mais de 2 MB (até um máximo de 8 MB)

Essas etapas dependem do nível de acesso do contêiner de blob: **Público** ou **Sem acesso anônimo**.

**Para determinar esse nível de acesso**

1.  Abra o **Gerenciador de Armazenamento do Azure**. 

2.  Em **Contêineres de Blob**, selecione o contêiner de blob que você deseja. 

3.  Selecione **Segurança**, **Nível de Acesso**.

Se o nível de acesso de segurança do blob é **Público**, siga estas etapas.

![Gerenciador de Armazenamento do Azure, com "Contêineres de Blob", "Segurança" e "Público" realçados](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. Carregue o esquema na conta de armazenamento e copie o URI.

    ![Conta de armazenamento com o URI realçado](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. Selecione o **arquivo grande** em **Adicionar Esquema** e forneça o URI na caixa de texto **URI de Conteúdo**.

    ![Esquemas, com o botão "Adicionar" e "Arquivo grande" realçados](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

Se o nível de acesso da segurança do blob é **Sem acesso anônimo**, siga estas etapas.

![Gerenciador de Armazenamento do Azure, com "Contêineres de Blob", "Segurança" e "Sem acesso anônimo" realçados](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. Carregue o esquema na sua conta de armazenamento.

    ![Conta de armazenamento](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. Gere uma Assinatura de Acesso Compartilhado para o esquema.

    ![Conta de armazenamento com a guia assinaturas de acesso compartilhado realçada](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. Escolha **Arquivo grande** em **Adicionar Esquema** e forneça o URI da Assinatura de Acesso Compartilhado na caixa de texto **URI de Conteúdo**.

    ![Esquemas, com o botão "Adicionar" e "Arquivo grande" realçados](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. Na folha **Esquemas** da conta de integração, você deve ver o esquema recém-adicionado.

    ![Sua conta de integração com "Esquemas" e o novo esquema realçados](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Editar esquemas

1. Escolha o bloco **Esquemas**.

2. Após a folha **Esquemas** se abrir, selecione o esquema que deseja editar.

3. Na folha **Esquemas**, selecione **Editar**.

    ![Folha Esquemas](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. Selecione o arquivo de esquema que você deseja editar e, em seguida, selecione **Abrir**.

    ![Arquivo de esquema aberto para edição](media/logic-apps-enterprise-integration-schemas/edit-31.png)

O Azure mostrará uma mensagem informando que o esquema foi carregado com êxito.

## <a name="delete-schemas"></a>Excluir esquemas

1. Escolha o bloco **Esquemas**.

2. Após a folha **Esquemas** se abrir, selecione o esquema que deseja excluir.

3. Na folha **Esquemas**, selecione **Excluir**.

    ![Folha Esquemas](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. Clique em **Sim** para confirmar que deseja excluir o esquema selecionado.

    ![Mensagem de confirmação "Excluir esquema"](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    Na folha **Esquemas**, a lista de esquemas será atualizada e não incluirá mais o esquema excluído.

    ![Conta de integração com "Esquemas" realçado](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack").  

