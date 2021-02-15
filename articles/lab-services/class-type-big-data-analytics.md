---
title: Configurar um laboratório para ensinar Big Data análises usando Azure Lab Services | Microsoft Docs
description: Saiba como configurar um laboratório para ensinar a análise de Big Data usando a implantação do Docker da Hortonworks Data Platform (HDP).
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 5eb9cd00350c41645d4427e30a6f25a6c163358c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659889"
---
# <a name="set-up-a-lab-for-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Configurar um laboratório para análise de Big Data usando a implantação do Docker da plataforma de dados HortonWorks

Este artigo mostra como configurar um laboratório para ensinar uma classe de análise de Big Data.  Com esse tipo de classe, os alunos aprendem como lidar com grandes volumes de dados e aplicam algoritmos de aprendizado estatísticos e de máquina para gerar informações de dados.  Um objetivo fundamental para os alunos é aprender a usar as ferramentas de análise de dados, como o [pacote de software livre Apache Hadoop](https://hadoop.apache.org/) , que fornece ferramentas para armazenar, gerenciar e processar Big Data.

Neste laboratório, os alunos usarão uma versão comercial popular do Hadoop fornecida pelo [Cloudera](https://www.cloudera.com/), chamada [HDP (Hortonworks Data Platform)](https://www.cloudera.com/products/hdp.html).  Especificamente, os alunos usarão o [HDP sandbox 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) , que é uma versão simplificada e fácil de usar da plataforma que é gratuita de custo e voltada para aprendizado e experimentação.  Embora essa classe possa usar VMS (máquinas virtuais) do Windows ou Linux com a área restrita HDP implantada, este artigo mostrará como usar o Windows.

Outro aspecto interessante desse laboratório é que implantaremos a HDP sandbox nas VMs do laboratório usando contêineres do [Docker](https://www.docker.com/) .  Cada contêiner do Docker fornece seu próprio ambiente isolado para que os aplicativos de software sejam executados dentro do.  Conceitualmente, os contêineres do Docker são como VMs aninhadas e podem ser usados para implantar e executar facilmente uma ampla variedade de aplicativos de software com base em imagens de contêiner fornecidas no [Hub do Docker](https://www.docker.com/products/docker-hub).  O script de implantação do Cloudera para a área restrita do HDP obtém automaticamente a [imagem do Docker do HDP sandbox 3.0.1](https://hub.docker.com/r/hortonworks/sandbox-hdp) do Hub do Docker e executa dois contêineres do Docker:
  - área restrita-HDP
  - área restrita-proxy

## <a name="lab-configuration"></a>Configuração do laboratório

Para configurar este laboratório, você precisa de uma assinatura do Azure e uma conta de laboratório para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você poderá criar uma nova conta de laboratório no Azure Lab Services. Para obter mais informações sobre como criar uma nova conta de laboratório, consulte [tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).  Você também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Configurações de conta do laboratório

Habilite as configurações descritas na tabela a seguir para a conta do laboratório. Para obter mais informações sobre como habilitar imagens do Marketplace, consulte [especificar imagens do Marketplace disponíveis para criadores de laboratório](./specify-marketplace-images.md).

| Configuração da conta de laboratório | Instruções |
| ------------------- | ------------ |
|Imagem do Marketplace| Habilite a imagem do Windows 10 pro para uso em sua conta de laboratório.|

### <a name="lab-settings"></a>Configurações do laboratório

Use as configurações na tabela abaixo ao configurar um laboratório de sala de aula.  Para obter mais informações sobre como criar um laboratório de sala de aula, consulte [configurar um tutorial de laboratório de sala de aula](tutorial-setup-classroom-lab.md).

| Configurações do laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da Máquina Virtual| Médio (virtualização aninhada). Esse tamanho de VM é mais adequado para bancos de dados relacionais, cache na memória e análise.  Este tamanho também dá suporte à virtualização aninhada.|  
|Imagem de máquina virtual| Windows 10 Pro|

> [!NOTE] 
> Precisamos usar a mídia média (virtualização aninhada), já que a implantação do HDP sandbox usando o Docker requer:
>   - Windows Hyper-V com virtualização aninhada
>   - Pelo menos 10 GB de RAM

## <a name="template-machine-configuration"></a>Configuração da máquina de modelo

Para configurar o computador do modelo, iremos:
- Instalar o Docker
- Implantar HDP sandbox
- Usar o PowerShell e o Windows Agendador de Tarefas para iniciar automaticamente os contêineres do Docker

### <a name="install-docker"></a>Instalar o Docker

As etapas nesta seção são baseadas nas [instruções do Cloudera para a implantação com contêineres do Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html). 

Para usar contêineres do Docker, primeiro você deve instalar o Docker desktop na VM do modelo:

1. Siga as etapas na [seção pré-requisitos](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) para instalar o [Docker for Windows](https://docs.docker.com/docker-for-windows/install/). 

    > [!IMPORTANT] 
    > Verifique se a opção de configuração **usar contêineres do Windows em vez de contêineres do Linux** foi deixada desmarcada.

1. Verifique se os **contêineres do Windows e os recursos do Hyper-V** estão ativados.
   ![Ativar ou desativar recursos do Windows](./media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Siga as etapas na seção [memória para Windows](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) para definir a configuração de memória do Docker.

    > [!WARNING]
    > Se você marcar inadvertidamente a opção **usar contêineres do Windows em vez de contêineres do Linux** ao instalar o Docker, você não verá as definições de configuração de memória.  Para corrigir isso, você pode alternar para o uso de contêineres do Linux [clicando no ícone do Docker na bandeja do sistema do Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog); Quando o menu do Docker Desktop for aberto, selecione **alternar para contêineres do Linux**.
 
### <a name="deploy-hdp-sandbox"></a>Implantar HDP sandbox

Nesta seção, você implantará a área restrita do HDP e acessará também a área restrita do HDP usando o navegador.

1. Verifique se você instalou o [git bash](https://gitforwindows.org/) , conforme listado na [seção pré-requisitos](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) do guia, pois isso é recomendado para concluir as próximas etapas.

1. Usando o [Guia de implantação e instalação do Cloudera para o Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html), conclua as etapas nas seções a seguir:
   
   -    Implantar HDP sandbox
   -    Verificar a área restrita do HDP

    > [!WARNING] 
    > Ao baixar o arquivo. zip mais recente para HDP, certifique-se de *não* salvar o arquivo. zip em um caminho de diretório que inclua espaço em branco.

    > [!NOTE] 
    > Se você receber uma exceção durante a implantação informando que a **unidade não foi compartilhada**, você precisará compartilhar sua unidade C com o Docker para que os contêineres do Linux do HDP possam acessar arquivos locais do Windows.  Para corrigir isso, [clique no ícone do Docker na bandeja do sistema do Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) para abrir o menu do Docker desktop e selecione **configurações**.  Quando a caixa **de diálogo Configurações do Docker** for aberta, selecione **recursos > compartilhamento de arquivos** e verifique a unidade **C** .  Em seguida, você pode repetir as etapas para implantar a área restrita do HDP.

1. Depois que os contêineres do Docker para a área restrita do HDP são implantados e executados, você pode acessar o ambiente iniciando o navegador e seguindo as instruções do Cloudera para abrir a [página inicial da área restrita](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) e iniciar o painel do HDP.

    > [!NOTE] 
    > Essas instruções pressupõem que você primeiro mapeou o endereço IP local do ambiente de área restrita para o sandbox-hdp.hortonworks.com no arquivo de host em sua VM de modelo.  Se você **não** fizer esse mapeamento, poderá acessar a página de boas-vindas da área restrita navegando até `http://localhost:8080` .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Iniciar automaticamente os contêineres do Docker quando os alunos fizerem logon

Para fornecer uma experiência fácil de usar para alunos, usaremos um script do PowerShell que automaticamente:
  - Inicia os contêineres do Docker da área restrita do HDP quando um aluno é iniciado e se conecta à sua VM do laboratório.
  - Inicia o navegador e navega para a página de boas-vindas da área restrita.
Também usaremos o Windows Agendador de Tarefas para executar esse script automaticamente quando um aluno fizer logon em sua VM.
Para configurar isso, siga estas etapas: [script de análise de Big data](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Estimativa de custo

Se você quiser estimar o custo deste laboratório, poderá usar o exemplo a seguir.

Para uma classe de 25 alunos com 20 horas de tempo de aula agendada e 10 horas de cota para tarefas ou atividades, o preço do laboratório seria:
  - 25 alunos * (20 + 10) horas * 55 unidades de laboratório * 0, 1 USD por hora = 412,50 USD

Para obter mais detalhes sobre os preços, confira [Preço do Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão

Este artigo descreveu as etapas necessárias para criar um laboratório para uma classe de análise de Big Data que usa a plataforma de dados Hortonworks implantada com o Docker.  A configuração para esse tipo de classe pode ser usada para classes de análise de dados semelhantes.  Essa configuração também pode ser aplicável a outros tipos de classes que usam o Docker para implantação.

## <a name="next-steps"></a>Próximas etapas

As próximas etapas são comuns à configuração de qualquer laboratório.

- [Criar e gerenciar um modelo](how-to-create-manage-template.md)
- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de registro de email para alunos](how-to-configure-student-usage.md#send-invitations-to-users)