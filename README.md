# Sistema de Gestión de Tienda de Cristales

### Descripción General

Sistema web desarrollado en **ASP.NET Core MVC** para la gestión integral de una tienda de venta de cristales y vidrios. El sistema permite administrar clientes, coordinar visitas a domicilio, gestionar trabajos realizados y mantener un flujo de trabajo desde la solicitud del cliente hasta la entrega del producto final.

### Flujo de Trabajo

#### 1. Solicitud de Cliente
Un cliente solicita un servicio a domicilio porque se le rompió un vidrio en su casa y necesita conocer el precio. El sistema captura los siguientes datos del cliente:
- Apellido y Nombre
- Dirección completa
- Teléfono de contacto
- Horario disponible para la visita

#### 2. Gestión de Clientes
Los clientes se almacenan en el sistema para:
- Consultar historial de trabajos realizados anteriormente
- Ofrecer atención personalizada
- Buscar trabajos previos realizados al cliente
- Verificar conformidad con trabajos anteriores (satisfacción del cliente)

#### 3. Sistema de Tickets
Cada solicitud de cliente genera un **Ticket** que pasa por diferentes estados:
- **Sin Visitar**: Ticket creado, pendiente de asignación
- **Visitado**: Empleado ya realizó la visita y tomó las medidas
- **Concretado**: Trabajo confirmado y en proceso de fabricación/entrega

#### 4. Asignación y Visita
Una vez cargados los datos del cliente:
- El empleado asignado recibe una notificación (WhatsApp o app) con:
  - Ubicación del domicilio en el mapa
  - Si tiene múltiples visitas pendientes, se muestran como puntos en el mapa
  - Sistema de ruta optimizada (similar a misiones pendientes en videojuegos)
- El empleado puede marcar tickets como visitados y completar la ruta

#### 5. Registro de Medidas y Detalles
Cuando el empleado visita el domicilio y toma las medidas, se registran:
- Descripción detallada de lo que necesita el cliente
- Cantidad de vidrios requeridos
- Tipo de vidrio necesario
- Medidas de cada vidrio
- Precio unitario y precio total

#### 6. Compulsa/Ticket para Taller
Una vez confirmadas las medidas y detalles del trabajo:
- Se genera una **Compulsa** o **Orden de Taller** para los cortadores de vidrio
- Esta orden contiene todos los detalles necesarios para la fabricación

#### 7. Orden de Trabajo
Se genera una **Orden de Trabajo** imprimible que incluye:
- Presupuesto detallado
- Datos completos del cliente
- Descripción del trabajo a realizar
- Medidas y especificaciones técnicas

#### 8. Sistema de Recordatorios
El sistema incluye un módulo de **Recordatorios/Tareas** para usuarios:
- Permite anotar tareas pendientes
- Gestión de recordatorios diarios
- ABM completo de recordatorios
- Cada usuario puede gestionar sus propios recordatorios

## Estructura de Base de Datos

### Entidades Principales

#### 1. Usuario
Almacena información de los usuarios del sistema (administradores, empleados, etc.)
- **IdUsuario**: Identificador único
- **NombreUsuario**: Nombre de usuario para login
- **Email**: Correo electrónico
- **PasswordHash**: Contraseña encriptada
- **Nombre**: Nombre real
- **Apellido**: Apellido
- **Avatar**: Ruta al archivo de avatar
- **IdRol**: Relación con Rol
- **Activo**: Indica si el usuario está activo

#### 2. Rol
Define los roles del sistema
- **IdRol**: Identificador único
- **Nombre**: Nombre del rol (Administrador, Empleado, Taller, etc.)
- **Descripcion**: Descripción del rol

#### 3. Cliente
Almacena información de los clientes
- **IdCliente**: Identificador único
- **Apellido**: Apellido del cliente
- **Nombre**: Nombre del cliente
- **Direccion**: Dirección completa
- **Telefono**: Teléfono de contacto
- **HorarioDisponible**: Horario preferido para visitas
- **FechaCreacion**: Fecha de registro en el sistema
- **Activo**: Indica si el cliente está activo

#### 4. Ticket
Representa cada solicitud/visita de un cliente
- **IdTicket**: Identificador único
- **IdCliente**: Relación con Cliente
- **IdUsuarioAsignado**: Relación con Usuario (empleado asignado)
- **IdEstadoTicket**: Relación con EstadoTicket
- **FechaCreacion**: Fecha de creación del ticket
- **FechaVisita**: Fecha programada/realizada de la visita
- **Latitud**: Coordenada de latitud para el mapa
- **Longitud**: Coordenada de longitud para el mapa
- **Observaciones**: Observaciones generales

#### 5. EstadoTicket
Catálogo de estados del ticket
- **IdEstadoTicket**: Identificador único
- **Nombre**: Nombre del estado (Sin Visitar, Visitado, Concretado)
- **Descripcion**: Descripción del estado

#### 6. TrabajoRealizado
Detalles de cada trabajo realizado al cliente
- **IdTrabajoRealizado**: Identificador único
- **IdTicket**: Relación con Ticket
- **IdCliente**: Relación con Cliente
- **Descripcion**: Descripción detallada del trabajo
- **CantidadVidrios**: Cantidad de vidrios requeridos
- **IdTipoVidrio**: Relación con TipoVidrio
- **Medidas**: Medidas de los vidrios (JSON o texto)
- **PrecioUnitario**: Precio por unidad
- **PrecioTotal**: Precio total del trabajo
- **FechaRealizacion**: Fecha de realización
- **Conforme**: Indica si el cliente estuvo conforme (true/false/null)
- **ArchivoPresupuesto**: Ruta al archivo PDF del presupuesto

#### 7. TipoVidrio
Catálogo de tipos de vidrios disponibles
- **IdTipoVidrio**: Identificador único
- **Nombre**: Nombre del tipo de vidrio
- **Descripcion**: Descripción del tipo
- **PrecioBase**: Precio base por m²

#### 8. Compulsa
Orden de trabajo para el taller
- **IdCompulsa**: Identificador único
- **IdTicket**: Relación con Ticket
- **IdTrabajoRealizado**: Relación con TrabajoRealizado
- **IdUsuarioTaller**: Relación con Usuario (empleado del taller)
- **FechaCreacion**: Fecha de creación de la compulsa
- **FechaEntregaEstimada**: Fecha estimada de entrega
- **Estado**: Estado de la compulsa (Pendiente, En Proceso, Completada)
- **Observaciones**: Observaciones para el taller

#### 9. Recordatorio
Recordatorios/tareas pendientes de los usuarios
- **IdRecordatorio**: Identificador único
- **IdUsuario**: Relación con Usuario
- **Titulo**: Título del recordatorio
- **Descripcion**: Descripción detallada
- **FechaCreacion**: Fecha de creación
- **FechaVencimiento**: Fecha límite (opcional)
- **Completado**: Indica si está completado
- **Prioridad**: Nivel de prioridad (Alta, Media, Baja)

### Relaciones

1. **Usuario → Rol**: Muchos a Uno (un usuario tiene un rol)
2. **Cliente → Ticket**: Uno a Muchos (un cliente puede tener múltiples tickets)
3. **Usuario → Ticket**: Muchos a Uno (un empleado puede tener múltiples tickets asignados)
4. **EstadoTicket → Ticket**: Uno a Muchos (un estado puede tener múltiples tickets)
5. **Ticket → TrabajoRealizado**: Uno a Muchos (un ticket puede tener múltiples trabajos)
6. **Cliente → TrabajoRealizado**: Uno a Muchos (un cliente puede tener múltiples trabajos)
7. **TipoVidrio → TrabajoRealizado**: Uno a Muchos (un tipo de vidrio puede estar en múltiples trabajos)
8. **Ticket → Compulsa**: Uno a Muchos (un ticket puede generar múltiples compulsas)
9. **TrabajoRealizado → Compulsa**: Uno a Muchos (un trabajo puede generar múltiples compulsas)
10. **Usuario → Compulsa**: Muchos a Uno (un empleado del taller puede tener múltiples compulsas)
11. **Usuario → Recordatorio**: Uno a Muchos (un usuario puede tener múltiples recordatorios)

## Requisitos Técnicos y Su Implementación

### Requisitos Mínimos

1. **4+ Clases/Tablas relacionadas con relación 1 a muchos**
   - ✅ Implementado: Cliente-Ticket, Ticket-TrabajoRealizado, Usuario-Ticket, Usuario-Recordatorio, etc.

2. **Seguridad con Login, Authorize y Roles**
   - 📍 Implementación: Controladores con `[Authorize]` y `[Authorize(Roles = "Administrador")]`
   - 📍 Área de autenticación con Identity Framework
   - 📍 Roles: Administrador, Empleado, Taller

3. **Avatar en Usuarios**
   - 📍 Implementación: Campo `Avatar` en tabla `Usuario`, upload de archivos en formulario de registro/edición

4. **Uso de Archivos (adicional al avatar)**
   - 📍 Implementación: Campo `ArchivoPresupuesto` en `TrabajoRealizado`, almacenamiento de PDFs de presupuestos

5. **ABM con Vue.js y AJAX**
   - 📍 Implementación: ABM de Recordatorios desarrollado completamente con Vue.js y llamadas AJAX

6. **Listados con Paginado**
   - 📍 Implementación: Paginado en servidor para Tickets, Clientes, TrabajosRealizados

7. **Búsqueda vía AJAX para entidades relacionadas**
   - 📍 Implementación: Búsqueda de clientes al crear/editar tickets mediante AJAX

8. **API con JWT**
   - 📍 Implementación: API REST para gestión de tickets y trabajos, autenticación con JWT
   - 📍 Colección Postman incluida para pruebas

## Tecnologías Utilizadas

- **Backend**: ASP.NET Core MVC
- **Frontend**: Razor Views, Vue.js (para ABM de Recordatorios)
- **Base de Datos**: SQL Server (o SQLite para desarrollo)
- **Autenticación**: ASP.NET Core Identity
- **API**: ASP.NET Core Web API con JWT
- **Mapas**: Integración con Google Maps API o similar

## Estructura del Proyecto

```
Proyecto final/
├── README.md
├── .gitignore
├── DiagramaER.md
├── src/
│   ├── CristalesApp/          # Proyecto principal MVC
│   ├── CristalesApp.API/      # Proyecto API
│   └── CristalesApp.Data/     # Capa de datos
└── docs/
    └── diagrama-er.png        # Diagrama visual (opcional)
```

## Instalación y Configuración

1. Clonar el repositorio
2. Restaurar paquetes NuGet
3. Configurar cadena de conexión en `appsettings.json`
4. Ejecutar migraciones: `dotnet ef database update`
5. Ejecutar seeders para datos iniciales (roles, usuarios, tipos de vidrio)

## Usuarios por Rol

- **Administrador**: admin@cristales.com / probando123
- **Empleado**: empleado@cristales.com / probando123
- **Taller**: taller@cristales.com / probando123

## Colección Postman

La colección de Postman para probar la API se encuentra en: `docs/Postman/CristalesApp_API.postman_collection.json`

## Diagrama de Entidad-Relación

Ver archivo `DiagramaER.md` o `docs/diagrama-er.png` para el diagrama visual completo.

## Estado del Proyecto

- [x] Narrativa definida
- [x] Base de datos diseñada
- [ ] Proyecto ASP.NET Core creado
- [ ] Implementación de autenticación
- [ ] Implementación de ABMs
- [ ] Implementación de API con JWT
- [ ] Integración con mapas
- [ ] Sistema de recordatorios con Vue.js
- [ ] Generación de presupuestos PDF
- [ ] Testing y documentación


