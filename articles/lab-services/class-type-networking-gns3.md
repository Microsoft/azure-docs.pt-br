---
title: Configurar um laboratório de rede com Azure Lab Services e GNS3 | Microsoft Docs
description: Saiba como configurar um laboratório usando o Azure Lab Services para ensinar a rede com o GNS3.
ms.topic: article
ms.date: 01/19/2021
ms.openlocfilehash: dec5dea13d5a89536a06da45fc57d33881a9b3ad
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500507"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>Configurar um laboratório para ensinar uma classe de rede 
Este artigo mostra como configurar uma classe que se concentra em permitir que os alunos emulem, configurem, testem e solucionem problemas de redes virtuais e reais usando o software [GNS3](https://www.gns3.com/) . 

Este artigo tem duas seções principais. A primeira seção aborda como criar o laboratório de sala de aula. A segunda seção aborda como criar o computador de modelo com a virtualização aninhada habilitada e com o GNS3 instalado e configurado.

## <a name="lab-configuration"></a>Configuração do laboratório
Para configurar este laboratório, você precisa de uma assinatura do Azure para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você pode criar uma nova conta de laboratório no Azure Lab Services ou usar uma conta existente. Consulte o tutorial a seguir para criar uma nova conta de laboratório: [tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um novo laboratório e, em seguida, aplicar as seguintes configurações:

| Tamanho da máquina virtual | Imagem |
| -------------------- | ----- | 
| Grande (Virtualização Aninhada) | Windows 10 pro, versão 1909 |

## <a name="template-machine"></a>Computador de modelo 

Após a criação do computador de modelo, inicie o computador e conecte-o para concluir as três principais tarefas a seguir. 
 
1. Prepare o computador de modelo para virtualização aninhada.
2. Instale o GNS3.
3. Crie uma VM GNS3 aninhada no Hyper-V.
4. Configure o GNS3 para usar a VM do Windows Hyper-V.
5. Adicionar dispositivos apropriados.
6. Modelo de publicação.


### <a name="prepare-template-machine-for-nested-virtualization"></a>Preparar computador de modelo para virtualização aninhada
- Siga as instruções neste [artigo](how-to-enable-nested-virtualization-template-vm.md) para preparar sua máquina virtual de modelo para a virtualização aninhada. 

### <a name="install-gns3"></a>Instalar o GNS3
- Siga as instruções para [instalar o GNS3 no Windows](https://docs.gns3.com/docs/getting-started/installation/windows).  Certifique-se de incluir a instalação da **VM GNS3** na caixa de diálogo do componente, veja abaixo.

![SelectGNS3vm](./media/class-type-networking-gns3/gns3-select-vm.png)

Eventualmente, você vai atingir a seleção de VM GNS3. Certifique-se de selecionar a opção **Hyper-V** .

![SelectHyperV](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  Esta opção baixará o script do PowerShell e os arquivos VHD para criar a VM GNS3 no Gerenciador do Hyper-V. Continue a instalação usando os valores padrão. **Quando a instalação for concluída, não inicie o GNS3**.

### <a name="create-gns3-vm"></a>Criar VM GNS3
Depois que a instalação for concluída, um arquivo zip **"GNS3.VM.Hyper-V.2.2.17.zip"** será baixado para a mesma pasta que o arquivo de instalação, que contém as unidades e o script do PowerShell para criar a VM do Hyper-V.
- **Extraia tudo** no GNS3.VM.Hyper-V.2.2.17.zip.  Essa ação irá extrair as unidades e o script do PowerShell para criar a VM.
- **Execute com o PowerShell** no script do powershell "create-vm.ps1" clicando com o botão direito do mouse no arquivo.
- Uma solicitação de alteração de política de execução pode aparecer. Insira "Y" para executar o script.

![PSExecutionPolicy](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- Depois que o script for concluído, você poderá confirmar se a VM "VM GNS3" foi criada no Gerenciador do Hyper-V.

### <a name="configure-gns3-to-use-hyper-v-vm"></a>Configurar o GNS3 para usar a VM do Hyper-V
Agora que o GNS3 está instalado e a VM GNS3 foi adicionada, inicie o GNS3 para vincular os dois juntos.  O [Assistente de instalação do GNS3 será iniciado automaticamente.](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard)  
- Use **executar dispositivos da máquina virtual.** opção.  Use os padrões para o restante do assistente até atingir a **ferramenta VMware vmrun não encontrada.** .

![VMWareError](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- Escolha **OK** e **cancele** o assistente.
- Para concluir a conexão com a VM do Hyper-V, abra a VM **Editar**  ->  **preferências**  ->  **GNS3** e selecione **habilitar a VM GNS3** e selecione a opção **Hyper-V** .
 
![EnableGNS3VMs](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>Adicionar dispositivos apropriados

Neste ponto, você desejará adicionar os dispositivos apropriados [para a classe.](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace)

### <a name="publish-template"></a>Publicar modelo

Agora que a VM de modelo está configurada corretamente e está pronta para publicação, há alguns pontos-chave a serem verificados.
- Verifique se a VM GNS3 está desligada ou desativada.  A publicação enquanto a VM ainda estiver em execução corromperá a VM.
- Fechar GNS3, publicar enquanto e executando pode levar a efeitos colaterais indesejados.
- Limpe todos os arquivos de instalação ou outros arquivos desnecessários.

## <a name="cost"></a>Custo  

Se desejar estimar o custo desse laboratório, você poderá usar o seguinte exemplo: 
 
Para uma classe de 25 alunos com 20 horas de tempo de aula agendada e 10 horas de cota para tarefas ou atividades, o preço do laboratório seria: 

25 alunos * (20 + 10) horas * 84 unidades de laboratório * 0, 1 USD por hora = 630 USD. 

**Importante:** A estimativa de custo é apenas para fins de exemplo.  Para obter detalhes atuais sobre preços, consulte [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Este artigo descreveu as etapas para criar um laboratório para a configuração de rede usando o GNS3.

## <a name="next-steps"></a>Próximas etapas
As próximas etapas são comuns à configuração de qualquer laboratório:

- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Enviar links de registro por email para alunos](how-to-configure-student-usage.md#send-invitations-to-users).