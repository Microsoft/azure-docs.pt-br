---
title: Testar a definição de interface do usuário para Aplicativos Gerenciados do Azure | Microsoft Docs
description: Descreve como testar a experiência do usuário para criar o Aplicativo Gerenciado do Azure por meio do portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2018
ms.author: tomfitz
ms.openlocfilehash: b1392c29881a9077e26baafc8972148800d03d3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746048"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>Testar a interface do portal do Azure para o aplicativo gerenciado
Após [criar o arquivo createUiDefinition.json](create-uidefinition-overview.md) para o Aplicativo Gerenciado do Azure, será necessário testar a experiência do usuário. Para simplificar o teste, use um script que carregue o arquivo no portal. Não é necessário realmente implantar o aplicativo gerenciado.

## <a name="prerequisites"></a>Pré-requisitos

* Um arquivo **createUiDefinition.json**. Se não tiver esse arquivo, copie o [arquivo de exemplo ](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json) e salve-o localmente.

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="download-test-script"></a>Baixar script de teste

Para testar a interface no portal, copie um dos scripts a seguir para o computador local:

* [Script de sideload do PowerShell](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Script de sideload do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>Executar script

Para ver o arquivo de interface no portal, execute o script baixado. O script cria uma conta de armazenamento na assinatura do Azure e carrega o arquivo createUiDefinition.json para a conta de armazenamento. A conta de armazenamento é criada na primeira vez que você executa o script ou em caso de exclusão da conta de armazenamento. Se a conta de armazenamento já existe em sua assinatura do Azure, o script a reutiliza. O script abre o portal e carrega o arquivo da conta de armazenamento.

Forneça um local para a conta de armazenamento e especifique a pasta que tem o arquivo createUiDefinition.json.

Para o PowerShell, use:

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Para a CLI do Azure, use:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Se o arquivo createUiDefinition.json estiver na mesma pasta que o script e você já tiver criado a conta de armazenamento, você não precisará fornecer esses parâmetros.

Para o PowerShell, use:

```powershell
.\SideLoad-CreateUIDefinition.ps1
```

Para a CLI do Azure, use:

```azurecli
./sideload-createuidef.sh
```

## <a name="test-your-interface"></a>Testar a interface

O script abre uma nova guia no navegador. Em seguida, exibe o portal com a interface para criar o aplicativo gerenciado.

![Exibir portal](./media/test-createuidefinition/view-portal.png)

Antes de preencher os campos, abra as Ferramentas para Desenvolvedores na Web no navegador. O **Console** exibe mensagens importantes sobre a interface.

![Selecionar console](./media/test-createuidefinition/select-console.png)

Se houver um erro na definição de interface, a descrição será exibida no console.

![Mostrar erro](./media/test-createuidefinition/show-error.png)

Forneça valores para os campos. Quando terminar, os valores que são passados para o modelo serão exibidos.

![Mostrar valores](./media/test-createuidefinition/show-json.png)

Você pode usar esses valores como o arquivo de parâmetro para testar seu modelo de implantação.

## <a name="troubleshooting-the-interface"></a>Solução de problemas da interface

Alguns erros comuns que você pode ver são:

* O portal não carrega sua interface. Em vez disso, ele mostra um ícone de uma nuvem com gotas caindo. Normalmente, você vê esse ícone quando há um erro de sintaxe em seu arquivo. Abra o arquivo no VS Code (ou outro editor de JSON com validação de esquema) e procure erros de sintaxe.

* O portal para de responder na tela de resumo. Geralmente, essa interrupção ocorre quando há um bug na seção de saída. Por exemplo, você pode ter referenciado um controle que não existe.

* Um parâmetro na saída está vazio. O parâmetro pode estar referenciando uma propriedade que não existe. Por exemplo, a referência para o controle é válida, mas a referência da propriedade não é válida.

## <a name="test-your-solution-files"></a>Testar os arquivos de solução

Agora que você verificou que a interface do portal está funcionando conforme o esperado, é hora de validar que o arquivo createUiDefinition está corretamente integrado ao arquivo mainTemplate.json. É possível executar um teste de script de validação para testar o conteúdo dos arquivos de solução, incluindo o arquivo createUiDefinition. O script valida a sintaxe JSON, verifica expressões regex em campos de texto e garante que os valores de saída da interface do portal correspondam aos parâmetros do modelo. Para obter mais informações sobre como executar esse script, consulte [Executar verificações de validação estática para modelos](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Próximas etapas

Após validar a interface do portal, saiba como tornar o[aplicativo gerenciado do Azure disponível no Marketplace](publish-marketplace-app.md).
