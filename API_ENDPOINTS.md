# API Endpoints - Landing Evento QR

## Base URL
```
https://apisozarusac.com/BackendJava
```

## Endpoints Públicos (No requieren autenticación)

### 1. Buscar Usuario por DNI
**Endpoint:** `GET /api/user/buscar-por-dni/{dni}`

**Descripción:** Busca un usuario en la base de datos por su DNI (username)

**Parámetros:**
- `dni` (path parameter): DNI del usuario (8 dígitos)

**Ejemplo de Request:**
```
GET https://apisozarusac.com/BackendJava/api/user/buscar-por-dni/73583958
```

**Ejemplo de Response (Éxito):**
```json
{
    "status": "success",
    "message": "Usuario encontrado",
    "data": {
        "dni": "73583958",
        "nombres": "JUAN",
        "apellidos": "PEREZ GARCIA",
        "nombresCompletos": "JUAN PEREZ GARCIA",
        "sede": "LIMA CENTRO",
        "email": "juan.perez@midas.pe",
        "telefono": "987654321"
    }
}
```

**Ejemplo de Response (No encontrado):**
```json
{
    "status": "error",
    "message": "Usuario no encontrado con el DNI proporcionado",
    "data": null
}
```

---

### 2. Crear Reserva de Evento QR
**Endpoint:** `POST /api/evento-qr/create`

**Descripción:** Crea una nueva reserva para el evento y genera un código QR

**Headers:**
```
Content-Type: application/json
```

**Body (JSON):**
```json
{
    "dni": "73583958",
    "nombresApellidos": "JUAN PEREZ GARCIA",
    "whatsapp": "987654321",
    "sede": "LIMA CENTRO"
}
```

**Ejemplo de Request:**
```
POST https://apisozarusac.com/BackendJava/api/evento-qr/create
Content-Type: application/json

{
    "dni": "73583958",
    "nombresApellidos": "JUAN PEREZ GARCIA",
    "whatsapp": "987654321",
    "sede": "LIMA CENTRO"
}
```

**Ejemplo de Response (Éxito):**
```json
{
    "status": "success",
    "message": "Reserva creada exitosamente",
    "data": {
        "id": 123,
        "dni": "73583958",
        "nombres": "JUAN PEREZ GARCIA",
        "whatsapp": "987654321",
        "sede": "LIMA CENTRO",
        "qrData": "EVENTO-MIDAS-2025-73583958-123",
        "fechaRegistro": "2025-09-30T22:45:00",
        "estado": "ACTIVO"
    }
}
```

**Ejemplo de Response (Error - DNI duplicado):**
```json
{
    "status": "error",
    "message": "Ya existe una reserva con este DNI",
    "data": null
}
```

---

## Notas Importantes

### ⚠️ Antes de usar estos endpoints:

1. **Redesplegar el Backend:**
   - Los cambios en `SecurityConfig.java` deben estar desplegados en el servidor
   - Sin estos cambios, los endpoints devolverán error 403 (Forbidden) o 500

2. **Verificar que el servicio esté corriendo:**
   ```bash
   sudo systemctl status tu-servicio-java
   ```

3. **Ver logs en caso de error:**
   ```bash
   sudo journalctl -u tu-servicio-java -f
   ```

### 🔧 Pasos para Redesplegar el Backend:

1. **Conectarse al servidor:**
   ```bash
   ssh usuario@apisozarusac.com
   ```

2. **Navegar al directorio del proyecto:**
   ```bash
   cd /ruta/al/CRM-FINAL
   ```

3. **Hacer pull de los cambios (si usas Git):**
   ```bash
   git pull origin main
   ```

4. **Compilar el proyecto:**
   ```bash
   ./mvnw clean package -DskipTests
   ```

5. **Reiniciar el servicio:**
   ```bash
   sudo systemctl restart tu-servicio-java
   ```

6. **Verificar que esté corriendo:**
   ```bash
   sudo systemctl status tu-servicio-java
   ```

---

## Testing con cURL

### Buscar Usuario:
```bash
curl -X GET "https://apisozarusac.com/BackendJava/api/user/buscar-por-dni/73583958" \
  -H "Content-Type: application/json"
```

### Crear Reserva:
```bash
curl -X POST "https://apisozarusac.com/BackendJava/api/evento-qr/create" \
  -H "Content-Type: application/json" \
  -d '{
    "dni": "73583958",
    "nombresApellidos": "JUAN PEREZ GARCIA",
    "whatsapp": "987654321",
    "sede": "LIMA CENTRO"
  }'
```

---

## Archivos Modificados

### Backend (CRM-FINAL):
- `src/main/java/com/midas/crm/config/SecurityConfig.java`
  - Agregada variable `eventoQrRoute`
  - Agregadas rutas públicas para `/api/user/buscar-por-dni/**` y `/api/evento-qr/**`

### Frontend (landing-eventoqr):
- `src/pages/registro.astro`
  - Actualizada URL de búsqueda de usuario
  - Agregado CDN de qrcode desde unpkg

---

## Troubleshooting

### Error 403 Forbidden
- **Causa:** El SecurityConfig no está actualizado en el servidor
- **Solución:** Redesplegar el backend con los cambios

### Error 404 Not Found
- **Causa:** La URL del endpoint es incorrecta
- **Solución:** Verificar que estés usando `/api/user/` y no `/api/usuarios/`

### Error 500 Internal Server Error
- **Causa:** Error en el servidor (ver logs)
- **Solución:** Revisar los logs del servidor con `journalctl`

### CORS Error
- **Causa:** El frontend está en un dominio diferente
- **Solución:** Verificar la configuración de CORS en SecurityConfig

