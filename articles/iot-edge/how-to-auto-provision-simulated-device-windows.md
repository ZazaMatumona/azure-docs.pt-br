---
title: Provisionar dispositivos Windows automaticamente com DPS-Azure IoT Edge | Microsoft Docs
description: Usar um dispositivo simulado em seu computador Windows para testar o provisionamento automático de dispositivos para o Azure IoT Edge com o serviço de provisionamento de dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8278d9f2129ab8b213cf1b561f4b82b56dffc8da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82131032"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Criar e provisionar um dispositivo de IoT Edge simulado com um TPM virtual no Windows

Azure IoT Edge dispositivos podem ser provisionados automaticamente usando o serviço de [provisionamento de dispositivos](../iot-dps/index.yml) , assim como os dispositivos que não estão habilitados para a borda. Se você não estiver familiarizado com o processo de provisionamento automático, examine os [conceitos de provisionamento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

O DPS dá suporte a atestado de chave simétrica para dispositivos IoT Edge no registro individual e no registro de grupo. Para o registro de grupo, se você marcar a opção "é IoT Edge dispositivo" como verdadeira no atestado de chave simétrica, todos os dispositivos registrados nesse grupo de registro serão marcados como IoT Edge dispositivos.

Este artigo mostra como testar o provisionamento automático em um dispositivo IoT Edge simulado com as seguintes etapas:

* Criar uma nova instância para o Serviço de Provisionamento de Dispositivos (DPS) no Hub IoT.
* Crie um dispositivo simulado em seu computador Windows com um simulado Trusted Platform Module (TPM) para segurança de hardware.
* Crie um registro individual para o dispositivo.
* Instale o runtime do IoT Edge e conecte o dispositivo ao Hub IoT.

> [!TIP]
> Este artigo descreve como testar o provisionamento automático usando o atestado de TPM em dispositivos virtuais, mas grande parte dele se aplica ao usar o hardware TPM físico também.

## <a name="prerequisites"></a>Pré-requisitos

* Computador de desenvolvimento do Windows. Este artigo usa Windows 10.
* Um Hub IoT ativo.

> [!NOTE]
> O TPM 2,0 é necessário ao usar o atestado de TPM com o DPS e só pode ser usado para criar registros individuais, não de grupo.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT

Criar uma nova instância do Serviço de Provisionamento de Dispositivos no Hub IoT no Microsoft Azure e vincular ao seu hub IoT. Você pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de executar o Serviço de Provisionamento de Dispositivo, copie o valor do **Escopo de ID** da página de visão geral. Você usa esse valor ao configurar o runtime do Azure IoT Edge.

> [!TIP]
> Se você estiver usando um dispositivo TPM físico, você precisará determinar a **chave de endosso**, que é exclusiva de cada chip TPM e é obtida do fabricante do chip TPM associado a ela. Você pode derivar uma **ID de registro** exclusiva para seu dispositivo TPM, por exemplo, criando um hash SHA-256 da chave de endosso.
>
> Siga as instruções no artigo [como gerenciar registros de dispositivo com o portal do Azure](../iot-dps/how-to-manage-enrollments.md) para criar seu registro no DPS e continue com a seção [instalar o IOT Edge Runtime](#install-the-iot-edge-runtime) neste artigo para continuar.

## <a name="simulate-a-tpm-device"></a>Simular um dispositivo TPM

Crie um dispositivo TPM simulado em sua máquina de desenvolvimento do Windows. Recupere a **ID de registro** e a **chave de endosso** para seu dispositivo e use-as para criar uma entrada de registro individual no DPS.

Ao criar uma inscrição no DPS, tem a oportunidade de declarar um **Estado inicial do dispositivo duplo**. No dispositivo gêmeo, você pode definir tags para agrupar dispositivos por qualquer métrica que precisar em sua solução, como região, ambiente, local ou tipo de dispositivo. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-at-scale.md).

Escolha o idioma SDK que você deseja usar para criar o dispositivo simulado e siga as etapas até que você crie o registro individual.

Ao criar o registro individual, selecione **true** para declarar que o dispositivo TPM simulado em seu computador de desenvolvimento do Windows é um **dispositivo IOT Edge**.

> [!TIP]
> No CLI do Azure, você pode criar um [registro](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) ou um [grupo de registro](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) e usar o sinalizador **habilitado para borda** para especificar que um dispositivo ou grupo de dispositivos é um dispositivo IOT Edge.

Dispositivo simulado e guias de inscrição individuais:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Depois de criar o registro individual, salve o valor do **ID de registro**. Você usa esse valor ao configurar o runtime do Azure IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do Azure IoT Edge

O runtime do IoT Edge é implantado em todos os dispositivos IoT Edge. Seus componentes são executados em contêineres e permitem implantar contêineres adicionais no dispositivo para que você possa executar o código na borda.

Você precisará das seguintes informações ao provisionar seu dispositivo:

* O valor de **escopo da ID** de DPS
* A **ID de registro** do dispositivo que você criou

Instale o IoT Edge tempo de execução no dispositivo que está executando o TPM simulado. Você configurará o tempo de execução de IoT Edge para o provisionamento automático, não manual.

> [!TIP]
> Mantenha a janela que está executando o simulador do TPM aberta durante a instalação e teste.

Para obter informações mais detalhadas sobre como instalar o IoT Edge no Windows, incluindo pré-requisitos e instruções para tarefas como gerenciar contêineres e atualizar IoT Edge, consulte [instalar o Azure IOT Edge tempo de execução no Windows](how-to-install-iot-edge-windows.md).

1. Abra uma janela do PowerShell no modo de administrador. Certifique-se de usar uma sessão AMD64 do PowerShell ao instalar o IoT Edge, não o PowerShell (x86).

1. O comando **Deploy-IoTEdge** verifica se o computador Windows está em uma versão com suporte, ativa o recurso de contêineres e, em seguida, baixa o tempo de execução do Moby e o tempo de execução do IOT Edge. O padrão do comando é usar contêineres do Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Neste ponto, os dispositivos IoT Core podem ser reiniciados automaticamente. Outros dispositivos Windows 10 ou Windows Server podem solicitar a reinicialização. Nesse caso, reinicie o dispositivo agora. Quando o dispositivo estiver pronto, execute o PowerShell como administrador novamente.

1. O comando **Initialize-IoTEdge** configura o runtime do IoT Edge em seu computador. O comando assumirá como padrão o provisionamento manual com contêineres do Windows. Use o `-Dps` sinalizador para usar o serviço de provisionamento de dispositivos em vez do provisionamento manual.

   Substitua os valores de espaço reservado para `{scope_id}` e `{registration_id}` pelos dados coletados anteriormente.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Se o runtime foi iniciado com êxito, você pode entrar em seu Hub IoT e iniciar a implantação de módulos do IoT Edge em seu dispositivo. Use os seguintes comandos em seu dispositivo para verificar o runtime instalado e iniciado com êxito.  

Verifique o status do serviço do IoT Edge.

```powershell
Get-Service iotedge
```

Examine os logs de serviço pelos últimos 5 minutos usando.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Módulos de execução da lista.

```powershell
iotedge list
```

## <a name="next-steps"></a>Próximas etapas

O processo de registro do serviço de provisionamento de dispositivo permite definir a ID do dispositivo e as marcas do dispositivo gêmeo ao mesmo tempo, como provisionar o novo dispositivo. Você pode usar esses valores para dispositivos individuais ou grupos de dispositivos usando o gerenciamento automático de dispositivo de destino. Saiba como [Implantar e monitorar os módulos de IoT Edge em escala usando o portal do Azure](how-to-deploy-at-scale.md) ou [usando a CLI do Azure](how-to-deploy-cli-at-scale.md)
