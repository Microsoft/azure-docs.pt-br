---
title: Use as máquinas virtuais de ciência de dados do Azure
description: Conecte-se a um Azure ciência de dados Máquina Virtual (DSVM) para estender o poder de computação disponível para Notebooks do Azure.
services: app-service
documentationcenter: ''
author: getroyer
manager: andneil
ms.assetid: 0ccc2529-e17f-4221-b7c7-9496d6a731cc
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: getroyer
ms.openlocfilehash: fe9886429a5e894f40c04b1f65094e412c1dc9e2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441197"
---
# <a name="use-azure-data-science-virtual-machines"></a>Use as máquinas virtuais de ciência de dados do Azure

Por padrão, os projetos executados **computação gratuita** camada, o que é limitada a 4 GB de memória e 1 GB de dados para evitar abusos. Você pode ignorar essas limitações usando uma máquina virtual diferente da provisionada em uma assinatura do Azure. Para essa finalidade, a melhor opção é uma máquina Virtual de ciência de dados (DSVM) do Azure usando o **máquina de Virtual de ciência de dados para Linux (Ubuntu)** imagem. Tal uma DSVM vem pré-configurada com tudo o que você precisa para blocos de anotações do Azure e é exibido automaticamente na **executar** lista suspensa em blocos de anotações do Azure.

> [!Note]
> Blocos de anotações do Azure tem suporte apenas em DSVMs criados com a imagem do Linux Ubuntu em. Não há suporte para blocos de anotações nas imagens CentOS Linux, Windows 2016 ou Windows 2012.

## <a name="create-a-dsvm-instance"></a>Criar uma instância DSVM

Para criar uma nova instância de DSVM, siga as instruções para [Criar uma VM de Ciência de Dados do Ubuntu](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Para mais informações, inclusive os detalhes de preços, consulte [as máquinas virtuais de ciência de dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Conectar-se à DSVM

Após o DSVM estiver criado, selecione o **executar** lista suspensa no Azure Notebooks painel do projeto e selecione a instância apropriada do DSVM. A lista suspensa mostra as instâncias do DSVM se as seguintes condições forem verdadeiras:

- Você está conectado ao Azure Notebooks com uma conta que usa o AAD (Azure Active Directory), por exemplo, uma conta corporativa.
- Sua conta está conectada a uma assinatura do Azure.
- Você tem uma ou mais máquinas virtuais na assinatura, com pelo menos acesso de leitor, que usa a máquina Virtual de ciência de dados para a imagem do Linux (Ubuntu).)

![Instâncias de Máquina Virtual de Ciência de Dados na lista suspensa no painel do projeto](media/project-compute-tier-dsvm.png)

Quando você seleciona uma instância de DSVM, o Azure Notebooks pode solicitar as credenciais de máquina específicas que usou quando criou a VM.

Se qualquer uma das condições não forem atendidos, você poderá se conectar com a DSVM. Na lista suspensa, selecione o **direta de computação** opção, que solicitará um nome (para mostrar na lista), endereço IP da VM e porta (normalmente 8000, a porta padrão para que monitora JupyterHub) e as credenciais VM:

![Prompt que coleta informações do servidor para a opção de Computação Direta](media/project-compute-tier-direct.png)

Você pode obter esses valores da página de DSVM no portal do Azure.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Acessar arquivos do Azure Notebooks do DSVM

Acesso de sistema de arquivos tem suporte para versões do DSVM 19.06.15 ou posterior. Para verificar a versão, primeiro conecte-se à sua DSVM via SSH, em seguida, execute o comando a seguir: `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` (você deve usar o endereço IP mostrado aqui). O número de versão é mostrado na saída para "version".

Para preservar a paridade de caminhos de arquivo com o **computação gratuita** camada, é possível apenas abrir um projeto ao mesmo tempo em uma DSVM. Para abrir um novo projeto, você deve desligar o projeto aberto pela primeira vez.

Quando um projeto é executado em uma máquina virtual, os arquivos são montados no diretório raiz do servidor do Jupyter (o diretório mostrado no JupyterHub), substituindo os arquivos de blocos de anotações do Azure padrão. Quando você desligar a VM usando o **desligamento** botão no bloco de anotações da interface do usuário, os blocos de anotações do Azure restaura os arquivos padrão.

![Botão de desligamento em blocos de anotações do Azure](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Criar novos usuários do DSVM

Se vários usuários compartilham um DSVM, você pode evitar o bloqueio uns aos outros, criando e usando um usuário DSVM para cada usuário do bloco de anotações:

1. Sobre o [Portal do Azure](https://portal.azure.com), navegue até sua máquina virtual.
1. Sob **suporte + solução de problemas** na margem esquerda, selecione **Redefinir senha**.
1. Insira um novo nome de usuário e senha e selecione **atualização**. (Os nomes de usuário existentes não são afetadas).
1. Repita a etapa anterior para usuários adicionais.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre DSVMs na [Introdução ao Azure dados máquinas virtuais de ciência](/azure/machine-learning/data-science-virtual-machine/overview).
