---
title: Implantar a função de gateway RD área de trabalho virtual do Windows-Azure
description: Como implantar a função de gateway RD na área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71bd7d38727d99c05a15c54e5141c613960d9050
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220812"
---
# <a name="deploy-the-rd-gateway-role-in-windows-virtual-desktop-preview"></a>Implantar a função de gateway RD na área de trabalho virtual do Windows (versão prévia)

> [!IMPORTANT]
> Esse recurso está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendamos usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo lhe dirá como usar a função de gateway de área de trabalho remota (versão prévia) para implantar Área de Trabalho Remota servidores de gateway em seu ambiente. Você pode instalar as funções de servidor em máquinas físicas ou máquinas virtuais, dependendo se você estiver criando um ambiente local, baseado em nuvem ou híbrido.

## <a name="install-the-rd-gateway-role"></a>Instalar a função de gateway de área de trabalho remota

1. Entre no servidor de destino com credenciais administrativas.

2. Em **Gerenciador do servidor**, selecione **gerenciar** e selecione **adicionar funções e recursos**. O instalador **adicionar funções e recursos** será aberto.

3. Em **Antes de começar**, selecione **Avançar**.

4. Em **Selecionar tipo de instalação**, selecione instalação baseada em **função ou recurso** e, em seguida, selecione **Avançar**.

5. Para **selecionar servidor de destino**, selecione **selecionar um servidor no pool de servidores**. Para **pool de servidores**, selecione o nome do computador local. Quando terminar, selecione **Avançar**.

6. Em **selecionar**  >  **funções** de funções de servidor, selecione **serviços de área de trabalho remota**. Quando terminar, selecione **Avançar**.

7. Em **serviços de área de trabalho remota**, selecione **Avançar.**

8. Para **selecionar serviços de função**, selecione somente **área de trabalho remota gateway** quando for solicitado a adicionar os recursos necessários, selecione **Adicionar recursos**. Quando terminar, selecione **Avançar**.

9. Para **serviços de acesso e política de rede**, selecione **Avançar**.

10. Para **função de servidor Web (IIS)**, selecione **Avançar**.

11. Para **selecionar serviços de função**, selecione **Avançar**.

12. Para **confirmar as seleções de instalação**, selecione **instalar**. Não feche o instalador enquanto o processo de instalação estiver ocorrendo.

## <a name="configure-rd-gateway-role"></a>Configurar função de gateway de área de trabalho remota

Depois que a função de gateway de área de trabalho remota for instalada, você precisará configurá-la.

Para configurar a função de gateway de área de trabalho remota:

1. Abra o **Gerenciador do servidor** e, em seguida, selecione **serviços de área de trabalho remota**.

2. Vá para **servidores**, clique com o botão direito do mouse no nome do servidor e selecione **Gerenciador de gateway de área de trabalho remota**.

3. No Gerenciador de gateway de área de trabalho remota, clique com o botão direito do mouse no nome do gateway e selecione **Propriedades**.

4. Abra a guia **certificado SSL** , selecione a bolha **importar um certificado para o gateway de área de trabalho remota** e, em seguida, selecione **procurar e importar certificado...**.

5. Selecione o nome do arquivo PFX e, em seguida, selecione **abrir**.

6. Digite a senha para o arquivo PFX quando solicitado.

7. Depois de importar o certificado e sua chave privada, a exibição deverá mostrar os atributos de chave do certificado.

>[!NOTE]
>Como a função de gateway de área de trabalho remota deve ser pública, recomendamos que você use um certificado emitido publicamente. Se você usar um certificado emitido de forma particular, precisará configurar todos os clientes com a cadeia de confiança do certificado com antecedência.

## <a name="next-steps"></a>Próximas etapas

Se você quiser adicionar alta disponibilidade à sua função de gateway de área de trabalho remota, consulte [Adicionar alta disponibilidade para a Web de área de trabalho remota e Web front de gateway](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).