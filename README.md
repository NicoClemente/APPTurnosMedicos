# Sistema de Gestión de Turnos Médicos

**Integrantes:** Nicolas Clemente y Santiago Schroh

Una API REST desarrollada en Flask para la gestión completa de turnos médicos, que permite administrar pacientes, médicos, agendas de atención y turnos de forma eficiente.

## 📋 Características

- **Gestión de Pacientes**: CRUD completo para registro y administración de pacientes
- **Gestión de Médicos**: Administración de médicos con sistema de habilitación/deshabilitación
- **Agenda Médica**: Configuración de horarios de atención por día de la semana
- **Sistema de Turnos**: Reserva y gestión de citas médicas con validaciones avanzadas
- **Validaciones Inteligentes**: Control de horarios, fechas y disponibilidad
- **Persistencia en CSV**: Almacenamiento de datos en archivos CSV para simplicidad

## 🚀 Tecnologías Utilizadas

- **Python 3.x**
- **Flask** - Framework web
- **CSV** - Almacenamiento de datos
- **Requests** - Para generar datos de prueba desde API externa
- **Datetime** - Manejo de fechas y horarios

## 📁 Estructura del Proyecto

```
proyecto/
├── app.py                          # Archivo principal de la aplicación
├── controladores/                  # Controladores (rutas) de la API
│   ├── __init__.py
│   ├── ruta_pacientes.py          # Endpoints de pacientes
│   ├── ruta_medicos.py            # Endpoints de médicos
│   ├── ruta_agenda_medicos.py     # Endpoints de agenda médica
│   └── ruta_turnos_medicos.py     # Endpoints de turnos
└── modelos/                       # Modelos y lógica de negocio
    ├── __init__.py
    ├── pacientes.py               # Lógica de pacientes
    ├── pacientes.csv              # Datos de pacientes
    ├── medicos.py                 # Lógica de médicos
    ├── medicos.csv                # Datos de médicos
    ├── agenda_medicos.py          # Lógica de agenda médica
    ├── agenda_medicos.csv         # Datos de agenda
    ├── turnos.py                  # Lógica de turnos
    └── turnos.csv                 # Datos de turnos
```

## 🛠️ Instalación y Configuración

### Prerrequisitos
- Python 3.7 o superior
- pip (gestor de paquetes de Python)

### Pasos de instalación

1. **Clonar el repositorio**
```bash
git clone <url-del-repositorio>
cd sistema-turnos-medicos
```

2. **Crear entorno virtual (recomendado)**
```bash
python -m venv venv
source venv/bin/activate  # En Windows: venv\Scripts\activate
```

3. **Instalar dependencias**
```bash
pip install flask requests
```

4. **Ejecutar la aplicación**
```bash
python app.py
```

La aplicación estará disponible en `http://localhost:5000`

## 📚 Documentación de la API

### 👥 Pacientes

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| `GET` | `/pacientes` | Obtener todos los pacientes |
| `GET` | `/pacientes/{id}` | Obtener paciente por ID |
| `POST` | `/pacientes` | Crear nuevo paciente |
| `PUT` | `/pacientes/{id}` | Modificar paciente existente |

**Estructura de datos del paciente:**
```json
{
    "dni": "string",
    "nombre": "string",
    "apellido": "string",
    "telefono": "string",
    "email": "string",
    "direccion_calle": "string",
    "direccion_numero": "string"
}
```

### 👨‍⚕️ Médicos

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| `GET` | `/medicos` | Obtener todos los médicos |
| `GET` | `/medicos/{id}` | Obtener médico por ID |
| `POST` | `/medicos` | Crear nuevo médico |
| `PUT` | `/medicos/{id}` | Modificar médico existente |
| `PUT` | `/medicos/desabilitar/{id}` | Deshabilitar médico |

**Estructura de datos del médico:**
```json
{
    "dni": "string",
    "nombre": "string",
    "apellido": "string",
    "matricula": "string",
    "telefono": "string",
    "email": "string"
}
```

### 📅 Agenda Médica

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| `GET` | `/agenda_medicos` | Obtener agendas disponibles |
| `POST` | `/agenda_medicos` | Crear nueva agenda |
| `PUT` | `/agenda_medicos/{id_medico}` | Modificar agenda existente |
| `DELETE` | `/agenda_medicos/{id_medico}` | Eliminar agenda del médico |

**Estructura de datos de la agenda:**
```json
{
    "id_medico": "integer",
    "dia_numero": "integer (0-6, donde 0=Lunes)",
    "hora_inicio": "string (HH:MM)",
    "hora_fin": "string (HH:MM)"
}
```

### 🗓️ Turnos

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| `GET` | `/turnos` | Obtener turnos pendientes |
| `GET` | `/turnos/{id_medico}` | Obtener turnos de un médico |
| `POST` | `/turnos` | Crear nuevo turno |
| `DELETE` | `/turnos/{id_medico}/{id_paciente}` | Eliminar turno |

**Estructura de datos del turno:**
```json
{
    "id_medico": "integer",
    "id_paciente": "integer",
    "hora_turno": "string (HH:MM)",
    "fecha_solicitud": "string (YYYY/MM/DD)"
}
```

## ⚠️ Validaciones Importantes

### 🕐 Turnos
- Los turnos se asignan cada **15 minutos** (ej: 09:00, 09:15, 09:30, 09:45)
- Solo se pueden solicitar turnos con **máximo 30 días** de anticipación
- No se permiten turnos en fechas pasadas
- Se valida que el médico esté habilitado
- Se verifica disponibilidad de horario según la agenda del médico

### 📅 Agenda
- Los días se representan con números: 0=Lunes, 1=Martes, ..., 6=Domingo
- La hora de inicio debe ser menor a la hora de fin
- No se permite duplicar agendas para el mismo médico y día
- Las horas deben estar en formato HH:MM

### 👨‍⚕️ Médicos
- Los médicos pueden ser habilitados/deshabilitados
- Solo médicos habilitados pueden tener turnos asignados

## 🔄 Datos de Prueba

La aplicación genera automáticamente datos de prueba al iniciar:
- **3 pacientes** con datos aleatorios de la API RandomUser
- **3 médicos** con datos aleatorios de la API RandomUser

## 📝 Ejemplos de Uso

### Crear un nuevo paciente
```bash
curl -X POST http://localhost:5000/pacientes \
  -H "Content-Type: application/json" \
  -d '{
    "dni": "12345678",
    "nombre": "Juan",
    "apellido": "Pérez",
    "telefono": "123456789",
    "email": "juan@email.com",
    "direccion_calle": "Av. Principal",
    "direccion_numero": "123"
  }'
```

### Crear una agenda para un médico
```bash
curl -X POST http://localhost:5000/agenda_medicos \
  -H "Content-Type: application/json" \
  -d '{
    "id_medico": 1,
    "dia_numero": 1,
    "hora_inicio": "09:00",
    "hora_fin": "17:00"
  }'
```

### Reservar un turno
```bash
curl -X POST http://localhost:5000/turnos \
  -H "Content-Type: application/json" \
  -d '{
    "id_medico": 1,
    "id_paciente": 1,
    "hora_turno": "10:15",
    "fecha_solicitud": "2025/07/15"
  }'
```

## 🐛 Manejo de Errores

La API retorna códigos de estado HTTP apropiados:
- `200` - Operación exitosa
- `201` - Recurso creado exitosamente
- `400` - Error en los datos enviados
- `404` - Recurso no encontrado

Todos los errores incluyen un mensaje descriptivo en formato JSON:
```json
{
    "error": "Descripción del error"
}
```

## 🤝 Contribuciones

Este proyecto fue desarrollado como parte de un trabajo académico por:
- **Nicolas Clemente**
- **Santiago Schroh**

## 📄 Licencia

Este proyecto es de uso académico y educativo.

## 🔧 Mejoras Futuras

- Implementación de base de datos relacional
- Sistema de autenticación y autorización
- Interfaz web para usuarios finales
- Notificaciones por email/SMS
- Historial médico de pacientes
- Reportes y estadísticas
