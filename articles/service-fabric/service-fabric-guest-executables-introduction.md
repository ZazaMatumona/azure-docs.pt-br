---
title: Empacotar um executável existente no Azure Service Fabric
description: Aprenda sobre como empacotar um aplicativo existente como um executável do convidado, para que ele possa ser implementado em um cluster do Service Fabric.
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 8b808d092001196a4d2150e44d508e031db95554
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86247380"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Implantar um executável existente no Service Fabric
Você pode executar qualquer tipo de código, como Node.js, Java ou C++ no Service Fabric como um serviço. O Service Fabric se refere a esses tipos de serviço como executáveis convidados.

Os executáveis convidados são tratados pela Service Fabric como serviços sem monitoração de estado. Como resultado, eles são colocados em nós em um cluster, com base na disponibilidade e em outras métricas. Este artigo descreve como empacotar e implantar um executável convidado em um cluster do Service Fabric usando o Visual Studio ou um utilitário de linha de comando.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Benefícios de executar um executável convidado no Service Fabric
Há várias vantagens de executar um convidado executável em um cluster de Service Fabric:

* Alta disponibilidade. Os aplicativos que são executados no Service Fabric estão no modo de alta disponibilidade. O Service Fabric garante que as instâncias de um aplicativo estão em execução.
* Monitoramento de integridade. O monitoramento de integridade do Service Fabric detecta se um aplicativo está em execução e fornece informações de diagnóstico se houver uma falha.   
* Gerenciamento do ciclo de vida do aplicativo. Além de oferecer atualizações sem tempo de inatividade, o Service Fabric fornecerá reversão automática da versão anterior, se houver um evento de integridade deficiente durante uma atualização.    
* Densidade. Você pode executar vários aplicativos no cluster, o que elimina a necessidade um hardware próprio para a execução de cada aplicativo.
* Capacidade de descoberta: usando o REST, você pode chamar o serviço Nomenclatura do Service Fabric para localizar outros serviços no cluster. 

## <a name="samples"></a>Exemplos
* [Exemplo de empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemplo de dois executáveis convidados (C# e nodejs) se comunicando por meio do Serviço de nomenclatura usando REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Visão geral do aplicativo e dos arquivos de manifesto do serviço
Como parte da implantação de um executável convidado, é útil entender o modelo de implantação e empacotamento do Service Fabric conforme descrito no [modelo de aplicativo](service-fabric-application-model.md). O modelo de empacotamento do Service Fabric depende de dois arquivos XML: o aplicativo e o manifesto do serviço. A definição de esquema dos arquivos ApplicationManifest.xml e ServiceManifest.xml é instalada com o SDK do Service Fabric em *C:\Arquivos de Programas\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifesto do aplicativo** O manifesto do aplicativo é usado para descrever o aplicativo. Ele lista os serviços que o compõem e os outros parâmetros usados para definir como um ou mais serviços devem ser implantados, como o número de instâncias.

  No Service Fabric, um aplicativo é uma unidade de implantação e atualização. Um aplicativo pode ser atualizado como uma única unidade em que falhas e reversões potencias são gerenciadas. O Service Fabric garante que o processo de atualização seja completamente bem-sucedido ou, se a atualização falhar, ele não deixará o aplicativo em um estado desconhecido/instável.
* **Manifesto do serviço** O manifesto do serviço descreve os componentes de um serviço. Ele inclui dados como o nome e o tipo do serviço e seu código e configuração. O manifesto do serviço também inclui alguns parâmetros adicionais que podem ser usados para configurar o serviço após sua implantação.

## <a name="application-package-file-structure"></a>Estrutura de arquivos do pacote de aplicativos
Para implantar um aplicativo no Service Fabric, o aplicativo deve seguir uma estrutura de diretórios predefinida. A seguir está um exemplo dessa estrutura.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

O ApplicationPackageRoot contém o arquivo ApplicationManifest.xml que define o aplicativo. Um subdiretório para cada serviço incluído no aplicativo é usado para conter todos os artefatos que o serviço requer. Esses subdiretórios são ServiceManifest.xml e, normalmente, o seguinte:

* *Código*. Esse diretório contém o código de serviço.
* *Configuração*. Esse diretório contém um arquivo de Settings.xml (e outros arquivos, se necessário) que o serviço pode acessar em tempo de execução para recuperar definições de configuração específicas.
* *Dados*. Esse é um diretório adicional para armazenar dados locais adicionais que podem ser necessário para o serviço. Os dados devem ser usados para armazenar somente dados efêmeros. O Service Fabric não copia nem replica alterações ao diretório de dados se o serviço precisar ser realocado (por exemplo, durante o failover).

> [!NOTE]
> Você não precisa criar os diretórios `config` e `data` se não precisar deles.
>
>

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos para tarefas e informações relacionadas.
* [Implantar um executável convidado](service-fabric-deploy-existing-app.md)
* [Implantar vários executáveis de convidado](./service-fabric-deploy-existing-app.md)
* [Crie o primeiro aplicativo executável do convidado utilizando o Visual Studio ](quickstart-guest-app.md)
* [Amostra de empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), incluindo um link para o pré-lançamento da ferramenta de empacotamento
* [Exemplo de dois executáveis convidados (C# e nodejs) se comunicando por meio do Serviço de nomenclatura usando REST](https://github.com/Azure-Samples/service-fabric-containers)
