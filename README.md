# Documentación de la API para Facturas Electrónicas (ECF DGII)

### Introducción:


### Este proyecto es una API desarrollada con Flask que permite la validación y registro de facturas electrónicas (ECF) con la DGII, así como la gestión de usuarios y clientes. La aplicación integra firmas electrónicas para la validación de las facturas y realiza procesos de verificación a través de diferentes rutas.


### Requisitos Previos:

### Para ejecutar este proyecto, necesitas tener instaladas las dependencias localizadas en el archivo requirements.txt

#### De igual manera debes contar con:

#####Python 3.x 
#####PostgreSQL
#####Certificado P12 el cual lo otorga la DGII
#####Un ERP en el cual facturar, se necesita para hacer la conexión con esta API
#####Conocimientos intermedio de POO



    Módulos adicionales importados en el proyecto (aplication.controllers.API_DATA, aplication.integrations.dgii_integration, etc.).

    Además, asegúrate de que los siguientes elementos estén correctamente configurados:

    Un archivo de configuración que maneje claves secretas (por ejemplo, SECRET_KEY).
    Archivos y módulos externos para la integración con la DGII, firma de facturas y validación de datos.
    Directorios para almacenamiento de usuarios, archivos XML y logs.


### Estructura de Rutas:

### A continuación, se detalla cada una de las rutas implementadas en la API:

    Rutas Principales:
    ```
    POST /api/fct/
    ```
    Esta ruta permite recibir y validar las facturas electrónicas enviadas por los usuarios.

    Parámetros de cabecera:

    Authorization: Token del usuario para la autenticación.
    Ambiente: Define el ambiente en el cual se trabaja (producción o prueba).
    Cuerpo de la solicitud: JSON con la información de la factura y los ítems.

    Validaciones realizadas:

    Campos vacíos o nulos.
    Validación de IndicadorFacturacion, IndicadorBienOServicio, entre otros.
    Respuesta: Retorna el estado de la factura, con detalles si es aceptada o rechazada.
    ```
    POST /api/fct/aprobacion/
    
    ```
    Ruta para aprobar facturas electrónicas previamente validadas.

    Cuerpo de la solicitud: JSON con los detalles de la factura.
    Respuesta: Retorna los detalles de la aprobación o rechazo de la factura.
    ```
    GET /validar
    ```
    Ruta que permite validar una cédula o RNC contra un archivo local.

    Parámetros de consulta:

    cedulaornc: Parámetro de cédula o RNC que se quiere validar.
    Respuesta: Información de la validación de la cédula o RNC, o un error si no es válido.
    
    ```
    GET /
    Ruta para renderizar el formulario de login.
    
    ```
    ```
    Métodos disponibles: 

    GET y POST.

    Validaciones: Verifica la cédula o RNC y la contraseña ingresada.

    GET /Admin_dashboard

    Página del panel de control para administradores. Solo accesible si el usuario está autenticado.

    GET /dashboard

    Página del panel de control para usuarios. Solo accesible si el usuario ha iniciado sesión correctamente.
    
     ```
### Clases Importantes:

    General_Options

    Clase que gestiona la eliminación de archivos y la verificación de directorios en segundo plano utilizando hilos. Se asegura de que los directorios necesarios existan y que los archivos antiguos sean eliminados periódicamente.

    LoggerConfig

    Clase que gestiona la configuración del sistema de logs para registrar eventos importantes en el sistema, como errores o validaciones fallidas.


### Manejo de Errores:

    El proyecto maneja errores HTTP de la siguiente manera:

    401 Unauthorized: Se redirige al usuario a la página de inicio de sesión si no tiene autorización.

    400 Bad Request: Se responde cuando no se proporcionan los parámetros requeridos o son incorrectos.   

###  Autenticación:

        La autenticación se realiza a través de tokens que se envían en los encabezados de las solicitudes. El sistema también gestiona sesiones utilizando cookies firmadas con la clave secreta SECRET_KEY.

### Integraciones Externas:

    El proyecto realiza integraciones con:

    DGII: Para la validación y firma de facturas electrónicas.

    Signature: Para el manejo de firmas electrónicas en las facturas.

###  Consideraciones Finales:

    Es importante mantener actualizados los archivos de integración con la DGII y el módulo de firma para   evitar problemas de validación de facturas.

    La limpieza periódica de archivos es gestionada de forma automática por la clase General_Options,   asegurando que el sistema no se sobrecargue de archivos innecesarios.



### Descripción General:

    Este módulo proporciona una API para gestionar usuarios, clientes, y facturas electrónicas (ECF) en   conformidad con la normativa de la DGII. El sistema está integrado con PostgreSQL para gestionar las  bases de datos de usuarios y facturas, y cuenta con un sistema de logs rotativos para monitorear las operaciones.


### Clases Principales:

    Config
    Maneja la conexión a la base de datos PostgreSQL y provee una interfaz para ejecutar consultas.

    Atributos:

    __DATABASE: Diccionario que contiene las credenciales de la base de datos (dbname, user, password, host,    port).
    __conn: Objeto de conexión a la base de datos.


    ### Métodos:
        __connect(): Conecta a la base de datos usando las credenciales almacenadas.
        close(): Cierra la conexión a la base de datos si está activa.

        cursor(): Context manager para manejar operaciones con el cursor de PostgreSQL, gestiona la apertura ycierre del cursor de forma segura.

### User

    Maneja las operaciones relacionadas con los usuarios, tales como la validación de tokens, el registro de facturas, y la gestión de clientes.

    
    Atributos:
    
    __token: Token de autenticación del usuario.
    
    __config: Instancia de la clase Config para manejar la conexión a la base de datos.
    
    Métodos:
    
    get_rnc(): Retorna el RNC del usuario.
    
    get_razon_social(): Retorna la razón social del usuario.
    
    get_nombre_comercial(): Retorna el nombre comercial del usuario.
    
    get_direccion(): Retorna la dirección del usuario.
    
    get_municipio(): Retorna el municipio del usuario.
    
    get_provincia(): Retorna la provincia del usuario.
    
    get_telefono(): Retorna el teléfono del usuario.
    
    get_email(): Retorna el correo electrónico del usuario.
    
    get_p12_password(): Retorna la contraseña del certificado P12 del usuario.
    
    get_token(): Retorna el token del usuario.
    
    get_quantity_ecf_suministrados(): Devuelve la cantidad de ECF (facturas electrónicas) suministradas por     
    el usuario.
    
    view_ecf_suministrados(max=None): Devuelve una lista de las facturas electrónicas aceptadas para el     
    usuario, opcionalmente se puede limitar el número de resultados con max.
    
    get_all_clients(rnc, max=None): Devuelve todos los clientes asociados con un RNC determinado, con una   
    opción para limitar el número de resultados.
    
    register_client(rnc, razon_social, direccion, email): Registra un nuevo cliente en la base de datos.
    
    register_invoice(tipo_ecf, numero_comprobante, estado, fecha, token, produccion, mensaje, trackid, rnc):    
    Registra una factura en la base de datos.
    
    accept_ecf(tokenUSER, IPUSER, **kwargs): Acepta una factura electrónica y la registra en la base de datos.
    
    get_last_ecf_accepted(token, ECF_FIRST): Devuelve el último ECF aceptado para un token de usuario   
    específico.

### LoggerConfig
    Configura el sistema de logging para el proyecto, utilizando archivos rotativos diarios para almacenar  los registros.

    Atributos:
    logger: Instancia del logger configurado para escribir en un archivo con rotación diaria.

    Métodos:
    log_message(message, level='info'): Registra un mensaje con el nivel de severidad especificado. Los   niveles pueden ser debug, info, warning, error, o critical.

### Flujo de Operación

    Conexión a la Base de Datos:

    La clase Config maneja la conexión con la base de datos PostgreSQL y proporciona un context manager   (cursor) para ejecutar consultas de forma segura y eficiente.

    Autenticación de Usuarios:

    La clase User toma un token_auth en su constructor y lo utiliza para validar al usuario en la base de datos. Si el token es válido, se cargan los detalles del usuario desde la tabla usuario.

    Gestión de Facturas Electrónicas (ECF):

    Los usuarios pueden registrar y consultar facturas electrónicas. La clase User incluye métodos para   registrar facturas (register_invoice) y consultar facturas anteriores (view_ecf_suministrados).

    Registro de Clientes:

    Los clientes se gestionan mediante la tabla cliente en la base de datos, y los usuarios pueden registrar    nuevos clientes utilizando el método register_client.

    Logs:

    El sistema de logs está configurado para escribir mensajes de log en archivos rotativos diarios, lo que   permite una fácil auditoría de las operaciones del sistema.



### Ejemplo de Uso

    Registro de una Factura Electrónica:

    ```
    user = User(token_auth="mi_token_secreto")

    Registrar una factura
    invoice_id = user.register_invoice(
    tipo_ecf="01",
    numero_comprobante="B0100000001",
    estado="Aceptado",
    fecha=datetime.datetime.now(),
    token="mi_token_secreto",
    produccion=False,
    mensaje="Factura validada correctamente",
    trackid={"id": 12345},
    rnc="101010101"
    )

    print(f"Factura registrada con ID: {invoice_id}")

    ```
### Consultar Facturas Aceptadas:


```
user = User(token_auth="mi_token_secreto")

 Ver las últimas 5 facturas aceptadas

facturas = user.view_ecf_suministrados(max=5)
for factura in facturas:
    print(f"Fecha: {factura[0]}, Número ECF: {factura[1]}, Estado: {factura[2]}")

```


### Consideraciones Finales:

    El sistema gestiona las conexiones a la base de datos de forma segura mediante el uso de un     contextmanager en la clase Config, asegurando que las transacciones se realicen correctamente y que los recursos sean liberados.

    La clase LoggerConfig garantiza que las operaciones sean auditables mediante el uso de archivos de log  con rotación diaria.

    Este sistema está preparado para manejar facturas electrónicas en ambientes de prueba y producción,   aunque es importante que los usuarios no autorizados para producción sean bloqueados durante la     validación.



## Documentación del Módulo de Gestión de Usuarios y Tokens:


    Este módulo maneja la creación y gestión de usuarios, así como la generación y almacenamiento de tokens seguros utilizando cifrado simétrico con la biblioteca cryptography.fernet. El módulo está diseñado para integrarse con una base de datos PostgreSQL y ofrece funciones de gestión de usuarios y autenticación basada en tokens.


### Clases Principales:

UserManager

Descripción:

La clase UserManager se encarga de la gestión de usuarios en la base de datos. Proporciona métodos para verificar la existencia de un usuario, crear un nuevo usuario y obtener los detalles de un usuario.


    Métodos:

    __init__(self, db_config)

    Descripción: Inicializa la clase con la configuración de la base de datos.

    Parámetros:

    db_config (dict): Diccionario con los parámetros de conexión a la base de datos.

    rnc_exists(self, rnc)

    Descripción: Verifica si un RNC ya existe en la base de datos.

    Parámetros:

    rnc (str): El RNC que se va a verificar.

    Retorna:

    bool: True si el RNC existe, False si no.

    create_user(self, nombre, rnc, razon_social, nombre_comercial, direccion, municipio, provincia, telefono, 
    email, produccion, correo, token)

    Descripción: Crea un nuevo usuario en la base de datos si el RNC no existe.

    Parámetros:

    nombre (str): Nombre del usuario.

    rnc (str): RNC del usuario.

    razon_social (str): Razón social del usuario.

    nombre_comercial (str): Nombre comercial del usuario.

    direccion (str): Dirección del usuario.

    municipio (str): Municipio del usuario.

    provincia (str): Provincia del usuario.

    telefono (str): Teléfono del usuario.

    email (str): Correo electrónico del usuario.

    produccion (bool): Indica si el usuario está en producción o no.

    correo (str): Correo para notificaciones.

    token (str): Token de autenticación del usuario.

    Retorna:

    int: El ID del usuario recién creado.

    Excepciones:

    ValueError: Si el RNC ya existe en la base de datos.

    psycopg2.Error: Si ocurre un error al intentar insertar el nuevo usuario.

    get_user(self, nombre)

    Descripción: Obtiene los detalles de un usuario a partir de su nombre.

    Parámetros:

    nombre (str): El nombre del usuario.

    Retorna:

    tuple: Una tupla con los detalles del usuario (ID, nombre, token).

### TokenManager:


    Descripción:

    La clase TokenManager se encarga de la generación, almacenamiento y validación de tokens. Los tokens 
    están cifrados utilizando cryptography.fernet para asegurar su integridad y confidencialidad.


    Métodos:

    __init__(self, db_config, encryption_key=None)

    Descripción: Inicializa la clase con la configuración de la base de datos y una clave de cifrado 
    opcional. Si no se proporciona una clave de cifrado, se genera una nueva.

    Parámetros:

    db_config (dict): Diccionario con los parámetros de conexión a la base de datos.

    encryption_key (bytes, opcional): Clave de cifrado para Fernet. Si no se proporciona, se genera una nueva.

    generate_token(self, usuario_id)

    Descripción: Genera un token seguro para un usuario, con una caducidad de 1 hora a partir del momento de 
    generación.

    Parámetros:

    usuario_id (int): El ID del usuario para el cual se generará el token.

    Retorna:

    str: El token generado (cifrado con Fernet).

    store_token(self, usuario_id)

    Descripción: Genera un token y lo almacena en la base de datos asociado al usuario correspondiente.

    Parámetros:

    usuario_id (int): El ID del usuario para el cual se generará y almacenará el token.

    Retorna:

    str: El token generado y almacenado.

    Excepciones:

    psycopg2.Error: Si ocurre un error durante la actualización del token en la base de datos.

    Nota:

    El método verify_token está comentado, pero podría implementarse para verificar la validez y caducidad de 
    los tokens.


### Ejemplo de Uso:

    Creación de un Usuario

     db_config = {
        'dbname': 'mi_base_de_datos',
        'user': 'mi_usuario',
        'password': 'mi_contraseña',
        'host': 'localhost',
        'port': '5432'
    }

### user_manager = UserManager(db_config)

### Crear un nuevo usuario
    try:
     usuario_id = user_manager.create_user(
            nombre="Juan Pérez",
            rnc="101010101",
         razon_social="Empresa S.A.",
            nombre_comercial="Comercial JP",
            direccion="Calle Falsa 123",
            municipio="Santo Domingo",
         provincia="Distrito Nacional",
         telefono="809-123-4567",
            email="juan@example.com",
            produccion=False,
            correo="notificaciones@example.com",
            token=None
        )
        print(f"Usuario creado con ID: {usuario_id}")
    except ValueError as e:
     print(e)


### Generación de un Token:

    Generación de un Token

    token_manager = TokenManager(db_config)

### Generar y almacenar un token para un usuario

    usuario_id = 1  # ID del usuario en la base de datos
    token = token_manager.store_token(usuario_id)
    print(f"Token generado: {token}")

### Consideraciones Finales:

    Seguridad:

    Los tokens están cifrados utilizando Fernet (cifrado simétrico), lo que garantiza que solo el  servidor que posee la clave de cifrado puede descifrar los tokens.

    Caducidad de Tokens: 
    Los tokens generados tienen una caducidad de 1 hora por defecto. Esto puede ser a justado modificando la lógica de tiempo en generate_token.

### Aquí tienes la documentación para el código proporcionado, que describe el uso de Flask, Blueprints y el archivo General_Options dentro del módulo files:


### Documentación del Servidor Flask con Blueprints:

    Este es un servidor básico de Flask que utiliza Blueprints para estructurar y organizar mejor las rutas de la API. Además, ejecuta una funcionalidad auxiliar de manejo de archivos a través de la clase General_Options, importada desde el módulo files.


    Componentes Clave:

    Blueprints

    En este proyecto, se utiliza Flask Blueprints para dividir las rutas de la aplicación en componentes  reutilizables y más fáciles de gestionar. En este caso, las rutas relacionadas con la API están  registradas en el api_blueprint.

    Importante:

    api_blueprint: El Blueprint relacionado con las rutas de la API está ubicado en aplication.routes.  api_routes. Este Blueprint se registra en la aplicación Flask con el prefijo /api. Esto significa que todas las rutas dentro de este Blueprint estarán bajo el prefijo /api.

### Registro de Blueprints:

```
app.register_blueprint(api_blueprint, url_prefix='/api')

```

### Este comando registra el api_blueprint para que todas las rutas definidas dentro del Blueprint se accedan con el prefijo /api

### Secret Key para Cookies de Sesión:

    La variable SECRET_KEY se utiliza para firmar las cookies de sesión en Flask, proporcionando seguridad al   evitar modificaciones no autorizadas de las cookies en el navegador del usuario.

    ```
    SECRET_KEY = 'Con esta clave secreta se firmarán las cookies de sesión'

    ```
### Nota: En un entorno de producción, esta clave debe ser almacenada de manera segura y no debe estar hardcodeada en el código.


### General_Options:

    Esta clase, que se importa desde aplication.controllers.files, se ejecuta cuando se inicia el servidor. Aunque no se proporcionó el código de la clase General_Options, generalmente, este tipo de clases se  utilizan para realizar tareas de fondo como la limpieza de archivos, verificación de rutas o configuraciones adicionales antes de levantar el servidor.

```
files.General_Options()

```

### Instrucciones de Ejecución:

    El servidor Flask está configurado para ejecutarse en modo de desarrollo con debug=True, lo que habilita el reinicio automático del servidor cuando se realizan cambios en el código.

    Para ejecutar la aplicación:

    Instalar dependencias: Asegúrate de tener instaladas las dependencias necesarias como Flask

    ```
    pip install flask


    ```

### Ejecutar la aplicación: 

    Usa el siguiente comando para iniciar la aplicación Flask.


    ```
    python nombre_del_archivo.py

    ```
### Ejecutar la aplicación: Usa el siguiente comando para iniciar la aplicación Flask.


    ```
    python nombre_del_archivo.py


    ```

### El servidor estará disponible en http://127.0.0.1:5000/ y las rutas dentro del api_blueprint estarán bajo el prefijo /api. Por ejemplo, una ruta en api_blueprint definida como /ruta será accesible en http://127.0.0.1:5000/api/ruta.


### Consideraciones Finales:

    Estructuración con Blueprints:
     Usar Blueprints ayuda a modularizar la aplicación, lo que es útil a medida   que la aplicación crece y necesita manejar múltiples conjuntos de rutas.

    Seguridad: 
    
    Asegúrate de que la SECRET_KEY se maneje de manera segura en producción.

    Tareas de fondo: La clase General_Options parece realizar tareas necesarias antes o durante la ejecución de la aplicación. Asegúrate de revisar su implementación para comprender completamente su funcionalidad.


### Esta documentación proporciona una descripción clara de los componentes principales de tu servidor Flask y su funcionalidad básica. Si necesitas agregar más detalles sobre las rutas o la clase General_Options, no dudes en mencionarlo.


### Este proyecto es una API desarrollada con Flask para gestionar y validar facturas electrónicas (ECF) a través de integraciones con la DGII, y para realizar validaciones de cédulas y RNC. Incluye características como la autenticación basada en tokens, la firma de facturas, y la gestión de archivos asociados con los usuarios.

### Rutas Principales:

    
    POST /api/fct/ - Procesamiento de Facturas Electrónicas
    
    Esta ruta procesa una factura electrónica (ECF) enviada por el usuario.

    
    Flujo General:
    
    Validación de la solicitud:
    
    Verifica que se incluyan el token de autorización y el ambiente.
    
    Valida los campos enviados en la factura, asegurándose de que no estén vacíos o sean inválidos.
    
    Crea un directorio para almacenar la factura en formato JSON si aún no existe.
    
    Validación de ítems:
    
    Verifica que cada ítem tenga valores correctos para campos como IndicadorFacturacion,   
    IndicadorBienOServicio, CantidadItem, entre otros.
    
    Firma de la factura:
    
    Firma electrónicamente la factura utilizando la clase Signature y envía la información a la DGII a través   
    de la API de integración APIS_DGII.
    
    Registro de la factura:
    
    Registra la factura en la base de datos del usuario y retorna el resultado al cliente, incluyendo un    
    enlace a la consulta de la factura en la DGII si es aceptada.
    
    Posibles Respuestas:
    
    200 OK: Factura aceptada o aceptada condicionalmente.
    
    400 Bad Request: Faltan campos o están vacíos.
    
    401 Unauthorized: Token de autorización no válido.

### POST /api/fct/aprobacion/ - Aprobación de Facturas
    
    Esta ruta recibe una factura que ya ha sido validada y firma la aprobación, verificando que el usuario esté autorizado y que la firma de la factura sea correcta.

    Parámetros:
    
    El cuerpo de la solicitud debe ser un JSON con los detalles de la factura a aprobar.
    
    El token de autorización se pasa a través de los headers.
    
    Respuestas:
    
    200 OK: Factura aprobada exitosamente.
    
    400 Bad Request: Error en la firma o aprobación.

### GET /validar - Validación de Cédula o RNC:

    Permite la validación de una cédula o RNC contra un archivo de texto local (simulando una consulta a la DGII).

    Parámetros de Consulta:
    cedulaornc: Parámetro requerido en la URL para validar la cédula o RNC.

    Respuestas:
    200 OK: Retorna el resultado de la validación si la cédula o RNC es válida.
    400 Bad Request: Si no se pasa la cédula o RNC o si el formato es incorrecto.


### GET / y POST / - Página de Inicio (Login):

    La página de inicio permite a los usuarios iniciar sesión introduciendo su cédula o RNC y una contraseña.  También se valida si el usuario tiene permisos de administrador.

    Flujo:
    Si el usuario es un administrador, será redirigido a la página de administración (/Admin_dashboard).
    Si el usuario es regular, será redirigido a su página de dashboard (/dashboard).

    Respuestas:
    200 OK: Página de login o redirección a la página correspondiente.
    400 Bad Request: Si no se pasa la cédula o contraseña.
    401 Unauthorized: Si la cédula o RNC no coinciden con la contraseña.

### GET /Admin_dashboard - Panel de Administración:

    Página reservada para administradores. Solo accesible si el usuario ha iniciado sesión como administrador.

    ### GET /dashboard - Panel de Usuario
    Página de dashboard para usuarios regulares. Solo accesible si el usuario ha iniciado sesión    correctamente.

### Clases Importantes:
    
    User
    
    La clase User se utiliza para gestionar las operaciones relacionadas con los usuarios, como obtener   información de un usuario, registrar clientes y facturas, y verificar el token de autenticación.

    
    Métodos Relevantes:
    
    get_rnc(): Retorna el RNC del usuario.
    
    get_last_ecf_accepted(): Obtiene el último ECF aceptado para un usuario.
    
    register_client(): Registra un nuevo cliente en la base de datos.
    
    register_invoice(): Registra una factura en la base de datos.


### Signature:

        Clase utilizada para la firma electrónica de facturas. Es esencial para validar que la factura cumple con las normativas requeridas por la DGII.

        Métodos Relevantes:

        Semilla_Signature(): Genera una semilla para la firma.
        Invoice_Signature(): Firma la factura antes de enviarla a la DGII.

### APIS_DGII:

    lase encargada de la integración con las APIs de la DGII. Esta clase gestiona la comunicación entre la API desarrollada y los servicios de la DGII.

    Métodos Relevantes:

    validar_Semilla_Stage(): Valida la semilla de la firma en la DGII.

    remove_file(): Elimina archivos antiguos no necesarios.


### Manejo de Errores:

    El proyecto tiene un manejo de errores básico, especialmente en la validación de solicitudes y la autenticación de usuarios.

    401 Unauthorized: Redirige al usuario al formulario de inicio de sesión si no tiene autorización.

    400 Bad Request: Retorna un mensaje de error si faltan parámetros en las solicitudes o si son incorrectos.

### Consideraciones de Seguridad:

    SECRET_KEY: Se utiliza para firmar las cookies de sesión. Es importante que esta clave sea segura y no se exponga en entornos de producción.

    Tokens de Autenticación: El token del usuario se verifica en cada solicitud para asegurarse de que esté autorizado para realizar la operación.

### Ejecución

    Para ejecutar este proyecto:

    Instalar dependencias: Asegúrate de tener Flask y las demás dependencias instaladas.

    ```

    pip install flask

    ```
### Iniciar la aplicación:

        ```
        python nombre_del_archivo.py

        El servidor estará disponible en http://127.0.0.1:5000/

        ```

# Documentación de la API de Validación de Cédula o RNC


### Este script permite validar una cédula o RNC desde un archivo de texto que contiene registros estructurados, identificando el estado y el nombre asociados a una cédula o RNC dada. El archivo de entrada debe tener las cédulas o RNC en el primer campo de cada línea y otros datos relevantes en los siguientes campos, separados por barras verticales (|).


### Dependencias:

    os: Módulo utilizado para interactuar con el sistema de archivos (e.g., verificar si un archivo existe).

    re: Módulo para trabajar con expresiones regulares, usado para eliminar caracteres no numéricos.


### Clases:

    CedulaInvalidaException:

    Esta clase define una excepción personalizada que se lanza cuando una cédula o RNC no cumple con los requisitos de longitud.


    ```
    class CedulaInvalidaException(Exception):
    """Excepción personalizada para cédula o RNC inválida."""
    pass


    ```

### Funciones:

    validar_desde_archivo(ruta_archivo, cedulaornc):

    Esta función toma como entrada una ruta a un archivo y una cédula o RNC. Busca la cédula o RNC en el  archivo y devuelve el nombre y estado asociados si se encuentra. Si no, lanza un error adecuado.

    Parámetros:

    ruta_archivo: str. La ruta al archivo que contiene las cédulas o RNC con los demás datos.
    cedulaornc: str. La cédula o RNC que se desea buscar.

### Lógica:

    Eliminación de caracteres no numéricos: Se usa una expresión regular para eliminar caracteres que no sean números de la cédula o RNC antes de la validación.


    ```
    cedulaornc = re.sub(r'\D', '', cedulaornc)

    ```
### Validación de la longitud:

     Se verifica que la cédula tenga exactamente 11 dígitos o que el RNC tenga 9 dígitos. Si no cumple con estos requisitos, se lanza una excepción personalizada (CedulaInvalidaException).

        

     if len(cedulaornc) != 11 and len(cedulaornc) != 9:
    raise CedulaInvalidaException("La Cédula o el RNC es inválido. Debe tener 11 o 9 dígitos.")


### Verificación de la existencia del archivo: 

    Antes de intentar abrir el archivo, se verifica si este existe. Si no existe, la función retorna un error.


        if not os.path.exists(ruta_archivo):
    return {"error": f"El archivo '{ruta_archivo}' no existe."}, 400



### Lectura del archivo:

     Si el archivo existe, se abre en modo lectura con la codificación iso-8859-1 (comúnmente usada en archivos con caracteres especiales en español). Cada línea del archivo es procesada, y los campos se separan por el carácter |


     with open(ruta_archivo, 'r', encoding='iso-8859-1') as archivo:
    for numero_linea, linea in enumerate(archivo, 1):
        campos = linea.strip().split('|')


### Validación de campos: 
    Se verifica si la línea tiene al menos 10 campos, y se extraen el RNC o cédula (primer campo), el nombre (segundo campo), y el estado (décimo campo).
    
    if len(campos) >= 10:
    cedula_rnc_archivo = re.sub(r'\D', '', campos[0].strip())
    nombre = campos[1].strip()
    estado = campos[9].strip()

### Comparación y retorno de resultados:

     Si la cédula o RNC coincide, se devuelve un diccionario con los datos encontrados y un código de estado    200. Si no se encuentra la cédula o RNC, devuelve un error 404.


     if cedulaornc == cedula_rnc_archivo:
    resultado = {
        "cedula_rnc": cedula_rnc_archivo,
        "nombre": nombre,
        "estado": estado
    }
    encontrado = True
    break
    

### Excepciones:

    
    CedulaInvalidaException: Si la cédula o RNC no tiene la longitud adecuada, se lanza esta excepción.
    
    FileNotFoundError: Se lanza si el archivo no puede ser encontrado en la ruta especificada.
    
    Otras excepciones: Si ocurre cualquier otra excepción durante la lectura del archivo, se captura y  
    devuelve un error de servidor (500).
    
    Retornos:
    
    Si la cédula o RNC se encuentra: Retorna un diccionario con la cédula/RNC, nombre y estado, junto con un código 200.

    ```
    return resultado, 200

    ```

### Si la cédula o RNC no se encuentra: Retorna un mensaje de error con un código 404.

```

return {"error": f"Cédula o RNC '{cedulaornc}' no encontrada en el archivo."}, 404

```

### Si el archivo no existe o ocurre un error: Retorna un mensaje de error y un código 400 o 500, según corresponda.



### Ejemplo de Uso:
```
try:
    resultado, codigo = validar_desde_archivo('datos.txt', '00123456789')
    if codigo == 200:
        print("Resultado:", resultado)
    else:
        print("Error:", resultado["error"])
except CedulaInvalidaException as e:
    print(f"Excepción capturada: {str(e)}")

```

### Este código buscará la cédula 00123456789 en el archivo datos.txt. Si la cédula es válida y se encuentra en el archivo, se imprimirá el nombre y estado asociados. Si no es válida, o no se encuentra, se imprimirá un mensaje de error o excepción.



### Esta API proporciona endpoints para gestionar procesos de producción y aprobación de facturas. Los endpoints están diseñados para recibir datos en formato JSON y devolver respuestas en consecuencia.

    Blueprint: api_blueprint
    El blueprint api_blueprint agrupa las rutas de la API y facilita su registro en la aplicación Flask.

    Endpoints:

    /fct/
    Método: POST

    Descripción: Procesa la producción utilizando los datos enviados en la solicitud.

    Cuerpo de la Solicitud:

    Tipo: application/json

        Formato esperado:

        json

         {
         "campo1": "valor1",
         "campo2": "valor2",
    
        }

### Respuestas:

    
    200 OK: La factura fue aprobada con éxito.
    
    400 Bad Request: Hay un error en los datos enviados.
    
    404 Not Found: La factura no se encuentra.
    
    500 Internal Server Error: Ocurrió un error en el servidor.

### Notas:

    Asegúrate de manejar correctamente los errores y validar los datos recibidos en cada endpoint.
    Utiliza herramientas como Postman o cURL para probar los endpoints durante el desarrollo.

### Documentación de la API de Comunicación:

    Este módulo proporciona una interfaz para interactuar con la API de la Dirección General de Impuestos Internos (DGII) de la República Dominicana. Permite realizar operaciones como obtener una "semilla", validar esta semilla, enviar facturas electrónicas y consultar el estado de las solicitudes. El módulo maneja tanto el ambiente de prueba como el de producción.


### Requisitos:

    Asegúrate de que las siguientes bibliotecas están instaladas:

    requests
    xml.etree.ElementTree
    requests_toolbelt
    Si alguna de estas bibliotecas no está instalada, se lanzará una excepción.

    Estructura del Código
    Importación de Bibliotecas
    El módulo comienza intentando importar las bibliotecas necesarias. En caso de que no se puedan importar,se captura la excepción y se muestra un mensaje de error.

    ```

        try:
        import requests
        import xml.etree.ElementTree as ET
        from requests_toolbelt.multipart.encoder import MultipartEncoder
        import time
        import os
        import re
    except ImportError as e:
        print(f"Error al importar las librerias: {e}")
     raise
    except Exception as e:
        print(f"Error al importar las librerias: {e}")
        raise
    ```


### Clase APIS_DGII:

    Esta clase encapsula todos los métodos necesarios para interactuar con la API de la DGII.

    Constructor __init__

    ```
    def __init__(self, ambiente='prueba'):

    ```


### Parámetros:

    ambiente: Puede ser 'prueba' o 'produccion', determina el entorno de la API que se utilizará.

    Métodos

    Semilla()


### Obtiene una "semilla" del servidor.

        ```
        def Semilla(self):

        ```
### Retorno:
     Devuelve un tuple que contiene el valor y la fecha de la semilla, o None si hay un error.

    validar_Semilla(path)

    Valida una semilla utilizando un archivo XML.


    ```
    def validar_Semilla(self, path):
    ```
### Parámetros:

    path: Ruta del archivo XML que contiene la semilla.

    Retorno: Devuelve un token de la respuesta JSON.

    FacturasElectronicas(token, filename)

    Envía una factura electrónica al servidor.

    ```
    def FacturasElectronicas(self, token, filename):

    ```

### Parámetros:


    token: Token de autorización.

    filename: Ruta del archivo XML que contiene la factura.

    Retorno: Devuelve el estado de la factura o un mensaje de error.

    Estado(token, track_id)

    Consulta el estado de una factura utilizando el track_id.

```
def remove_file(self):

```

### Crear una instancia de la clase APIS_DGII en ambiente de prueba
    api_dgii = APIS_DGII(ambiente='prueba')

### Obtener la semilla:

    valor, fecha = api_dgii.Semilla()
    print(f"Semilla: {valor}, Fecha: {fecha}")

### Validar la semilla utilizando un archivo XML:

    token = api_dgii.validar_Semilla('ruta/a/archivo.xml')
    print(f"Token obtenido: {token}")

### Enviar una factura electrónica:

    resultado = api_dgii.FacturasElectronicas(token, 'ruta/a/factura.xml')
    print(resultado)

### Consultar el estado de la factura:

    estado = api_dgii.Estado(token, 'track_id_ejemplo')
    print(estado)

### Eliminar archivos XML en el directorio actual:

    api_dgii.remove_file()


### Manejo de Errores:

    El módulo maneja errores de conexión utilizando excepciones. Si se produce un error al realizar una  solicitud a la API, se imprime un mensaje de error y, en algunos casos, se lanza una excepción.

    Conclusión:
    
    Este módulo permite una integración sencilla con la API de la DGII para la gestión de facturas  electrónicas. Asegúrate de manejar correctamente los errores y de validar las respuestas del servidor para garantizar un funcionamiento adecuado.
