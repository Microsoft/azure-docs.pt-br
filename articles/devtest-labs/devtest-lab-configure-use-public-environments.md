---
title: Configurar e usar ambientes públicos no Azure DevTest Labs | Microsoft Docs
description: Este artigo descreve como configurar e usar ambientes públicos (Azure Resource Manager modelos em um repositório git) no Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 61cabdb296c3fff75137c7ce7e87652241fd2926
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85482659"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Configurar e usar ambientes públicos no Azure DevTest Labs
O Azure DevTest Labs tem um [repositório público de modelos do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) que pode ser usado para criar ambientes sem a necessidade de conectar uma origem do GitHub externa por conta própria. Esse repositório inclui modelos usados com frequência como os Aplicativos Web do Azure, Cluster do Service Fabric e ambiente de desenvolvimento do Farm do SharePoint. Esse recurso é semelhante ao repositório público de artefatos incluído em todos os laboratórios que você cria. O repositório de ambiente permite que você comece rapidamente com modelos de ambiente criados previamente com parâmetros de entrada mínimos para fornecer uma experiência de introdução simples aos recursos de PaaS nos laboratórios. 

## <a name="configuring-public-environments"></a>Configurar ambientes públicos
Como proprietário de um laboratório, é possível habilitar o repositório de ambiente público para o laboratório durante a criação do laboratório. Para habilitar ambientes públicos do laboratório, selecione **Ativar** do campo **Ambientes públicos** ao criar um laboratório. 

![Habilitar ambiente público para um novo laboratório](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


Para laboratórios existentes, o repositório de ambiente público não é habilitado. Habilite manualmente o uso de modelos no repositório. Para laboratórios criados usando modelos do Resource Manager, o repositório também é desabilitado por padrão.

É possível habilitar/desabilitar ambientes públicos para o laboratório e também disponibilizar somente ambientes específicos para usuários do laboratório usando as seguintes etapas: 

1. Selecione **Configuração e políticas** para o laboratório. 
2. Na seção **BASES DE MÁQUINA VIRTUAL**, selecione **Ambientes públicos**.
3. Para habilitar ambientes públicos ao laboratório, selecione **Sim**. Caso contrário, selecione **Não**. 
4. Se você habilitou ambientes públicos, todos os ambientes no repositório estarão habilitados por padrão. É possível desmarcar um ambiente para torná-lo indisponível aos usuários do laboratório. 

![Página de ambientes públicos](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Use modelos de ambiente como usuário de laboratório
Como usuário de laboratório, é possível criar um novo ambiente a partir da lista de modelos de ambiente habilitados, simplesmente selecionando **+Adicionar** na barra de ferramentas da página do laboratório. A lista de bases inclui os modelos de ambientes públicos habilitados pelo administrador de laboratório na parte superior da lista.

![Modelos de ambiente público](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Próximas etapas
Esse repositório é um repositório de software livre que você pode contribuir para adicionar seus próprios modelos do Resource Manager usados com frequência e úteis. Para contribuir, basta enviar uma solicitação de pull no repositório.  
