---
title: Associar o Cache do Azure para Redis ao aplicativo do Azure Spring Cloud
description: Como associar o Cache Redis do Azure ao aplicativo Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 6bbd54be46effe324199639477f9ca4ab31bea98
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091395"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Associar o Cache do Azure para Redis ao aplicativo do Azure Spring Cloud 

Em vez de configurar manualmente seus aplicativos Spring Boot, associe automaticamente serviços do Azure selecionados aos seus aplicativos usando o Azure Spring Cloud. Este artigo mostra como associar seu aplicativo ao Cache do Azure para Redis.

## <a name="prerequisites"></a>Pré-requisitos

* Uma instância do Azure Spring Cloud implantada
* Uma instância de serviço do Cache Redis do Azure
* A extensão do Azure Spring Cloud para a CLI do Azure

Caso não tenha uma instância do Azure Spring Cloud implantada, siga as etapas do [início rápido sobre como implantar um aplicativo do Azure Spring Cloud](spring-cloud-quickstart-launch-app-portal.md).

## <a name="bind-azure-cache-for-redis"></a>Associar o Cache Redis do Azure

1. Adicione a seguinte dependência ao arquivo pom.xml do projeto:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Remova as propriedades de `spring.redis.*` do arquivo `application.properties`

1. Atualize a implantação atual usando `az spring-cloud app update` ou crie uma implantação usando `az spring-cloud app deployment create`.

1. Acesse sua página de serviços do Azure Spring Cloud no portal do Azure. Acesse o **Painel do Aplicativo** e selecione o aplicativo a ser associado ao Cache do Azure para Redis. Esse é o mesmo aplicativo que você atualizou ou implantou na etapa anterior.

1. Selecione **Associação de serviço** e **Criar associação de serviço**. Preencha o formulário, lembrando-se de selecionar o valor de **Tipo de associação** **Cache do Azure para Redis**, o servidor do Cache do Azure para Redis e a opção de chave **Primária**.

1. Reinicie o aplicativo. A associação agora deverá funcionar.

1. Para que a associação de serviço esteja correta, selecione o nome da associação e verifique os detalhes dela. O campo `property` deve ter esta aparência:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a associar seu aplicativo do Azure Spring Cloud ao Cache do Azure para Redis. Para saber mais sobre como associar serviços ao seu aplicativo, confira [Associar a uma instância do Banco de Dados do Azure para MySQL](spring-cloud-tutorial-bind-mysql.md).
