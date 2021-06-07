---
title: Diagnosticar falhas de artefato em uma máquina virtual Azure DevTest Labs
description: O DevTest Labs fornece informações que você pode usar para diagnosticar uma falha de artefato. Este artigo mostra como solucionar problemas de falhas de artefato.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 440ce6a537ac8d6a21ae8010bfbb3c38a82bf01e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85480806"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnosticar falhas de artefato no laboratório 
Depois de criar um artefato, você pode verificar se ele foi bem-sucedido ou falhou. Os logs de artefato no Azure DevTest Labs fornecem informações que você pode usar para diagnosticar uma falha de artefato. Você tem duas opções para exibir as informações de logs do artefato para uma VM do Windows:

* No portal do Azure
* Na VM

> [!NOTE]
> Para garantir que falhas sejam corretamente identificadas e explicadas, é importante que o artefato tenha a estrutura correta. Para saber mais sobre como construir corretamente um artefato, confira [Criar artefatos personalizados](devtest-lab-artifact-author.md). Para ver um exemplo de um artefato corretamente estruturado, verifique o artefato [Testar tipos de parâmetro](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Solucionar falhas de artefato usando o Portal do Azure

1. No Portal do Azure, na lista de recursos, selecione seu laboratório.
2. Escolha a VM do Windows que inclui o artefato que você deseja investigar.
3. No painel esquerdo, em **GERAL**, selecione **Artefatos**. É exibida uma lista de artefatos associados a essa VM. O nome e o status do artefato são indicados.

   ![Status do artefato](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure-new.png)

4. Escolha um artefato que mostra um status de **Falha**. O artefato é aberto. Uma mensagem de extensão que inclui detalhes sobre a falha do artefato é mostrada.

   ![Mensagem de erro do artefato](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Solucionar falhas de artefato de dentro da máquina virtual

1. Entre na VM que contém o artefato que você deseja diagnosticar.
2. Navegue até C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, em que *1.9* é o número de versão da Extensão de Script Personalizado do Azure.

   ![O arquivo de status](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status-new.png)

3. Abra o arquivo de **status**.

Para obter instruções sobre como localizar os arquivos de log em uma VM do **Linux** , consulte o seguinte artigo: [usar a extensão de script personalizado do Azure versão 2 com máquinas virtuais do Linux](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Postagens de blogs relacionadas
* [Ingresse uma VM em um domínio do Active Directory existente usando um modelo do Resource Manager no DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Próximas etapas
* Saiba como [adicionar um repositório Git a um laboratório](devtest-lab-add-artifact-repo.md).

