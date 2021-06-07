---
title: Início Rápido do Azure – Definir e recuperar um segredo do Key Vault usando o portal do Azure | Microsoft Docs
description: Início Rápido que mostra como definir e recuperar um segredo do Azure Key Vault usando o portal do Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: ba5b375f75d2655045e62583679839c8dd2a5720
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752436"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Início Rápido: definir e recuperar um segredo do Azure Key Vault usando o portal do Azure

O Azure Key Vault é um serviço de nuvem que funciona como um repositório seguro de segredos. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Os cofres de chaves do Azure podem ser criados e gerenciados por meio do portal do Azure. Neste início rápido, você cria um cofre de chaves e o utiliza para armazenar um segredo. 

Para obter mais informações, confira 
- [Visão Geral do Key Vault](../general/overview.md)
- [Visão Geral dos Segredos](about-secrets.md).

## <a name="prerequisites"></a>Pré-requisitos

Para acessar o Azure Key Vault, você precisará de uma assinatura do Azure. Se você ainda não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Todo o acesso a segredos ocorre por meio do Azure Key Vault. Para este início rápido, crie um cofre de chaves usando o [portal do Azure](../general/quick-create-portal.md), a [CLI do Azure](../general/quick-create-cli.md) ou o [Azure PowerShell](../general/quick-create-powershell.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, siga estas etapas:

1. Navegue até seu novo cofre de chaves no portal do Azure
1. Na página de configurações do Key Vault, selecione **Segredos**.
1. Clique em **Gerar/Importar**.
1. Na tela **Criar um segredo**, escolha os seguintes valores:
    - **Opções de upload**: Manual.
    - **Nome**: Digite um nome para o segredo. O nome do segredo precisa ser exclusivo dentro de um Key Vault. O nome deve ser uma sequência de 1 a 127 caracteres, que começam com uma letra e contêm somente 0 – 9, a – z, A – Z e -. Para obter mais informações sobre Nomenclatura, confira [Objetos, identificadores e controle de versão do Key Vault](../general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)
    - **Valor**: digite um valor para o segredo. As APIs do Key Vault aceitam e retornam valores do segredo como cadeias de caracteres. 
    - Deixe os outros valores com seus padrões. Clique em **Criar**.

Quando receber a mensagem de que o segredo foi criado com êxito, clique nele na lista. 

Para obter mais informações sobre atributos de segredos, confira [Sobre segredos do Azure Key Vault](./about-secrets.md)

## <a name="retrieve-a-secret-from-key-vault"></a>Recuperar um segredo do Key Vault

Se você clicar na versão atual, poderá ver o valor especificado na etapa anterior.

![Propriedades do segredo](../media/quick-create-portal/current-version-hidden.png)

Ao clicar no botão "Mostrar o valor de segredo" no painel direito, será possível ver o valor oculto. 

![O valor do segredo apareceu](../media/quick-create-portal/current-version-shown.png)

Você também pode usar a [CLI do Azure]() ou o [Azure PowerShell]() para recuperar o segredo criado anteriormente.

## <a name="clean-up-resources"></a>Limpar os recursos

Outros tutoriais e inícios rápidos do Key Vault complementam este início rápido. Se você planeja continuar a trabalhar com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.
Quando não for mais necessário, exclua o grupo de recursos, que excluirá o Key Vault e os recursos relacionados. Para excluir o grupo de recursos pelo portal:

1. Insira o nome do grupo de recursos na caixa de pesquisa na parte superior do portal. Quando você vir o grupo de recursos usado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Na caixa **DIGITE O NOME DO GRUPO DE RECURSOS:** , digite o nome do grupo de recursos e selecione **Excluir**.

> [!NOTE]
> É importante observar que depois que um segredo, uma chave, um certificado ou um cofre de chaves for excluído, ele permanecerá recuperável por um período configurável de 7 a 90 dias calendários. Se nenhuma configuração for especificada, o período de recuperação padrão será definido como 90 dias. Isso fornece aos usuários tempo suficiente para perceber uma exclusão de segredo acidental e responder. Para obter mais informações sobre como excluir e recuperar cofres de chaves e objetos do cofre de chaves, confira [Visão geral da exclusão temporária do Azure Key Vault](../general/soft-delete-overview.md)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault e armazenou um segredo nele. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Leia [Proteger o acesso a um Key Vault](../general/security-overview.md)
- Confira [Usar o Key Vault com o aplicativo Web do Serviço de Aplicativo](../general/tutorial-net-create-vault-azure-web-app.md)
- Confira [Usar o Key Vault com o aplicativo implantado na VM](../general/tutorial-net-virtual-machine.md)
- Confira o [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Examine a [Visão geral de segurança do Key Vault](../general/security-overview.md)