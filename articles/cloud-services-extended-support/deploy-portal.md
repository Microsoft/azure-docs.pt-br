---
title: Implantar um Serviço de Nuvem do Azure (suporte estendido) – portal do Azure
description: Implantar um Serviço de Nuvem do Azure (suporte estendido) usando o portal do Azure
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 306294cc654e46dbe8af80b25cb9c709071fad20
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166319"
---
# <a name="deploy-a-azure-cloud-services-extended-support-using-the-azure-portal"></a>Implantar Serviços de Nuvem do Azure (suporte estendido) usando o portal do Azure
Este artigo explica como usar o portal do Azure para criar uma implantação do Serviço de Nuvem (suporte estendido). 

## <a name="before-you-begin"></a>Antes de começar

Examine os [pré-requisitos de implantação](deploy-prerequisite.md) dos Serviços de Nuvem (suporte estendido) e crie os recursos associados. 

## <a name="deploy-a-cloud-services-extended-support"></a>Implantar Serviços de Nuvem (suporte estendido) 
1. Entre no [Portal do Azure](https://portal.azure.com)

2.  Usando a barra de pesquisa na parte superior do portal do Azure, procure e selecione **Serviços de Nuvem (suporte estendido)** .

    :::image type="content" source="media/deploy-portal-1.png" alt-text="Imagem mostrando a folha todos os recursos no portal do Azure.":::
 
3.  No painel Serviços de Nuvem (suporte estendido), selecione **Criar**. 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="Imagem mostrando a compra de um serviço de nuvem do marketplace.":::

4. A janela de criação dos Serviços de Nuvem (suporte estendido) será aberta na guia **Básico**. 
    - Escolha uma Assinatura.
    - Escolha um grupo de recursos ou crie um.
    - Insira o nome desejado para a implantação do Serviço de Nuvem (suporte estendido).
        - O nome DNS do serviço de nuvem é separado e especificado pelo rótulo de nome DNS do endereço IP público e pode ser modificado na seção de IP público na guia de configuração.
    -  Selecione a região na qual implantar.

    :::image type="content" source="media/deploy-portal-3.png" alt-text="Imagem mostrando a folha inicial dos Serviços de Nuvem (suporte estendido).":::

5. Adicione os arquivos de configuração, pacote e definição do serviço de nuvem. Você pode adicionar arquivos existentes do Armazenamento de Blobs ou carregá-los do computador local. Se for carregar do computador local, eles serão armazenados em uma conta de armazenamento. 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="Imagem mostrando a seção de carregamento da guia básico durante a criação.":::

6. Depois que todos os campos forem concluídos, vá até a guia **Configuração** e conclua. 
    - Selecione uma rede virtual para associar ao Serviço de Nuvem ou crie uma. 
        - As implantações do Serviço de Nuvem (suporte estendido) **precisam** estar em uma rede virtual. A rede virtual também **precisa** ser referenciada no arquivo de Configuração de Serviço (.cscfg) na seção `NetworkConfiguration`.
    - Selecione um endereço IP público existente para associar ao Serviço de Nuvem ou crie um.
        - Se você tiver **pontos de extremidade de entrada IP** definidos no arquivo de Definição de Serviço (.csdef), um endereço IP público precisará ser criado para o Serviço de Nuvem. 
        - Os Serviços de Nuvem (suporte estendido) dão suporte somente ao SKU de endereço IP básico.
        - Se a sua Configuração de Serviço (.cscfg) contiver um endereço IP reservado, o tipo de alocação do IP público deverá ser definido como **Estático**. 
        - Opcionalmente, atribua um nome DNS para o ponto de extremidade de serviço de nuvem atualizando a propriedade do rótulo DNS do endereço IP público associado ao serviço de nuvem.  
    - (Opcional) Iniciar o serviço de nuvem. Escolha entre iniciar ou não iniciar o serviço imediatamente após a criação.
    - Selecionar um Key Vault 
        - O Key Vault é necessário quando você especifica um ou mais certificados no arquivo de Configuração do Serviço (.cscfg). Quando você selecionar um cofre de chaves, tentaremos localizar os certificados selecionados de seu arquivo de Configuração de Serviço (.cscfg) com base em suas impressões digitais. Se algum certificado estiver ausente no cofre de chaves, você poderá carregá-los agora e clicar em **Atualizar**.   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="Imagem mostrando a folha de configuração no portal do Azure ao criar Serviços de Nuvem (suporte estendido).":::

7. Depois que todos os campos forem concluídos, vá para a guia **Examinar e criar** para validar sua configuração de implantação e criar seu Serviço de Nuvem (suporte estendido).

## <a name="next-steps"></a>Próximas etapas 
- Examine as [perguntas frequentes](faq.md) sobre os Serviços de Nuvem (suporte estendido).
- Implante um Serviço de Nuvem (suporte estendido) usando o [portal do Azure](deploy-portal.md), o [PowerShell](deploy-powershell.md), o [Modelo](deploy-template.md) ou o [Visual Studio](deploy-visual-studio.md).
- Visite o [repositório de exemplos dos Serviços de Nuvem (suporte estendido)](https://github.com/Azure-Samples/cloud-services-extended-support)
