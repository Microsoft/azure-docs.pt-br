---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3da4fd26b3f985e034ca60039c09412e8237e965
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98109484"
---
Para criar uma conta de armazenamento de uso geral v2 no portal do Azure, siga estas etapas:

1. No menu do portal do Azure, selecione **Todos os serviços**. Na lista de recursos, digite **Contas de armazenamento**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Contas de Armazenamento**.
1. Na janela **Contas de Armazenamento** que aparece, escolha **Adicionar**.
1. Na guia **Noções Básicas**, selecione a assinatura na qual você deseja criar a conta de armazenamento.
1. No campo **Grupo de recursos**, selecione o grupo de recursos desejado ou crie um novo.  Para obter mais informações sobre os grupos de recursos do Azure, confira [Visão geral do Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
1. Em seguida, insira um nome para sua conta de armazenamento. O nome escolhido deve ser exclusivo no Azure. O nome também deve ter entre 3 e 24 caracteres e pode incluir apenas números e letras minúsculas.
1. Selecione um local para sua conta de armazenamento ou use o local padrão.
1. Selecione um nível de desempenho. A camada padrão é *Standard*.
1. Defina o campo **Tipo de conta** para *Armazenamento V2 (uso geral v2)* .
1. Especifique como a conta de armazenamento será replicada. A opção de replicação padrão é *RA-GRS (armazenamento com redundância geográfica com acesso de leitura)* . Para saber mais sobre as opções de replicação disponíveis, confira [redundância do Armazenamento do Azure](../articles/storage/common/storage-redundancy.md).
1. Opções adicionais estão disponíveis nas abas **Rede**, **Proteção de dados**, **Avançado** e **Marcas**. Para usar o Azure Data Lake Storage, escolha a guia **Avançado** e, em seguida, defina **Namespace hierárquico** como **Habilitado**. Para obter mais informações, confira [Introdução ao Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md)
1. Selecione **Revisar + Criar** para examinar as configurações da conta de armazenamento e criar a conta.
1. Selecione **Criar**.

A imagem abaixo mostra as configurações na guia **Noções Básicas** para uma nova conta de armazenamento:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Captura de tela mostrando como criar uma conta de armazenamento no portal do Azure":::
