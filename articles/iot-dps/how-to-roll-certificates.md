---
title: Como implantar certificados X.509 no Serviço de Provisionamento de Dispositivos no Hub IoT do Azure | Microsoft Docs
description: Como distribuir certificados x. 509 com sua instância de serviço de provisionamento de dispositivos
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8cf5f262a758efe08ad73e2d8066ad4b736e76d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626870"
---
# <a name="how-to-roll-x509-device-certificates"></a>Como implantar certificados de dispositivo X.509

Durante o ciclo de vida de sua solução de IoT, você precisará implantar certificados. Duas das principais razões para implantar certificados seria uma violação de segurança e expirações de certificado. 

A implantação de certificados é uma prática recomendada de segurança para ajudar a proteger seu sistema no caso de uma violação. Como parte da [Metodologia Assume Breach](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), a Microsoft defende a necessidade de ter processos reativos de segurança em vigor, juntamente com medidas preventivas. Implantar seus certificados de dispositivo deve ser incluído como parte desses processos de segurança. A frequência em que você implantar seus certificados dependerá de necessidades de segurança da sua solução. Os clientes com soluções que envolvem dados altamente confidenciais podem implantar certificado diariamente, enquanto outros revertem seus certificados a cada dois anos.

Implantar certificados de dispositivo envolverá atualizar o certificado armazenado no dispositivo e IoT Hub. Depois disso, o dispositivo pode um reprovisionamento de si mesmo com o Hub IoT usando [provisionamento automático](concepts-auto-provisioning.md) normal com o Serviço de provisionamento do dispositivo.


## <a name="obtain-new-certificates"></a>Obter novos certificados

Há várias maneiras de obter novos certificados para seus dispositivos IoT. Isso inclui a obtenção de certificados de fábrica do dispositivo, gerar seus próprios certificados e contratar outras empresas para gerenciar a criação de certificado para você. 

Os certificados são assinados por cada um para formar uma cadeia de confiança de um certificado de autoridade de certificação raiz para um [certificado de folha](concepts-security.md#end-entity-leaf-certificate). Um certificado de autenticação é o certificado usado para assinar o certificado de folha no final da cadeia de confiança. Um certificado de autenticação pode ser um certificado de autoridade de certificação raiz ou um certificado intermediário na cadeia de confiança. Para saber mais, confira [Certificados X.509](concepts-security.md#x509-certificates).
 
Há duas maneiras diferentes de obter um certificado de assinatura. A primeira maneira que é recomendada para sistemas de produção, é comprar um certificado de assinatura de uma autoridade de certificação (CA) raiz. Dessa forma encadeia segurança até uma fonte confiável. 

A segunda maneira é criar seus próprios certificados X.509 usando uma ferramenta como o OpenSSL. Essa abordagem é ótima para testar os certificados x.509, mas fornece algumas garantias em relação à segurança. É recomendável que você só use essa abordagem para teste, a menos que você preparou atuar como seu próprio provedor de autoridade de certificação.
 

## <a name="roll-the-certificate-on-the-device"></a>Implantar o certificado no dispositivo

Certificados em um dispositivo devem sempre ser armazenados em um local seguro, como um [módulo de segurança de hardware (HSM)](concepts-device.md#hardware-security-module). A maneira de implantar certificados de dispositivo dependerá de como foram criados e instalados nos dispositivos em primeiro lugar. 

Se você obter seus certificados de terceiros, você deve examinar como distribuição seus certificados. O processo pode ser incluído em sua organização com eles, ou pode ser um serviço separado que oferecem. 

Se você estiver gerenciando seus próprios certificados de dispositivo, você precisará criar seu próprio pipeline para a atualização de certificados. Verifique se ambos os certificados de folha antiga e nova têm o mesmo nome comum (CN). Por ter o mesmo CN, o dispositivo pode Reprovisionar em si sem criar um registro duplicado. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Implantar o certificado no Hub IoT

O certificado do dispositivo pode ser adicionado manualmente para um Hub IoT. O certificado também pode ser automatizado usando uma instância de serviço de provisionamento de dispositivos. Neste artigo, presumiremos que uma instância do serviço de provisionamento de dispositivo está sendo usada para oferecer suporte ao provisionamento automático.

Quando um dispositivo for inicialmente provisionado por meio do provisionamento automático, ele é inicializado para cima e entra em contato com o serviço de provisionamento. O serviço de provisionamento responde ao executar uma verificação de identidade antes de criar uma identidade de dispositivo em um Hub IoT usando o certificado de folha do dispositivo como a credencial. O serviço de provisionamento, em seguida, informa o dispositivo, ele é atribuído a qual Hub IoT e o dispositivo, em seguida, usa seu certificado de folha para autenticar e conectar-se ao Hub IoT. 

Depois que um novo certificado de folha for revertido para o dispositivo, ele pode não mais se conectar ao Hub IoT porque ele está usando um novo certificado para se conectar. O Hub IoT só reconhece o dispositivo com o certificado antigo. O resultado da tentativa de conexão do dispositivo será um erro de conexão "não autorizado". Para resolver esse erro, você deve atualizar a entrada de registro do dispositivo para a conta para o novo certificado de folha do dispositivo. Em seguida, o serviço de provisionamento pode atualizar as informações de registro de dispositivo Hub IoT conforme necessário, quando o dispositivo for provisionado novamente. 

Uma possível exceção para essa falha de conexão seria um cenário que você criou um [Grupo de Registro](concepts-service.md#enrollment-group) para seu dispositivo no serviço de provisionamento. Nesse caso, se você não estiver implatando a raiz ou os certificados intermediários na cadeia de certificados do dispositivo de confiança, em seguida, o dispositivo será reconhecido se o novo certificado fizer parte da cadeia de confiança definida no grupo de registro. Se este cenário surgir como reação a uma violação de segurança, você deve pelo menos colocar na lista de bloqueios dos certificados de dispositivo específico no grupo que são considerados para serem violados. Para obter mais informações, consulte [Dispositivos específicos de lista de bloqueios em um grupo de certificado](https://docs.microsoft.com/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group).

Atualização das entradas de registro para certificados implantados é realizada na página **Gerenciar Registros**. Para acessar essa página, siga essas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até a Instância de Serviço de Provisionamento do Dispositivos no Hub IoT que tem a entrada de registro para seu dispositivo.

2. Clique em **Gerenciar registros**.

    ![Gerenciar registros](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Como tratar a atualização da entrada de registro dependerá se você estiver usando registros individuais ou registros em grupo. Além disso, os procedimentos recomendados diferem dependendo se você estiver implantando certificados devido a uma violação de segurança ou expiração do certificado. As seções a seguir descrevem como manusear essas atualizações.


## <a name="individual-enrollments-and-security-breaches"></a>Registros individuais e violações de segurança

Se você estiver implantando certificados em resposta a uma violação de segurança, você deve usar a abordagem a seguir que exclui o certificado atual imediatamente:

1. Clique em **Registros Individuais**e clique na entrada da ID de registro na lista. 

2. Clique no botão **Excluir o certificado atual** e em seguida, clique no ícone de pasta para selecionar o novo certificado a ser carregado para a entrada de registro. Clique em **Salvar** ao terminar.

    Essas etapas devem ser concluídas para o certificado primário e secundário, se ambos estiverem comprometidos.

    ![Gerenciar registros individuais](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Depois que o certificado comprometido tiver sido removido do serviço de provisionamento, o certificado ainda poderá ser usado para fazer conexões de dispositivo ao hub IoT, desde que um registro de dispositivo para ele exista. Há duas maneiras de abordar isso: 

    A primeira maneira seria acessar manualmente seu hub IoT e remover imediatamente o registro de dispositivo associado ao certificado comprometido. Em seguida, quando o dispositivo provisionar novamente com um certificado atualizado, será criado um novo registro de dispositivo.     

    ![Remova o registro de dispositivo do hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A segunda maneira seria usar o reprovisionamento de suporte para reprovisionar o dispositivo para o mesmo hub IoT. Essa abordagem pode ser usada para substituir o certificado para o registro de dispositivo no hub IoT. Para obter mais informações, confira [Como reprovisionar dispositivos](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Registros individuais e a expiração do certificado

Se você estiver implantando certificados para lidar com as expirações de certificado, você deve usar a configuração do certificado secundário da seguinte maneira para reduzir o tempo de inatividade para dispositivos de tentativa de provisionar.

Mais tarde quando o certificado secundário também se aproximar da expiração e precisar ser implantado, você pode rotacionar usando a configuração primária. Girar entre os certificados primários e secundários dessa maneira reduz o tempo de inatividade para dispositivos de tentativa de provisionar.


1. Clique em **Registros Individuais**e clique na entrada da ID de registro na lista. 

2. Clique em **Certificado Secundário** e, em seguida, clique no ícone de pasta para selecionar o novo certificado a ser carregado para a entrada de registro. Clique em **Salvar**.

    ![Gerenciar registros individuais usando o certificado secundário](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Mais tarde quando o certificado primário tiver expirado, volte e exclua o certificado primário clicando no botão **Excluir o certificado atual**.

## <a name="enrollment-groups-and-security-breaches"></a>Grupos de registro e violações de segurança

Para atualizar um registro de grupo em resposta a uma violação de segurança, você deve usar uma das abordagens a seguir que exclui o atual da autoridade de certificação, certificado raiz ou intermediário imediatamente.

#### <a name="update-compromised-root-ca-certificates"></a>Atualizar certificados de autoridade de certificação de raiz comprometido

1. Clique o **certificados** guia para sua instância de serviço de provisionamento de dispositivos.

2. Clique no certificado comprometido na lista e, em seguida, clique no botão **Excluir**. Confirme a exclusão digitando o nome do certificado e clique em **OK**. Repita esse processo para todos os certificados comprometidos.

    ![Excluir o certificado de Autoridade de Certificação](./media/how-to-roll-certificates/delete-root-cert.png)

3. Siga as etapas descritas em [Configura Certificados de Autoridade de Certificação](how-to-verify-certificates.md) para adicionar e verificar os novos certificados de autoridade de certificação raiz.

4. Clique na guia **Gerenciar inscrições** para a instância do serviço de provisionamento de dispositivos e clique na lista **Grupos de inscrição**. Clique no nome do grupo de registro na lista.

5. Clique em **Certificado de Autoridade de Certificação**e selecione o novo certificado de Autoridade de Certificação raiz. Em seguida, clique em **Salvar**. 

    ![Selecione o novo certificado de Autoridade de Certificação raiz](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Depois que o certificado comprometido tiver sido removido do serviço de provisionamento, o certificado ainda poderá ser usado para fazer conexões de dispositivo ao hub IoT, desde que registros de dispositivo para ele existam. Há duas maneiras de abordar isso: 

    A primeira maneira seria acessar manualmente seu hub IoT e remover imediatamente o registro de dispositivo associado ao certificado comprometido. Em seguida, quando os dispositivos forem provisionados novamente com certificados atualizados, um novo registro de dispositivo será criado para cada um deles.     

    ![Remova o registro de dispositivo do hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A segunda maneira seria usar o reprovisionamento de suporte para reprovisionar os dispositivos para o mesmo hub IoT. Essa abordagem pode ser usada para substituir certificados para os registros de dispositivos no hub IoT. Para obter mais informações, confira [Como reprovisionar dispositivos](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Atualize os certificados intermediários comprometidos

1. Clique em **Grupos de Registro**e, em seguida, clique no nome de grupo na lista. 

2. Clique em **Certificado Intermediário**, e **Excluir o Certificado Atual**. Clique no ícone de pasta para navegar até o novo certificado intermediário a ser carregado para o grupo de registros. Quando tiver terminado, clique em **Salvar**. Essas etapas devem ser concluídas para o certificado primário e secundário, se ambos estiverem comprometidos.

    Esse novo certificado intermediário deve ser assinado por um certificado de autoridade de certificação de raiz verificado que já foi adicionado ao serviço de provisionamento. Para saber mais, confira [Certificados X.509](concepts-security.md#x509-certificates).

    ![Gerenciar registros individuais](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Depois que o certificado comprometido tiver sido removido do serviço de provisionamento, o certificado ainda poderá ser usado para fazer conexões de dispositivo ao hub IoT, desde que registros de dispositivo para ele existam. Há duas maneiras de abordar isso: 

    A primeira maneira seria acessar manualmente seu hub IoT e remover imediatamente o registro de dispositivo associado ao certificado comprometido. Em seguida, quando os dispositivos forem provisionados novamente com certificados atualizados, um novo registro de dispositivo será criado para cada um deles.     

    ![Remova o registro de dispositivo do hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A segunda maneira seria usar o reprovisionamento de suporte para reprovisionar os dispositivos para o mesmo hub IoT. Essa abordagem pode ser usada para substituir certificados para os registros de dispositivos no hub IoT. Para obter mais informações, confira [Como reprovisionar dispositivos](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Grupos de registro e expiração do certificado

Se você estiver implantando certificados para lidar com as expirações de certificado, você deve usar a configuração do certificado secundário da seguinte maneira para garantir que não haja tempo de inatividade para dispositivos de tentativa de provisionar.

Mais tarde quando o certificado secundário também se aproximar da expiração e precisar ser implantado, você pode rotacionar usando a configuração primária. Girar entre os certificados primários e secundários dessa maneira garante o tempo de inatividade para dispositivos de tentativa de provisionar. 

#### <a name="update-expiring-root-ca-certificates"></a>Atualizar certificados de autoridade de certificação raiz expirando

1. Siga as etapas descritas em [Configura Certificados de Autoridade de Certificação](how-to-verify-certificates.md) para adicionar e verificar os novos certificados de autoridade de certificação raiz.

2. Clique na guia **Gerenciar inscrições** para a instância do serviço de provisionamento de dispositivos e clique na lista **Grupos de inscrição**. Clique no nome do grupo de registro na lista.

3. Clique em **Certificado de Autoridade de Certificação**e selecione o novo certificado de autoridade de certificação raiz na configuração do **Certificado Secundário**. Em seguida, clique em **Salvar**. 

    ![Selecione o novo certificado de Autoridade de Certificação raiz](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Posteriormente, quando o certificado principal expirar, clique na guia **Certificados** para a instância do serviço de provisionamento de dispositivo. Clique no certificado comprometido na lista e, em seguida, clique no botão **Excluir**. Confirme a exclusão digitando o nome do certificado e clique em **OK**.

    ![Excluir o certificado de Autoridade de Certificação](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Mesclar certificados intermediários em expiração


1. Clique em **Grupos de Registro**e, em seguida, clique no nome de grupo na lista. 

2. Clique em **Certificado Secundário** e, em seguida, clique no ícone de pasta para selecionar o novo certificado a ser carregado para a entrada de registro. Clique em **Salvar**.

    Esse novo certificado intermediário deve ser assinado por um certificado de autoridade de certificação de raiz verificado que já foi adicionado ao serviço de provisionamento. Para saber mais, confira [Certificados X.509](concepts-security.md#x509-certificates).

   ![Gerenciar registros individuais usando o certificado secundário](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Mais tarde quando o certificado primário tiver expirado, volte e exclua o certificado primário clicando no botão **Excluir o certificado atual**.


## <a name="reprovision-the-device"></a>Reprovisionar o dispositivo

Depois que o certificado for lançado no dispositivo e no Serviço de provisionamento de dispositivos, o dispositivo poderá ser reprovado entrando em contato com o serviço de provisionamento de dispositivos. 

Uma maneira fácil de programação de dispositivos para reprovisionar é programar o dispositivo para contatar o serviço de provisionamento para percorrer o fluxo de provisionamento se o dispositivo recebe um erro "não autorizado" de tentar se conectar ao hub IoT.

Outra maneira são os antigos e os novos certificados para que seja válido a uma sobreposição curta e usar o hub IoT para enviar um comando para dispositivos registrá-los novamente por meio do serviço de provisionamento para atualizar as informações de conexão do Hub IoT. Como cada dispositivo pode processar comandos de forma diferente, você terá que programar seu dispositivo para saber o que fazer quando o comando é invocado. Há várias maneiras que você pode comandar o seu dispositivo por meio do Hub IoT, e é recomendável usar [métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md) ou [trabalhos](../iot-hub/iot-hub-devguide-jobs.md) para iniciar o processo.

Depois que o reprovisionamento for concluído, os dispositivos poderão se conectar ao Hub IoT usando os novos certificados.


## <a name="blacklist-certificates"></a>Certificados de lista de bloqueios

Em resposta a uma violação de segurança, talvez seja necessário um certificado de dispositivo. Para um certificado de dispositivo de lista de bloqueios, desabilite a entrada de registro para o dispositivo/certificado de destino. Para obter mais informações, consulte dispositivos na lista de bloqueios no artigo [Gerenciar o Cancelamento do Registro](how-to-revoke-device-access-portal.md).

Depois que um certificado é incluído como parte de uma entrada de registro desabilitado, qualquer tentativa de registrar com um hub IoT usando esses certificados falhará mesmo se ela estiver habilitada como parte de outra entrada de registro.
 



## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre os certificados x. 509 no serviço de provisionamento de dispositivos, consulte [Segurança](concepts-security.md) 
- Para saber como fazer uma prova de posse para certificados de autoridade de certificação X.509 com o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure, consulte [Como verificar os certificados](how-to-verify-certificates.md)
- Para saber mais sobre como usar o portal para criar um grupo de registro, consulte [Gerenciando registros de dispositivos com o portal do Azure](how-to-manage-enrollments.md).










