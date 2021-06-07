---
title: Guia de Início Rápido – Criar um Azure Key Vault com o portal do Azure
description: Guia de início rápido que mostra como criar um Azure Key Vault usando o portal do Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/08/2020
ms.author: mbaldwin
ms.openlocfilehash: 2d8bfdf3a4ad8b713fb6c937d61fa437fc7977f2
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749664"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-portal"></a>Início Rápido: Criar um cofre de chaves usando o portal do Azure

O Azure Key Vault é um serviço de nuvem que fornece um repositório seguro para [chaves](../keys/index.yml), [segredos](../secrets/index.yml) e [certificados](../certificates/index.yml). Para obter mais informações sobre o Key Vault, confira [Sobre o Azure Key Vault](overview.md); para obter mais informações sobre o que pode ser armazenado em um cofre de chaves, confira [Sobre chaves, segredos e certificados](about-keys-secrets-certificates.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Neste guia de início rápido, você criará um cofre de chaves com o [portal do Azure](https://portal.azure.com). 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-vault"></a>Criar um cofre

1. No menu do portal do Azure ou na página **Início**, selecione **Criar um recurso**.
2. Digite **Key Vault** na caixa Pesquisar.
3. Na lista de resultados, escolha **Key Vault**.
4. Na seção Key Vault, escolha **Criar**.
5. A seção **Criar cofre de chaves** fornece as seguintes informações:
    - **Name**: um nome exclusivo é necessário. Para este início rápido, usamos **Contoso-vault2**. 
    - **Assinatura**: Escolha uma assinatura.
    - Em **Grupo de Recursos**, escolha **Criar novo** e digite um nome para o grupo de recursos.
    - No menu suspenso **Local**, escolha um local.
    - Deixe as outras opções em seus padrões.
6. Depois de fornecer as informações acima, selecione **Criar**.

Anote as duas propriedades listadas abaixo:

* **Nome do cofre**: no exemplo, é **Contoso-Vault2**. Você usará esse nome nas outras etapas.
* **URI do cofre**: no exemplo, é https://contoso-vault2.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Nesse ponto, sua conta do Azure é a única autorizada a executar operações nesse novo cofre.

![Saída após a conclusão da criação do Key Vault](../media/quick-create-portal/vault-properties.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Outros tutoriais e inícios rápidos do Key Vault complementam este início rápido. Se você planeja continuar a trabalhar com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.
Quando não for mais necessário, exclua o grupo de recursos, que excluirá o Key Vault e os recursos relacionados. Para excluir o grupo de recursos pelo portal:

1. Insira o nome do grupo de recursos na caixa de pesquisa na parte superior do portal. Quando você vir o grupo de recursos usado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Na caixa **DIGITE O NOME DO GRUPO DE RECURSOS:** , digite o nome do grupo de recursos e selecione **Excluir**.


## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault usando o portal do Azure. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](overview.md)
- Examine a [Visão geral de segurança do Azure Key Vault](security-overview.md)
- Confira o [Guia do desenvolvedor do Azure Key Vault](developers-guide.md)
