---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: rifox
ms.openlocfilehash: 86ab1bc1b9612c663076e5ed55ae5531317906f2
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078570"
---
## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../../create-communication-resource.md).
- `User Access Token` para habilitar el cliente de llamada. Para más información sobre [cómo obtener `User Access Token`](../../../access-tokens.md)
- Opcional: Complete el inicio rápido para [empezar a agregar llamadas a su aplicación](../../getting-started-with-calling.md)

## <a name="setting-up"></a>Instalación

### <a name="install-the-package"></a>Instalar el paquete

> [!NOTE]
> En este documento se usa la versión 1.0.0 del SDK de llamadas.

Busque el nivel de proyecto build.gradle y asegúrese de agregar `mavenCentral()` a la lista de repositorios en `buildscript` y `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
A continuación, en el nivel de módulo build.gradle, agregue las siguientes líneas a la sección de dependencias

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0'
    ...
}

```

## <a name="object-model"></a>Modelo de objetos

Las siguientes clases e interfaces controlan algunas de las características principales del SDK de llamadas de Azure Communication Services:

| Nombre                                  | Descripción                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient es el punto de entrada principal al SDK de llamadas.|
| CallAgent | CallAgent se usa para iniciar y administrar llamadas. |
| CommunicationTokenCredential | CommunicationTokenCredential se usa como la credencial de token para crear una instancia de CallAgent.|
| CommunicationIdentifier | CommunicationIdentifier se usa como un tipo de participante diferente que podría formar parte de una llamada.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>Inicialización de CallClient, creación de CallAgent y acceso a DeviceManager

Para crear una instancia de `CallAgent` , debe llamar al método `createCallAgent` en una instancia de `CallClient`. De este modo, se devuelve un objeto de instancia de `CallAgent` de manera asincrónica.
El método `createCallAgent` toma `CommunicationUserCredential` como argumento, que encapsula un [token de acceso](../../../access-tokens.md).
Para tener acceso a `DeviceManager`, debe crearse primero una instancia de callAgent y, a continuación, puede usar el método `CallClient.getDeviceManager` para obtener DeviceManager.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential).get();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```
Para establecer un nombre para mostrar para el autor de la llamada, use este método alternativo:

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential, callAgentOptions).get();
```


## <a name="place-an-outgoing-call-and-join-a-group-call"></a>Realización de una llamada saliente y unión a una llamada grupal

Para crear e iniciar una llamada, es necesario llamar al método `CallAgent.startCall()` y proporcionar el `Identifier` de los destinatarios.
Para unirse a una llamada grupal, debe llamar al método `CallAgent.join()` y proporcionar el groupId. Los identificadores de grupo deben tener el formato GUID o UUID.

La creación y el inicio de la llamada son sincrónicos. La instancia de llamada le permite suscribirse a todos los eventos de la llamada.

### <a name="place-a-11-call-to-a-user"></a>Realización de una llamada 1:1 a un usuario
Para realizar una llamada a otro usuario de Communication Services, invoque el método `call` en `callAgent` y pase un objeto con la clave `communicationUserId`.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUserIdentifier acsUserId = new CommunicationUserIdentifier(<USER_ID>);
CommunicationUserIdentifier participants[] = new CommunicationUserIdentifier[]{ acsUserId };
call oneToOneCall = callAgent.startCall(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Realización de una llamada 1:n con usuarios y RTC
> [!WARNING]
> Actualmente la llamada RTC no está disponible.

Para realizar una llamada 1:n a un usuario y un número de RTC, debe especificar el número de teléfono del destinatario.
El recurso de Communication Services debe estar configurado para permitir llamadas RTC:
```java
CommunicationUserIdentifier acsUser1 = new CommunicationUserIdentifier(<USER_ID>);
PhoneNumberIdentifier acsUser2 = new PhoneNumberIdentifier("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.startCall(participants, startCallOptions);
```

### <a name="place-a-11-call-with-video-camera"></a>Realización de una llamada 1:1 con videocámara
> [!WARNING]
> Actualmente, solo se admite una secuencia de vídeo local saliente. Para realizar una llamada con vídeo, debe enumerar las cámaras locales mediante la API `deviceManager` `getCameras`.
Una vez que seleccione la cámara deseada, úsela para crear una instancia de `LocalVideoStream` y pásela a `videoOptions` como elemento de la matriz `localVideoStream` a un método `call`.
Una vez que la llamada se conecta, iniciará automáticamente el envío de una secuencia de vídeo desde la cámara seleccionada hasta los demás participantes.

> [!NOTE]
> Debido a cuestiones de privacidad, el vídeo no se compartirá con la llamada si no se muestra como una versión preliminar localmente.
Consulte [Versión preliminar de la cámara local](#local-camera-preview) para obtener más detalles.
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager(appContext).get().getCameras().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;
VideoOptions videoOptions = new VideoOptions(localVideoStreams);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new CreateViewOptions(ScalingMode.FIT));
// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUserIdentifier[] participants = new CommunicationUserIdentifier[]{ new CommunicationUserIdentifier("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.startCall(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>Unión a una llamada grupal
Para iniciar una llamada grupal nueva o unirse a una en curso, debe llamar al método "join" y pasar un objeto con una propiedad `groupId`. El valor debe ser un GUID.
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

### <a name="accept-a-call"></a>Aceptación de una llamada
Para aceptar una llamada, llame al método "accept" en un objeto de llamada.

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
Call call = incomingCall.accept(context).get();
```

Para aceptar una llamada con la cámara de vídeo activada:

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
Call call = incomingCall.accept(context, acceptCallOptions).get();
```

La llamada entrante se puede obtener mediante la suscripción al evento `onIncomingCall` en el objeto `callAgent`:

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    IncomingCall incomingCall;
    callAgent.addOnIncomingCallListener(new IncomingCallListener() {
        void onIncomingCall(IncomingCall inboundCall) {
            // Look for incoming call
            incomingCall = inboundCall;
        }
    });
    return incomingCall;
}
```


## <a name="push-notifications"></a>Notificaciones de inserción

### <a name="overview"></a>Información general
Las notificación push móviles son las notificaciones emergentes que se ven en los dispositivos móviles. En el caso de las llamadas, nos centraremos en las notificaciones push VoIP (voz sobre IP). Nos registraremos para recibir y gestionar notificaciones push y, a continuación, anular dicho registro.

### <a name="prerequisites"></a>Requisitos previos

Una cuenta de Firebase configurada con Cloud Messaging (FCM) habilitado y con su servicio Firebase Cloud Messaging conectado a una instancia de Azure Notification Hubs. Consulte [Notificaciones de Communication Services](../../../../concepts/notifications.md) para más información.
Además, en el tutorial se da por supuesto que está usando la versión 3.6 de Android Studio o una versión posterior para compilar la aplicación.

Para la aplicación de Android es necesario un conjunto de permisos a fin de poder recibir mensajes de notificaciones de Firebase Cloud Messaging. En el archivo `AndroidManifest.xml`, agregue el siguiente conjunto de permisos justo después de *<manifest ...>* o debajo de la etiqueta *</application>* .

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>Registro de notificaciones push

A fin de registrarse para recibir notificaciones push, la aplicación debe llamar a `registerPushNotification()` en una instancia de *CallAgent* con un token de registro de dispositivos.

Para obtener el token de registro de dispositivos, agregue el SDK de Firebase al archivo *build.gradle* del módulo de aplicación agregando las siguientes líneas en la sección `dependencies` si aún no está ahí:

```
    // Add the SDK for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

En el archivo *build.gradle* del nivel de proyecto, agregue lo siguiente en la sección `dependencies` si aún no está ahí:

```
    classpath 'com.google.gms:google-services:4.3.3'
```

Agregue el siguiente complemento al principio del archivo si aún no está ahí:

```
apply plugin: 'com.google.gms.google-services'
```

Seleccione *Sincronizar ahora* en la barra de herramientas. Agregue el siguiente fragmento de código para obtener el token de registro de dispositivos generado por el SDK de Firebase Cloud Messaging para la instancia de la aplicación cliente. Asegúrese de agregar las siguientes importaciones al encabezado de la actividad principal de la instancia. Son necesarias para que el fragmento de código recupere el token:

```java
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

Agregue este fragmento de código para recuperar el token:

```java
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w("PushNotification", "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d("PushNotification", "Device Registration token retrieved successfully");
                    }
                });
```
Registre el token de registro de dispositivos con el SDK de servicios de llamada para recibir notificaciones push de llamadas entrantes:

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>Control de notificaciones push

Para recibir notificaciones push de llamadas entrantes, llame a *handlePushNotification()* en una instancia de *CallAgent* con una carga.

Para obtener la carga de Firebase Cloud Messaging, primero cree un servicio (Archivo > Nuevo > Servicio > Servicio) que amplíe la clase *FirebaseMessagingService* del SDK de Firebase e invalide el método `onMessageReceived`. Este método es el controlador de eventos al que se llama cuando Firebase Cloud Messaging entrega la notificación push a la aplicación.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
Agregue la siguiente definición de servicio al archivo `AndroidManifest.xml`, en la etiqueta <application>:

```XML
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

- Una vez recuperada la carga, puede pasarse al SDK de *Communication Services* que se va a analizar en un objeto *IncomingCallInformation* interno, que se administrará llamando al método *handlePushNotification* en una instancia de *CallAgent*. Se crea una instancia de `CallAgent` mediante la llamada al método `createCallAgent(...)` en la clase `CallClient`.

```java
try {
    IncomingCallInformation notification = IncomingCallInformation.fromMap(pushNotificationMessageDataFromFCM);
    Future handlePushNotificationFuture = callAgent.handlePushNotification(notification).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

Cuando la administración del mensaje de notificación push se realice correctamente y todos los controladores de eventos se registren correctamente, la aplicación llamará.

### <a name="unregister-push-notifications"></a>Anulación del registro de notificaciones push

Las aplicaciones pueden anular el registro de notificaciones push en cualquier momento. Llame al método `unregisterPushNotification()` en callAgent para anular el registro.

```java
try {
    callAgent.unregisterPushNotification().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>Administración de llamadas
Durante una llamada, puede acceder a las propiedades de la llamada y realizar varias operaciones para administrar la configuración relacionada con el vídeo y el audio.

### <a name="call-properties"></a>Propiedades de llamada

Obtenga el id. único de esta llamada:

```java
String callId = call.getId();
```

Para información sobre los demás participantes de la llamada, revise la colección `remoteParticipant` de la instancia de `call`:

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

La identidad del autor de la llamada si la llamada es entrante:

```java
CommunicationIdentifier callerId = call.getCallerInfo().getIdentifier();
```

Obtenga el estado de la llamada: 

```java
CallState callState = call.getState();
```

Devuelve una cadena que representa el estado actual de una llamada:
* "NONE": estado inicial de la llamada.
* "EARLY_MEDIA": indica un estado en el que se reproduce un anuncio antes de conectar la llamada.
* "CONNECTING": estado de transición inicial una vez que se realiza o acepta la llamada.
* "RINGING": en el caso de una llamada saliente, indica que se está llamando a los participantes remotos.
* "CONNECTED": la llamada está conectada.
* "LOCAL_HOLD": el participante local ha puesto la llamada en espera; no fluye ningún elemento multimedia entre el punto de conexión local y los participantes remotos.
* "REMOTE_HOLD": un participante remoto ha puesto la llamada en espera; no fluye ningún elemento multimedia entre el punto de conexión local y los participantes remotos.
* "DISCONNECTING": estado de transición antes de que la llamada pase al estado "Desconectado".
* "DISCONNECTED": estado final de la llamada.
* "IN_LOBBY": en busca de interoperabilidad de una reunión de Teams.

Para saber por qué ha finalizado una llamada, revise la propiedad `callEndReason`. Contiene código o subcódigo: 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

Para ver si la llamada actual es una llamada entrante o saliente, revise la propiedad `callDirection`:

```java
CallDirection callDirection = call.getCallDirection(); 
// callDirection == CallDirection.INCOMING for incoming call
// callDirection == CallDirection.OUTGOING for outgoing call
```

Para ver si el micrófono actual está silenciado, revise la propiedad `muted`:

```java
boolean muted = call.isMuted();
```

Para revisar las secuencias de vídeo activas, compruebe la colección `localVideoStreams`:

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>Silencio y reactivación del sonido

Para silenciar o reactivar el sonido del punto de conexión local, puede usar las API asincrónicas `mute` y `unmute`:

```java
Context appContext = this.getApplicationContext();
call.mute(appContext).get();
call.unmute(appContext).get();
```
### <a name="change-the-volume-of-the-call"></a>Cambio del volumen de la llamada
    
Mientras se encuentra en una llamada, las teclas de volumen de hardware en el teléfono deben permitir al usuario cambiar el volumen de llamada.
Esto se consigue mediante el método `setVolumeControlStream` con el tipo de secuencia `AudioManager.STREAM_VOICE_CALL` en la actividad donde se realiza la llamada.
Esto permite que las teclas de volumen de hardware cambien el volumen de la llamada (que se indica mediante un icono de teléfono o algo similar en el control deslizante del volumen) e impide que se cambie el volumen de otros perfiles de sonido, como alarmas, multimedia o el volumen de todo el sistema. Para obtener más información, puede consultar [Cómo controlar cambios en la salida de audio | Desarrolladores de Android](https://developer.android.com/guide/topics/media-apps/volume-and-earphones).
    
```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    ...
    setVolumeControlStream(AudioManager.STREAM_VOICE_CALL);
}
```

    
### <a name="start-and-stop-sending-local-video"></a>Inicio y detención del envío de vídeo local

Para iniciar un vídeo, tiene que enumerar las cámaras con la API `getCameraList` en el objeto `deviceManager`. Luego, cree una instancia de `LocalVideoStream` pasando la cámara deseada y pásela a la API `startVideo` como un argumento:

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);
Future startVideoFuture = call.startVideo(appContext, currentLocalVideoStream);
startVideoFuture.get();
```

Una vez que logre empezar a enviar vídeo, se agregará una instancia de `LocalVideoStream` a la colección `localVideoStreams` en la instancia de llamada.

```java
currentLocalVideoStream == call.getLocalVideoStreams().get(0);
```

Para detener el vídeo local, pase la instancia `LocalVideoStream` disponible en la colección `localVideoStreams`:

```java
call.stopVideo(appContext, currentLocalVideoStream).get();
```

Puede cambiar a un dispositivo de cámara distinto mientras se envía vídeo si invoca `switchSource` en una instancia de `LocalVideoStream`:
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>Administración de participantes remotos

El tipo `RemoteParticipant` representa a todos los participantes remotos y estos están disponibles a través de la colección `remoteParticipants` en una instancia de llamada.

### <a name="list-participants-in-a-call"></a>Enumeración de participantes en una llamada
La colección `remoteParticipants` devuelve una lista de los participantes remotos en una llamada determinada:
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>Propiedades de los participantes remotos
Todo participante remoto especificado tiene un conjunto de propiedades y colecciones asociadas:

* Obtenga el identificador de este participante remoto.
"Identity" es uno de los tipos de "Identifier":
```java
CommunicationIdentifier participantIdentifier = remoteParticipant.getIdentifier();
```

* Obtenga el estado de este participante remoto.
```java
ParticipantState state = remoteParticipant.getState();
```
El estado puede ser uno de los siguientes:
* "IDLE": estado inicial.
* "EARLY_MEDIA": se reproduce un anuncio antes de que el participante se conecte a la llamada.
* "RINGING": la llamada del participante está sonando.
* "CONNECTING": estado de transición mientras el participante se conecta a la llamada.
* "CONNECTED": el participante se conecta a la llamada.
* "HOLD": el participante está en espera.
* "IN_LOBBY": el participante está esperando que se admita en la sala. Actualmente solo se usa en el escenario de interoperabilidad de Teams
* "DISCONNECTED": estado final; el participante se desconecta de la llamada.


* Para saber por qué el participante dejó la llamada, revise la propiedad `callEndReason`:
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* Para comprobar si este participante remoto está silenciado o no, revise la propiedad `isMuted`:
```java
boolean isParticipantMuted = remoteParticipant.isMuted();
```

* Para comprobar si este participante remoto está hablando o no, revise la propiedad `isSpeaking`:
```java
boolean isParticipantSpeaking = remoteParticipant.isSpeaking();
```

* Para revisar todas las secuencias de vídeo que un participante determinado envía en esta llamada, compruebe la colección `videoStreams`:
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```

### <a name="add-a-participant-to-a-call"></a>Incorporación de un participante a una llamada

Para agregar un participante a una llamada (ya sea un usuario o un número de teléfono), puede invocar `addParticipant`. Esto devolverá de manera sincrónica la instancia del participante remoto.

```java
const acsUser = new CommunicationUserIdentifier("<acs user id>");
const acsPhone = new PhoneNumberIdentifier("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
AddPhoneNumberOptions addPhoneNumberOptions = new AddPhoneNumberOptions(new PhoneNumberIdentifier("<alternate phone number>"));
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone, addPhoneNumberOptions);
```

### <a name="remove-participant-from-a-call"></a>Eliminación del participante de una llamada
Para quitar un participante de una llamada (ya sea un usuario o un número de teléfono), puede invocar `removeParticipant`.
Esto se resolverá de manera asincrónica una vez que el participante se quite de la llamada.
El participante también se quitará de la colección `remoteParticipants`.
```java
RemoteParticipant acsUserRemoteParticipant = call.getParticipants().get(0);
RemoteParticipant acsPhoneRemoteParticipant = call.getParticipants().get(1);
call.removeParticipant(acsUserRemoteParticipant).get();
call.removeParticipant(acsPhoneRemoteParticipant).get();
```

## <a name="render-remote-participant-video-streams"></a>Representación de secuencias de vídeo de participantes remotos
Para una lista de las secuencias de vídeo y de las secuencias de uso compartido de pantalla de participantes remotos, revise las colecciones `videoStreams`:
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
Para representar una `RemoteVideoStream` de un participante remoto, debe suscribirse a un evento `OnVideoStreamsUpdated`.

En el evento, el cambio de la propiedad `isAvailable` a true indica que el participante remoto está enviando una secuencia actualmente. Si es el caso, cree una nueva instancia de `Renderer` y, a continuación, cree una nueva instancia de `RendererView` con la API de `createView` asincrónica y adjunte `view.target` en cualquier parte de la interfaz de usuario de la aplicación.

Cada vez que cambia la disponibilidad de una secuencia remota, puede elegir destruir todo el representador, un `RendererView` específico o mantenerlos, pero esto hará que el fotograma del vídeo se vea en blanco.

```java
VideoStreamRenderer remoteVideoRenderer = new VideoStreamRenderer(remoteParticipantStream, appContext);
VideoStreamRendererView uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.FIT));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>Propiedades de secuencias de vídeo remotas
La secuencia de vídeo remota tiene un par de propiedades

* `Id`: id. de una secuencia de vídeo remota
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType`: puede ser "Video" o "ScreenSharing"
```java
MediaStreamType type = remoteVideoStream.getMediaStreamType();
```

* `isAvailable`: indica si el punto de conexión del participante remoto envía secuencias de manera activa
```java
boolean availability = remoteVideoStream.isAvailable();
```

### <a name="renderer-methods-and-properties"></a>Métodos y propiedades del representador
Objeto del representador que sigue a las API

* Cree una instancia de `VideoStreamRendererView` que se pueda adjuntar posteriormente en la interfaz de usuario de la aplicación para representar la secuencia de vídeo remota.
```java
// Create a view for a video stream
VideoStreamRendererView.createView()
```
* Eliminación del representador y de todos los elementos `VideoStreamRendererView` asociados a este representador. Se llamará cuando se hayan quitado todas las vistas asociadas de la interfaz de usuario.
```java
VideoStreamRenderer.dispose()
```

* `StreamSize`: tamaño (ancho y alto) de una secuencia de vídeo remota
```java
StreamSize renderStreamSize = VideoStreamRenderer.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```

### <a name="rendererview-methods-and-properties"></a>Métodos y propiedades de RendererView
Al crear un objeto `VideoStreamRendererView`, puede especificar las propiedades `ScalingMode` y `mirrored` que se aplicarán a esta vista: el modo de escalado puede ser "CROP" | "FIT".

```java
VideoStreamRenderer remoteVideoRenderer = new VideoStreamRenderer(remoteVideoStream, appContext);
VideoStreamRendererView rendererView = remoteVideoRenderer.createView(new CreateViewOptions(ScalingMode.Fit));
```

A continuación, el elemento RendererView creado se puede asociar a la interfaz de usuario de la aplicación mediante el siguiente fragmento de código:
```java
layout.addView(rendererView);
```

Posteriormente podrá actualizar el modo de escalado invocando la API `updateScalingMode` en el objeto RendererView con ScalingMode.CROP | ScalingMode.FIT como argumento.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.CROP)
```


## <a name="device-management"></a>Administración de dispositivos

`DeviceManager` permite enumerar los dispositivos locales que se pueden usar en una llamada para transmitir las secuencias de audio o vídeo. También permite solicitar permiso a un usuario para acceder a su micrófono y cámara mediante la API nativa del explorador.

Puede llamar al método `callClient.getDeviceManager()` para acceder a `deviceManager`.
> [!WARNING]
> Actualmente, se debe crear una instancia de un objeto `callAgent` para poder obtener acceso a DeviceManager.

```java
Context appContext = this.getApplicationContext();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```

### <a name="enumerate-local-devices"></a>Enumeración de los dispositivos locales

Para acceder a los dispositivos locales, puede usar métodos de enumeración en el Administrador de dispositivos. La enumeración es una acción sincrónica.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]
```

### <a name="local-camera-preview"></a>Vista previa de la cámara local

Puede usar `DeviceManager` y `Renderer` para empezar a representar secuencias desde la cámara local. Esta secuencia no se enviará a los demás participantes, porque es una fuente de vista previa local. Se trata de una acción asincrónica.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;
videoOptions = new VideoOptions(localVideoStreams);

VideoStreamRenderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);
VideoStreamRendererView uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="record-calls"></a>Registro de llamadas
> [!WARNING]
> Hasta la versión 1.1.0 y la versión beta 1.1.0-beta.1 de la llamada de ACS, Android SDK tiene `isRecordingActive` y `addOnIsRecordingActiveChangedListener` como parte del objeto `Call`. En el caso de las versiones beta, esas API se movieron como una característica extendida de `Call`, tal como se describe a continuación.

> [!NOTE]
> Esta API se ofrece a los desarrolladores como versión preliminar y puede cambiar en función de los comentarios que recibamos. No utilice esta API en un entorno de producción. Para usar esta API, utilice la versión "beta" del Android SDK de llamada de ACS.

La grabación de llamadas es una característica extendida de la API `Call` principal. Primero debe obtener el objeto de API de la característica de grabación:

```java
RecordingFeature callRecordingFeature = call.api(RecordingFeature.class);
```

Después, para comprobar si se está grabando la llamada, inspeccione la propiedad `isRecordingActive` de `callRecordingFeature`. Devuelve `boolean`.

```java
boolean isRecordingActive = callRecordingFeature.isRecordingActive();
```

También puede suscribirse a los cambios de grabación:

```java
private void handleCallOnIsRecordingChanged(PropertyChangedEvent args) {
    boolean isRecordingActive = callRecordingFeature.isRecordingActive();
}

callRecordingFeature.addOnIsRecordingActiveChangedListener(handleCallOnIsRecordingChanged);

```

Si desea iniciar la grabación desde la aplicación, siga los pasos para configurar la grabación de llamadas que aparecen en [Introducción a la grabación de llamadas](../../../../concepts/voice-video-calling/call-recording.md).

Una vez configurada la grabación de llamadas en el servidor, desde la aplicación de Android debe obtener el valor `ServerCallId` de la llamada y, luego, enviarlo al servidor para iniciar el proceso de grabación. Puede encontrar el valor `ServerCallId` si usa `getServerCallId()` de la clase `CallInfo`, que se puede encontrar en el objeto de clase con `getInfo()`.

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to start the call recording.
} catch (ExecutionException | InterruptedException e) {
} catch (UnsupportedOperationException unsupportedOperationException) {
}
```

Cuando se inicia la grabación desde el servidor, se desencadenará el evento `handleCallOnIsRecordingChanged` y el valor de `callRecordingFeature.isRecordingActive()` será `true`.

Al igual que al comenzar a grabar la llamada, si desea detener esta grabación, deberá obtener `ServerCallId` y enviarlo al servidor de grabación para que pueda detener la grabación de llamadas.

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to stop the call recording.
} catch (ExecutionException | InterruptedException e) {
} catch (UnsupportedOperationException unsupportedOperationException) {
}
```

Cuando se detiene la grabación desde el servidor, se desencadenará el evento `handleCallOnIsRecordingChanged` y el valor de `callRecordingFeature.isRecordingActive()` será `false`.


## <a name="call-transcription"></a>Transcripción de llamadas
> [!WARNING]
> Hasta la versión 1.1.0 y la versión beta 1.1.0-beta.1 de la llamada de ACS, Android SDK tiene `isTranscriptionActive` y `addOnIsTranscriptionActiveChangedListener` como parte del objeto `Call`. En el caso de las versiones beta, esas API se movieron como una característica extendida de `Call`, tal como se describe a continuación.
    
> [!NOTE]
> Esta API se ofrece a los desarrolladores como versión preliminar y puede cambiar en función de los comentarios que recibamos. No utilice esta API en un entorno de producción. Para usar esta API, utilice la versión "beta" del Android SDK de llamada de ACS.

La transcripción de llamadas es una característica extendida de la API `Call` principal. Primero debe obtener el objeto de API de la característica de transcripción:

```java
TranscriptionFeature callTranscriptionFeature = call.api(TranscriptionFeature.class);
```

Luego, a fin de comprobar si se está transcribiendo la llamada, revise la propiedad `isTranscriptionActive` de `callTranscriptionFeature`. Devuelve `boolean`.

```java
boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
```

También puede suscribirse a los cambios en la transcripción:

```java
private void handleCallOnIsTranscriptionChanged(PropertyChangedEvent args) {
    boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
}

callTranscriptionFeature.addOnIsTranscriptionActiveChangedListener(handleCallOnIsTranscriptionChanged);

```    

## <a name="eventing-model"></a>Modelo de eventos
Puede suscribirse a la mayoría de las propiedades y colecciones que se van a notificar al cambiar los valores.

### <a name="properties"></a>Propiedades
Para suscribirse a eventos `property changed`:

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>Colecciones
Para suscribirse a eventos `collection updated`:

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
