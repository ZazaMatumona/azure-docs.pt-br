---
title: Suporte a TLS no Hub IoT do Azure
description: Melhores práticas no uso de conexões TLS seguras para dispositivos e serviços se comunicando com o Hub IoT
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: jlian
ms.openlocfilehash: 8c52037684215d1672ed813389d0bbace9a03e42
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85080614"
---
# <a name="tls-support-in-iot-hub"></a>Suporte a TLS no Hub IoT

O Hub IoT usa o protocolo TLS para proteger as conexões contra dispositivos e serviços de IoT. Atualmente, três versões do protocolo TLS são compatíveis, principalmente as versões 1.0, 1.1 e 1.2.

O TLS 1.0 e o 1.1 são considerados herdados e são planejados para substituição. Para obter mais informações, confira [Substituir TLS 1.0 e 1.1 para o Hub IoT](iot-hub-tls-deprecating-1-0-and-1-1.md). É altamente recomendável que você use o TLS 1.2 como a versão preferencial do TLS ao se conectar ao Hub IoT.

## <a name="tls-12-enforcement-available-in-select-regions"></a>Imposição de TLS 1,2 disponível em regiões selecionadas

Para aumentar a segurança, configure seus hubs IoT para permitir *somente* conexões de cliente que usam TLS versão 1,2 e para impor o uso de [codificações recomendadas](#recommended-ciphers). Este recurso só tem suporte nestas regiões:

* Leste dos EUA
* Centro-Sul dos Estados Unidos
* Oeste dos EUA 2
* Governo dos EUA do Arizona
* Gov. dos EUA – Virgínia

Para essa finalidade, provisione um novo Hub IoT em qualquer uma das regiões compatíveis e defina a propriedade `minTlsVersion` como `1.2` na especificação de recurso do Hub IoT do modelo de Azure Resource Manager:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

O recurso de Hub IoT criado usando essa configuração recusará clientes de dispositivos e serviços que tentam se conectar usando as versões 1.0 e 1.1 do TLS. Da mesma maneira, o handshake do TLS será recusado se a mensagem "OLÁ" do cliente não listar nenhuma das [codificações recomendadas](#recommended-ciphers).

> [!NOTE]
> A propriedade `minTlsVersion` é somente leitura e não pode ser alterada depois que o recurso de Hub IoT é criado. Portanto, é essencial que você teste e valide corretamente e com antecedência que *todos* os dispositivos e serviços de IoT são compatíveis com o TLS 1.2 e com as [codificações recomendadas](#recommended-ciphers).
> 
> Após os failovers, a propriedade `minTlsVersion` de seu Hub IoT permanecerá efetiva na região emparelhada geograficamente após o failover.

## <a name="recommended-ciphers"></a>Codificações recomendadas

Os Hubs IoT configurados para aceitar somente o TLS 1.2 também vão impor o uso das seguintes codificações recomendadas:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

Para os Hubs IoT não configurados para imposição do TLS 1.2, o TLS 1.2 ainda funcionará com as seguintes codificações:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="use-tls-12-in-your-iot-hub-sdks"></a>Usar o TLS 1.2 nos SDKs do Hub IoT

Use os links abaixo para configurar o TLS 1.2 e as codificações permitidas nos SDKs do cliente do Hub IoT.

| Linguagem | Versões compatíveis com o TLS 1.2 | Documentação |
|----------|------------------------------------|---------------|
| C        | Marca 2019-12-11 ou mais recente            | [Link](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versão 2.0.0 ou mais recente             | [Link](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versão 1.21.4 ou mais recente            | [Link](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versão 1.19.0 ou mais recente            | [Link](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versão 1.12.2 ou mais recente            | [Link](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-in-your-iot-edge-setup"></a>Usar o TLS 1.2 na configuração do IoT Edge

Dispositivos do IoT Edge podem ser configurados para usar o TLS 1.2 ao se comunicarem com o Hub IoT. Para essa finalidade, use a página de documentação do [IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).