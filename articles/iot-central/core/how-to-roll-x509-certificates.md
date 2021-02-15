---
title: Distribuir certificados X. 509 no Azure IoT Central
description: Como distribuir certificados X. 509 com seu aplicativo IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a9e35c7d4d64279c65971dd512bcd2107dad6594
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92000065"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>Como distribuir certificados de dispositivo X. 509 no aplicativo IoT Central

Durante o ciclo de vida de sua solução de IoT, você precisará implantar certificados. Duas das principais razões para implantar certificados seria uma violação de segurança e expirações de certificado.

Se você tiver uma violação de segurança, os certificados sem interrupção serão uma prática recomendada de segurança para ajudar a proteger seu sistema. Como parte da [Metodologia Assume Breach](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), a Microsoft defende a necessidade de ter processos reativos de segurança em vigor, juntamente com medidas preventivas. Implantar seus certificados de dispositivo deve ser incluído como parte desses processos de segurança. A frequência em que você implantar seus certificados dependerá de necessidades de segurança da sua solução. Os clientes com soluções que envolvem dados altamente confidenciais podem implantar certificado diariamente, enquanto outros revertem seus certificados a cada dois anos.


## <a name="obtain-new-x509-certificates"></a>Obter novos certificados X. 509

Você pode criar seus próprios certificados X. 509 usando uma ferramenta como o OpenSSL. Essa abordagem é ótima para testar os certificados x.509, mas fornece algumas garantias em relação à segurança. Use essa abordagem somente para teste, a menos que você esteja preparado para atuar como seu próprio provedor de autoridade de certificação.

## <a name="enrollment-groups-and-security-breaches"></a>Grupos de registro e violações de segurança

Para atualizar um registro de grupo em resposta a uma violação de segurança, você deve usar a seguinte abordagem que atualiza o certificado atual imediatamente:

1. Navegue até **Administração**  no painel esquerdo e selecione **conexão de dispositivo**.

2. Selecione **grupos de registro**e selecione o nome do grupo na lista.

3. Para atualização de certificado, selecione **gerenciar primário** ou **gerenciar secundário**.

4. Adicione e verifique o certificado raiz X. 509 no grupo de registro.

   Conclua estas etapas para os certificados primários e secundários, se ambos estiverem comprometidos.

## <a name="enrollment-groups-and-certificate-expiration"></a>Grupos de registro e expiração do certificado

Se você estiver sem interrupção de certificados para lidar com expirações de certificado, use a seguinte abordagem para atualizar o certificado atual imediatamente:

1. Navegue até **Administração**  no painel esquerdo e selecione **conexão de dispositivo**.

2. Selecione **grupos de registro**e selecione o nome do grupo na lista.

3. Para atualização de certificado, selecione **gerenciar primário**.

4. Adicione e verifique o certificado raiz X. 509 no grupo de registro.

5. Posteriormente, quando o certificado secundário tiver expirado, volte e atualize esse certificado secundário.

## <a name="individual-enrollments-and-security-breaches"></a>Registros individuais e violações de segurança

Se você estiver sem interrupção de certificados em resposta a uma violação de segurança, use a seguinte abordagem para atualizar o certificado atual imediatamente:

1. Selecione **dispositivos**e selecione o dispositivo.

2. Selecione **conectar**e selecione o método conectar como **registro individual**

3. Selecione **certificados (X. 509)** como mecanismo.

    ![Gerenciar registros individuais](./media/how-to-roll-x509-certificates/certificate-update.png)

4. Para atualização de certificado, selecione o ícone de pasta para selecionar o novo certificado a ser carregado para a entrada de registro. Clique em **Salvar**.

    Conclua estas etapas para os certificados primários e secundários, se ambos estiverem comprometidos

## <a name="individual-enrollments-and-certificate-expiration"></a>Registros individuais e a expiração do certificado

Se você estiver implantando certificados para lidar com as expirações de certificado, você deve usar a configuração do certificado secundário da seguinte maneira para reduzir o tempo de inatividade para dispositivos de tentativa de provisionar.

Quando o certificado secundário se aproximar da expiração e precisar ser revertido, você poderá girar para usar a configuração primária. Girar entre os certificados primários e secundários dessa maneira reduz o tempo de inatividade para dispositivos de tentativa de provisionar.

1. Selecione **dispositivos**e selecione o dispositivo.

2. Selecione **conectar**e selecione o método conectar como **registro individual**

3. Selecione **certificados (X. 509)** como mecanismo.

    ![Gerenciar registros individuais](./media/how-to-roll-x509-certificates/certificate-update.png)

4. Para atualização de certificado secundário, selecione o ícone de pasta para selecionar o novo certificado a ser carregado para a entrada de registro. Clique em **Salvar**.

5. Posteriormente, quando o certificado primário tiver expirado, volte e atualize esse certificado primário.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como distribuir certificados X. 509 no aplicativo IoT Central do Azure, você pode se [conectar ao azure IOT central](concepts-get-connected.md).


