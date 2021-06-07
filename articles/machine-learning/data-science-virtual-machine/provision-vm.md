---
title: 'Início Rápido: Criar uma Máquina Virtual de Ciência de Dados do Windows'
titleSuffix: Azure Data Science Virtual Machine
description: Configure e crie uma Máquina Virtual de Ciência de Dados no Azure para realizar a análise e o aprendizado de máquina.
services: machine-learning
ms.service: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: 53c7eb899578a044ecca0900da722c594bdac8dc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691834"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Início Rápido: Configurar a Máquina Virtual de Ciência de Dados para Windows

Coloque tudo em funcionamento com uma Máquina Virtual de Ciência de Dados do Windows Server 2019.

## <a name="prerequisite"></a>Pré-requisito

Para criar uma Máquina Virtual de Ciência de Dados do Windows, é necessário ter uma assinatura do Azure. [Experimentar o Azure gratuitamente](https://azure.com/free).
Observe que as contas gratuitas do Azure não dão suporte a SKUs de máquina virtual habilitada para GPU.

## <a name="create-your-dsvm"></a>Criar sua DSVM

Para criar uma instância de DSVM:

1. Acesse o [portal do Azure](https://portal.azure.com). Você pode ser solicitado a entrar na sua conta do Azure, caso ainda não esteja conectado.
1. Localize a listagem de máquinas virtuais digitando "máquina virtual de ciência de dados" e selecionando "Máquina Virtual de Ciência de Dados – Windows 2019".

1. Selecione o botão **Criar** na parte inferior.

1. Você deve ser redirecionado para a folha "Criar uma máquina virtual".

1. Preencha a guia **Noções básicas**:
      * **Assinatura**: se você tiver mais de uma assinatura, selecione aquela em que o computador será criado e cobrado. Você deve ter privilégios de criação de recurso nessa assinatura.
      * **Grupo de recursos**: crie um novo grupo ou use um existente.
      * **Nome da máquina virtual**: Insira o nome da máquina virtual. É assim que ela aparecerá no seu portal do Azure.
      * **Localização**: selecione o datacenter mais apropriado. Para um acesso mais rápido à rede, é o data center que contém a maioria dos seus dados ou que está mais próximo de sua localização física. Leia mais sobre as [Regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Imagem**: Mantenha o valor padrão.
      * **Size**: Isso deve ser preenchido automaticamente com um tamanho apropriado para cargas de trabalho gerais. Leia mais sobre [Tamanhos de VM do Windows no Azure](../../virtual-machines/sizes.md).
      * **Nome de usuário**: Insira o nome de usuário do administrador. Esse é o nome de usuário que você usará para fazer logon em sua máquina virtual e não precisa ser o mesmo que o seu nome de usuário do Azure.
      * **Senha**: Insira a senha que você usará para fazer logon em sua máquina virtual.    
1. Selecione **Examinar + criar**.
1. **Examinar + criar**
   * Verifique se todas as informações inseridas estão corretas. 
   * Selecione **Criar**.


> [!NOTE]
> * Você não paga taxas de licenciamento para o software que vem pré-carregado na máquina virtual. Você paga o custo de computação para o tamanho do servidor que você escolheu na etapa **Tamanho**.
> * O provisionamento leva de 10 a 20 minutos. Você pode exibir o status da VM no portal do Azure.

## <a name="access-the-dsvm"></a>Acessar a DSVM

Depois que a VM for criada e provisionada, siga as etapas listadas para [conectar-se à sua máquina virtual baseada no Azure](../../marketplace/azure-vm-create-using-approved-base.md). Use as credenciais da conta do administrador que você configurou na etapa **Básico** da criação da máquina virtual. 

Você está pronto para começar a usar as ferramentas que estão instaladas e pré-configuradas na VM. Muitas das ferramentas podem ser acessadas por meio de blocos do menu **Iniciar** e ícones da área de trabalho.

<a name="tools"></a>


## <a name="next-steps"></a>Próximas etapas

* Explore as ferramentas na DSVM abrindo o menu **Iniciar**.
* Para saber mais sobre o Azure Machine Learning, leia [O que é o Azure Machine Learning?](../overview-what-is-azure-ml.md) e teste os [tutoriais](../index.yml).
* Leia o artigo [Ciência de dados com uma Máquina Virtual de Ciência de Dados do Windows no Azure](./vm-do-ten-things.md)
