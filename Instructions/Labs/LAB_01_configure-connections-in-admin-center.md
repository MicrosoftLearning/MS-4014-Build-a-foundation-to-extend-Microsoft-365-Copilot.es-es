# Laboratorio de práctica 1: Configuración de las conexiones para el conector de Microsoft Graph

## Inquilinos de WWL: términos de uso

Si se te proporciona un inquilino porque estás realizando un curso dirigido por un instructor, ten en cuenta que ese inquilino está disponible únicamente como apoyo para los laboratorios prácticos del curso.

Los inquilinos no deben compartirse ni usarse para otros fines que no sean los de los laboratorios prácticos. El inquilino usado en este curso es un inquilino de prueba y no se puede usar ni tener acceso a él después de que la clase haya terminado y no sea apto para la extensión.

Los inquilinos no se deben convertir a suscripciones de pago. Los inquilinos obtenidos como parte de este curso siguen siendo propiedad de Microsoft Corporation y nos reservamos el derecho de acceso y recuperación en cualquier momento.

## Resumen

En este laboratorio, usarás el Centro de administración de Microsoft 365 para crear una conexión a los archivos de clientes mediante el conector de recursos compartidos de archivos de Microsoft.

Imagina que eres un administrador de servicio de asistencia al cliente de Contoso, una empresa mediana. Tu equipo ha estado teniendo problemas con los tiempos de respuesta y la eficacia general al controlar las consultas de los clientes. Decides aprovechar el Centro de administración de Microsoft 365 para crear una conexión FileShare que permita a los agentes de servicio de asistencia al cliente acceder y hacer referencia rápidamente a los archivos de cliente.

## Ejercicio 1: Configuración de las conexiones del Centro de administración de Microsoft 365

### Inicio de sesión en la máquina virtual

El proveedor de hospedaje de laboratorio proporciona una contraseña para la cuenta de administrador MOD, que es el administrador de inquilinos predeterminado. Con fines de seguridad, Microsoft ha configurado el inquilino de prueba para que todos los usuarios predefinidos deban cambiar su contraseña en el siguiente inicio de sesión. Inicie sesión en **LON-CL1** con la cuenta de **Administrador** local que ha creado el proveedor de hospedaje de laboratorio con la contraseña **Pa55w.rd**.

### Tarea 1: Conceder de permisos en Azure Portal

1. Ve a Azure Portal **https://www.portal.azure.com** e inicia sesión con tus credenciales administrativas. No guardes la contraseña y selecciona **Sí** para **Mantener sesión iniciada.**
2. En la pantalla **Bienvenido a Microsoft Azure**, selecciona **Cancelar**.
1. Selecciona el icono del menú desplegable en el lado izquierdo de la pantalla para mostrar el menú Portal. Selecciona **Microsoft Entra ID -> Administrar -> Registros de aplicaciones**.
1. Selecciona **Nuevo registro** en la barra de menú superior. Aparece la página **Registrar una aplicación**. En esta página, proporciona un nombre para la aplicación; vamos a llamar a esta aplicación **Contoso Fileshare**. Deja la opción predeterminada de tipos de cuentas compatibles como **Solo cuentas en este directorio organizativo (Solo en Contoso: espacio empresarial único).** No selecciones un **URI de redirección** opcional.
1. Seleccione **Registrar**. Se crea la aplicación y se le asigna un Id. de aplicación. Usarás esta información al crear el Agente del conector de Graph (GCA) en los pasos siguientes. Sin embargo, antes de crear el GCA, vamos a configurar la configuración necesaria.

El siguiente paso es conceder permisos para el agente del conector de Graph de Azure Portal:

1. Selecciona la opción **Administrar -> Permisos API** del menú de la izquierda.
1. Selecciona **Agregar un permiso -> Microsoft Graph -> Permisos de aplicación**, y permite permisos a las siguientes API:

    - Directorio -> Directory.Read.All
    - ExternalConnection -> ExternalConnection.ReadWrite.OwnedBy
    - ExternalItem -> ExternalItem.ReadWrite.All
      
1. Seleccione el botón **Agregar permisos**.
1. Selecciona **Conceder consentimiento de administrador para Contoso** y confirma al seleccionar **Sí**.

**Nota:** No cierres esta pestaña del explorador Edge. Las siguientes tareas requieren copiar y pegar información de Azure Portal.

### Tarea 2: Instalar el GCA

1. Abre una nueva pestaña del explorador Microsoft Edge. Ve a la siguiente dirección URL para descargar el agente del conector de Graph: **https://www.microsoft.com/en-us/download/details.aspx?id=104045**. Seleccione el botón **Descargar**. 
1. Abre el archivo **GcaInstaller_3.1.1.0.msi** y sigue las instrucciones del Asistente para instalación. 
2. En la barra de **búsqueda** de la parte inferior de la pantalla, escribe **Configuración del agente del conector de Graph** y selecciona la aplicación en el menú cuando aparezca.
3. Para permitir que la aplicación realice cambios en el dispositivo, selecciona **Sí**.
4. Inicia sesión y registra la GCA con la cuenta **administrativa MOD**. Aparece una confirmación de que la autenticación se ha completado en el explorador Edge. Puede cerrar esta ventana.
5. Para abrir la aplicación GCA, selecciona el icono situado en la parte inferior de la pantalla.
1. Asigna a este agente el nombre **ContosoFiles**.
1. Selecciona la pestaña Microsoft Edge de Azure Portal (debe decir **Contoso Fileshare - Microsoft Azure**), ve a la pantalla **Información general** y copia el **Id. de aplicación (cliente)**. Pégalo en la aplicación de instalación de GCA.

Después, necesitas establecer un secreto de cliente para esta aplicación en Azure Portal.

1. Vuelve a la pestaña perimetral de Azure Portal y ve a **Certificados y secretos**.
1. Seleccione **+ Nuevo secreto de cliente**. Para agregar una **Descripción**, escribe **Archivos de Contoso**. Puedes dejar el campo de expiración establecido en los 180 días predeterminados.
2. Seleccione **Agregar**.
3. Copia el campo **Valor** del secreto silencioso.
1. Vuelve a la aplicación del agente del conector de Graph y pega el valor en el campo **Contraseña de aplicación (secreto de cliente)** de la aplicación del instalador de GCA.
1. Seleccione **Registrar**.
1. Una vez completado el registro, cierra la aplicación Instalador.

### Tarea 3: Descargar los archivos de recursos desde GitHub

Para configurar el conector mediante el GCA, necesitarás archivos locales para el sistema. 

1. Abre un nuevo explorador Edge y escribe **https://github.com/MicrosoftLearning/MS-4014-Build-a-foundation-to-extend-Microsoft-365-Copilot/tree/master/ResourceFiles** en la barra de direcciones.
2. Selecciona el primer archivo de la carpeta, **Contoso Chai Tea market trends 2023.xls** para abrirlo.
3. Selecciona el botón de **puntos suspensivos (más acciones de archivo)** en la parte superior derecha de la pantalla y después selecciona **Descargar**.
4. Repite el paso 3 para cada uno de los archivos restantes.
5. Puedes cerrar esta ventana una vez completadas las descargas.
6. Usa el explorador de archivos y ve al directorio C:\. Crea una carpeta denominada **ResourceFiles**.
7. Abre una nueva ventana del explorador de archivos y ve a la carpeta **Descargar** para acceder a los archivos de Contoso que has descargado de GitHub. Copia estos archivos en el directorio **C:\ResourceFiles**.

Usarás estos archivos como el contenido de origen de la conexión que crees en el Centro de administración de Microsoft.

### Tarea 4: Abrir el Centro de administración de Microsoft

1. Abre una nueva pestaña del explorador Microsoft Edge. En el explorador Microsoft Edge, ve a la página **Página principal de Microsoft 365** y escribe la siguiente dirección URL en la barra de direcciones: **https://portal.office.com**
1. Si se te pide que inicies sesión, escribe el **Nombre de usuario** y la **Contraseña** de LON-CL1 que te ha proporcionado tu proveedor de hospedaje de laboratorio para tu inquilino de prueba de Microsoft 365. El nombre de usuario debe tener el formato **<admin@xxxxxZZZZZZ.onmicrosoft.com>**, donde xxxxxZZZZZZ es el prefijo de inquilino asignado por el proveedor de hospedaje de laboratorio. 
1. La página **Le damos la bienvenida a Microsoft 365** aparece en tu explorador Microsoft Edge en la pestaña **Inicio | Microsoft 365**. Esta es la página principal de Microsoft 365 del administrador MOD.
1. Selecciona **Administrador** en la lista de iconos de aplicaciones que aparecen en el panel de navegación; esta acción abre el **Centro de administración de Microsoft 365** en una nueva pestaña del explorador.
1. Selecciona **... Mostrar todo** para mostrar el menú de navegación completo. Selecciona **Configuración** -> **Búsqueda e inteligencia.**
1. En la pestaña **Información general** que aparece, desplázate hacia abajo. Selecciona **Agregar la primera conexión** en el cuadro **Conectores**.

A partir de aquí, se muestra una lista de orígenes de datos disponibles. Estamos configurando una conexión mediante el conector de recurso compartido de archivos. Este conector permite que la búsqueda de Microsoft 365 Copilot haga referencia a archivos de clientes dentro de una búsqueda de Copilot y permite tiempos de respuesta más rápidos y respuestas más precisas a medida que un agente del servicio de asistencia al cliente encuentra respuestas.

1. Selecciona **Recurso compartido de archivos** y después **Siguiente**.
1. Escribe el **Nombre para mostrar**. El nombre para mostrar es un nombre único que se muestra a los usuarios. Vamos a escribir **Archivos de Contoso** en el campo.
1. Escribe la **ruta de acceso de la carpeta.** Tenemos archivos de ejemplo configurados en el directorio siguiente:

   **C:\ResourceFiles**

Esta ruta de acceso es el directorio donde se almacenan los archivos.

1. Selecciona el GCA que hemos creado en los pasos anteriores (ContosoFiles) en el campo **Agente del conector de Graph** si no es la opción predeterminada.
1. Selecciona **Windows** como tipo de autenticación y después escribe el **Nombre de usuario** y la **Contraseña** de LON-CLI para la autenticación de Windows.
1. Selecciona la casilla de verificación para autorizar a Microsoft a crear un índice de datos de terceros en tu inquilino.
1. Seleccione **Crear**.  Aparece una pantalla de éxito y tu conexión comienza a sincronizarse. Puedes escribir el tipo específico de contenido, los departamentos de tu organización que pueden beneficiarse de su uso o ejemplos de flujos de trabajo en el cuadro **Descripción del conector**. Por ejemplo:

    **Este conector contiene información en el servidor de recursos compartidos de archivos local. Incluye perfiles de cliente y preguntas de cliente que pueden beneficiar al departamento de soporte técnico para responder a las consultas de los clientes.**
1. Selecciona **Listo.** Tu conexión aparece ahora en la pestaña **Búsqueda e inteligencia** del Centro de administración de Microsoft 365, y se puede hacer referencia a ella en los resultados de búsqueda o al crear un agente de Microsoft 365 Copilot.
