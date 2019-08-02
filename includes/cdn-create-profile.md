---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 05/24/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 8aa6cb3f10b86a6821cd93190ecc2135508739cb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594006"
---
## <a name="create-a-new-cdn-profile"></a>Criar um novo perfil CDN

Um perfil CDN é um contêiner para pontos de extremidade CDN e especifica um tipo de preço.

1. No Portal do Azure, no canto superior esquerdo, selecione **Criar um recurso**. 
    
    O **Novo** painel é exibido.
   
2. Selecione **Web + Celular**, depois **CDN**.
   
    ![Selecionar recursos CDN](./media/cdn-create-profile/cdn-new-resource.png)

    O painel **Perfil CDN** é exibido.

3. Para as configurações de perfil da CDN, use os valores especificados na tabela a seguir:
   
    | Configuração  | Valor |
    | -------- | ----- |
    | **Nome** | Insira *my-cdn-profile-123* como nome de perfil. Esse nome deve ser globalmente exclusivo. Caso ele já esteja em uso, você pode inserir um nome diferente. |
    | **Assinatura** | Selecione uma assinatura do Azure na lista suspensa. |
    | **Grupo de recursos** | Selecione **Criar novo** e insira *my-resource-group-123* como o nome para seu novo grupo de recursos. Caso ele já esteja em uso, é possível inserir um nome diferente ou selecionar **Usar existente** e depois **my-resource-group-123** na lista suspensa. | 
    | **Localização do grupo de recursos** | Selecione **Centro dos EUA** na lista suspensa. |
    | **Tipo de preços** | Selecione **Verizon Standard** na lista suspensa. |
    | **Criar um novo ponto de extremidade CDN** | Deixe não selecionado. |  
   
    ![Novo perfil CDN](./media/cdn-create-profile/cdn-new-profile.png)

4. Selecione **Fixar no painel** para salvar o perfil em seu painel após sua criação.
    
5. Selecione **Criar** para criar o perfil. 

    Somente para perfis da **CDN Standard do Azure da Microsoft**, a conclusão do perfil é finalizada em duas horas. 

