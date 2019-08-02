---
title: Tutorial – Usar a Central de Segurança do Azure para VMs Linux no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá sobre os recursos da Central de Segurança do Azure para ajudar a proteger as máquinas virtuais do Linux no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/11/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 671b7de8852b0aac30e35b76d89eb4489a206f91
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708557"
---
# <a name="tutorial-use-azure-security-center-to-monitor-linux-virtual-machines"></a>Tutorial: Usar a Central de Segurança do Azure para monitorar máquinas virtuais do Linux

A Central de Segurança do Azure pode ajudá-lo a ganhar visibilidade em suas práticas de segurança de recursos do Azure. A Central de segurança oferece o monitoramento de segurança integrado. Ela pode detectar ameaças que não seriam notadas de outra forma. Neste tutorial, você saberá mais sobre a Central de Segurança do Azure e como:
 
> [!div class="checklist"]
> * Configurar a coleta de dados
> * Definir políticas de segurança
> * Exibir e corrigir problemas de integridade de configuração
> * Examinar ameaças detectadas

## <a name="security-center-overview"></a>Visão geral da Central de Segurança

A Central de Segurança do Azure identifica possíveis problemas de configuração da VM (máquina virtual) e ameaças de segurança direcionadas. Elas podem incluir VMs que não tenham grupos de segurança de rede, discos não criptografados e ataques de protocolo RDP de força bruta. Essas informações são exibidas no painel da Central de Segurança em grafos fáceis de ler.

Para acessar o painel da Central de segurança, no portal do Azure, no menu, selecione **Central de Segurança**. No painel, é possível visualizar a integridade da segurança de seu ambiente do Azure, encontrar uma lista das recomendações atuais e exibir o estado atual dos alertas de ameaça. Você pode expandir cada gráfico de alto nível para exibir mais detalhes.

![Painel da Central de Segurança](./media/tutorial-azure-security/asc-dash.png)

A Central de segurança vai além da descoberta de dados para fornecer recomendações para problemas detectados. Por exemplo, se uma VM foi implantada sem um grupo de segurança de rede, a Central de segurança exibe uma recomendação com etapas de solução que você pode tomar. Você pode obter a correção automatizada sem deixar o contexto da Central de segurança.  

![Recomendações](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Configurar a coleta de dados

Antes que você possa obter visibilidade das configurações de segurança da VM, a coleta de dados da Central de Segurança precisa ser configurada. Isso envolve ativar a coleta de dados que instala o Microsoft Monitoring Agent automaticamente em todas as VMs em sua assinatura.

1. No painel da Central de Segurança, clique em **Política de segurança** e selecione sua assinatura. 
2. Para a **Coleta de dados**, no **Provisionamento Automático** selecione **Habilitar**.
3. Em **Configuração do workspace padrão**, deixe como **Usar workspaces criados pela Central de Segurança (padrão)** .
4. Em **Eventos de Segurança** mantenha a opção padrão **Comum**.
4. Clique em **Salvar** na parte superior da página. 

O agente de coleta de dados da Central de segurança é instalado em todas as VMs, e a coleta de dados é iniciada. 

## <a name="set-up-a-security-policy"></a>Configurar uma política de segurança

Políticas de segurança são usadas para definir os itens para os quais a Central de segurança coleta dados e faz recomendações. Você pode aplicar diferentes políticas de segurança a diferentes conjuntos de recursos do Azure. Embora, por padrão, os recursos do Azure são avaliados em relação a todos os itens de política, você pode desativar itens individuais de política para todos os recursos do Azure ou para um grupo de recursos. Para obter informações detalhadas sobre as políticas de segurança da Central de Segurança, consulte [Definir políticas de segurança na Central de Segurança do Azure](../../security-center/security-center-policies.md). 

Para configurar uma política de segurança para uma assinatura inteira:

1. No painel da Central de Segurança, selecione **Política de segurança** e selecione sua assinatura.
2. Na folha **Política de segurança**, selecione **Política de segurança**. 
3. Na folha **Política de segurança – política de segurança**, habilite ou desabilite os itens da política que você deseja aplicar à assinatura.
4. Quando terminar de selecionar as configurações, selecione **Salvar** na parte superior da folha. 

![Política exclusiva](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Exibir a integridade da configuração da VM

Depois de ter ativado a coleta de dados e definir uma política de segurança, a Central de segurança começa a fornecer alertas e recomendações. À medida que as VMs são implantadas, o agente de coleta de dados é instalado. A Central de segurança é preenchida com os dados para as novas VMs. Para obter informações detalhadas sobre a integridade de configuração de VM, consulte [Proteger suas VMs na Central de segurança](../../security-center/security-center-virtual-machine-recommendations.md). 

Conforme os dados são coletados, a integridade de recursos de cada VM e os recursos do Azure relacionados são agregados. As informações são mostradas em um gráfico de fácil leitura. 

Para exibir a integridade dos recursos:

1.  No painel da Central de Segurança, em **Prevenção**, selecione **Computação**. 
2.  Na folha **Computação**, selecione **VMs e computadores**. Essa exibição fornece um resumo do status de configuração de todas as VMs.

![Computar integridade](./media/tutorial-azure-security/compute-health.png)

Para ver todas as recomendações para uma VM, selecione a VM. 

## <a name="remediate-configuration-issues"></a>Corrigir problemas de configuração

Depois que a Central de Segurança começar a ser preenchida com os dados de configuração, as recomendações serão feitas de acordo com a política de segurança configurada. Por exemplo, se uma VM tiver sido configurada sem um grupo de segurança de rede associado, uma recomendação será feita para a criação de um. 

Para ver uma lista de todas as recomendações: 

1. No painel da Central de Segurança, selecione **Recomendações**.
2. Selecione uma recomendação específica. É exibida uma lista de todos os recursos para os quais a recomendação se aplica.
3. Para aplicar uma recomendação, selecione o recurso. 
4. Siga as instruções para obter as etapas de correção. 

Em muitos casos, a Central de Segurança fornece etapas acionáveis que você pode seguir para trabalhar com a recomendação sem sair da Central de Segurança. No exemplo a seguir, a Central de segurança detecta um grupo de segurança de rede que tenha uma regra de entrada sem restrições. Na página de recomendação, você pode selecionar o botão **Editar regras de entrada**. A interface do usuário é necessária para modificar a aparência da regra. 

![Recomendações](./media/tutorial-azure-security/remediation.png)

À medida que as recomendações são corrigidas, elas são marcadas como resolvidas. 

## <a name="view-detected-threats"></a>Exibir as ameaças detectadas

Além das recomendações de configuração de recursos, a Central de Segurança fornece alertas de detecção de ameaças. O recurso de alertas de segurança agrega os dados coletados de cada VM, os logs de rede do Azure e as soluções de parceiros conectadas para detectar ameaças de segurança aos recursos do Azure. Para obter informações detalhadas sobre as funcionalidades de detecção de ameaças da Central de Segurança, consulte [Funcionalidades de detecção da Central de Segurança do Azure](../../security-center/security-center-detection-capabilities.md).

O recurso de alertas de segurança exige que o tipo de preço da Central de Segurança seja aumentado de *Gratuito* para *Standard*. Uma **avaliação gratuita** está disponível quando você muda para esse tipo de preço mais alto. 

Para alterar o tipo de preço:  

1. No painel da Central de Segurança, clique em **Política de segurança** e selecione sua assinatura.
2. Selecione **Tipo de preço**.
3. Selecione **Standard** e, em seguida, clique em **Salvar** na parte superior da folha.


Após alterar o tipo de preço, o grafo de alertas de segurança começará a ser preenchido conforme forem detectadas ameaças de segurança.

![Alertas de segurança](./media/tutorial-azure-security/security-alerts.png)

Selecione um alerta para exibir informações. Por exemplo, você pode ver uma descrição da ameaça, do tempo de detecção, de todas as tentativas de ameaça e da correção recomendada. No exemplo a seguir, um ataque de força bruta do RDP foi detectado, com 294 tentativas de RDP com falha. Uma solução recomendada é fornecida.

![Ataque de RDP](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Próximas etapas
Nesse tutorial, você configurou a Central de Segurança do Azure e, em seguida, analisou VMs na Central de Segurança. Você aprendeu como:

> [!div class="checklist"]
> * Configurar a coleta de dados
> * Definir políticas de segurança
> * Exibir e corrigir problemas de integridade de configuração
> * Examinar ameaças detectadas

Avance para o próximo tutorial para saber mais sobre como criar um pipeline de CI/CD com Jenkins, GitHub e Docker.

> [!div class="nextstepaction"]
> [Criar uma infra-estrutura de CI/CD com Jenkins, GitHub e Docker](tutorial-jenkins-github-docker-cicd.md)

