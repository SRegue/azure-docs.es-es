---
title: Administración y supervisión de copias de seguridad del agente de MARS
description: Aprenda a administrar y supervisar las copias de seguridad del agente de Microsoft Azure Recovery Services (MARS) con el servicio Azure Backup.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: c7a696c4059ebc7cc28a34a299060039ac1c0c62
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111902958"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Administración de copias de seguridad del agente de Microsoft Azure Recovery Services (MARS) con el servicio Azure Backup

En este artículo se describe cómo administrar archivos y carpetas de los que se ha realizado una copia de seguridad con el agente de Microsoft Azure Recovery Services.

## <a name="modify-a-backup-policy"></a>Modificación de una directiva de copia de seguridad

Al modificar la directiva de copia de seguridad, puede agregar nuevos elementos, quitar elementos existentes de la copia de seguridad o excluir archivos de la copia de seguridad mediante la configuración de exclusión.

- **Agregar elementos**: use esta opción solo para agregar nuevos elementos a la copia de seguridad. Para quitar elementos existentes, use **Quitar elementos** o la opción **Configuración de exclusión**.  
- **Quitar elementos**: utilice esta opción para quitar elementos de la copia de seguridad.
  - Para quitar todos los elementos de un volumen, utilice **Configuración de exclusión** en lugar de **Quitar elementos**.
  - Si se borran todas las selecciones de un volumen, las copias de seguridad anteriores de los elementos se conservarán en función de la configuración de retención establecida en el momento de la última copia de seguridad, sin posibilidad de modificación.
  - Si vuelve a seleccionar estos elementos, se realizará una primera copia de seguridad completa y los cambios en la directiva nueva no se aplicarán a las copias de seguridad anteriores.
  - Si se anula la selección del volumen completo, se conservará la copia de seguridad anterior sin posibilidad de modificar la directiva de retención.
- **Configuración de exclusión**: utilice esta opción para excluir elementos específicos de la copia de seguridad.

### <a name="add-new-items-to-existing-policy"></a>Adición de nuevos elementos a la directiva existente

1. En **Acciones**, seleccione **Programar copia de seguridad**.

    ![Programar una copia de seguridad de Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

2. En la pestaña **Seleccionar el elemento de directiva**, seleccione **Modificar una programación de copia de seguridad de los archivos y carpetas** y seleccione **Siguiente**.

    ![Selección de elementos de la directiva](./media/backup-azure-manage-mars/select-policy-items.png)

3. En la pestaña **Modify or stop schedule backup** (Modificar o detener una copia de seguridad programada), seleccione **Cambiar la hora o las horas de las copias de seguridad** y seleccione **Siguiente**.

    ![Modificación o programación de una copia de seguridad](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. En la pestaña **Seleccionar elementos de los que realizar copia de seguridad**, seleccione **Agregar elementos** para agregar los elementos de los que desea realizar una copia de seguridad.

    ![Modificación o programación de una copia de seguridad para agregar elementos](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. En la ventana **Seleccionar elementos**, seleccione los archivos o las carpetas que desea agregar y seleccione **Aceptar**.

    ![Selección de los elementos](./media/backup-azure-manage-mars/select-item.png)

6. Complete los pasos siguientes y seleccione **Finalizar** para completar la operación.

### <a name="add-exclusion-rules-to-existing-policy"></a>Adición de reglas de exclusión a la directiva existente

Puede agregar reglas de exclusión para omitir archivos y carpetas que no desea incluir en la copia de seguridad. Esto puede hacerse al definir una nueva directiva o al modificar una directiva existente.

1. En el panel Acciones, seleccione **Programar copia de seguridad**. Vaya a **Seleccionar elementos de los que realizar copia de seguridad** y seleccione **Configuración de exclusión**.

    ![Configuración de exclusión](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. En **Configuración de exclusión**, seleccione **Agregar exclusión**.

    ![Agregar exclusión](./media/backup-azure-manage-mars/add-exclusion.png)

3. En **Seleccionar elementos que excluir**, explore los archivos y carpetas, seleccione los elementos que desee excluir y haga clic en **Aceptar**.

    ![Seleccione los elementos que desea excluir](./media/backup-azure-manage-mars/select-items-exclude.png)

4. De forma predeterminada, se excluirán todas las **subcarpetas** de las carpetas seleccionadas. Puede seleccionar **Sí** o **No** para cambiar esto. Puede editar y especificar los tipos de archivo que se van a excluir como se muestra a continuación:

    ![Seleccionar tipos de subcarpetas](./media/backup-azure-manage-mars/subfolders-type.png)

5. Complete los pasos siguientes y seleccione **Finalizar** para completar la operación.

### <a name="remove-items-from-existing-policy"></a>Quitar elementos de la directiva existente

1. En el panel Acciones, seleccione **Programar copia de seguridad**. Vaya a **Seleccionar elementos de los que realizar copia de seguridad**. En la lista, seleccione los archivos y las carpetas que desea quitar de la programación de copia de seguridad y seleccione **Quitar elementos**.

    ![Seleccione los elementos que desea quitar](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Proceda con precaución al quitar completamente un volumen de la directiva.  Si necesita volver a agregarlo, se tratará como un nuevo volumen. La siguiente copia de seguridad programada realizará una copia de seguridad inicial (copia de seguridad completa) en lugar de una copia de seguridad incremental. Si necesita quitar o añadir temporalmente elementos más adelante, se recomienda usar **Configuración de exclusión** en lugar de **Quitar elementos** para garantizar que se haga una copia de seguridad incremental en lugar de una copia de seguridad completa.

2. Complete los pasos siguientes y seleccione **Finalizar** para completar la operación.

## <a name="stop-protecting-files-and-folder-backup"></a>Dejar de proteger la copia de seguridad de carpetas y archivos

Hay dos formas de detener la protección de una copia de seguridad de archivos y carpetas:

- **Detener la protección y conservar los datos de copia de seguridad**.
  - Esta opción detendrá la protección para todos los trabajos de copia de seguridad futuros.
  - El servicio Azure Backup seguirá conservando todos los puntos de recuperación existentes.  
  - Podrá restaurar los datos de copia de seguridad de los puntos de recuperación que no hayan expirado.
  - Si decide reanudar la protección, podrá usar la opción *Vuelva a habilitar la programación de copia de seguridad*. Después de la operación, los datos se conservarán de acuerdo con la nueva directiva de retención.
- **Detener la protección y eliminar los datos de copia de seguridad**.
  - Esta opción detendrá la protección de los datos para todos los trabajos de copia de seguridad futuros. Si las características de seguridad del almacén no están habilitadas, todos los puntos de recuperación se eliminan inmediatamente.<br>Si las características de seguridad están habilitadas, la eliminación se retrasa 14 días y recibirá un correo electrónico de alerta de con el mensaje *Los datos de este elemento de copia de seguridad se han eliminado. Estos datos estarán disponibles temporalmente durante un período de 14 días, tras el cual se eliminarán de forma permanente* y la acción recomendada *Vuelva a proteger el elemento de copia de seguridad en el plazo de 14 días para recuperar los datos*.<br>En este estado, la directiva de retención se sigue aplicando y los datos de copia de seguridad siguen siendo facturables. [Más información](backup-azure-security-feature.md#enable-security-features) sobre cómo habilitar las características de seguridad del almacén.
  - Para reanudar la protección, vuelva a proteger el servidor en el plazo de 14 días a partir de la operación de eliminación. En esta duración, también puede restaurar los datos en un servidor alternativo.

### <a name="stop-protection-and-retain-backup-data"></a>Detener la protección y conservar los datos de copia de seguridad

1. Abra la consola de administración de MARS, vaya al **panel Acciones** y **seleccione Programar copia de seguridad**.

    ![Seleccionar Programar copia de seguridad](./media/backup-azure-manage-mars/mars-actions.png)
1. En la página **Seleccionar el elemento de directiva**, seleccione **Modificar una programación de copia de seguridad de los archivos y carpetas** y, después, **Siguiente**.

    ![Seleccionar el elemento de directiva](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. En la página **Modificar o detener una copia de seguridad programada**, seleccione **Detener la programación de copias de seguridad, pero conservar las copias de seguridad almacenadas hasta que se vuelva a activar una programación**. Después, seleccione **Siguiente**.

    ![Detención de una copia de seguridad programada.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. En **Pause Scheduled Backup** (Pausar copias de seguridad programadas), revise la información y seleccione **Finalizar**.

    ![Pausa de una copia de seguridad programada](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. En **Modificar progreso de la copia de seguridad**, compruebe si el estado de pausa de la copia de seguridad programada es correcto y seleccione **Cerrar** para finalizar.

### <a name="stop-protection-and-delete-backup-data"></a>Detener la protección y eliminar los datos de copia de seguridad

1. Abra la consola de administración de MARS, vaya al panel **Acciones** y seleccione **Programar copia de seguridad**.
2. En la página **Modificar o detener una copia de seguridad programada**, seleccione **Dejar de utilizar esta programación de copias de seguridad y eliminar todas las copias de seguridad almacenadas**. Después, seleccione **Siguiente**.

    ![Modificación o detención de una copia de seguridad programada.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. En la página **Detención de una copia de seguridad programada**, seleccione **Finalizar**.

    ![Detener una copia de seguridad programada y seleccionar finalizar](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Se le pedirá que escriba un PIN (número de identificación personal) de seguridad, que debe generar manualmente. Para ello, primero inicie sesión en Azure Portal.
5. Vaya a **Almacén de Recovery Services** > **Configuración** > **Propiedades**.
6. En **PIN de seguridad**, seleccione **Generar**. Copie este código PIN. Este PIN solo es válido durante cinco minutos.
7. En la consola de administración, pegue el código PIN y seleccione **Aceptar**.

    ![Generación de un PIN de seguridad.](./media/backup-azure-delete-vault/security-pin.png)

8. En la página **Modificar progreso de la copia de seguridad**, aparece el mensaje siguiente: *Los datos de copia de seguridad eliminados se conservarán durante 14 días. Tras ese período, se eliminarán permanentemente los datos de copia de seguridad.*  

    ![Modificar progreso de la copia de seguridad](./media/backup-azure-delete-vault/deleted-backup-data.png)

Después de eliminar los elementos de copia de seguridad locales, siga los pasos siguientes desde el portal.

## <a name="re-enable-protection"></a>Volver a habilitar la protección

Si ha detenido la protección y conservado los datos y, después, decide reanudar la protección, puede volver a habilitar la programación de copia de seguridad mediante la modificación de la directiva de copia de seguridad.

1. En **Acciones**, seleccione **Programar copia de seguridad**.
1. Seleccione **Vuelva a habilitar la programación de copia de seguridad. También puede modificar las horas o los elementos de las copias de seguridad** y seleccionar **Siguiente**.<br>

    ![Volver a habilitar la programación de copia de seguridad](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. En **Seleccionar elementos de los que realizar copia de seguridad**, seleccione **Siguiente**.

    ![Seleccionar los elementos de los que realizar una copia de seguridad](./media/backup-azure-manage-mars/re-enable-next.png)
1. En **Especifique la programación de copia de seguridad**, indique la programación de copia de seguridad y seleccione **Siguiente**.
1. En **Seleccionar directiva de retención**, especifique la duración de la retención y seleccione **Siguiente**.
1. Por último, en la pantalla **Confirmación**, revise los detalles de la directiva y seleccione **Finalizar**.

## <a name="re-generate-passphrase"></a>Regeneración de la frase de contraseña

Una frase de contraseña se usa para cifrar y descifrar los datos durante la copia de seguridad o la restauración del entrono local o la máquina local mediante el agente de MARS en Azure o desde Azure. Si ha perdido u olvidado la frase de contraseña, puede volver a generarla (siempre que el equipo siga registrado en el almacén de Recovery Services y la copia de seguridad se haya configurado) mediante estos pasos:

1. En la consola del agente de MARS, vaya a **Panel acciones** > **Cambiar propiedades** >. Luego, vaya a la **pestaña Cifrado**.<br>
1. Active la casilla **Cambiar frase de contraseña**.<br>
1. Escriba una nueva frase de contraseña o seleccione **Generar frase de contraseña**.
1. Seleccione **Examinar** para guardar la nueva frase de contraseña.

    ![Generar la frase de contraseña.](./media/backup-azure-manage-mars/passphrase.png)

1. Seleccione **Aceptar** para aplicar los cambios.  Si la [característica de seguridad](./backup-azure-security-feature.md#enable-security-features) está habilitada en Azure Portal para el almacén de Recovery Services, se le pedirá que escriba el PIN de seguridad. Para recibir el PIN, siga los pasos indicados en este [artículo](./backup-azure-security-feature.md#authentication-to-perform-critical-operations).<br>
1. Pegue el PIN de seguridad desde el portal y seleccione **Aceptar** para aplicar los cambios.<br>

    ![Pegar el PIN de seguridad](./media/backup-azure-manage-mars/passphrase2.png)
1. Asegúrese de que la frase de contraseña se guarda de forma segura en una ubicación alternativa (distinta de la máquina de origen), preferiblemente en Azure Key Vault. Realice un seguimiento de todas las frases de contraseña si tiene varias máquinas de las que se realiza una copia de seguridad con los agentes de MARS.

## <a name="managing-backup-data-for-unavailable-machines"></a>Administración de datos de copia de seguridad para máquinas no disponibles

En esta sección se describe un escenario en el que la máquina de origen protegida con MARS ya no está disponible porque se eliminó, dañó, infectó con malware o ransomware, o bien se retiró.

En el caso de estas máquinas, el servicio Azure Backup garantiza que el punto de recuperación más reciente no expire (es decir, no se elimine) según las reglas de retención especificadas en la directiva de copia de seguridad. Por lo tanto, puede restaurar la máquina de forma segura.  Tenga en cuenta los siguientes escenarios que puede implementar en los datos con copia de seguridad:

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>Escenario 1: la máquina de origen no está disponible y ya no es necesario que conserve los datos de copia de seguridad

- Puede eliminar los datos con copia de seguridad de Azure Portal siguiendo los pasos indicados en [este artículo](backup-azure-delete-vault.md#delete-protected-items-on-premises).

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>Escenario 2: la máquina de origen no está disponible y es necesario que conserve los datos de copia de seguridad

La administración de la directiva de copia de seguridad para MARS se realiza a través de la consola MARS y no a través del portal. Si es necesario que amplíe la configuración de retención para los puntos de recuperación existentes, tendrá que restaurar la máquina, instalar la consola MARS y ampliar la directiva.

- Para restaurar la máquina, realice los pasos siguientes:
  1. [Restaure la máquina virtual a una máquina de destino alternativa](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. Vuelva a crear la máquina de destino con el mismo nombre de host que la máquina de origen
  1. Instale el agente y vuelva a registrarse en el mismo almacén y con la misma frase de contraseña
  1. Inicie el cliente de MARS para ampliar la duración de retención según sus requisitos
- La máquina recién restaurada, protegida con MARS, seguirá realizando copias de seguridad.  

## <a name="configuring-antivirus-for-the-mars-agent"></a>Configuración del antivirus para el agente de MARS

Se recomienda la siguiente configuración para el software antivirus con el fin de evitar conflictos con el funcionamiento del agente de MARS.

1. **Agregue exclusiones de rutas de acceso**: para evitar la degradación del rendimiento y los posibles conflictos, excluya las siguientes rutas de la supervisión en tiempo real por parte del software antivirus:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` y subcarpetas
    1. **Carpeta temporal**: si la carpeta temporal no está en la ubicación estándar, agréguela también a las exclusiones.  [Consulte aquí los pasos](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-) para determinar la ubicación de la carpeta temporal.
1. **Agregue exclusiones de binarios**: para evitar la degradación de las actividades de copia de seguridad y consola, excluya los procesos de los siguientes binarios de la supervisión en tiempo real por parte del software antivirus:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>Aunque la exclusión de estas rutas de acceso será suficiente para la mayoría de los programas antivirus, puede que algunas sigan pudiendo interferir con las operaciones del agente de MARS. Si ve errores inesperados, desinstale temporalmente el software antivirus y supervise si el problema desaparece. Si se resuelve el problema, póngase en contacto con el proveedor del software antivirus para obtener ayuda con la configuración adecuada de su producto.

## <a name="monitor-using-backup-reports"></a>Supervisión mediante Informes de Backup

Azure Backup proporciona una solución de informes que usa registros de Azure Monitor y libros de Azure. Para empezar, debe tener [Informes de Backup configurado](configure-reports.md) para el almacén. Una vez configurado, los datos comienzan a fluir al área de trabajo y se pueden consultar mediante los informes de copia de seguridad.

Para supervisar el uso de datos de copia de seguridad y la renovación diaria, siga estos pasos:

1. Vaya al panel **Información general** del almacén y haga clic en **Informes de Backup**.

1. En la hoja **Informe de Backup**, en la sección **Información general**, seleccione el área de trabajo de Log Analytics configurada. 

1. Establezca el filtro de informe **Solución de Backup** en **Agente de Azure Backup** para ver solo las copias de seguridad del agente de MARS. 

   Establezca **Nombre de suscripción**, **Ubicación del almacén** y **Nombre del almacén** según corresponda.
 
    ![Establezca el filtro de informe Solución de Backup.](./media/backup-azure-manage-mars/set-report-filter-backup-solution.png)

1. Para ver el uso por entidad facturada, vaya a la pestaña **Uso**. 

   Se muestran el total de instancias protegidas facturadas y los datos de uso del almacenamiento. También puede ver la información de tendencia.
 
    ![Ver el uso por entidad facturada.](./media/backup-azure-manage-mars/view-usage-by-billed-entity.png)

1. Para ver el promedio de datos de copia de seguridad agregados por los trabajos de copia de seguridad para cada volumen del servidor protegido, vaya a la pestaña **Trabajos**. 
 
    ![Ver el promedio de datos de copia de seguridad.](./media/backup-azure-manage-mars/view-average-backup-data.png)

Obtenga más información sobre [otras pestañas de informes](configure-reports.md) y sobre cómo recibir dichos [informes por correo electrónico](backup-reports-email.md).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre los escenarios admitidos y las limitaciones, consulte la [matriz de compatibilidad para el agente de MARS](./backup-support-matrix-mars-agent.md).
- Obtenga más información acerca del [comportamiento de retención de la directiva de copia de seguridad a petición](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
- Para consultar las preguntas más frecuentes, vea [Preguntas más frecuentes: agente de Microsoft Azure Recovery Services (MARS)](backup-azure-file-folder-backup-faq.yml).
