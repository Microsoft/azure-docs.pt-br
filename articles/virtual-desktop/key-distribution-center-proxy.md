---
title: Configurar o Kerberos centro de distribuição de chaves proxy da área de trabalho virtual do Windows-Azure
description: Como configurar um pool de hosts de área de trabalho virtual do Windows para usar um proxy de centro de distribuição de chaves Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 102ddc1c8937c66a92416ddb6d5f2d25f2a3c349
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219648"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Configurar um proxy de centro de distribuição de chaves Kerberos (versão prévia)

> [!IMPORTANT]
> Esse recurso está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendamos usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo mostrará como configurar um proxy de centro de distribuição de chaves (KDC) Kerberos (versão prévia) para seu pool de hosts. Esse proxy permite que as organizações se autentiquem com o Kerberos fora de seus limites corporativos. Por exemplo, você pode usar o proxy KDC para habilitar a autenticação de cartão inteligente para clientes externos.

## <a name="how-to-configure-the-kdc-proxy"></a>Como configurar o proxy KDC

Para configurar o proxy KDC:

1. Entre no portal do Azure como administrador.

2. Vá para a página da área de trabalho virtual do Windows.

3. Selecione o pool de hosts para o qual você deseja habilitar o proxy KDC e, em seguida, selecione **Propriedades de RDP**.

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela da página portal do Azure mostrando um usuário selecionando pools de hosts, depois o nome do pool de hosts de exemplo e as propriedades de RDP.](media/rdp-properties.png)

4. Selecione a guia **avançado** e insira um valor no seguinte formato sem espaços:

    > kdcproxyname: s:\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela mostrando a guia Avançado selecionada, com o valor inserido conforme descrito na etapa 4.](media/advanced-tab-selected.png)

5. Selecione **Salvar**.

6. O pool de hosts selecionado agora deve começar a emitir arquivos de conexão RDP com o campo kdcproxyname que você inseriu incluído.

## <a name="next-steps"></a>Próximas etapas

A função RDGateway no Serviços de Área de Trabalho Remota inclui um serviço de proxy KDC. Consulte [implantar a função de gateway RD na área de trabalho virtual do Windows](rd-gateway-role.md) para saber como configurar uma para ser um destino para a área de trabalho virtual do Windows.
