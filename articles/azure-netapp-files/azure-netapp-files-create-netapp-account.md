---
title: Criar uma conta do NetApp para acesso do Azure NetApp Files | Microsoft Docs
description: Descreve como acessar Azure NetApp Files e criar uma conta do NetApp para que você pode configurar um pool de capacidade e criar um volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: bb43a75b6a221c15c8724302797d04c22e04c8d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61086101"
---
# <a name="create-a-netapp-account"></a>Criar uma conta do NetApp
Criar uma conta do NetApp permite que você configure um pool de capacidade e, subsequentemente, crie um volume. Você pode usar a folha de Azure NetApp Files para criar uma nova conta do NetApp.

## <a name="before-you-begin"></a>Antes de começar
É necessário ter registrado sua assinatura para usar o Provedor de Recursos do NetApp e a versão prévia do recurso.

[Registro no Azure NetApp Files](azure-netapp-files-register.md)

## <a name="steps"></a>Etapas 

1. Entre no Portal do Azure. 
2. Acesse a folha Azure NetApp Files usando um dos seguintes métodos:  
   * Procure os **Azure NetApp Files** na caixa de pesquisa do portal do Azure.  
   * Clique em **Todos os serviços** na navegação e, em seguida, filtre para o Azure NetApp Files.  

   Você pode "favoritar" a folha Azure NetApp Files clicando no ícone de estrela ao lado dele. 

3. Clique em **+ Adicionar** para criar uma nova conta do NetApp.  
   A janela Nova conta do NetApp é exibida.  

4. Forneça as seguintes informações para sua conta do NetApp: 
   * **Nome da conta**  
     Especifique um nome exclusivo para a assinatura.
   * **Assinatura**  
     Selecione uma assinatura de suas assinaturas existentes.
   * **Grupo de recursos**   
     Use um Grupo de Recursos existente ou crie um novo.
   * **Localidade**  
     Selecione a região onde deseja que a conta e seus recursos filho a ser localizado.  

     ![Nova conta do NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Clique em **Criar**.     
   A conta do NetApp que você criou agora aparece na folha Azure NetApp Files. 

## <a name="next-steps"></a>Próximas etapas  

[Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)

