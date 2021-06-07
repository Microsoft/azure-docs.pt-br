---
title: Configurar um laboratório voltado para o aprendizado profundo usando o Azure Lab Services | Microsoft Docs
description: Saiba como configurar um laboratório voltado para o aprendizado profundo no NLP (processamento de linguagem natural) usando Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 01199e76b7b9cb1a6360b5aba010d0cfd8936c23
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91251449"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Configurar um laboratório voltado para o aprendizado profundo em processamento em idioma natural usando o Azure Lab Services
Este artigo mostra como configurar um laboratório voltado para o aprendizado profundo em NLP (processamento de idioma natural) usando o Azure Lab Services. O NLP (processamento de idioma natural) é uma forma de IA (inteligência artificial) que habilita os computadores com tradução, reconhecimento de fala e outras funcionalidades de reconhecimento vocal.  

Os alunos que fazem uma aula de NLP obtêm uma VM (máquina virtual) do Linux para saber como aplicar algoritmos de rede neural para desenvolver modelos de aprendizado profundo que são usados para analisar a linguagem humana escrita. 

## <a name="lab-configuration"></a>Configuração do laboratório
Para configurar este laboratório, você precisa de uma assinatura do Azure para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de ter uma assinatura do Azure, você poderá criar uma conta de laboratório no Azure Lab Services ou usar uma existente. Confira o tutorial a seguir para criar uma conta de laboratório: [Tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).
 
Depois de criar a conta de laboratório, habilite as seguintes configurações nela: 

| Configuração da conta de laboratório | Instruções |
| ----------- | ------------ |  
| Imagens do Marketplace | Habilite a imagem da Máquina Virtual de Ciência de Dados para Linux (Ubuntu) para uso em sua conta de laboratório.  Confira o seguinte artigo para obter instruções: [Especificar imagens do marketplace disponíveis para os criadores de laboratório](specify-marketplace-images.md). | 

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um laboratório e aplicar as seguintes configurações:

| Configurações do laboratório | Valor/instruções | 
| ------------ | ------------------ |
| Tamanho da VM (máquina virtual) | **GPU pequena (computação)**. Esse tamanho é mais adequado para aplicativos com uso intensivo de computação e de rede, como Inteligência Artificial e Aprendizado Profundo. |
| Imagem da VM | [Máquina Virtual de Ciência de Dados para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Essa imagem fornece estruturas e ferramentas de aprendizado profundo para machine learning e ciência de dados. Para exibir a lista completa de ferramentas instaladas nessa imagem, confira o seguinte artigo: [O que está incluso na DSVM?](../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Habilitar a conexão de área de trabalho remota | <p>A imagem da Ciência de Dados já está configurada para usar o X2Go para que professores e alunos possam se conectar usando uma área de trabalho remota da GUI.  O X2Go *não* exige que a configuração **Habilitar conexão de área de trabalho remota** esteja habilitada.  Essa configuração só precisa ser habilitada se você optar por usar o RDP.

>**Importante**: Embora seja recomendável usar o X2Go com a imagem de Ciência de Dados, se você optar por usar o RDP, será necessário se conectar à VM do Linux usando o SSH pela primeira vez e instalar os pacotes RDP e GUI.  Em seguida, você/os alunos pode se conectar à VM do Linux usando o RDP posteriormente.  Para obter mais informações, consulte [Habilitar área de trabalho remota gráfica para VMs do Linux](how-to-enable-remote-desktop-linux.md).

A imagem da Máquina Virtual de Ciência de Dados para Linux fornece as estruturas e ferramentas de aprendizado profundo necessárias para esse tipo de classe. Como resultado, após a criação do computador de modelo, você não precisará personalizá-la ainda mais. Ela pode ser publicada para os alunos usarem. Selecione o botão **Publicar** na página de modelo para publicar o modelo no laboratório.  

## <a name="cost"></a>Custo
Se desejar estimar o custo desse laboratório, você poderá usar o seguinte exemplo: 

Para uma classe de 25 alunos com 20 horas de tempo de aula agendada e 10 horas de cota para tarefas ou atividades, o preço do laboratório seria: 25 alunos * (20 + 10) horas * 139 Unidades de Laboratório * 0,01 USD por hora = 1042,5 USD

Para obter mais detalhes sobre os preços, confira [Preço do Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Este artigo descreveu as etapas para criar um laboratório para a classe de processamento de idioma natural. Você pode usar uma configuração semelhante para outras classes de aprendizado profundo.

## <a name="next-steps"></a>Próximas etapas
As próximas etapas são comuns à configuração de qualquer laboratório:

- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Enviar links de registro por email para alunos](how-to-configure-student-usage.md#send-invitations-to-users). 

