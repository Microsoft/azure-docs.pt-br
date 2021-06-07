---
title: Automatizar a instalação do runtime de integração auto-hospedada usando scripts do PowerShell local
description: Para automatizar a instalação do runtime de integração auto-hospedada em computadores locais.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/09/2020
ms.openlocfilehash: 8cbe54a23cb1c8b55afd86a18b51c0e392c3f78a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376200"
---
# <a name="automating-self-hosted-integration-runtime-installation-using-local-powershell-scripts"></a>Automatizar a instalação do runtime de integração auto-hospedada usando scripts do PowerShell local
Para automatizar a instalação do runtime de integração auto-hospedada em computadores locais (além de VMs do Azure em que é possível aproveitar o modelo do Resource Manager em vez disso), é possível scripts do PowerShell local. Este artigo apresenta dois scripts que podem ser usados.

## <a name="prerequisites"></a>Pré-requisitos

* Iniciar o PowerShell no seu computador local. Para executar os scripts, é preciso escolher **Executar como administrador**.
* [Baixe](https://www.microsoft.com/download/details.aspx?id=39717) o software do runtime de integração auto-hospedada. Copie o caminho em que está o arquivo baixado. 
* Também será necessária uma **chave de autenticação** para registrar o runtime de integração auto-hospedada.
* Para automatizar atualizações manuais, é preciso ter um runtime de integração auto-hospedada pré-configurado.

## <a name="scripts-introduction"></a>Introdução a scripts 

> [!NOTE]
> Esses scripts são criados usando o [utilitário de linha de comando documentado](./create-self-hosted-integration-runtime.md#set-up-an-existing-self-hosted-ir-via-local-powershell) no runtime de integração auto-hospedada. Se necessário, é possível personalizar esses scripts adequadamente para atender às suas necessidades de automação.
> Os scripts precisam ser aplicados por nó, portanto, execute-o em todos os nós no caso de configuração de alta disponibilidade (2 ou mais nós).

* Para automatizar a configuração: Instalar e registrar um novo nó de runtime de integração auto-hospedada usando **[InstallGatewayOnLocalMachine.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/InstallGatewayOnLocalMachine.ps1)** : o script pode ser usado para instalar o nó de runtime de integração auto-hospedada e registrá-lo com uma chave de autenticação. O script aceita dois argumentos, **primeiro** especificando o local do [runtime de integração auto-hospedada](https://www.microsoft.com/download/details.aspx?id=39717) em um disco local, **segundo** especificando a **chave de autenticação** (para registrar o nó IR auto-hospedado).

* Para automatizar atualizações manuais: Atualize o nó do IR auto-hospedado com uma versão específica ou com a versão mais recente **[script-update-gateway.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/script-update-gateway.ps1)** : também é possível caso você tenha desativado a atualização automática ou queira ter mais controle das atualizações. O script pode ser usado para atualizar o nó do runtime de integração auto-hospedada para a versão mais recente ou para uma versão superior especificada (o downgrade não funciona). Ele aceita um argumento para especificar o número de versão (exemplo: -versão 3.13.6942.1). Quando nenhuma versão é especificada, ele sempre atualiza o IR auto-hospedado para a versão mais recente encontrada nos [downloads](https://www.microsoft.com/download/details.aspx?id=39717).
    > [!NOTE]
    > Somente as últimas 3 versões podem ser especificadas. Em um cenário ideal, isso é usado para atualizar um nó existente para a versão mais recente. **PRESSUPÕE-SE QUE VOCÊ TENHA UM IR DE INTEGRAÇÃO AUTO-HOSPEDADA** . 

## <a name="usage-examples"></a>Exemplos de uso

### <a name="for-automating-setup"></a>Para automatizar a configuração
1. Baixe o IR auto-hospedado [aqui](https://www.microsoft.com/download/details.aspx?id=39717). 
1. Especifique o caminho em que o MSI SHIR (arquivo de instalação) baixado está. Por exemplo, se o caminho for *C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi*, será possível usar o exemplo de linha de comando do PowerShell abaixo para essa tarefa:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\InstallGatewayOnLocalMachine.ps1 -path "C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi" -authKey "[key]"
   ```

    > [!NOTE]
    > Substitua [chave] pela chave de autenticação para registrar seu IR.
    > Substitua "nome de usuário" pelo seu nome de usuário.
    > Especifique o local do arquivo "InstallGatewayOnLocalMachine.ps1" ao executar o script. Neste exemplo, armazenamos ele na área de trabalho.

1. Se houver um IR auto-hospedado pré-instalado em seu computador, o script o desinstalará automaticamente e, em seguida, configurará um novo. Você verá a seguinte janela: ![Configurar o runtime de integração](media/self-hosted-integration-runtime-automation-scripts/integration-runtime-configure.png)

1. Quando a instalação e o registro da chave forem concluídos, você verá os resultados *Instalação bem-sucedida do gateway* e *Registro bem-sucedido do gateway* em seu PowerShell local.
        [![resultado da execução do script 1](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png#lightbox)

### <a name="for-automating-manual-updates"></a>Para automatizar atualizações manuais
Esse script é usado para atualizar/instalar + registrar o runtime de integração auto-hospedada mais recente. O script faz o seguinte:
1. Verificar a versão atual do IR auto-hospedado
2. Obter a versão mais recente ou versão especificada do argumento
3. Se houver uma versão mais recente que a versão atual:
    * baixar o msi do IR auto-hospedado
    * fazer upgrade

É possível seguir o exemplo de linha de comando abaixo para usar este script:
* Baixar e instalar o gateway mais recente:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1
   ```    
* Baixar e instalar o gateway da versão especificada:
   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1 -version 3.13.6942.1
   ``` 
   Se sua versão atual já for a mais recente, você verá o resultado a seguir, sugerindo que nenhuma atualização é necessária.   
    [![resultado da execução do script 2](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png#lightbox)