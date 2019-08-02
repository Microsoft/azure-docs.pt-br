---
title: Proteção Avançada contra Ameaças do Windows Defender com a Central de Segurança do Azure
description: Este documento apresenta a integração entre a Central de Segurança do Azure e a Proteção Avançada contra Ameaças do Windows Defender.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2018
ms.author: v-mohabe
ms.openlocfilehash: 87f5a14bcd6003ad81b663ed97e5349dcbff2a30
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296525"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Proteção Avançada contra Ameaças do Windows Defender com a Central de Segurança do Azure

A Central de Segurança do Azure está estendendo sua oferta de plataformas de proteção de cargas de trabalho de nuvem, integrando-se com a ATP ([Proteção Avançada contra Ameaças do Windows Defender](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp)).
Essa alteração traz recursos abrangentes de EDR (detecção e resposta de ponto de extremidade). Com a integração ao Windows Defender ATP, você pode detectar anormalidades. Você também pode detectar e responder a ataques avançados em pontos de extremidade de servidor monitorados pela Central de Segurança do Azure.

## <a name="windows-defender-atp-features-in-security-center"></a>Recursos do Windows Defender ATP na central de segurança

Ao usar o Windows Defender ATP, você obtém:

- **Sensores de detecção de violação de postagem de última geração**: os sensores do Windows Defender ATP para servidores Windows coletam uma grande variedade de sinais comportamentais.

- **Detecção de violação de postagem com base em análise e com o poder da nuvem**: O Windows Defender ATP se adapta rapidamente a ameaças que mudam constantemente. Ele usa análise avançada e Big Data. O Windows Defender ATP é amplificado pela potência do Gráfico de Segurança Inteligente com sinais pelo Windows, o Azure e o Office para detectar ameaças desconhecidas. Ele fornece alertas acionáveis e permite que você responda rapidamente.

- **Inteligência contra ameaças**: o Windows Defender ATP identifica ferramentas, técnicas e procedimentos de invasores. Quando os detecta, ele gera alertas. Ele usa dados gerados pelos caçadores de ameaças da Microsoft e pelas equipes de segurança, incrementados por inteligência oferecida por parceiros.

Essas funcionalidades agora estão disponíveis na Central de Segurança do Azure:

- **Integração automatizada**: o sensor do Windows Defender ATP é habilitado automaticamente para os servidores Windows que são integrados à Central de Segurança do Azure.

- **Painel de controle único**: o console da Central de Segurança do Azure mostra os alertas do Windows Defender ATP.

- **Investigação de computador detalhada**: os clientes da Central de Segurança do Azure podem acessar o console do Windows Defender ATP para realizar uma investigação detalhada a fim de revelar o escopo de uma violação.

![Central de Segurança do Azure, exibindo uma lista de alertas e informações gerais sobre cada alerta](media/security-center-wdatp/image1.png)

Você pode investigar o alerta repassando-o ao Windows Defender ATP. Lá você encontrará informações adicionais, como a árvore de processos do alerta e o gráfico de incidentes. Você também pode ver uma linha do tempo detalhada do computador, mostrando cada comportamento por um período de histórico de até seis meses.

![Página do Windows Defender ATP com informações detalhadas sobre um alerta](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Suporte à plataforma

O Windows Defender ATP na central de segurança dá suporte à detecção em sistemas operacionais Windows Server 2012 R2 e Windows Server 2016 que pertencem a uma assinatura de serviço padrão.

> [!NOTE]
> Quando você usa a central de segurança do Azure para monitorar servidores, um locatário do Windows Defender ATP é criado automaticamente e os dados do Windows Defender ATP são armazenados na Europa por padrão. Se você precisar mover seus dados para outro local, precisará entrar em contato com Suporte da Microsoft para redefinir o locatário.

## <a name="onboarding-servers-to-security-center"></a>Integração de servidores à Central de Segurança 

Para a integração de servidores à Central de segurança, clique em **Ir para a Central de Segurança do Azure para a integração de servidores** do servidor de integração do Windows Defender ATP.

1. Na folha **Integração**, selecione ou crie um workspace no qual armazenar os dados. <br>
2. Se você não conseguir ver todos os seus workspaces, pode ser devido à falta de permissões, verifique se que seu workspace está definido na camada Standard de Segurança do Azure. Para saber mais, confira [Atualizar para a camada Standard da Central de Segurança para segurança aprimorada](security-center-pricing.md).
    
3. Selecione **Adicionar servidores** para exibir instruções sobre como instalar o Microsoft Monitoring Agent. 

4. Após a integração, monitore as máquinas **Computação e aplicativos**.

   ![Integrar computadores](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-windows-defender-atp-integration"></a>Integração habilitada com o Windows Defender ATP

Para exibir se a integração do Windows Defender ATP está habilitada, selecione preços da **central** > de segurança **& configurações** > clique em sua assinatura.

  ![Gerenciamento de Política da Central de Segurança do Azure](media/security-center-wdatp/policy-management.png)

Aqui você pode ver as integrações habilitadas no momento.

  ![Página de configurações de detecção de ameaças da Central de Segurança do Azure com a integração ao Windows Defender ATP habilitada](media/security-center-wdatp/enable-integrations.png)

- Se você já integrou os servidores à camada Standard da Central de Segurança do Azure, nenhuma ação adicional é necessária. A Central de Segurança do Azure integrará automaticamente os servidores ao Windows Defender ATP. Esse processo pode levar até 24 horas.

- Se você nunca integrou os servidores à camada Standard da Central de Segurança do Azure, integre-os à Central de Segurança do Azure como de costume.

- Se você integrou os servidores por meio do Windows Defender ATP:
  - Veja a documentação para obter diretrizes sobre [como remover computadores de servidor](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Integre esses servidores à Central de Segurança do Azure.

## <a name="access-to-the-windows-defender-atp-portal"></a>Acesso ao portal do Windows Defender ATP

Siga as instruções em [Atribuir acesso de usuário ao portal](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection).

## <a name="set-the-firewall-configuration"></a>Definir a configuração do firewall

Quando um sensor do Windows Defender ATP estiver se conectando do contexto do sistema, caso você tenha um proxy ou firewall bloqueando o tráfego anônimo, verifique se o tráfego anônimo é permitido. Siga as instruções em [Permitir o acesso a URLs do serviço Windows Defender ATP no servidor proxy](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testar o recurso

Para gerar um alerta de teste benigno do Windows Defender ATP:

1. Use a Área de Trabalho Remota para acessar uma VM do Windows Server 2012 R2 ou uma VM do Windows Server 2016.  Abra uma janela de Prompt de Comando.

2. No prompt, copie e execute o comando a seguir. A janela do prompt de comando será fechada automaticamente.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Uma janela do prompt de comando com o comando acima](media/security-center-wdatp/image4.jpeg)

3. Se o comando for bem-sucedido, você verá um novo alerta no painel da Central de Segurança do Azure e no portal do Windows Defender ATP. Esse alerta pode levar alguns minutos para aparecer.

4. Para examinar o alerta na Central de Segurança, acesse **Alertas de segurança** >  **Linha de Comando do PowerShell Suspeita**.

5. Na janela de investigação, selecione o link para ir para o portal do Windows Defender ATP.

## <a name="next-steps"></a>Próximas etapas

- [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md): Saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciando recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger seus recursos do Azure.
- [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): Saiba como monitorar a integridade dos recursos do Azure.
