---
title: 'Versão prévia: implantar uma VM de inicialização confiável'
description: Implante uma VM que usa inicialização confiável.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: f5e361d32cf2ab436f92ce2ca86a054a6dd3337e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553741"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Implantar uma VM com inicialização confiável habilitada (visualização)

A [inicialização confiável](trusted-launch.md) é uma maneira de melhorar a segurança das VMs de [geração 2](generation-2.md) . O lançamento confiável protege contra técnicas de ataque avançadas e persistentes combinando tecnologias de infraestrutura como o vTPM e a inicialização segura.

> [!IMPORTANT]
> A inicialização confiável está atualmente em visualização pública.
> 
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
>
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-using-the-portal"></a>Implantar usando o portal

Crie uma máquina virtual com a inicialização confiável habilitada.

1. Entre no [Portal](https://aka.ms/TL_preview) do Azure.
1. Pesquisar **máquinas virtuais**.
1. Em **Serviços**, selecione **Máquinas virtuais**.
1. Na página **máquinas virtuais** , selecione **Adicionar** e, em seguida, selecione **máquina virtual**.
1. Em **detalhes do projeto**, verifique se a assinatura correta está selecionada.
1. Em **grupo de recursos**, selecione **criar novo** e digite um nome para o grupo de recursos ou selecione um grupo de recursos existente no menu suspenso.
1. Em **detalhes da instância**, digite um nome para o nome da máquina virtual e escolha uma região que ofereça suporte à [inicialização confiável](trusted-launch.md#public-preview-limitations).
1. Em **imagem**, selecione uma [imagem que ofereça suporte à inicialização confiável](trusted-launch.md#public-preview-limitations). Você pode ver apenas a versão de Gen 1 da imagem, tudo bem, vá para a próxima etapa.
1. Alterne para a guia **avançado** selecionando-o na parte superior da página.
1. Role para baixo até a seção **geração de VM** e selecione **Gen 2**.
1. Ainda na guia **avançado** , role para baixo até **inicialização confiável** e marque a caixa de seleção **inicialização confiável** . Isso fará mais duas opções aparecendo-Secure boot e vTPM. Selecione as opções apropriadas para sua implantação.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="Captura de tela mostrando as opções de inicialização confiável.":::

1. Volte para a guia **noções básicas** , em **imagem**, e verifique se você vê a seguinte mensagem: **esta imagem dá suporte à versão prévia de inicialização confiável. Configure na guia Avançado**. A imagem Gen 2 agora deve ser selecionada.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Captura de tela mostrando a mensagem confirmando que esta é uma imagem Gen2 que dá suporte à inicialização confiável.":::

1.  Selecione um tamanho de VM que ofereça suporte à inicialização confiável. Consulte a lista de [tamanhos com suporte](trusted-launch.md#public-preview-limitations).
1.  Preencha as informações de **conta de administrador** e **as regras de porta de entrada**.
1.  Na parte inferior da página, selecione **revisar + criar**
1.  Na página **criar uma máquina virtual** , você pode ver os detalhes sobre a VM que você está prestes a implantar. Quando estiver pronto, selecione **Criar**.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Sceenshot da página validação, mostrando que as opções de inicialização confiáveis estão incluídas.":::


Levará alguns minutos para que sua VM seja implantada. 

## <a name="deploy-using-a-template"></a>Implantar usando um modelo

Você pode implantar VMs de inicialização confiáveis usando um modelo de início rápido:

**Linux**:    
[![Implantar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Implantar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Exibir e atualizar

Você pode exibir a configuração de inicialização confiável para uma VM existente visitando a página de **visão geral** para a VM no Portal.

Para alterar a configuração de inicialização confiável, no menu à esquerda, selecione **configuração** na seção **configurações** . Você pode habilitar ou desabilitar a inicialização segura e o vTPM da seção de **inicialização confiável** . Selecione **salvar** na parte superior da página quando terminar. 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Captura de tela de como alterar a configuração de inicialização confiável.":::

Se a VM estiver em execução, você receberá uma mensagem informando que a VM será reiniciada para aplicar a configuração de inicialização confiável modificada. Selecione **Sim** e aguarde até que a VM seja reiniciada para que as alterações entrem em vigor.


## <a name="verify-secure-boot-and-vtpm"></a>Verificar a inicialização segura e o vTPM

Você pode validar se a inicialização segura e o vTPM estão habilitados na máquina virtual.
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: validar se a inicialização segura estiver em execução

SSH para a VM e, em seguida, execute o seguinte comando: 

```bash
mokutil --sb-state
```

Se a inicialização segura for habilitada, o comando retornará:
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: validar se o vTPM estiver habilitado

SSH em sua VM. Verifique se o dispositivo tpm0 está presente: 

```bash
ls /dev/tpm0
```

Se vTPM estiver habilitado, o comando retornará:

```output
/dev/tpm0
```

Se vTPM estiver desabilitado, o comando retornará:

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: validar se a inicialização segura está em execução

Conecte-se à VM usando a área de trabalho remota e, em seguida, execute `msinfo32.exe` .

No painel direito, verifique se o estado de inicialização segura está **ativado**.

## <a name="enable-the-azure-security-center-experience"></a>Habilitar a experiência da central de segurança do Azure

Para habilitar a central de segurança do Azure a exibir informações sobre suas VMs de inicialização confiáveis, você precisa habilitar várias políticas. A maneira mais fácil de habilitar as políticas é implantando esse [modelo do Resource Manager](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) em sua assinatura. 

Selecione o botão abaixo para implantar as políticas para sua assinatura:

[![Implantar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

O modelo precisa ser implantado apenas uma vez por assinatura. Ele instala `GuestAttestation` e `AzureSecurity` extensões automaticamente em todas as VMs com suporte. Se você receber erros, tente reimplantar o modelo novamente.

Para obter vTPM e recomendações de inicialização segura para VMs de inicialização confiáveis, consulte [Adicionar uma iniciativa personalizada à sua assinatura](https://docs.microsoft.com/azure/security-center/custom-security-policies#to-add-a-custom-initiative-to-your-subscription).
 
## <a name="sign-things-for-secure-boot-on-linux"></a>Assinar coisas para inicialização segura no Linux

Em alguns casos, talvez seja necessário assinar coisas para inicialização segura de UEFI.  Por exemplo, talvez seja necessário percorrer [como assinar coisas para inicialização segura](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) para o Ubuntu. Nesses casos, você precisa inserir as chaves de registro do utilitário MOK para sua VM. Para fazer isso, você precisa usar o console serial do Azure para acessar o utilitário MOK.

1. Habilite o console serial do Azure para Linux. Para obter mais informações, consulte [console serial para Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/serial-console-linux).
1. Faça logon no [Portal do Azure](https://portal.azure.com).
1. Pesquise **máquinas virtuais** e selecione sua VM na lista.
1. No menu à esquerda, em **suporte + solução de problemas**, selecione **console serial**. Uma página será aberta à direita, com o console serial.
1. Faça logon na VM usando o console serial do Azure. Para **logon**, insira o nome de usuário que você usou ao criar a VM. Por exemplo, *azureuser*. Quando solicitado, insira a senha associada ao nome de usuário.
1. Quando você estiver conectado, use `mokutil` para importar o arquivo de chave pública `.der` .

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. Reinicialize o computador do console serial do Azure digitando `sudo reboot` . Uma contagem regressiva de 10 segundos será iniciada.
1. Pressione a tecla para cima ou para baixo para interromper a contagem regressiva e aguarde no modo de console UEFI. Se o temporizador não for interrompido, o processo de inicialização continuará e todas as alterações de MOK serão perdidas.
1. Selecione a ação apropriada no menu do utilitário MOK.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Captura de tela mostrando as opções disponíveis no menu de gerenciamento do MOK no console serial.":::


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre VMs de [inicialização confiável](trusted-launch.md) e de [geração 2](generation-2.md) .
