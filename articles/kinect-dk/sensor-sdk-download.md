---
title: Download do SDK do Sensor do Azure Kinect
description: Saiba como baixar e instalar o SDK do Sensor do Azure Kinect no Windows e no Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure, kinect, sdk, baixar atualização, mais recente, disponível, instalar
ms.openlocfilehash: 48cdd35a80d68a7ec0d900639c0fca3a2c66787e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171781"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Download do SDK do Sensor do Azure Kinect

Esta página tem os links de download para cada versão do SDK do Sensor do Azure Kinect. O instalador fornece todos os arquivos necessários para desenvolver para o Azure Kinect.

## <a name="azure-kinect-sensor-sdk-contents"></a>Conteúdo do SDK do Sensor do Azure Kinect

- Cabeçalhos e bibliotecas para criar um aplicativo usando o Azure Kinect DK.
- DLLs redistribuíveis necessárias para aplicativos que usam o Azure Kinect DK.
- [Azure Kinect Viewer](azure-kinect-viewer.md).
- [Azure Kinect Recorder](azure-kinect-recorder.md).
- [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md).

## <a name="windows-download-link"></a>Link de download para Windows

[Instalador Microsoft](https://download.microsoft.com/download/3/d/6/3d6d9e99-a251-4cf3-8c6a-8e108e960b4b/Azure%20Kinect%20SDK%201.4.1.exe) | [Código-fonte do GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1093)

> [!NOTE]
> Ao instalar o SDK, lembre-se do caminho de instalação. Por exemplo, "C:\Arquivos de Programas\Azure Kinect SDK 1.2". Você vai encontrar as ferramentas referenciadas em artigos localizados neste caminho.

Você poderá encontrar versões anteriores do SDK do Sensor do Azure Kinect e do Firmware no [GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="linux-installation-instructions"></a>Instruções de instalação para Linux

Atualmente, a única distribuição compatível é o Ubuntu 18.04. Para solicitar compatibilidade com outras distribuições, confira [esta página](https://aka.ms/azurekinectfeedback).

Primeiro você vai precisar configurar o [Repositório de Pacotes da Microsoft](https://packages.microsoft.com/), seguindo as instruções fornecidas [aqui](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software).

Em seguida, você poderá instalar os pacotes necessários. O pacote `k4a-tools` inclui o [Azure Kinect Viewer](azure-kinect-viewer.md), o [Azure Kinect Recorder](record-sensor-streams-file.md) e o [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md). Para instalar, execute

 `sudo apt install k4a-tools`

 O pacote `libk4a<major>.<minor>-dev` contém os cabeçalhos e os arquivos CMake que devem ser criados em relação a `libk4a`.
O pacote `libk4a<major>.<minor>` contém os objetos compartilhados necessários para a execução de executáveis que dependem de `libk4a`.

 Os tutoriais básicos exigem o pacote `libk4a<major>.<minor>-dev`. Para instalar, execute

 `sudo apt install libk4a1.1-dev`

Se o comando for executado com sucesso, o SDK estará pronto para uso.

## <a name="change-log-and-older-versions"></a>Log de alterações e versões mais antigas

Você pode encontrar o log de alterações do SDK do Sensor do Azure Kinect [aqui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md).

Se você precisar de uma versão mais antiga do SDK do Sensor do Azure Kinect, encontre-a [aqui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Próximas etapas

[Configurar o Azure Kinect DK](set-up-azure-kinect-dk.md)
