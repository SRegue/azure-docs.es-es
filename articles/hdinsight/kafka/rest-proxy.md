---
title: 'Proxy de REST de Apache Kafka: Azure HDInsight'
description: Obtenga información sobre cómo realizar operaciones de Apache Kafka mediante un servidor proxy REST de Kafka en Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: 634526e8a136023368a06887af648ca3925151a0
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396937"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interacción con clústeres de Apache Kafka en Azure HDInsight mediante un proxy de REST

El proxy REST de Kafka le permite interactuar con el clúster de Kafka mediante una API REST a través de HTTPS. Esta acción significa que los clientes de Kafka pueden estar fuera de la red virtual. Los clientes pueden realizar llamadas HTTPS sencillas y seguras al clúster de Kafka, en lugar de depender de las bibliotecas de Kafka. En este artículo se muestra cómo crear un clúster de Kafka habilitado para servidor proxy REST. También proporciona código de ejemplo que muestra cómo realizar llamadas al servidor proxy REST.

## <a name="rest-api-reference"></a>Referencia de la API REST

Para conocer las operaciones que admite la API REST de Kafka, consulte [Referencia de la API del servidor proxy REST de Kafka en HDInsight](/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Información previa

:::image type="content" source="./media/rest-proxy/rest-proxy-architecture.png" alt-text="Diseño del servidor proxy REST de Kafka" border="false":::

Para obtener la especificación completa de las operaciones que admite la API, consulte [API del servidor proxy REST de Apache Kafka](/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Punto de conexión de proxy de REST

Al crear un clúster de Kafka en HDInsight con el servidor proxy REST, se crea un nuevo punto de conexión público para el clúster, que se puede encontrar en las **Propiedades** del clúster de HDInsight en Azure Portal.

### <a name="security"></a>Seguridad

El acceso al proxy de REST de Kafka se administra con grupos de seguridad de Azure Active Directory. Al crear el clúster de Kafka, proporcione al grupo de seguridad de Azure AD acceso al punto de conexión REST. El propietario del grupo debe registrar en este grupo los clientes de Kafka que necesitan acceso al servidor proxy REST. El propietario del grupo puede realizar el registro mediante el portal o PowerShell.

Para las solicitudes al punto de conexión proxy REST, las aplicaciones cliente deben obtener un token de OAuth. El token se usa para comprobar la pertenencia al grupo de seguridad. Busque a continuación un [ejemplo de aplicación cliente](#client-application-sample) que muestre cómo obtener un token de OAuth. La aplicación cliente pasa el token de OAuth de la solicitud HTTPS al servidor proxy REST.

> [!NOTE]
> Consulte [Administración del acceso a recursos y aplicaciones con grupos en Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md) para más información sobre los grupos de seguridad de AAD. Para obtener más información sobre cómo funcionan los tokens de OAuth, consulte [Autorización del acceso a aplicaciones web de Azure Active Directory mediante el flujo de concesión de código OAuth 2.0](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).

## <a name="kafka-rest-proxy-with-network-security-groups"></a>Proxy REST de Kafka con grupos de seguridad de red
Si aporta su propia red virtual y controla el tráfico de red con grupos de seguridad de red, permita el tráfico **entrante** en el puerto **9400**, además del puerto 443. Esto garantizará que el proxy REST de Kafka sea accesible.

## <a name="prerequisites"></a>Prerrequisitos

1. Registrar una aplicación con Azure AD. Las aplicaciones cliente que escriba para interactuar con el proxy de REST de Kafka usarán el identificador y el secreto de la aplicación para autenticarse en Azure.

1. Cree un grupo de seguridad de Azure AD. Agregue la aplicación que ha registrado en Azure AD al grupo de seguridad como un **miembro** del grupo. Este grupo de seguridad se usará para controlar qué aplicaciones pueden interactuar con el proxy de REST. Para obtener más información sobre cómo crear grupos de Azure AD, consulte [Creación de un grupo básico e incorporación de miembros con Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Validar que el grupo es de tipo **Seguridad**.
    :::image type="content" source="./media/rest-proxy/rest-proxy-group.png" alt-text="Grupo de seguridad" border="true":::

    Valide que la aplicación es miembro del grupo.
    :::image type="content" source="./media/rest-proxy/rest-proxy-membergroup.png" alt-text="Comprobación de la pertenencia" border="true":::

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Creación de un clúster de Kafka con el proxy de REST habilitado

En los pasos siguientes se usa Azure Portal. Para ver un ejemplo con la CLI de Azure, consulte [Creación de un clúster para el proxy REST de Apache Kafka con la CLI de Azure](tutorial-cli-rest-proxy.md).

1. Durante el flujo de trabajo de creación del clúster de Kafka, en la pestaña **Seguridad y redes**, active la opción **Habilitar proxy REST de Kafka**.

     :::image type="content" source="./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png" alt-text="En la captura de pantalla se muestra la página Crear clúster H D Insight con la opción Seguridad y redes seleccionada." border="true":::

1. Haga clic en **Seleccionar grupo de seguridad**. En la lista de grupos de seguridad, seleccione el que quiere que tenga acceso al proxy de REST. Puede usar el cuadro de búsqueda para buscar el grupo de seguridad adecuado. Haga clic en el botón **Seleccionar** de la parte inferior.

     :::image type="content" source="./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png" alt-text="Captura de pantalla que muestra la página Crear clúster de H D Insight con la opción para seleccionar un grupo de seguridad." border="true":::

1. Complete los pasos restantes para crear el clúster como se describe en [Creación de un clúster de Apache Kafka en Azure HDInsight mediante Azure Portal](./apache-kafka-get-started.md).

1. Una vez creado el clúster, vaya a las propiedades del clúster para registrar la URL del proxy de REST de Kafka.

     :::image type="content" source="./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png" alt-text="ver la URL de proxy de REST de Kafka" border="true":::

## <a name="client-application-sample"></a>Nombre de la aplicación cliente

Puede usar el código de Python siguiente para interactuar con el proxy de REST en el clúster de Kafka. Para ejecutar el ejemplo de código, siga estos pasos:

1. Guarde el código de ejemplo en una máquina con Python instalado.
1. Instale las dependencias de Python necesarias ejecutando `pip3 install msal`.
1. Modifique la sección de código **Configure these properties** (Configurar estas propiedades) y actualice las siguientes propiedades en su entorno:

    |Propiedad |Descripción |
    |---|---|
    |Id. de inquilino|Inquilino de Azure donde se encuentra la suscripción.|
    |Id. de cliente|Identificador de la aplicación que registró en el grupo de seguridad.|
    |Secreto del cliente|Secreto de la aplicación que registró en el grupo de seguridad.|
    |Kafkarest_endpoint|Obtenga este valor en la pestaña **Propiedades** de la información general del clúster, tal como se describe en la [sección de implementación](#create-a-kafka-cluster-with-rest-proxy-enabled). Debería tener el siguiente formato: `https://<clustername>-kafkarest.azurehdinsight.net`.|

1. Desde la línea de comandos, ejecute el archivo Python mediante la ejecución de `sudo python3 <filename.py>`.

Este código realiza la siguiente acción:

1. Recupera un token de OAuth de Azure AD.
1. Muestra cómo realizar una solicitud al servidor proxy REST de Kafka.

Para obtener más información sobre cómo obtener tokens de OAuth en Python, consulte [Clase AuthenticationContext de Python](/python/api/adal/adal.authentication_context.authenticationcontext). Es posible que vea un retraso en tanto que los `topics` que no se crean o eliminan mediante el servidor proxy REST de Kafka se reflejan. Este retraso se debe a la actualización de la memoria caché. Se ha mejorado el **valor** campo de Producer API. Ahora, acepta objetos JSON y cualquier forma serializada.

```python
#Required python packages
#pip3 install msal

import json
import msal
import random
import requests
import string
import sys
import time

def get_random_string():
    letters = string.ascii_letters
    random_string = ''.join(random.choice(letters) for i in range(7))

    return random_string


#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Get access token
# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
)

# The pattern to acquire a token looks like this.
result = None
result = app.acquire_token_for_client(scopes=[scope])
accessToken = result['access_token']
verify_https = True
request_timeout = 10

# Print access token
print("Access token: " + accessToken)

# API format
api_version = 'v1'
api_format = kafkarest_endpoint + '/{api_version}/{rest_api}'
get_topic_api = 'metadata/topics'
topic_api_format = 'topics/{topic_name}'
producer_api_format = 'producer/topics/{topic_name}'
consumer_api_format = 'consumer/topics/{topic_name}/partitions/{partition_id}/offsets/{offset}?count={count}'  # by default count = 1

# Request header
headers = {
    'Authorization': 'Bearer ' + accessToken,
    'Content-type': 'application/json'          # set Content-type to 'application/json'
}

# New topic
new_topic = 'hello_topic_' + get_random_string()
print("Topic " + new_topic + " is going to be used for demo.")

topics = []

# Create a  new topic
# Example of topic config
topic_config = {
    "partition_count": 1,
    "replication_factor": 1,
    "topic_properties": {
        "retention.ms": 604800000,
        "min.insync.replicas": "1"
    }
}

create_topic_url = api_format.format(api_version=api_version, rest_api=topic_api_format.format(topic_name=new_topic))
response = requests.put(create_topic_url, headers=headers, json=topic_config, timeout=request_timeout, verify=verify_https)
print(response.content)

if response.ok:
    while new_topic not in topics:
        print("The new topic " + new_topic + " is not visible yet. sleep 30 seconds...")
        time.sleep(30)
        # List Topic
        get_topic_url = api_format.format(api_version=api_version, rest_api=get_topic_api)

        response = requests.get(get_topic_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
        topic_list = response.json()
        topics = topic_list.get("topics", [])
else:
    print("Topic " + new_topic + " was created. Exit.")
    sys.exit(1)

# Produce messages to new_topic
# Example payload of Producer REST API
payload_json = {
    "records": [
        {
            "key": "key1",
            "value": "**********"         # A string                              
        },
        {
            "partition": 0,
            "value": 5                    # An integer
        },
        {
            "value": 3.14                 # A floating number
        },
        {
            "value": {                    # A JSON object
                "id": 1,
                "name": "HDInsight Kafka REST proxy"
            }
        },
        {
            "value": [                    # A list of JSON objects
                {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                {
                    "id": 2,
                    "name": "HDInsight Kafka REST proxy 2"
                },
                {
                    "id": 3,
                    "name": "HDInsight Kafka REST proxy 3"
                }
            ]
        },
        {
            "value": {                  # A nested JSON object
                "group id": 1,
                "HDI Kafka REST": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                "HDI Kafka REST server info": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1",
                    "servers": [
                        {
                            "server id": 1,
                            "server name": "HDInsight Kafka REST proxy server 1"
                        },
                        {
                            "server id": 2,
                            "server name": "HDInsight Kafka REST proxy server 2"
                        },
                        {
                            "server id": 3,
                            "server name": "HDInsight Kafka REST proxy server 3"
                        }
                    ]
                }
            }
        }
    ]
}

print("Payloads in a Producer request: \n", payload_json)
producer_url = api_format.format(api_version=api_version, rest_api=producer_api_format.format(topic_name=new_topic))
response = requests.post(producer_url, headers=headers, json=payload_json, timeout=request_timeout, verify=verify_https)
print(response.content)

# Consume messages from the topic
partition_id = 0
offset = 0
count = 2

while True:
    consumer_url = api_format.format(api_version=api_version, rest_api=consumer_api_format.format(topic_name=new_topic, partition_id=partition_id, offset=offset, count=count))
    print("Consuming " + str(count) + " messages from offset " + str(offset))

    response = requests.get(consumer_url, headers=headers, timeout=request_timeout, verify=verify_https)

    if response.ok:
        messages = response.json()
        print("Consumed messages: \n" + json.dumps(messages, indent=2))
        next_offset = response.headers.get("NextOffset")
        if offset == next_offset or not messages.get("records", []):
            print("Consumer caught up with producer. Exit for now...")
            break

        offset = next_offset

    else:
        print("Error " + str(response.status_code))
        break
        
# List partitions
get_partitions_url = api_format.format(api_version=api_version, rest_api=partitions_api_format.format(topic_name=new_topic))
print("Fetching partitions from  " + get_partitions_url)

response = requests.get(get_partitions_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition_list = response.json()
print("Partition list: \n" + json.dumps(partition_list, indent=2))

# List a partition
get_partition_url = api_format.format(api_version=api_version, rest_api=partition_api_format.format(topic_name=new_topic, partition_id=partition_id))
print("Fetching metadata of a partition from  " + get_partition_url)

response = requests.get(get_partition_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition = response.json()
print("Partition metadata: \n" + json.dumps(partition, indent=2))

```

Consulte a continuación otro ejemplo sobre cómo obtener un token de Azure para el servidor proxy REST mediante el comando curl. **Observe que se tiene que especificar `scope=https://hib.azurehdinsight.net/.default` al obtener un token.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Pasos siguientes

* [Documentos de referencia de la API de proxy de REST de Kafka](/rest/api/hdinsight-kafka-rest-proxy/)
